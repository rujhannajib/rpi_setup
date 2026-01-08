# Display custom messages after SSH

This guide explains how to display custom messages after every SSH

## Step 1: Create a custom script in /etc/update-motd.d/

- Run:

```bash
sudo nano /etc/update-motd.d/99-custom
```

- Paste:

```bash
#!/bin/bash

HOST=$(hostname)
UPTIME=$(uptime -p)
TEMP=$(vcgencmd measure_temp 2>/dev/null | cut -d= -f2)
IP=$(hostname -I | awk '{print $1}')
DATE=$(date)

echo "========================================"
echo " Welcome to Raspberry Pi"
echo " Hostname : $HOST"
echo " IP       : $IP"
echo " Uptime   : $UPTIME"
echo " Temp     : $TEMP"
echo " Time     : $DATE"
echo "========================================"
```

# Step 2: Make it executable

- Run:

```bash
sudo chmod +x /etc/update-motd.d/99-custom
```
