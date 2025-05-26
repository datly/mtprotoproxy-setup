
# MTProtoProxy Setup Guide

**MTProtoProxy** is a proxy server that supports Telegram's MTProto protocol, allowing users to access Telegram more smoothly in regions with restrictions.

---

## Step 1: Update the System

```bash
sudo apt update
```

## Step 2: Install Required Packages

```bash
sudo apt install git python3 python3-pip -y
```

## Step 3: Clone the MTProtoProxy Source Code

```bash
git clone https://github.com/alexbers/mtprotoproxy.git
cd mtprotoproxy
```

## Step 4: Update the `requirements.txt` File

Create or edit the `requirements.txt` file to add the required libraries:

```bash
vi requirements.txt
```

Contents of `requirements.txt`:

```
aiohttp
pysocks
configparser
```

## Step 5: Install Python Dependencies

```bash
pip3 install -r requirements.txt
```

## Step 6: Test Run the Proxy

```bash
python3 mtprotoproxy.py
```

> **Note:** You may need to further configure the `config.ini` file to set up tokens and proxy ports for production environments.

---

## Step 7: Create a systemd Service to Run MTProtoProxy in the Background

Create the service file:

```bash
sudo vi /etc/systemd/system/mtproxy.service
```

Contents of the file:

```ini
[Unit]
Description=MTProto Proxy for Telegram
After=network.target

[Service]
WorkingDirectory=/home/ubuntu/mtprotoproxy
ExecStart=/usr/bin/python3 mtprotoproxy.py
Restart=always

[Install]
WantedBy=multi-user.target
```

> 📌 **Note:** Make sure the `WorkingDirectory` and `ExecStart` paths match the actual installation location on your system.

## Step 8: Enable and Start the Service

```bash
sudo systemctl daemon-reload
sudo systemctl enable mtproxy
sudo systemctl start mtproxy
```

## Step 9: Check Service Status

```bash
sudo systemctl status mtproxy
```

## Step 10: View Proxy Logs

```bash
journalctl -u mtproxy -f
```

---

## Step 11: Example `config.ini` Configuration

Before running the proxy in a production setting, create a `config.ini` file for detailed configuration:

```ini
PORT = 443  # Listening port

USERS = {
    # 'username': '32_character_secret_hex'
    'user1': '0123456789abcdef0123456789abcdef',
    'user2': 'abcdefabcdefabcdefabcdefabcdefab',
}

# Fake TLS domain (optional, helps bypass DPI)
TLS_DOMAIN = 'www.google.com'

# Log to console
LOG_TO_CONSOLE = True

# Limit number of clients per IP
CLIENTS_PER_USER = 100

# Timeout if client sends nothing
IDLE_TIMEOUT = 300

# Block specific IPs (optional)
# BLOCKED_IPS = ['192.168.1.100', '10.0.0.0/24']
```

### Generate a Random Secret

You can generate a `secret` using the following command:

```bash
openssl rand -hex 16
```

---

## Reference Resources

- Official repository: [https://github.com/alexbers/mtprotoproxy](https://github.com/alexbers/mtprotoproxy)
