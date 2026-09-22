# Mini SIEM Home Lab — Wazuh

## 1. Server creation (Ubuntu 20.04 — Wazuh manager, indexer, dashboard)

```bash
curl -sO https://packages.wazuh.com/4.9/wazuh-install.sh
sudo bash ./wazuh-install.sh -a
```

Enable services on boot:

```bash
sudo systemctl enable wazuh-indexer wazuh-manager wazuh-dashboard
```

Dashboard: `https://<manager-ip>` — login `admin` + generated password.

## 2. Agent deployment (Ubuntu 16.04 — victim)

```bash
wget https://packages.wazuh.com/4.x/apt/pool/main/w/wazuh-agent/wazuh-agent_4.9.2-1_amd64.deb
sudo WAZUH_MANAGER='<manager-ip>' WAZUH_AGENT_GROUP='client_linux' WAZUH_AGENT_NAME='ubuntu1604' \
  dpkg -i ./wazuh-agent_4.9.2-1_amd64.deb

sudo systemctl daemon-reload
sudo systemctl enable wazuh-agent
sudo systemctl start wazuh-agent
```

Verify from the manager:

```bash
sudo /var/ossec/bin/manage_agents -l
```

## 3. Attack (Kali Linux)

```bash
sudo apt install hydra -y
echo -e "123456\npassword\nadmin\nroot\ntoor" > passwords.txt
hydra -l ubuntu -P passwords.txt ssh://<victim-ip> -t 4
```

Check detection on the manager:

```bash
sudo tail -100 /var/ossec/logs/alerts/alerts.log
```
