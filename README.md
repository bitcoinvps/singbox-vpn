# Sing-Box: The Swiss Army Knife of Proxy Services
  
  
1. ## Installing gcc is the first step:
```
sudo su
apt update && apt install -y build-essential
```
  
2. ## Let's install the latest version of go (golang):
  
```
curl -fsL https://raw.githubusercontent.com/jetsung/golang-install/main/install.sh | bash
source /root/.bashrc
```
3. ## Run the following command to install the latest dev version of sing-box:
  
```
go install -v -tags "with_acme with_clash_api with_grpc with_quic with_wireguard with_ech with_utls with_gvisor with_shadowsocksr" github.com/sagernet/sing-box/cmd/sing-box@dev-next
```
  
4. ## Allow a normal user to run sing-box:
  
```
cp ~/go/bin/sing-box /usr/local/bin/
```
  
5. ## Next, create a sing-box directory to store assets and configurations:
```
mkdir /etc/sing-box/ && cd $_
```
6. ## Download GeoIP, and GeoSite:
```
wget -c -P /etc/sing-box "https://github.com/SagerNet/sing-geoip/releases/latest/download/geoip.db"
wget -c -P /etc/sing-box "https://github.com/SagerNet/sing-geosite/releases/latest/download/geosite.db"
```
7. ## Config.json: Select a protocol from the list below:
  
 - ## [VMESS-WS-CDN](https://github.com/bitcoinvps/sing-box-easy/tree/main/sing-box-config/vmess-ws-cdn)
 - ## [VMESS-WS-CDN-TLS](https://github.com/bitcoinvps/sing-box-easy/tree/main/sing-box-config/vmess-ws-cdn-tls)
 - ## [Iran-Multi-Hop (Client->VM1->VM2->Internet)](https://github.com/bitcoinvps/sing-box-easy/tree/main/sing-box-config/iran-multi-hop)
 - ## [Hysteria-Multi-Hop](https://github.com/bitcoinvps/sing-box-easy/tree/main/sing-box-config/vm1-vm2-hysteria-multi-hop)
 
8. ## Once you have downloaded the correct config.json file, you can now run the sing-box server:
```
cd /etc/sing-box && sing-box run
```
9. ## Check the client connection:
  
You can now connect your client. When the connection is unsuccessful, press ctrl + c to stop the server.
  
Change log>disabled = false at line 3 of config.json. You can find a possible solution by starting the server again and reading the server log.
  
In production, logs should always be turned off.
  
10. ## Creating sing-box systemd service:
After running sing-box and successfully connecting your client, it's time to install it as a service:
```
cat <<EOF  > /etc/systemd/system/sing-box.service
[Unit]
Description=sing-box Service
Documentation=https://sing-box.sagernet.org/
After=network.target nss-lookup.target
Wants=network.target
[Service]
Type=simple
ExecStart=sing-box run -c /etc/sing-box/config.json
Restart=on-failure
RestartSec=3s
RestartPreventExitStatus=23
LimitNPROC=10000
LimitNOFILE=1000000
[Install]
WantedBy=multi-user.target
EOF
```
  
11. ## Starting sing-box service:
```
systemctl daemon-reload && systemctl enable sing-box && systemctl start sing-box
systemctl status sing-box
```
  
If you need to check the logs in service mode you can always run:
```
journalctl -xefu sing-box
```
