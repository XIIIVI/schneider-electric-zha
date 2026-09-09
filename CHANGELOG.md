# Changelog

All notable changes to this project are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [5.0.0] - 2026-09-09

### Changed

- **Breaking**: `single-rocker.yaml` and `four-rockers.yaml` are renamed
  `two-functions.yaml` and `four-functions.yaml`. Both blueprints assign an
  independent action to each up/down press rather than to an entity, and
  "rocker" was counting physical paddles while the number that actually varies
  is how many such presses (functions) the blueprint exposes — one endpoint
  gives 2, two endpoints give 4. Each blueprint's description now links to the
  other. Home Assistant resolves an installed automation's blueprint by file
  path, so **every automation created from either blueprint must be recreated**
  after updating.

## [4.0.0] - 2026-09-08

### Changed

- **Breaking**: `socket-auto-off.yaml` no longer asks for the socket's power
  sensor. A device selector plus an entity selector for that same device's
  sensor is two inputs free to disagree; the trigger is now a `template` one
  resolving the sensor from the socket with `device_entities()`, filtered on
  `device_class: power`. The socket is the only entity input left. Automations
  created from 3.0.0 must be recreated.

### Fixed

- `socket-auto-off.yaml` no longer switches a socket off while its power sensor
  reads `unavailable` or `unknown`: the trigger keeps only readings that are
  numbers, so a socket whose sensor dropped off the network stays on. A socket
  exposing several power sensors now waits for the highest of them.

## [3.0.0] - 2026-09-08

### Added

- `socket-auto-off.yaml` — switches a Schneider Electric Zigbee socket
  (`SOCKET/OUTLET/1`, `SOCKET/OUTLET/2`) off once its own power sensor stays
  below a threshold for long enough. Threshold and duration are inputs
  (20 W for 3 minutes by default) and the socket is turned off through its
  device, so renaming the switch entity cannot break it.
- README: socket row in the supported-device table, import badge for the new
  blueprint, and a **Name, icon, area, labels and category** section explaining,
  with the Home Assistant source it comes from, why a blueprint cannot set any
  of them and where to set them instead.

### Changed

- **Breaking**: `install_area` and `install_labels` are renamed `extra_areas`
  and `extra_labels`, and the **Installation** section is renamed
  **Additional targets**. They never carried automation metadata — they add
  targets to the actions — and the old names said otherwise. Automations created
  from 2.0.0 must be recreated.

### Removed

- **Breaking**: the `install_icon` input. Home Assistant's automation schema is
  built with `script.make_script_schema(..., extra=vol.PREVENT_EXTRA)` and has no
  `icon`, `labels`, `area` or `category` key, so no blueprint can set them; the
  input only ever fed a variable nothing read. `alias` is accepted by the schema
  but `{**processed, **self.config_with_inputs}` in the blueprint model lets the
  automation entry's own name win, so a blueprint cannot set the name either.

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

[5.0.0]: https://github.com/XIIIVI/schneider-electric-zha/releases/tag/v5.0.0
[4.0.0]: https://github.com/XIIIVI/schneider-electric-zha/releases/tag/v4.0.0
[3.0.0]: https://github.com/XIIIVI/schneider-electric-zha/releases/tag/v3.0.0
[2.0.0]: https://github.com/XIIIVI/schneider-electric-zha/releases/tag/v2.0.0
[1.0.0]: https://github.com/XIIIVI/schneider-electric-zha/releases/tag/v1.0.0
