# La Marzocco Micra ESPHome Controller

This ESPHome configuration provides a touch screen and rotary encoder interface for controlling a La Marzocco Linea Micra coffee machine connected to Home Assistant.

## Features

- **Touch Screen Interface**: Display and control 6 machine parameters
- **Rotary Encoder Control**: Adjust selected parameters with the rotary encoder
- **3-Second Selection Timeout**: Automatically deselects after 3 seconds of inactivity
- **Real-time Updates**: Displays current machine state from Home Assistant

## Displayed Parameters

1. **Power State** - ON/OFF status of the machine
2. **Coffee Boiler Temperature** - Current temperature in °C (0.1°C resolution)
3. **Steam Power Level** - Level 1, 2, or 3
4. **Prebrewing Mode** - PreBrewing, PreInfusion, or Disabled
5. **PreInfusion Time** - Time in seconds (0.1s resolution)
6. **Brewing Duration** - Current shot duration in seconds (read-only)

## Hardware Requirements

- ESP32-S3 development board
- WaveShare touch screen display (360x360)
- Rotary encoder (connected to GPIO 8 and GPIO 7)
- CST816S touch controller

## Setup Instructions

### 1. Update Entity IDs

Edit `lamarzocco-controller.yaml` and update the entity ID substitutions at the top:

```yaml
substitutions:
  lm_entity: "lamarzocco.micra"  # Your La Marzocco entity ID
  lm_power: "switch.lamarzocco_micra_power"
  lm_coffee_temp: "number.lamarzocco_micra_coffee_boiler_temperature"
  lm_steam_level: "select.lamarzocco_micra_steam_boiler_level"
  lm_prebrew_mode: "select.lamarzocco_micra_pre_brewing_mode"
  lm_preinfusion_time: "number.lamarzocco_micra_pre_infusion_time"
  lm_status: "sensor.lamarzocco_micra_status"
  lm_brewing_start: "sensor.lamarzocco_micra_brewing_start_time"
```

**Important**: You need to find the actual entity IDs in your Home Assistant. These can be found in:
- Developer Tools > States
- Settings > Devices & Services > La Marzocco > Entities

### 2. Configure WiFi and API

Edit `lamarzocco/ha-config.yaml`:

1. Add your WiFi credentials to `secrets.yaml`:
   ```yaml
   wifi_ssid: "YourWiFiSSID"
   wifi_password: "YourWiFiPassword"
   ```

2. Get your ESPHome API encryption key from Home Assistant:
   - Go to Settings > Add-ons > ESPHome
   - Copy the encryption key
   - Replace `YOUR_API_KEY` in `ha-config.yaml`

3. Set your OTA password (replace `YOUR_PASSWORD`)

### 3. Update Timezone

Edit `lamarzocco-controller.yaml` and update the timezone in the time component:
```yaml
timezone: 'America/Winnipeg'  # Change to your timezone
```

### 4. Flash the Device

1. Install ESPHome (if not already installed)
2. Compile and flash the configuration:
   ```bash
   esphome compile lamarzocco-controller.yaml
   esphome upload lamarzocco-controller.yaml
   ```

## Usage

### Touch Selection

1. Touch any of the 6 parameter boxes on the screen
2. The selected box will be highlighted in blue
3. The selection will timeout after 3 seconds of inactivity

### Rotary Encoder Control

After selecting a parameter with touch:

- **Power (Element 0)**: Rotate encoder in either direction to toggle power
- **Temperature (Element 1)**: 
  - Clockwise: Increase by 0.1°C (max 100°C)
  - Counter-clockwise: Decrease by 0.1°C (min 80°C)
- **Steam Level (Element 2)**:
  - Clockwise: Cycle forward (Level1 → Level2 → Level3 → Level1)
  - Counter-clockwise: Cycle backward (Level3 → Level2 → Level1 → Level3)
- **Prebrewing Mode (Element 3)**:
  - Clockwise: Cycle forward (PreBrewing → PreInfusion → Disabled → PreBrewing)
  - Counter-clockwise: Cycle backward (Disabled → PreInfusion → PreBrewing → Disabled)
- **PreInfusion Time (Element 4)**:
  - Clockwise: Increase by 0.1s (max 25.0s)
  - Counter-clockwise: Decrease by 0.1s (min 1.0s)
- **Brewing Duration (Element 5)**: Read-only display (no control)

### Selection Timeout

After selecting an element, you have 3 seconds to adjust it with the rotary encoder. After 3 seconds of inactivity, the selection will automatically clear.

## Troubleshooting

### Entity IDs Not Found

If you see errors about entity IDs not being found:
1. Check that the La Marzocco integration is properly configured in Home Assistant
2. Verify entity IDs in Developer Tools > States
3. Update the substitutions in `lamarzocco-controller.yaml`

### Touch Screen Not Responding

1. Check the touch controller wiring (I2C pins 11 and 12)
2. Verify the touch controller is detected (check logs)
3. Adjust touch coordinates in `lamarzocco/waveshare.yaml` if needed

### Rotary Encoder Not Working

1. Verify encoder pins (GPIO 8 and GPIO 7)
2. Check encoder wiring (A and B phases)
3. Test encoder in ESPHome logs

### Display Issues

1. Check display wiring (SPI pins)
2. Verify display model matches configuration (JC3636W518V2)
3. Adjust display rotation if needed

## File Structure

```
lamarzocco-controller.yaml    # Main configuration file
lamarzocco/
  ├── esp-config.yaml         # ESP32 hardware configuration
  ├── ha-config.yaml          # Home Assistant API and WiFi
  ├── waveshare.yaml          # Display and touchscreen
  └── lvgl.yaml               # LVGL UI configuration (placeholder)
```

## Notes

- The brewing duration calculation is simplified and increments by 0.1s each second. For accurate timing, you may need to parse the ISO 8601 timestamp from the brewing_start_time attribute.
- Entity IDs may vary depending on your Home Assistant setup and La Marzocco integration version.
- The configuration assumes a 360x360 round display. Adjust UI element positions if using a different display size.

