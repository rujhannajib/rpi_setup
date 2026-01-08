# Display custom messages after SSH

This guide explains how to send a notification email after every SSH.

## Step 1: Create a custom script

- Run:

```bash
sudo nano /etc/profile.d/ssh-login-alert.sh
```

- Paste:

```bash
#!/bin/bash

# Only trigger for SSH sessions
if [ -n "$SSH_CONNECTION" ]; then
    USERNAME="$(whoami)"
    IP="$(echo $SSH_CONNECTION | awk '{print $1}')"
    DATE="$(date)"
    HOST="$(hostname)"

    echo -e "SSH Login Alert\n\nUser: $USERNAME\nHost: $HOST\nFrom IP: $IP\nTime: $DATE" \
    | mail -s "SSH Login on $HOST" youremail@mail.com
fi
```

# Step 2: Make it executable

- Run:

```bash
sudo chmod +x /etc/profile.d/ssh-login-alert.sh
```


