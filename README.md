# schneider-electric-airlink-zha

Home Assistant blueprints for **Schneider Electric Wiser AIRLINK** Zigbee wall switches, focused on:

- `S520531`
- `S920531`

These blueprints are designed for the **ZHA** integration and handle noisy duplicated ZHA events by matching only actionable command events (`args: []`).

## Included blueprints

- `blueprints/automation/schneider-s520531/single-rocker.yaml`
  - One rocker triggers one configured action.
- `blueprints/automation/schneider-s520531/switch-dual.yaml`
  - Two rockers control two entities (ON/OFF each).
- `blueprints/automation/schneider-s520531/dual-rocker.yaml`
  - Same logic as `switch-dual.yaml` (alias file for compatibility).
- `blueprints/automation/schneider-s520531/four-rockers.yaml`
  - Four independent actions using **endpoint 21 and 22** with their two states:
    - endpoint 21 + `on`
    - endpoint 21 + `off`
    - endpoint 22 + `on`
    - endpoint 22 + `off`

## Installation

1. Copy this repository's `blueprints/automation/schneider-s520531/` folder into your Home Assistant config:
   - `/config/blueprints/automation/schneider-s520531/`
2. In Home Assistant, go to **Settings → Automations & Scenes → Blueprints**.
3. Click **Reload Blueprints**.
4. Create a new automation from the blueprint you want.

## Device pairing (ZHA)

1. Open Home Assistant: **Settings → Devices & Services → Add Integration → Zigbee Home Automation (ZHA)**.
2. Click **Add Device** to enable join mode.
3. Put the AIRLINK switch in pairing mode according to the device manual.
4. Wait until the device appears in ZHA, then rename it and assign area if needed.

### Enable 4-rocker mode

To enable 4-rocker mode on the device:

- Press and hold **buttons 2 and 3 for 10 seconds**.
- Confirm success when the **red LED** lights up.

After that, re-test ZHA events and use the `four-rockers.yaml` blueprint.

## Factory reset

Perform factory reset according to Schneider Electric instructions for your exact model (`S520531` / `S920531`), then pair again in ZHA.

If you reset the device:

1. Remove old automations using the previous device entry if needed.
2. Re-pair the device in ZHA.
3. Re-select the new device in each blueprint automation.

## Official Schneider Electric documentation

- [Schneider Electric search — `S520531`](https://www.se.com/ww/en/search/S520531/)
- [Schneider Electric search — `S920531`](https://www.se.com/ww/en/search/S920531/)
- [Schneider Electric home](https://www.se.com/)

> Tip: search these references directly on Schneider Electric support pages to get the latest PDF manual and wiring notes for your market.

## Notes

- Some firmware versions emit two events per press (e.g. `attribute_updated` then `on/off`).
- These blueprints deliberately filter on `args: []` to avoid false triggers from attribute update events.
