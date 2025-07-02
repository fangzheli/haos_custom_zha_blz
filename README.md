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

# 5. Enjoy BLZ in ZHA 🎉


