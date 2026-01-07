# Reconnect Wifi if disconnected

This guide explains how to reconnect rapsberry Pi to wifi after being disconnected. We will set some intervals to make rapsberry Pi start a new wifi conenction, if the previous connenction is lost. It will also send a notification email to notify that internet connenction is established.

## Step 1: Create the script

- Open a script file:

```bash
nano ~/wifi_reconnect.sh
```

- Paste the following script:

```bash
#!/bin/bash

LOGFILE="$HOME/wifi_reconnect.log"
EMAIL="your_email@gmail.com"
HOST=$(hostname)
TIME=$(date)

# Check internet connectivity
if ping -c 1 -W 5 8.8.8.8 >/dev/null 2>&1; then
    echo "$TIME: Wi-Fi OK" >> "$LOGFILE"
    exit 0
fi

echo "$TIME: Wi-Fi DOWN — restarting NetworkManager" >> "$LOGFILE"

# Restart Wi-Fi
sudo systemctl restart NetworkManager

# Give it time to reconnect
sleep 30

# Check again
if ping -c 1 -W 5 8.8.8.8 >/dev/null 2>&1; then
    echo "$TIME: Wi-Fi restored successfully" >> "$LOGFILE"

    echo -e "Wi-Fi connection has been restored.\n\nHostname: $HOST\nTime: $TIME" \
    | mail -s "Wi-Fi Restored on Raspberry Pi" "$EMAIL"

else
    echo "$TIME: Wi-Fi still down after restart" >> "$LOGFILE"
fi

```

## Step 2: Make it executable

- Run:

```bash
chmod +x ~/wifi_reconnect.sh
```

## Step 3: Set an interval for the Pi to check the wifi

- Open crontab:

```bash
crontab -e
```

- Add this line to crontab:

```bash
0 */6 * * * /home/yourusername/wifi_reconnect.sh
```
