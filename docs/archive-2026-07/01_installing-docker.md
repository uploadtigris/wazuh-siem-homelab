# Installing Docker

Ran `ss -tulnp` to check open ports
All open ports are intentionally open 

Need to open these two ports to my Macbook
- **1514/tcp** — agent → manager event/data channel (encrypted).
- **1515/tcp** — agent enrollment (registering a new agent with the manager).

Configured static IP in my router's DHCP settings for my Mac's ethernet connection
- I needed to configure a static IP so I could configure firewalld with a specific IP that the ports would open up to
- 

Ran the following command on Fedora server - <MACBOOK_IP>
```
sudo firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="192.168.1.X/32" port port="1514" protocol="tcp" accept'
sudo firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="192.168.1.X/32" port port="1515" protocol="tcp" accept'
sudo firewall-cmd --reload
``` 

installing wazuh agent on MacOS
- https://documentation.wazuh.com/current/installation-guide/wazuh-agent/wazuh-agent-package-macos.html



