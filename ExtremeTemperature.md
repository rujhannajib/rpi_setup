# Raspberry Pi Temperature Email Alert (≥ 80 °C)

This guide explains how to monitor **Raspberry Pi CPU temperature** and send an **email alert when it exceeds 80 °C**, with **rate limiting** to prevent email spam.

---

## 📌 Overview

- Reads CPU temperature using `vcgencmd`
- Compares floating-point values using `awk`
- Sends an email alert when temperature exceeds a threshold
- Limits alerts to **one email per hour**
- Runs automatically using **cron**

---

## Reading Raspberry Pi temperature

Raspberry Pi exposes temperature via:
```bash
vcgencmd measure_temp
# Output: temp=83.4'C
```

## Create the script

- Shell scripts can’t compare floating points easily, so we use awk.
- to avoid email spam, we limit only one email per hour. We use timestamp file to keep track of last email sent

Script: temp_monitor.sh

Create the script:

```bash
nano ~/temp_monitor.sh
```

✅ Full script (clean + commented)
```bash
#!/bin/bash

# === CONFIG ===
THRESHOLD=80
EMAIL="" # put your email here
STATE_FILE="$HOME/.last_temp_email"
INTERVAL=3600  # 1 hour in seconds

# === GET TEMPERATURE ===
TEMP=$(vcgencmd measure_temp | grep -oP '[0-9.]+')

# === CHECK IF TEMP > THRESHOLD ===
if awk "BEGIN {exit !($TEMP > $THRESHOLD)}"; then

    NOW=$(date +%s)

    # === CHECK LAST EMAIL TIME ===
    if [ -f "$STATE_FILE" ]; then
        LAST_SENT=$(cat "$STATE_FILE")
    else
        LAST_SENT=0
    fi

    # === RATE LIMIT CHECK ===
    if (( NOW - LAST_SENT >= INTERVAL )); then

        HOST=$(hostname)
        DATE=$(date)

        echo -e "⚠️ Raspberry Pi temperature alert\n\nHostname: $HOST\nTemperature: ${TEMP}°C\nTime: $DATE" \
        | mail -s "🔥 Pi Temperature Alert (${TEMP}°C)" "$EMAIL"

        # Save timestamp
        echo "$NOW" > "$STATE_FILE"
    fi
fi
```

## Make it executable
```bash
chmod +x ~/temp_monitor.sh
```

## Run it automatically every minute (cron)

We check every minute, but email max once per hour.

Edit crontab:

```bash
crontab -e
```


Add:

```bash
* * * * * /home/yourusername/temp_monitor.sh
```


📌 Replace yourusername with your actual username.