# Finding La Marzocco Entity IDs in Home Assistant

This guide helps you find the correct entity IDs for your La Marzocco Micra in Home Assistant.

## Method 1: Using Developer Tools

1. Open Home Assistant
2. Go to **Developer Tools** (in the sidebar or via the menu)
3. Click on the **States** tab
4. In the filter box, type `lamarzocco` or `micra`
5. Look for entities like:
   - `switch.lamarzocco_micra_power` (or similar)
   - `number.lamarzocco_micra_coffee_boiler_temperature`
   - `select.lamarzocco_micra_steam_boiler_level`
   - `select.lamarzocco_micra_pre_brewing_mode`
   - `number.lamarzocco_micra_pre_infusion_time`
   - `sensor.lamarzocco_micra_status`

## Method 2: Using Devices & Services

1. Go to **Settings** > **Devices & Services**
2. Find the **La Marzocco** integration
3. Click on your device (Micra)
4. Click on **Entities** tab
5. You'll see all available entities with their IDs

## Method 3: Using the La Marzocco Integration

Based on the La Marzocco integration documentation, typical entity IDs are:

- **Power**: `switch.lamarzocco_<device_name>_power`
- **Coffee Boiler Temperature**: `number.lamarzocco_<device_name>_coffee_boiler_temperature`
- **Steam Boiler Level**: `select.lamarzocco_<device_name>_steam_boiler_level`
- **Pre-brewing Mode**: `select.lamarzocco_<device_name>_pre_brewing_mode`
- **Pre-infusion Time**: `number.lamarzocco_<device_name>_pre_infusion_time`
- **Status**: `sensor.lamarzocco_<device_name>_status`

Where `<device_name>` is typically your device name in lowercase (e.g., `micra`, `mini`, `gs3`).

## Common Entity ID Patterns

The La Marzocco integration typically creates entities with these patterns:

```
switch.lamarzocco_<device>_power
number.lamarzocco_<device>_coffee_boiler_temperature
select.lamarzocco_<device>_steam_boiler_level
select.lamarzocco_<device>_pre_brewing_mode
number.lamarzocco_<device>_pre_infusion_time
sensor.lamarzocco_<device>_status
sensor.lamarzocco_<device>_brewing_start_time
```

## Updating the Configuration

Once you have the entity IDs, update the substitutions in `lamarzocco-controller.yaml`:

```yaml
substitutions:
  lm_entity: "lamarzocco.micra"  # Main device entity
  lm_power: "switch.lamarzocco_micra_power"  # Replace with your actual ID
  lm_coffee_temp: "number.lamarzocco_micra_coffee_boiler_temperature"
  lm_steam_level: "select.lamarzocco_micra_steam_boiler_level"
  lm_prebrew_mode: "select.lamarzocco_micra_pre_brewing_mode"
  lm_preinfusion_time: "number.lamarzocco_micra_pre_infusion_time"
  lm_status: "sensor.lamarzocco_micra_status"
  lm_brewing_start: "sensor.lamarzocco_micra_brewing_start_time"
```

## Verifying Entity IDs

After updating, you can verify the entities are accessible:

1. In Home Assistant, go to **Developer Tools** > **Services**
2. Try calling a service on one of the entities (e.g., `switch.toggle` on the power entity)
3. If it works, the entity ID is correct

## Troubleshooting

If you can't find an entity:

1. Make sure the La Marzocco integration is properly set up
2. Check that your machine is connected and online
3. Some entities might only appear when the machine is in a certain state
4. Check the La Marzocco integration logs for any errors

