# Setup an automatic booting email notifications

## Step 1: Install mail dependencies

```bash
sudo apt update
sudo apt install msmtp msmtp-mta mailutils -y
```

## Step 2: Configure email (Gmail example)

Create the config file:

```bash
nano ~/.msmtprc
```

Paste this (replace values):

```bash
defaults
auth           on
tls            on
tls_trust_file /etc/ssl/certs/ca-certificates.crt
logfile        ~/.msmtp.log

account gmail
host smtp.gmail.com
port 587
from your_email@gmail.com
user your_email@gmail.com
password your_app_password

account default : gmail
```

🔐 Important (Gmail security):

- Enable 2-Step Verification

- Create an App Password

- Use the [app password](https://myaccount.google.com/apppasswords) here (NOT your real Gmail password)
- Save and exit.

Secure the file:

```bash
chmod 600 ~/.msmtprc
```

## Step 3: Create the startup email script

Create a script:

```bash
nano ~/send_boot_email.sh
```

Paste:

```bash
#!/bin/bash

HOSTNAME=$(hostname)
IP=$(hostname -I | awk '{print $1}')
DATE=$(date)

echo -e "Raspberry Pi booted!\n\nHostname: $HOSTNAME\nIP Address: $IP\nTime: $DATE" \
| mail -s "Pi Boot Notification" your_email@gmail.com
```

Save and exit.

Make it executable:

```bash
chmod +x ~/send_boot_email.sh
```

## Step 4: Create a systemd service (runs at boot)

Create service file:

```bash
sudo nano /etc/systemd/system/pi-boot-email.service
```

Paste:

```bash
[Unit]
Description=Send email when Raspberry Pi boots
After=network-online.target
Wants=network-online.target

[Service]
Type=oneshot
User=username
ExecStart=/home/username/send_boot_email.sh

[Install]
WantedBy=multi-user.target
```

⚠️ Use your actual username.

## Step 5: Enable the service

```bash
sudo systemctl daemon-reload
sudo systemctl enable pi-boot-email.service
```

🔄 Test it

Reboot:

```bash
sudo reboot
```

You should receive an email each time the Pi turns on.

🧪 Debugging (if no email arrives)

Check service status:

```bash

systemctl status pi-boot-email.service
```

Check mail log:

```bash
cat ~/.msmtp.log
```
