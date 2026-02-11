# Custom ZHA (BLZ fork) for Home Assistant OS

This repository contains a **complete override** of Home Assistant’s built-in Zigbee Home Automation (ZHA) integration, patched to add Bouffalo Lab **BLZ** radio support.

---

## Prerequisites

1. Home Assistant OS running with **Advanced Mode** enabled.  
2. The *Terminal & SSH* add-on (or another SSH method) installed and started.  
   The add-on exposes a normal SSH service on port **22** for your config
     folders

---

## Quick install

```bash
# 0. Tunnel in with an SSH add-on
#    (Terminal & SSH – official, or Advanced SSH & Web Terminal)

# 1. SSH into Home Assistant
ssh -p 22 <user>@<HA_IP>

# 2. Ensure /config/custom_components exists
mkdir -p /config/custom_components
cd /config

# 3. Clone the repo into a throw-away folder, copy just its “zha” directory
git clone --depth 1 https://github.com/fangzheli/haos_custom_zha_blz.git _tmp_zha
mv _tmp_zha/zha custom_components/
rm -rf _tmp_zha            # cleanup

# 4. Restart Home Assistant Core
ha core restart
```

---

## What is changed from upstream ZHA

Only **2 files** are modified from the stock HA ZHA component:

| File | Change |
|------|--------|
| `manifest.json` | Name, `zigpy_blz` logger, requirements point to [bouffalolab/zha](https://github.com/bouffalolab/zha/tree/feat/blz) and [zigpy-blz](https://github.com/bouffalolab/zigpy-blz), version string |
| `radio_manager.py` | Added `RadioType.blz` to `RECOMMENDED_RADIOS` |

All other files are identical to the upstream HA core stable release.

---

## Maintainer guide: updating to a new HA core release

When a new Home Assistant stable release comes out, follow these steps to
update the custom component.

### 1. Update the local HA core clone

```bash
cd /path/to/ha-core
git fetch origin
git fetch --tags
# Verify the latest stable tag
git tag -l "20[0-9][0-9].*" --sort=-v:refname | head -5
```

### 2. Run the update script

```bash
cd /path/to/haos_custom_zha_blz

# Auto-select latest stable tag (recommended)
./update_from_core.sh /path/to/ha-core

# Or specify a tag explicitly
./update_from_core.sh /path/to/ha-core 2026.2.1
```

The script will:
1. Checkout the stable tag in the core repo
2. Copy all ZHA files into `zha/`
3. Re-apply the BLZ patches (`manifest.json` + `radio_manager.py`)
4. Restore the original branch in the core repo

> **Important:** Always base on a **stable release tag**, not the `dev` branch.
> The `dev` branch may target a newer Python version than what HA stable Docker
> ships (e.g. `dev` requires Python 3.14+ while stable uses 3.13).

### 3. Verify the bouffalolab/zha compatibility

Check that the [bouffalolab/zha `feat/blz` branch](https://github.com/bouffalolab/zha/tree/feat/blz)
is compatible with the ZHA library version expected by the new HA core release.
The required version is shown in the script output (e.g. `ZHA 0.0.89`).

If incompatible, the `bouffalolab/zha` fork needs to be rebased onto the new
upstream ZHA release first.

### 4. Test (optional but recommended)

```bash
# Quick import test in a venv with the matching packages installed
python -c "from zha.application.const import RadioType; print(RadioType.blz)"
```

Or run a full Docker test:

```bash
docker run -d --name homeassistant --privileged \
  -v /path/to/config:/config --network=host \
  ghcr.io/home-assistant/home-assistant:stable
# Then install the custom component and verify ZHA loads
```

### 5. Commit and push

```bash
cd /path/to/haos_custom_zha_blz
git add -A
git commit -m "Update to HA core <tag>"
git push origin main
```
