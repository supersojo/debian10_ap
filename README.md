# debian10 ap setting


## 1. Install hostapd package

apt install hostapd

In /etc/default/hostapd,uncomment DEAMON_CONF

DEAMON_CONF="/etc/hostapd/hostapd.conf"

## 2. cat /etc/hostapd/hostapd.conf
```
interface=wls1b1 # Your wifi interface name
bridge=br0 # Use bridge mode, so no need to install dnsmasq
driver=nl80211 # Must set this
country_code=CN # Your country code
ssid=MERCURY_SUYU3 # Your SSID
hw_mode=g 
channel=6
wpa=2
wpa_passphrase=xxxx # Your pass
wpa_key_mgmt=WPA-PSK
wpa_pairwise=TKIP
rsn_pairwise=CCMP
bridge=br0 # Bind to which bridge
auth_algs=1
macaddr_acl=0
```
## 3. The network settings
#ls /etc/network/interfaces.d/
```
ens5 wls1b1 br0
```

#cat /etc/network/interfaces.d/br0
```
auto br0

iface br0 inet static

bridge_ports ens5 wls1b1

address 192.168.3.210

network 192.168.3.0

netmask 255.255.255.0

gateway 192.168.3.1
```

*Note:*
Make sure that in file /etc/resolv.conf
nameserver 192.168.3.1

#cat /etc/network/interfaces.d/ens5
```
auto ens5
iface ens5 inet manual
ethtool -s ens5 wol g # Support wol for wired interface
```
#cat /etc/network/interfaces.d/ens5
```
auto wls1b1
iface wls1b1 inet manual
```
## 4. Enable and start hostapd
```
systemctl unmask hostapd
systemctl daemon-reload
systemctl enable hostapd
systemctl start hostapd
```

## 5. Happy to use AP now.


## 6. The network topology
```
M150R (dhcp server) 192.168.3.1
         ||
br0 ---ens5
   \---wls1b1(AP mode) iwconfig wls1b1 -- SSID MERCURY_SUYU3 
        |\
        | +-- dev
      dev 
```
All connected devices will get IP&dns from M150R.

## reference
[how-to-setup-access-point-wifi-hotspot-on-debian](https://superuser.com/questions/748455/how-to-setup-access-point-wifi-hotspot-on-debian)
