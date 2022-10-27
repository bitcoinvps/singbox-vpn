
# Iran Multi Hop (VM1 inside and VM2 outside)

In the following we install sing-box on VM1 & VM2. You might want to test [VMESS-WS-CDN](https://github.com/bitcoinvps/sing-box-easy/tree/main/sing-box-config/vmess-ws-cdn) or [](https://github.com/bitcoinvps/sing-box-easy/tree/main/sing-box-config#vmess-ws-cdn-tls)[VMESS-WS-CDN-TLS](https://github.com/bitcoinvps/sing-box-easy/tree/main/sing-box-config/vmess-ws-cdn-tls) before using this. Both only require one VPS outside, which you can get from bitcoinvps.cloud anonymously.

#### Choose a low-latency server by checking [looking glass](https://bitcoinvps.cloud/looking-glass).

At the end of the process, the client should be able to connect to VM1 but have its internet routed through VM2. To prevent 1:1 traffic at the end, Iranian IP space and Iranian-hosted domain addresses will be routed through VM1. It allows end users to use banking apps, $napp, and similar apps without having to configure their clients or disconnect from VPNs. Due to Iranian regulations, the client will also be charged half the cost of his traffic at their Internet service provider. 
Thanks to [SamadiPour/iran-hosted-domains](https://github.com/SamadiPour/iran-hosted-domains) for providing the list of non-IR domains.

The first step in installing Singbox on both servers is to create an ssh tunnel between VM1 and VM2. Additionally, if public DNS access is blocked, you must install [Encrypted DNS Server on VM 2](https://github.com/bitcoinvps/sing-box-easy/tree/main/encrypted-dns-server) and [DNSCrypt-Proxy on VM 1](https://github.com/bitcoinvps/sing-box-easy/tree/main/dnscrypt-proxy-config). 

### To create the ssh tunnel on VM1:
```
ssh -D 8080 -N -f root@VM2IP
```
Setting the proxy is then required:
```
export all_proxy="socks5h://127.0.0.1:8080"
```

## To install and configure Sing-Box, follow the steps below on both servers:

##  1. [Install gcc](https://github.com/bitcoinvps/sing-box-easy#installing-gcc-is-the-first-step)
## 2. [Install go](https://github.com/bitcoinvps/sing-box-easy#lets-install-the-latest-version-of-go-golang)
## 3. [Install sing-box](https://github.com/bitcoinvps/sing-box-easy#run-the-following-command-to-install-the-latest-dev-version-of-sing-box)

Run the following on VM1 to force Golang (go) to use the proxy. If not, a 403 error (Access Forbidden) will occur and it will not be able to be installed:
```
env http_proxy=socks5://127.0.0.1:8080 https_proxy=socks5://127.0.0.1:8080 go install -v -tags "with_acme with_clash_api with_grpc with_quic with_wireguard with_ech with_utls with_gvisor with_shadowsocksr" github.com/sagernet/sing-box/cmd/sing-box@dev-next
```
## 4. [Allow a normal user to run sing-box](https://github.com/bitcoinvps/sing-box-easy#allow-a-normal-user-to-run-sing-box)
## 5. [Create a sing-box directory](https://github.com/bitcoinvps/sing-box-easy#next-create-a-sing-box-directory-to-store-assets-and-configurations)
## 6. [Download GeoIP and GeoSite](https://github.com/bitcoinvps/sing-box-easy#download-geoip-and-geosite)

## 7. Download proper config.json on each node:

### On VM2
```
curl https://raw.githubusercontent.com/bitcoinvps/sing-box-easy/main/sing-box-config/iran-multi-hop/vm2/config.json > /etc/sing-box/config.json
nano /etc/sing-box/config.json
```
Point a subdomain to your VM2 IP address. Replace it with "vm2.bitcoinvps.cloud" in config.json. Cloud proxy can be enabled after sing-box first runs (issuing certificates).

### On VM1
```
curl https://raw.githubusercontent.com/bitcoinvps/sing-box-easy/main/sing-box-config/iran-multi-hop/vm1/config.json > /etc/sing-box/config.json
nano /etc/sing-box/config.json
```
Replace "vm2.bitcoinvps.cloud" with your subdomain in config.json. Also check that the following is the same on both servers if you customized VM2 config.

![enter image description here](screenshot1.png)

This configuration creates a local proxy server on port 1080. You can use it in a variety of ways.


### VM1 vmess-in data should be used to connect your client:

![enter image description here](screenshot2.png)

For the connecting address you have two options:

 1. Use an A record with cloud proxy on, then hide it behind any free CDN provider. [CDN settings should be HTTP.](https://github.com/bitcoinvps/sing-box-easy/tree/main/sing-box-config/vmess-ws-cdn#cdn-setup)
2. Use VM1 IP Address.

A VMESS URL can be built to import into any client. Alter UUID, Path & your CDN subdomain:

    vmess://ws:35a79205-c162-4c84-a9f9-79a5fb3190c8@vm1.bitcoinvps.cloud:80/?path=/stream#vmess@BitcoinVPS.Cloud


## 8. [Run Sing-Box with logs enabled](https://github.com/bitcoinvps/sing-box-easy#once-you-have-downloaded-the-correct-configjson-file-you-can-now-run-the-sing-box-server) 
## 9. [Check client connection](https://github.com/bitcoinvps/sing-box-easy#check-the-client-connection)
## 10. [Creating sing-box systemd service](https://github.com/bitcoinvps/sing-box-easy#creating-sing-box-systemd-service)
## 11. [Starting sing-box service](https://github.com/bitcoinvps/sing-box-easy#starting-sing-box-service)

