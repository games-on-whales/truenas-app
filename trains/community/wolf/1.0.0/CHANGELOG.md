# Changelog

## [1.0.0] — 2026-05-26

### Added
- Initial TrueNAS SCALE app for Wolf game streaming server
- Support for NVIDIA, AMD, and Intel GPU acceleration
- Host networking mode for lowest-latency streaming
- Port-mapping mode as an alternative to host networking
- iX Volume and host path options for configuration persistence
- Configurable log level (DEBUG / INFO / WARNING / ERROR)
- Timezone configuration
- Optional additional environment variables
- Docker socket passthrough for Wolf's per-session game container management
- Full `/dev` passthrough with `c 13:* rmw` cgroup rule for virtual input devices
- Wolf Web UI accessible on port 47990 (configurable)
