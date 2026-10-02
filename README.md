# ha-blueprints

Home Assistant blueprints.

## Shelly button — toggle & hold to dim

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2FTimoPtr%2Fha-blueprints%2Fblob%2Fmain%2Fblueprints%2Fautomation%2Fshelly_button_toggle_dim.yaml)

Recreates the Shelly dimmer's local button behaviour from Home Assistant, for a Shelly input set to **detached** mode:

- **Short press** (`single_push`) toggles the light.
- **Hold** (`long_push`) ramps the brightness until the button is released (`btn_up`), stopping at the minimum brightness or at 100% — it never dims the light off.
- **Hold with the light off** first turns it on without a brightness (so Adaptive Lighting or the light's own default picks it), then ramps from there.
- **Direction**: up at the minimum, down at 100%. Otherwise, with an optional `input_boolean` direction helper, consecutive holds alternate up/down like the Shelly does; without it, up below 50% and down above.

Uses the Shelly `event.*` entity (not a device trigger), so it survives a device re-add.

### Requirements

- Home Assistant 2026.7 or newer.
- A Shelly Gen2+ input in detached mode, exposing an event entity with `single_push`, `long_push` and `btn_up`.

### Keeping the wall button working when HA is down

With the input detached, the button does nothing locally, so it stops working whenever Home Assistant is unreachable. [This Shelly script](https://gist.github.com/TimoPtr/578646ac806122925b7aca87c6737627) (written for the Pro Dimmer 2PM) detaches the inputs automatically while HA answers, and switches them back to a local mode (e.g. single-input dimming) when it doesn't.

### Settings

| Input | Default | |
|---|---|---|
| Step | 5 % | Brightness change per step |
| Step interval | 250 ms | Time between steps, also used as the transition |
| Minimum brightness | 1 % | Dimming down stops here |
| Direction helper | — | Optional `input_boolean` to alternate the direction between holds |

### Adaptive Lighting

No special setup is needed with [Adaptive Lighting](https://github.com/basnijholt/adaptive-lighting): every turn-on (short press, or a hold from off) is a bare `light.turn_on`/`light.toggle`, so Adaptive Lighting picks the brightness, and dimming by hand is then detected as manual control until the light is turned off.
