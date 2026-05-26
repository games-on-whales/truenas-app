# Wolf TrueNAS App

TrueNAS SCALE community application catalog entry for [Wolf](https://github.com/games-on-whales/wolf) — an open-source game streaming server compatible with Moonlight and other NVIDIA GameStream clients.

## Requirements

- TrueNAS SCALE 24.10 (Electric Eel) or later
- A GPU (NVIDIA, AMD, or Intel) with appropriate drivers installed on the host
- For NVIDIA GPUs: NVIDIA drivers must be installed on the TrueNAS host

## Repository Structure

```
trains/
└── community/
    └── wolf/
        ├── app_versions.json     # Version index
        └── 1.0.0/
            ├── metadata.yaml     # App metadata
            ├── app-readme.md     # User-facing documentation shown in TrueNAS UI
            ├── CHANGELOG.md      # Version history
            ├── questions.yaml    # UI configuration schema
            └── templates/
                └── docker-compose.yaml  # Container definition template
```

## What is Wolf?

Wolf is a game streaming server that lets you stream games from your server to any device running a Moonlight-compatible client. It handles GPU encoding, virtual display creation, audio capture, and virtual input device management entirely in software, making it well-suited for a headless TrueNAS server.

Wolf manages per-session containers for running games, so it requires access to the Docker socket and a range of hardware devices.

## Key Features

- GPU-accelerated video encoding (NVIDIA NVENC, AMD AMF, Intel QuickSync)
- Virtual display and audio output per streaming session
- Multiple simultaneous sessions
- Gamepad, keyboard, and mouse passthrough
- Compatible with Moonlight on Android, iOS, PC, and TV

## Hardware Access

Wolf requires elevated privileges to function:

| Resource | Purpose |
|---|---|
| `/dev/dri/*` | GPU access for rendering and encoding |
| `/dev/uinput` | Creating virtual input devices |
| `/dev/input/event*` | Reading input events |
| `/var/run/docker.sock` | Managing per-session game containers |
| `/tmp/sockets` | IPC between Wolf and virtual display/audio |
| `device_cgroup_rules: c 13:* rmw` | Dynamic input device creation |

## Development

To add a new app version, create a new directory under `trains/community/wolf/` named after the version and update `app_versions.json`.

### Validating the App

```bash
# Install the TrueNAS apps dev tools (requires Python 3.11+)
pip install git+https://github.com/truenas/apps.git#subdirectory=dev_tools

# Validate the catalog entry
truenas-app-validate trains/community/wolf/1.0.0
```

## References

- [Wolf Documentation](https://games-on-whales.github.io/wolf/stable)
- [Wolf GitHub](https://github.com/games-on-whales/wolf)
- [TrueNAS Apps Catalog](https://github.com/truenas/apps)
- [Moonlight Client](https://moonlight-stream.org/)
