# sv-vpn

## Set Admin Password

Reset admin account and password: 3
Proceed: y
Enter new Password: junioradmin

## Interface-Rollen zuweisen

Assign Interfaces: 1
Keine VLANs konfigurieren: n
Enter WAN Interface name - em0, em1, em2 oder a: (Mac-Adressen überprüfen, um WAN und LAN-Interface zu ermitteln): em1
LAN Interface: em0
Optional: Enter
Proceed: y

## Interface IP's setzen

Set interface(s) IP address: 2
Number of interfaces to configure: 1 (WAN)
Per DHCP?: n
IPv4: 203.0.113.14
IPv4 Subnetmask: 30
upstream gateway address: 203.0.113.13
should this gateway be the default gateway?: y
DHCP für IPv6?: n
IPv6 manuell?: Enter
DHCP Server aktivieren?: n
Revert to HTTP as the webConfigurator protocol?: y
Enter

Set interface(s) IP address: 2
Number of interfaces to configure: 2 (LAN)
Per DHCP?: n
IPv4: 10.1.0.254
IPv4 Subnetmask: 24
upstream gateway address: Enter
DHCP für IPv6?: n
IPv6 manuell?: Enter
DHCP Server aktivieren?: n
Revert to HTTP as the webConfigurator protocol?: y
Enter

### VPN: oslo-fw_sv-vpn

Die Konfiguration ist hier aufzufinden:

[Konfiguration oslo-fw_sv-vpn VPN](../../VPNs/oslo-fw_sv-vpn_VPN/oslo-fw_sv-vpn_VPN.md)
