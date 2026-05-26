# Wolf

Wolf is an open-source game streaming server that lets you stream games from your TrueNAS server to any device running a [Moonlight](https://moonlight-stream.org/) client (Android, iOS, PC, Apple TV, and more).

## Features

- **GPU-accelerated encoding** — supports NVIDIA NVENC, AMD AMF, and Intel QuickSync
- **Multiple simultaneous sessions** — each client gets an isolated virtual display and audio output
- **Full input passthrough** — gamepad, keyboard, and mouse are forwarded to the game session
- **Zero-client-side setup** — pair Moonlight with your server and play
- **Wolf Den dashboard** — web UI for managing paired clients, app definitions, and active sessions

## Requirements

### GPU

A GPU with hardware video encoding is strongly recommended:

| Vendor | Encoder | Notes |
|--------|---------|-------|
| NVIDIA | NVENC | Requires NVIDIA drivers installed on TrueNAS host |
| AMD | AMF / VAAPI | Requires `amdgpu` kernel driver |
| Intel | QuickSync / VAAPI | Requires `i915` kernel driver |

### NVIDIA: nvidia-driver-vol

NVIDIA GPUs require a pre-built Docker volume containing the host's NVIDIA userspace libraries. Before deploying this app, create it:

```bash
# Run once on the TrueNAS host (or use the TrueNAS NVIDIA plugin if available)
docker volume create nvidia-driver-vol
# Then populate it — see: https://games-on-whales.github.io/wolf/stable/user/quickstart.html
```

The volume must be named exactly `nvidia-driver-vol`. If it doesn't exist, Wolf will fail to start.

### Pairing

On first run Wolf generates a certificate and starts listening on its configured ports. Open Moonlight, add your TrueNAS IP as a host, and enter the PIN shown in Wolf Den to pair.

## Ports

| Port | Protocol | Purpose |
|------|----------|---------|
| 47984 | TCP | HTTPS (Moonlight discovery) |
| 47989 | TCP | HTTP (Moonlight discovery) |
| 48010 | TCP/UDP | RTSP / Input |
| 47998 | UDP | Video stream |
| 47999 | UDP | Control |
| 48000 | UDP | Audio stream |
| 48002 | UDP | Control |
| **8080** | **TCP** | **Wolf Den management dashboard** |

**Host networking is recommended** for lowest latency and to avoid NAT traversal issues.

## Storage

Four separate volumes are created — each can be backed up independently:

| Volume | Container path | Contents |
|--------|---------------|---------|
| wolf-cfg | `/etc/wolf/cfg` | config.toml, TLS certs, paired client records |
| wolf-steam | `/etc/wolf/steam` | Steam library metadata and per-app state |
| wolf-den | `/app/wolf-den` | Wolf Den session DB and settings |
| wolf-covers | `/etc/wolf/covers` | Game cover art |

> **Back up wolf-cfg.** Losing it requires re-pairing all Moonlight clients.

## First-time setup

1. Deploy the app with default settings.
2. Open the **Wolf Den** dashboard — click the **Web Portal** button in TrueNAS, or go to `http://<your-truenas-ip>:8080`.
3. Open Moonlight on your client device and add your TrueNAS IP as a PC.
4. Moonlight will prompt for a PIN — enter it in Wolf Den under **Pair**.
5. You should now see Wolf's default streaming apps (Desktop, Steam, etc.) in Moonlight.

## Advanced: Adding Games

Wolf's configuration lives in `/etc/wolf/cfg/config.toml`. Use Wolf Den to manage app definitions, or edit the file directly and restart the app. See the [Wolf documentation](https://games-on-whales.github.io/wolf/stable) for full config reference.

## Troubleshooting

- **No GPU encoding**: Ensure the correct GPU vendor is selected and host drivers are loaded (`nvidia-smi` or `ls /dev/dri`). For NVIDIA, confirm `nvidia-driver-vol` exists.
- **NVIDIA black screen**: `nvidia_drm.modeset=1` must be set as a kernel parameter. Check Tools > System Drivers in TrueNAS.
- **Cannot pair**: Open Wolf Den at port 8080 and check the connection indicator. Verify both containers are running in the TrueNAS app log viewer.
- **Wolf Den shows socket error**: Wolf Den waits up to `socketTimeout` seconds for `/tmp/sockets/wolf.sock`. If Wolf takes longer to start, increase the timeout in Wolf Den Configuration.
- **Input not working**: Wolf needs `/dev/uinput` and `/dev/uhid`. Confirm privileged mode is enabled and both devices exist on the host.
- **Audio issues**: Wolf writes PulseAudio/PipeWire sockets to the `wolf_socket` named volume at `/tmp/sockets`.
