# Wolf

Wolf is an open-source game streaming server that lets you stream games from your TrueNAS server to any device running a [Moonlight](https://moonlight-stream.org/) client (Android, iOS, PC, Apple TV, and more).

## Features

- **GPU-accelerated encoding** — supports NVIDIA NVENC, AMD AMF, and Intel QuickSync
- **Multiple simultaneous sessions** — each client gets an isolated virtual display and audio output
- **Full input passthrough** — gamepad, keyboard, and mouse are forwarded to the game session
- **Zero-client-side setup** — pair Moonlight with your server and play

## Requirements

### GPU

A GPU with hardware video encoding is strongly recommended:

| Vendor | Encoder | Notes |
|--------|---------|-------|
| NVIDIA | NVENC | Requires NVIDIA drivers installed on TrueNAS host |
| AMD | AMF / VAAPI | Requires `amdgpu` kernel driver |
| Intel | QuickSync / VAAPI | Requires `i915` kernel driver |

### Pairing

On first run Wolf generates a certificate and starts listening on its configured ports. Open the Moonlight app, add your TrueNAS IP as a host, and enter the PIN shown in the Wolf web UI (port 47990 by default) to pair.

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
| 47990 | TCP | Wolf Web UI (management) |

**Host networking is recommended** for lowest latency and to avoid NAT traversal issues.

## Storage

Wolf stores its configuration (certificates, paired clients, app definitions) in `/etc/wolf` inside the container. Map this to an iX Volume or a host path to persist configuration across container restarts and upgrades.

## First-time setup

1. Deploy the app with default settings.
2. Open the Wolf Web UI at `http://<your-truenas-ip>:47990`.
3. Open Moonlight on your client device and add your TrueNAS IP as a PC.
4. Moonlight will prompt for a PIN — enter it in the Wolf Web UI under **Pair**.
5. You should now see Wolf's default streaming apps (Desktop, Steam, etc.) in Moonlight.

## Advanced: Adding Games

Wolf's configuration lives in `/etc/wolf/config.toml`. See the [Wolf documentation](https://games-on-whales.github.io/wolf/stable) for instructions on adding custom app definitions and per-app Docker container settings.

## Troubleshooting

- **No GPU encoding**: Ensure the correct GPU vendor is selected and host drivers are loaded (`nvidia-smi` or `ls /dev/dri`).
- **Cannot pair**: Verify the app is running and port 47990 is reachable. Check Wolf logs via the TrueNAS app log viewer.
- **Input not working**: The container needs access to `/dev/uinput`. Confirm privileged mode is enabled.
- **Audio issues**: Wolf uses PulseAudio/PipeWire sockets in `/tmp/sockets`. Ensure this path is writable.
