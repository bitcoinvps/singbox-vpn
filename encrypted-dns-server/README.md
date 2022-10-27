## Install Encrypted DNS Server on VM2:
To have unfilitered DNS during hard times, the "[DNSCrypt-Proxy](https://github.com/DNSCrypt/dnscrypt-proxy)" needs to be installed on VM1 and the ["Encrypted DNS Server"](https://github.com/DNSCrypt/encrypted-dns-server) on VM2.
```
sudo su
mkdir /opt/encrypted-dns && cd $_
wget https://github.com/DNSCrypt/encrypted-dns-server/releases/download/0.9.9/encrypted-dns_0.9.9_amd64.deb
dpkg -i encrypted-dns_0.9.9_amd64.deb
```
### You now need to download and edit the configuration file. Changing listen_addrs and provider_name are probably the first things you should do.
```
curl https://raw.githubusercontent.com/bitcoinvps/sing-box-easy/main/encrypted-dns-server/encrypted-dns.toml > /opt/encrypted-dns/encrypted-dns.toml
nano /opt/encrypted-dns/encrypted-dns.toml
```
### Add a blacklist file if you wish. If you do not mind using this feature, don't forget to comment **domain_blacklist** in /opt/encrypted-dns/encrypted-dns.toml.
```
curl https://raw.githubusercontent.com/mhhakim/pihole-blocklist/master/list.txt > /opt/encrypted-dns/domain_blacklist.txt
```
### Create the encrypted DNS systemd service:
```
cat <<EOF > /etc/systemd/system/encrypted-dns.service
[Unit]
Description=DNSCrypt v2 server
ConditionFileIsExecutable=/usr/bin/encrypted-dns
After=syslog.target network-online.target
[Service]
StartLimitInterval=5
StartLimitBurst=10
ExecStart=/usr/bin/encrypted-dns -c /opt/encrypted-dns/encrypted-dns.toml
WorkingDirectory=/opt/encrypted-dns/
Restart=always
RestartSec=10
[Install]
WantedBy=multi-user.target
EOF
```
### Now start the service:
```
systemctl daemon-reload && systemctl enable encrypted-dns && systemctl start encrypted-dns
systemctl status encrypted-dns
```
By checking the service status, you can always see the DNS stamp (sdns://).
![enter image description here](/encrypted-dns-server/sdns.png)
