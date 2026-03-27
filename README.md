# Custom ZHA (BLZ fork) for Home Assistant OS

This repository contains a **complete override** of Home Assistant's built-in Zigbee Home Automation (ZHA) integration, patched to add Bouffalo Lab **BLZ** radio support.

---

## Install via HACS (recommended)

### Prerequisites

- Home Assistant OS with [HACS](https://hacs.xyz/) installed

### Steps

1. In Home Assistant, go to **HACS** > **Integrations**.
2. Click the three-dot menu (top right) > **Custom repositories**.
3. Add `https://github.com/bouffalolab/haos_custom_zha_blz` with category **Integration**.
4. Search for **ZHA with BLZ Radio Support** and click **Install**.
5. Restart Home Assistant.
6. Go to **Settings** > **Devices & Services** > **Add Integration** > **Zigbee Home Automation**.
7. Select **BLZ = Bouffalo Lab Zigbee radios: BL702/4/6** as the radio type.
8. Set the serial port (e.g. `/dev/ttyUSB0`) and baud rate `2000000`.

---

## What is changed from upstream ZHA

Only **2 files** are modified from the stock HA ZHA component:

| File | Change |
|------|--------|
| `manifest.json` | Name, `zigpy_blz` logger, requirements point to [bouffalolab/zha](https://github.com/bouffalolab/zha/tree/feat/blz) and [zigpy-blz](https://github.com/bouffalolab/zigpy-blz), version string, `homeassistant` minimum version |
| `radio_manager.py` | Added `RadioType.blz` to `RECOMMENDED_RADIOS` |

All other files are identical to the upstream HA core stable release.

---

## Appendix: Manual install via SSH

For advanced users who prefer not to use HACS.

### Prerequisites

1. Home Assistant OS running with **Advanced Mode** enabled.
2. The *Terminal & SSH* add-on (or another SSH method) installed and started.

### Steps

```bash
# 1. SSH into Home Assistant
ssh -p 22 <user>@<HA_IP>

# 2. Ensure /config/custom_components exists
mkdir -p /config/custom_components
cd /config

# 3. Download and extract the component
curl -sL https://github.com/bouffalolab/haos_custom_zha_blz/archive/refs/heads/main.tar.gz | tar xz
cp -r haos_custom_zha_blz-main/custom_components/zha custom_components/
rm -rf haos_custom_zha_blz-main

# 4. Restart Home Assistant Core
ha core restart
```

Then follow steps 6-8 from the HACS instructions above to configure the BLZ radio.
