# VIVID Dryer — Dynamic Presets for Klipper

A Klipper macro suite that provides a clean popup interface (Mainsail / Fluidd / KlipperScreen) to run a filament dryer heater with **user-configurable dynamic presets**.

Presets can be created, modified, and saved at runtime — no config edits or Klipper restart required.

---

## Features

- Popup UI with 4 selectable presets
- Dynamic preset editing from the console or UI
- Persistent storage across reboots
- Automatic conversion of hours → seconds
- Saves and restores the original `IDLE_TIMEOUT`
- Clean cancel and status commands
- Compatible with:
  - Mainsail
  - Fluidd
  - KlipperScreen

---

## Requirements

- Klipper with `[save_variables]` enabled
- A heater configured as:

```
Vivid_1_dryer
```

(You may rename this in the macro if needed.)

---

## Installation

1. Create a new file, for example:

```
vivid_dryer.cfg
```

2. Paste the macro configuration into that file.

3. Include it in your main `printer.cfg`:

```
[include vivid_dryer.cfg]
```

4. Restart Klipper.

---

## Usage

### Open Preset Popup

```
VIVID_DRYER
```

Displays a popup with the four configured presets.

---

### Save or Update a Preset (Dynamic)

```
VIVID_DRYER_SAVE_PRESET SLOT=<1..4> NAME="<label>" TEMP=<°C> HOURS=<hours>
```

Example:

```
VIVID_DRYER_SAVE_PRESET SLOT=1 NAME="PLA Gentle" TEMP=40 HOURS=4
```

Notes:

- `SLOT` must be **1–4**
- `NAME` must be quoted if it contains spaces
- Changes are saved immediately and persist across reboots
- Popup refreshes automatically

---

### Start Dryer From Preset

Simply tap the preset button in the popup.

Internally, the macro:

1. Sets the dryer heater temperature  
2. Sets `IDLE_TIMEOUT` to the requested duration  
3. Starts a timer  
4. Restores the original `IDLE_TIMEOUT` when finished  

---

### Cancel an Active Run

```
VIVID_DRYER_CANCEL
```

Immediately:

- Turns off the dryer heater  
- Restores the previous `IDLE_TIMEOUT`  
- Stops the timer  

---

### View Status

```
VIVID_DRYER_STATUS
```

Shows:

- Whether the dryer is active  
- Last preset used  
- Previous and current idle timeout values  
- Stored preset definitions  

---

## How Dynamic Presets Work

Preset data is stored using Klipper’s persistent variable system:

```
[save_variables]
```

Each preset stores:

- Name  
- Temperature (°C)  
- Duration (hours)  

These values are saved in:

```
~/printer_data/config/vivid_dryer_vars.cfg
```

No restart is required after editing presets.

---

## Default Presets

If no presets have been saved yet, defaults are used:

| Slot | Name     | Temp | Time |
|------|----------|------|------|
| 1    | Preset 1 | 40°C | 4 h  |
| 2    | Preset 2 | 45°C | 6 h  |
| 3    | Preset 3 | 50°C | 8 h  |
| 4    | Preset 4 | 55°C | 2 h  |

---

## Customization

### Max Temperature Limit

The macro enforces a maximum dryer temperature of **60°C** by default.

- Attempts to save a preset above the limit are rejected.
- Attempts to start the dryer above the limit are rejected.

To change the limit, edit `variable_max_temp` in the macro:

```
[gcode_macro VIVID_DRYER_START]
variable_max_temp: 60.0
```

### Change Heater Name

Edit all instances of:

```
HEATER=Vivid_1_dryer
```

to match your configuration.

---

### Change Number of Presets

This macro is designed for four presets for a clean UI.  
Expanding to more requires adding additional buttons and storage variables.

---

## Safety Notes

- Ensure your dryer hardware supports the requested temperatures  
- Verify proper thermal protection and ventilation  
- This macro does not enforce material-specific limits  
- Never leave heating equipment unattended  

---

## Troubleshooting

### Popup does not appear

- Ensure `[respond]` is enabled  
- Verify your UI supports prompt actions  
- Restart Klipper after installation  

---

### Presets not saving

- Confirm `[save_variables]` section exists  
- Check file permissions on the save file  
- Verify the path is correct  

---

### Heater not activating

- Confirm heater name matches configuration  
- Check Klipper logs for heater errors  

---

## License

MIT License — free to use, modify, and distribute.

---

## Acknowledgments

Designed for reliable long-duration filament drying workflows using Klipper.
