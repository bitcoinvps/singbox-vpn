# VMESS-Port:80-WebSocket-CDN

  

We hide our node IP behind a CDN and use Websockets on port 80 for transport

  

### I highly recommending using our services at [bitcoinvps.cloud](https://bitcoinvps.cloud). We provide free assistance with installing and configuring sing-box on our servers.

  

## Server Setup:

  

You can now download and edit the server configuration file:

  

    curl https://raw.githubusercontent.com/bitcoinvps/sing-box-easy/main/sing-box-config/vmess-ws-cdn/server/config.json > /etc/sing-box/config.json
    
    nano /etc/sing-box/config.json

  

It is possible to change these two parameters or leave them as they are:

  

- **UUID**: [Can be generated on this page if necessary.](https://www.uuidgenerator.net/version4)

- **Path**: /stream is the default path, you can change it or leave it as is

  
  

## CDN Setup:

  

Connect your domain to Cloudflare.

Navigate to SSL/TLS-> Overview

Make sure you match the below screenshot:

  

![Turn off SSL to use port 80 without TLS](cloudflarescreenshot1.png)

  

Create an A record for a subdomain (with any name you wish) pointing to your sing-box VPS:

  

![enter image description here](cloudflarescreenshot2.png)

  

This configuration does not include TLS, so you can use multiple CDN services simultaneously. For example you can connect another domain to Arvancloud Free CDN. Check the following screenshots:

  

![enter image description here](arvan1.png)

  
  

![enter image description here](arvan2.png)

![enter image description here](arvan3.png)

  

Ensure that your VPS provider supports IPv6. IPv6 can be configured with an AAAA record. IPv6 has been reported to work with nearly all protocols by some users. [Bitcoinvps.cloud](https://bitcoinvps.cloud) provides IPv6 for free with every VPS.

  

## Client configuration

  

A VMESS URL can be built to import into any client. Alter UUID, Path & your CDN subdomain:

  

> vmess://ws:**3ba295b2-5336-40fa-bfc8-69487d169b61-0**@**vmess.bitcoinvps.cloud**:80/?path=**/stream**#vmess@BitcoinVPS.Cloud

  

If you use any other clients that support VMESS protocol, then add manual config with following:

  

Add (manual) VMESS Server:

- You can type anything you like for name/alias/remarks

- For the address, use the CDN subdomain. Eg. : vmess.bitcoinvps.cloud

- Port: 80

- UUID: should match your server config.json UUID. default: 3ba295b2-5336-40fa-bfc8-69487d169b61

- Encryption: Auto

- Transport Protocol: WS

- Path: should match the path in config.json on your server. default: /stream

  

It is now possible for you to [set up Sing-Box service and run the server](https://github.com/bitcoinvps/sing-box-easy#run-sing-box-server/). After that, you can connect using the client of your choice.