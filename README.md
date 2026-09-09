# schneider-electric-zha

Home Assistant blueprints for **Schneider Electric** Zigbee wall switches paired
through the **ZHA** integration.

Tags: `schneider electric`, `zha`

## Supported devices

| Family | ZHA model | Typical references | Rocker events |
|---|---|---|---|
| Wiser AIRLINK / FLS switch | `FLS/AIRLINK/4` | `S520531`, `S920531` | OnOff cluster `6` — `on` / `off` |
| Wiser shutter switch | `NHPB/SHUTTER/1`, `1GANG/SHUTTER/1` | `S520567`, `S520567W` and equivalents | Window Covering cluster `258` (`0x0102`) — `up_open` / `down_close` |
| Zigbee socket | `SOCKET/OUTLET/1`, `SOCKET/OUTLET/2` | Wiser smart sockets and outlets | No rocker event — driven by the socket's own power sensor |

The two switch families do **not** speak the same Zigbee cluster, so every
rocker blueprint listens to both. On a shutter switch, `up_open` is treated as **ON** and
`down_close` as **OFF**.

Every trigger requires `args: []` so the duplicated `attribute_updated` events
some firmwares emit never fire the automation twice.

### ZHA event reference

This applies to the rocker blueprints only — `socket-auto-off.yaml` never looks
at a `zha_event`.

Pressing a rocker fires a `zha_event`. Watch it live under
**Developer tools → Events → listen to `zha_event`**:

```yaml
# AIRLINK / FLS
device_id: <device>
endpoint_id: 21        # 22 for the second rocker pair in 4-rocker mode
cluster_id: 6
command: "on"          # or "off"
args: []

# Wiser shutter switch
device_id: <device>
endpoint_id: 21        # endpoint 5 is the cover entity, not the rocker
cluster_id: 258
command: up_open       # or down_close, or stop (not handled by these blueprints)
args: []
```

## Blueprints

| Blueprint | What it does | Import |
|---|---|---|
| `two-functions.yaml` | One endpoint, two functions: one action when pressed up, one when pressed down. | [![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2FXIIIVI%2Fschneider-electric-zha%2Fblob%2Fmain%2Fblueprints%2Fautomation%2Fschneider-electric-zha%2Ftwo-functions.yaml) |
| `dual-rocker.yaml` | Two rockers control two entities (ON/OFF each). | [![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2FXIIIVI%2Fschneider-electric-zha%2Fblob%2Fmain%2Fblueprints%2Fautomation%2Fschneider-electric-zha%2Fdual-rocker.yaml) |
| `four-functions.yaml` | Two endpoints, four functions: one action per up/down press of each. | [![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2FXIIIVI%2Fschneider-electric-zha%2Fblob%2Fmain%2Fblueprints%2Fautomation%2Fschneider-electric-zha%2Ffour-functions.yaml) |
| `socket-auto-off.yaml` | Switches a socket off once its power stays below a threshold long enough. | [![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2FXIIIVI%2Fschneider-electric-zha%2Fblob%2Fmain%2Fblueprints%2Fautomation%2Fschneider-electric-zha%2Fsocket-auto-off.yaml) |

`socket-auto-off.yaml` asks for the socket, a power threshold and a duration —
nothing else. It resolves the socket's power sensor from the device itself with
`device_entities()`, so there is no second entity input to keep in sync, and it
never fires while that sensor reads `unavailable` or `unknown`.

All four require **Home Assistant 2024.10.0** or newer (`triggers:` / `actions:`
syntax and input sections).

### Additional targets

Every blueprint ends with a collapsed **Additional targets** section asking for
extra areas and extra labels. Both default to empty, and empty means "no extra
target" — only the entities you selected change.

In `dual-rocker.yaml` they are added to the `target:` of each ON/OFF action and
in `socket-auto-off.yaml` to the `target:` of the switch-off, so the areas and
the labels are switched along with the selected entities. In
`two-functions.yaml` and `four-functions.yaml` the actions are yours, so the two
values are exposed as the `extra_areas` and `extra_labels` variables instead —
usable from your own action sequence, e.g.
`target: {area_id: "{{ extra_areas }}"}`.

### Name, icon, area, labels and category

**A blueprint cannot set any of them.** This is a Home Assistant limit, not an
omission:

- [`components/automation/config.py`](https://github.com/home-assistant/core/blob/dev/homeassistant/components/automation/config.py)
  builds its schema with `script.make_script_schema(..., extra=vol.PREVENT_EXTRA)`
  and accepts only `id`, `alias`, `description`, `trace`, `initial_state`,
  `triggers`, `conditions`, `variables`, `trigger_variables`, `actions`, `mode`,
  `max` and `max_exceeded`. An `icon:`, `labels:`, `area:` or `category:` key in
  an automation is a **validation error**, not an ignored key. Those four live in
  the entity registry, the label registry and the category registry, written by
  the WebSocket API — that is, by the UI.
- `alias` (the name) *is* accepted, but
  [`components/blueprint/models.py`](https://github.com/home-assistant/core/blob/dev/homeassistant/components/blueprint/models.py)
  merges with `combined = {**processed, **self.config_with_inputs}`: the
  automation entry's own `alias` overrides anything the blueprint provides, and
  the UI always writes one.

So set them on the automation once, right after creating it:

| What | Where |
|---|---|
| **Name** | Asked in the save dialog, pre-filled with the blueprint name. Rename later with the ⋮ menu → **Rename**. |
| **Icon** | ⋮ menu → **Rename** → icon picker next to the name field. |
| **Area** | ⋮ menu → **Move to area**. |
| **Labels** | ⋮ menu → **Add label**, or select several automations in the list and label them at once. |
| **Category** | ⋮ menu → **Move to category**. |

## Installation

### Import from this repository (recommended)

Click an **Import blueprint** badge above, or in Home Assistant go to
**Settings → Automations & scenes → Blueprints → Import blueprint** and paste the
GitHub URL of the blueprint. Home Assistant records the URL as `source_url`, so
**Re-import blueprint** later pulls the updates.

### Manual copy

1. Copy `blueprints/automation/schneider-electric-zha/` into your Home Assistant
   config: `/config/blueprints/automation/schneider-electric-zha/`.
2. **Settings → Automations & scenes → Blueprints → Reload blueprints**.
3. Create a new automation from the blueprint you want.

## Device pairing (ZHA)

1. **Settings → Devices & services → Add integration → Zigbee Home Automation**.
2. Click **Add device** to open the join window.
3. Put the switch in pairing mode according to its manual.
4. Wait for the device to appear in ZHA, then rename it and assign its area.

### Enable 4-rocker mode (AIRLINK only)

- Press and hold **buttons 2 and 3 for 10 seconds**.
- Success is confirmed by the **red LED**.

Endpoint 22 then emits its own `on` / `off` events and `four-functions.yaml`
becomes usable. Shutter switches have a single rocker endpoint (21), so only the
first pair of actions of `four-functions.yaml` ever fires on them.

## Factory reset

Follow Schneider Electric's instructions for your exact reference, then pair the
device again in ZHA. After a reset:

1. Remove the automations bound to the old device entry.
2. Re-pair the device.
3. Re-select the new device in each blueprint automation.

## Official documentation

### Home Assistant

- [Blueprints](https://www.home-assistant.io/docs/blueprint/)
- [Blueprint schema](https://www.home-assistant.io/docs/blueprint/schema/)
- [Blueprint selectors](https://www.home-assistant.io/docs/blueprint/selectors/)
- [Automation YAML syntax](https://www.home-assistant.io/docs/automation/yaml/)
- [Zigbee Home Automation (ZHA) integration](https://www.home-assistant.io/integrations/zha/)
- [My Home Assistant links](https://www.home-assistant.io/integrations/my/)

### Zigbee device handlers

- [zigpy/zha-device-handlers](https://github.com/zigpy/zha-device-handlers)
- [Schneider Electric shutter quirk (`NHPB/SHUTTER/1`, `1GANG/SHUTTER/1`)](https://github.com/zigpy/zha-device-handlers/blob/dev/zhaquirks/schneiderelectric/shutters.py)
- [Schneider Electric switch quirks](https://github.com/zigpy/zha-device-handlers/tree/dev/zhaquirks/schneiderelectric)

### Schneider Electric

- [Wiser home automation](https://www.se.com/ww/en/home/smart-home/wiser/)
- [Product search — `S520531`](https://www.se.com/ww/en/search/S520531/)
- [Product search — `S920531`](https://www.se.com/ww/en/search/S920531/)
- [Product search — `S520567`](https://www.se.com/ww/en/search/S520567/)
- [Downloads and manuals](https://www.se.com/ww/en/download/)
