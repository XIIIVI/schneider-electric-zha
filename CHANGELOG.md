# Changelog

All notable changes to this project are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [2.0.0] - 2026-09-08

### Added

- Support for the Schneider Electric Wiser shutter switch (`NHPB/SHUTTER/1`,
  `1GANG/SHUTTER/1`) in all three blueprints: the device selector accepts the
  new models, and every rocker case gained a Window Covering trigger
  (cluster `258`, commands `up_open` / `down_close`) alongside the existing
  OnOff trigger (cluster `6`). `up_open` is treated as ON, `down_close` as OFF.
- Collapsed **Installation** section in every blueprint asking for one or more
  installation rooms, one or more labels, and an icon. In `dual-rocker.yaml` the
  rooms and labels are added to the target of each ON/OFF action; in
  `single-rocker.yaml` and `four-rockers.yaml` they are exposed to the
  user-supplied actions as the `install_area`, `install_labels` and
  `install_icon` variables.
- `source_url`, `author` and `homeassistant.min_version` in every blueprint, so
  Home Assistant can import them from their GitHub URL and offer
  **Re-import blueprint** afterwards.
- Separate ON and OFF actions in `single-rocker.yaml` (`action_on`,
  `action_off`).
- README: supported-device table, `zha_event` reference for both device
  families, **Import blueprint** badges, and links to the Home Assistant,
  zigpy and Schneider Electric documentation.
- Repository topics `schneider-electric`, `zha`, `home-assistant`, `blueprint`,
  and a `Tags: schneider electric, zha` line in every blueprint description —
  the blueprint schema has no tag key of its own.

### Changed

- **Breaking**: the project is now named `schneider-electric-zha`, and the
  blueprints moved from `blueprints/automation/schneider-s520531/` to
  `blueprints/automation/schneider-electric-zha/`. Existing manual
  installations must be copied to the new path and their automations recreated.
- **Breaking**: `single-rocker.yaml` replaces the single `rocker_action` input
  with `action_on` and `action_off`. It used to run the same action whichever
  way the rocker was pressed.
- **Breaking**: all three blueprints now require Home Assistant 2024.10.0 or
  newer and use the `triggers:` / `actions:` / `action:` syntax.
- Blueprint names are no longer AIRLINK-specific, since they now cover both
  device families.

### Fixed

- `single-rocker.yaml` quoted its trigger ids `on` and `off`, which YAML 1.1
  parses as the booleans `true` and `false`.

### Removed

- `switch-dual.yaml`, which was byte-for-byte identical to `dual-rocker.yaml`.

## [1.0.0] - 2026-09-07

### Added

- Initial Home Assistant blueprints for Schneider Electric Wiser AIRLINK ZHA
  wall switches (`S520531`, `S920531`): single rocker, dual rocker and
  four rockers, plus the project README.

[2.0.0]: https://github.com/XIIIVI/schneider-electric-zha/releases/tag/v2.0.0
[1.0.0]: https://github.com/XIIIVI/schneider-electric-zha/releases/tag/v1.0.0
