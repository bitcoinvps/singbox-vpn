## Install DNSCrypt-Proxy on VM1
### The first step is to create an SSH tunnel between VM1 and VM2. On VM1 run:
```
sudo su
ssh -D 8080 -N -f root@VM2IP
```
Setting a system-wide proxy is then required:
```
export all_proxy="socks5h://127.0.0.1:8080"
```
### Download and unpack DNSCrypt-Proxy
```
mkdir /opt/dnscrypt-proxy && cd $_
wget https://github.com/DNSCrypt/dnscrypt-proxy/releases/download/2.1.2/dnscrypt-proxy-linux_x86_64-2.1.2.tar.gz
tar xf dnscrypt-proxy-linux_x86_64-2.1.2.tar.gz
mv linux-x86_64/* /opt/dnscrypt-proxy
rm dnscrypt-proxy-linux_x86_64-2.1.2.tar.gz
rm -rf linux-x86_64
```
### You now need to download and edit the configuration file. Add your DNS stamp address at the end of the file:
```
curl https://raw.githubusercontent.com/bitcoinvps/sing-box-easy/main/dnscrypt-proxy-config/dnscrypt-proxy-sdns-only.toml > /opt/dnscrypt-proxy/dnscrypt-proxy.toml
nano /opt/dnscrypt-proxy/dnscrypt-proxy.toml
```
#### Instead of the above, you can download the below config if you want to benefit from public resolvers and keep your encrypted DNS server as a backup:
```
curl https://raw.githubusercontent.com/bitcoinvps/sing-box-easy/main/dnscrypt-proxy-config/dnscrypt-proxy.toml > /opt/dnscrypt-proxy/dnscrypt-proxy.toml
nano /opt/dnscrypt-proxy/dnscrypt-proxy.toml
```
### Install the DNSCrypt-Proxy systemd service now:
```
./dnscrypt-proxy -service install
```
### Now start the service:
```
systemctl daemon-reload && systemctl enable dnscrypt-proxy && systemctl start dnscrypt-proxy
systemctl status dnscrypt-proxy
```
### DNSScrypt-Proxy needs to be ready before being used. Take a look at the screenshots below:
![enter image description here](/dnscrypt-proxy-config/service-ready.png)
![enter image description here](/dnscrypt-proxy-config/service-ready2.png)
### It is now possible to check if DNSCrypt-Proxy is working:
```
host twitter.com 127.0.0.1
```
### If the DNS is working now you can set it as your default resolver:
```
cat <<EOF >/etc/resolv.conf
nameserver 127.0.0.1
EOF
```