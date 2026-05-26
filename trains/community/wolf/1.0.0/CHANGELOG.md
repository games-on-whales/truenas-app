# Changelog

## [1.0.0] — 2026-05-26

### Added
- Initial TrueNAS SCALE app for Wolf game streaming server
- Wolf Den management dashboard (enabled by default, port 8080)
  - Web UI for pairing Moonlight clients, managing app definitions, monitoring sessions
  - Connects to Wolf's REST API; shares the Wolf config volume
  - Enabled/disabled and port are independently configurable
- Support for NVIDIA, AMD, and Intel GPU acceleration
- Host networking mode for lowest-latency streaming
- Port-mapping mode as an alternative to host networking
- iX Volume and host path options for configuration persistence
- Configurable log level (DEBUG / INFO / WARNING / ERROR) for both Wolf and Wolf Den
- Timezone configuration
- Optional additional environment variables
- Docker socket passthrough for Wolf's per-session game container management
- Full `/dev` passthrough with `c 13:* rmw` cgroup rule for virtual input devices
- TrueNAS portal link points to Wolf Den (port 8080)
