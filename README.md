# Injection Molding Controller - User's Guide

## Table of Contents
1. [Overview](#overview)
2. [Getting Started](#getting-started)
3. [Home Screen](#home-screen)
4. [Heater Control](#heater-control)
5. [PID Tuning](#pid-tuning)
6. [Cycle Configuration](#cycle-configuration)
7. [Profiles](#profiles)
8. [WiFi & Web Interface](#wifi--web-interface)
9. [Settings](#settings)
10. [Firmware Updates](#firmware-updates)
11. [Troubleshooting](#troubleshooting)

---

## Overview

The Injection Molding Controller is an ESP32-S3 based system running on a LilyGo T-Display AMOLED. It provides:

- **3 Independent PID-controlled heaters** (Chamber, Mold, Auxiliary)
- **4 Pneumatic outputs** (Vise, Injection, Ejection, Feeder)
- **Automated injection molding cycles** with configurable timing
- **Profile management** to save and load complete machine configurations
- **Web interface** for remote monitoring and control
- **OTA firmware updates** with rollback capability
- **Real-time temperature graphing**

---

## Getting Started

### First Boot

1. Power on the controller
2. The home screen will display with all heaters disabled
3. Connect to WiFi (see [WiFi Configuration](#wifi--web-interface))
4. Configure your heater temperatures and PID parameters
5. Set cycle times
6. Save your configuration as a profile

### Display Layout

The controller uses a 536×240 pixel AMOLED touchscreen display with the following navigation:
- **Tap buttons** to interact
- **Back arrow (←)** in top-left returns to previous screen
- **Settings gear (⚙)** in top-right opens settings
- **Profile icon (💾)** opens profile management

---

## Home Screen

The home screen provides at-a-glance status of your entire system.

### Heater Panels (Left Side)

Each heater shows:
- **Name** (Chamber, Mold, Aux)
- **Current / Setpoint temperature** (e.g., "185°C / 200°C")
- **Power button** - Enable/disable the heater
- **Settings button** - Configure PID parameters

**Panel Colors:**
| Color | Meaning |
|-------|---------|
| 🟢 Dark Green | At temperature (within ±5° of setpoint) |
| 🟠 Dark Orange | Actively heating |
| 🔵 Dark Blue | Cooling (above setpoint) |
| 🔴 Dark Red | **FAULT** - Thermocouple error |
| ⚫ Gray | Disabled or idle |

### Status Panel (Right Side)

Shows system status indicators:
- **Heater status dots** (Green=heating, Gray=off, Red=fault)
- **Pneumatic device status**
- **WiFi connection status**

### Temperature Chart

Real-time graph showing temperature history for all three heaters:
- **Red line** - Chamber
- **Green line** - Mold
- **Blue line** - Auxiliary

### Cycle Control

- **Target Count** - Number of cycles to run
- **Remaining** - Cycles left
- **START/STOP** button - Begin or halt automated cycling

---

## Heater Control

### Enabling a Heater

1. On the home screen, tap the **power button** on the heater panel
2. The button turns green when enabled
3. The heater will begin heating toward the setpoint

### Temperature Display

Temperatures are shown as: `Current / Setpoint`

Example: `185°C / 200°C` means:
- Current temperature: 185°C
- Target temperature: 200°C

### Temperature Units

Switch between Celsius and Fahrenheit in **Settings → Temperature Unit**

---

## PID Tuning

Access PID settings by tapping the **gear icon** on any heater panel.

### Parameters

| Parameter | Description | Typical Range |
|-----------|-------------|---------------|
| **Setpoint** | Target temperature | 150-280°C |
| **Max Temp** | Safety limit | 250-350°C |
| **Kp** | Proportional gain | 2.0-10.0 |
| **Ki** | Integral gain | 0.01-0.5 |
| **Kd** | Derivative gain | 0.5-5.0 |

### Adjusting Values

- Tap the **value box** to open numeric keypad for direct entry
- Use **+/-** buttons for incremental adjustments:
  - Small: ±1 (temperature) or ±0.1 (PID)
  - Large: ±10 (temperature) or ±1.0 (PID)

### PID Tuning Guide

**Starting Values (Conservative):**
- Kp: 3.0
- Ki: 0.02
- Kd: 2.0

**Tuning Process:**
1. Start with Ki=0, Kd=0
2. Increase Kp until temperature oscillates around setpoint
3. Reduce Kp by 30%
4. Add Ki slowly (0.01 increments) to eliminate steady-state error
5. Add Kd to reduce overshoot

**Troubleshooting:**
| Problem | Solution |
|---------|----------|
| Slow to reach temperature | Increase Kp |
| Oscillating | Decrease Kp, increase Kd |
| Overshoot then settles | Increase Kd, decrease Ki |
| Never reaches setpoint | Increase Ki |
| Unstable/hunting | Decrease all values |

---

## Cycle Configuration

Access from **Home → Settings → Cycle Configuration**

### Cycle Times

| Parameter | Description |
|-----------|-------------|
| **Vise Time** | Duration vise is clamped (seconds) |
| **Injection Time** | Duration of injection (seconds) |
| **Ejection Time** | Duration of ejection (seconds) |
| **Feeder Time** | Duration of material feeding (seconds) |

### Cycle Sequence

When running, the controller executes:
1. **Vise closes** (Vise Time)
2. **Injection** (Injection Time)
3. **Hold/Cool** 
4. **Ejection** (Ejection Time)
5. **Feeder advances** (Feeder Time)
6. Repeat until target count reached

### Target Count

Set the number of cycles to run automatically. The controller will:
- Count down remaining cycles
- Stop automatically when complete
- Display "Complete" status

---

## Profiles

Profiles save your complete machine configuration for easy recall.

### What's Saved in a Profile

- All heater setpoints and max temperatures
- PID parameters (Kp, Ki, Kd) for all heaters
- Cycle times (Vise, Injection, Ejection, Feeder)
- Target cycle count

### Saving a Profile

1. Configure your machine settings
2. Tap the **Profile button (💾)** on home screen
3. Tap **"Save Current"**
4. Enter a profile name
5. Tap **Save**

### Loading a Profile

1. Tap the **Profile button (💾)**
2. Tap the profile you want to load
3. All settings are immediately applied

### Deleting a Profile

1. Open **Profiles**
2. Long-press or tap the **delete icon** on the profile
3. Confirm deletion

### Profile Limits

- Maximum 8 profiles
- Profile names up to 20 characters

---

## WiFi & Web Interface

### Connecting to WiFi

1. Go to **Settings → WiFi Configuration**
2. The controller scans for available networks
3. Tap your network name
4. Enter the password using the on-screen keyboard
5. Tap **Connect**

### Web Interface

Once connected, access the web interface from any browser on the same network:

```
http://[controller-ip-address]
```

The IP address is shown on the WiFi configuration screen.

### Web Features

- **Real-time temperature monitoring**
- **Heater enable/disable**
- **View and change setpoints**
- **Start/stop cycles**
- **View temperature history charts**

### API Endpoints

For integration with other systems:

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/api/status` | GET | Current temperatures and status |
| `/api/heater/{n}` | GET/POST | Individual heater control |
| `/api/cycle` | GET/POST | Cycle control |
| `/api/settings` | GET/POST | Configuration |

---

## Settings

Access via the **gear icon (⚙)** on the home screen.

### Display Settings

- **Brightness** - Adjust screen brightness (slider)
- **Temperature Unit** - Toggle °C / °F

### Hardware Configuration

Enable/disable hardware components:
- Heater Chamber
- Heater Mold
- Heater Aux
- Vise
- Injection
- Ejection
- Feeder

### Reset to Defaults

Restores all settings to factory defaults. **This cannot be undone.**

---

## Firmware Updates

### Checking for Updates

1. Go to **Settings → Firmware Update**
2. Tap **Check**
3. If an update is available, version and release notes are shown

### Installing Updates

1. Ensure WiFi is connected
2. Tap **Install**
3. Progress bar shows download status
4. Device automatically reboots when complete

### Rollback

If you experience issues after an update:

1. Go to **Settings → Firmware Update**
2. The **Previous Version** shows what you can rollback to
3. Tap **Rollback**
4. Device restarts with the previous firmware

**Note:** Rollback is only available after at least one OTA update. The previous firmware is stored on an alternate partition.

---

## Troubleshooting

### Heater Shows FAULT (Red)

**Cause:** Thermocouple disconnected or failed

**Solutions:**
1. Check thermocouple wiring connections
2. Verify thermocouple is properly inserted
3. Check for damaged thermocouple wires
4. Replace thermocouple if damaged

### Temperature Not Reaching Setpoint

**Possible Causes:**
- PID parameters too conservative
- Heater element underpowered
- Heat loss exceeds heater capacity

**Solutions:**
1. Increase Kp and Ki values
2. Check heater element resistance
3. Improve insulation

### Temperature Oscillating

**Cause:** PID parameters too aggressive

**Solutions:**
1. Decrease Kp
2. Increase Kd
3. Decrease Ki

### WiFi Won't Connect

**Solutions:**
1. Verify correct password
2. Move closer to WiFi router
3. Check router allows 2.4GHz (5GHz not supported)
4. Restart the controller

### OTA Update Fails

**Common Errors:**
- "Server did not report size" - GitHub URL issue
- "Wrong HTTP code" - Invalid download URL

**Solutions:**
1. Verify WiFi connection is stable
2. Try again later (GitHub may be busy)
3. Update via USB if OTA continues to fail

### Display Unresponsive

**Solutions:**
1. Press the RST button to restart
2. If frozen during update, wait - do not power off
3. Power cycle if completely unresponsive

---

## Specifications

| Feature | Specification |
|---------|---------------|
| Display | 1.91" AMOLED, 536×240, Touch |
| Processor | ESP32-S3, 240MHz |
| Flash | 16MB |
| PSRAM | 8MB |
| Heater Channels | 3 (PID controlled) |
| Pneumatic Outputs | 4 (relay controlled) |
| Temperature Sensors | MAX31855 thermocouples |
| Temperature Range | 0-500°C |
| WiFi | 2.4GHz 802.11 b/g/n |
| Power | 5V USB-C |

---

## Safety Warnings

⚠️ **HIGH TEMPERATURE HAZARD** - Heaters can exceed 300°C. Allow to cool before handling.

⚠️ **ELECTRICAL HAZARD** - Disconnect power before servicing electrical connections.

⚠️ **PINCH HAZARD** - Keep hands clear of vise and injection mechanisms during operation.

⚠️ **BURN HAZARD** - Molten plastic can cause severe burns. Use appropriate PPE.

---

## Support

For issues and feature requests:
- GitHub: [github.com/jramsey873/injection-molding-controller](https://github.com/jramsey873/injection-molding-controller)

---

*Firmware Version: 1.0.1*
