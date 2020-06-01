# Unifi controller config

## Getting Started

### Prerequisites

Two networks: default and secondary LAN with VLAN ID
```
ethernet eth1 {
    address 192.168.1.1/24
    description LAN
    firewall {
        ...
    }
    vif 2 {
        address 192.168.2.1/24
        firewall {
        ... 
    }
}
```

DHCP for both networks
```
shared-network-name net_LAN_eth1_192.168.1.0-24 {
    authoritative enable
    description vlan1
    subnet 192.168.1.0/24 {
        default-router 192.168.1.1
        dns-server 192.168.1.1
        domain-name <insert domain name>
        lease 86400
        start 192.168.1.6 {
            stop 192.168.1.149
        }
        unifi-controller 192.168.1.152
    }
}
shared-network-name net_PIA_LAN_eth1_192.168.2.0-24 {
    authoritative enable
    description vlan2
    subnet 192.168.2.0/24 {
        default-router 192.168.2.1
        dns-server 192.168.2.1
        lease 86400
        start 192.168.2.6 {
            stop 192.168.2.254
        }
        unifi-controller 192.168.1.152
    }
}
```

### Installing

Copy on USG under /config/auth:
* crt
* pem
* credentials
* ovpn file

opvn file should look like this:

```
client
dev tun
proto udp
remote <insert vpn address>
resolv-retry infinite
nobind
persist-key
persist-tun
cipher aes-128-cbc
auth sha1
tls-client
remote-cert-tls server
auth-user-pass /config/auth/credentials
comp-lzo
verb 1
reneg-sec 86400
max-routes 1000
route-nopull
crl-verify /config/auth/<insert filename>.pem
**ca /config/auth/<insert filename>.crt**
disable-occ
```

* Navigate on the unifi controller to data/sites/<insert siteid>
* Copy config.gateway.json
* Go to the unifi controller UI
* UniFi Controller Devices > USG > Config > Manage Device > Force provision 

https://help.ui.com/hc/en-us/articles/215458888-UniFi-How-to-further-customize-USG-configuration-with-config-gateway-json
