# 韌體下載
[Openwrt firmware-selector](https://firmware-selector.openwrt.org/?version=24.10.0-rc6&target=sunxi%2Fcortexa53&id=xunlong_orangepi-zero3)
## 1. Installed Packages
base-files ca-bundle dnsmasq dropbear e2fsprogs firewall4 fstools kmod-nft-offload libc libgcc libustream-mbedtls logd mkf2fs mtd netifd nftables odhcp6c odhcpd-ipv6only opkg partx-utils ppp ppp-mod-pppoe procd-ujail uboot-envtools uci uclient-fetch urandom-seed urngd luci
## 2. Script to run on first boot (uci-defaults)
```
# Beware! This script will be in /rom/etc/uci-defaults/ as part of the image.
# Uncomment lines to apply:
#
wlan_name="OpenWrt"
wlan_password="123456789"
#
root_password=""
lan_ip_address="192.168.100.10"
#
# pppoe_username=""
# pppoe_password=""

# log potential errors
exec >/tmp/setup.log 2>&1

if [ -n "$root_password" ]; then
  (echo "$root_password"; sleep 1; echo "$root_password") | passwd > /dev/null
fi

# Configure LAN
# More options: https://openwrt.org/docs/guide-user/base-system/basic-networking
if [ -n "$lan_ip_address" ]; then
  uci set network.lan.ipaddr="$lan_ip_address"
  uci commit network
fi

# Configure WLAN
# More options: https://openwrt.org/docs/guide-user/network/wifi/basic#wi-fi_interfaces
if [ -n "$wlan_name" -a -n "$wlan_password" -a ${#wlan_password} -ge 8 ]; then
  uci set wireless.@wifi-device[0].disabled='0'
  uci set wireless.@wifi-iface[0].disabled='0'
  uci set wireless.@wifi-iface[0].encryption='psk2'
  uci set wireless.@wifi-iface[0].ssid="$wlan_name"
  uci set wireless.@wifi-iface[0].key="$wlan_password"
  uci commit wireless
fi

# Configure PPPoE
# More options: https://openwrt.org/docs/guide-user/network/wan/wan_interface_protocols#protocol_pppoe_ppp_over_ethernet
if [ -n "$pppoe_username" -a "$pppoe_password" ]; then
  uci set network.wan.proto=pppoe
  uci set network.wan.username="$pppoe_username"
  uci set network.wan.password="$pppoe_password"
  uci commit network
fi

echo "All done!"

```
![image](https://github.com/user-attachments/assets/4b941379-9d9d-47bc-8dab-bf93f7d0065a)
# 燒錄至SD卡
## [balenaEtcher](https://etcher.balena.io)
# 登入網頁介面
1. SD卡插進zero3
2. usb-typeC 供電
3. 網路線連接
4. 同區域網pc網頁登入lan_ip_address="192.168.100.10", 登入root
![image](https://github.com/user-attachments/assets/32dea34d-60f4-43fb-9c4f-90a224210d29)
