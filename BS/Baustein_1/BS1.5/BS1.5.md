# BS1.5

## Plan
wir wollen die Konfiguration von Windows Servern mit Ansible umsetzen.

Dazu setzen wir eine Testumgebung aus einer Linux Ansible Control Node und zwei Windows-Servern um.

## Control-Node
| interface          | ip             | lan segment|
| ------------------ | -------------- | ---------- |
| NAT-Interface      | DHCP           | -          |
| ens37              | 192.168.1.1/24 | WAnsible   |

- ### Konfiguration

    - Control Node mit Netplan konfigurieren
    ![Control Node Netplan Config](./IMAGES/netplan_config.png)

## WS1-Ansible
| interface          | ip             | lan segment|
| ------------------ | -------------- | ---------- |
| NAT-Interface      | DHCP           | -          |
| Ethernet1          | 192.168.1.2/24 | WAnsible   |
### Konfiguration
**Skript:**
```shell
# ---------- Script-Anfang ----------  
# Set device name  
Rename-Computer -NewName 'WS1-Ansible' -Force  
# Rename network adapter
Rename-NetAdapter -Name 'Ethernet0' -NewName 'Internet'

Rename-NetAdapter -Name 'Ethernet1' -NewName 'WAnsible'  
# Setup dynamic for IPv4  
Set-NetIPInterface -InterfaceAlias 'WAnsible' -AddressFamily IPv4 -Dhcp Disabled  
New-NetIPAddress -InterfaceAlias 'WAnsible'-AddressFamily IPv4 -IPAddress '192.168.1.2' -PrefixLength 24 -DefaultGateway "192.168.1.1" 
# DNS-Client  
Set-DnsClientServerAddress -InterfaceAlias 'WAnsible' -ResetServerAddresses
Set-DnsClientServerAddress -InterfaceAlias 'WAnsible' -ServerAddresses ("192.168.1.1")
  
# Set timezone and time (timeserver)  
w32tm /config /syncfromflags:manual /manualpeerlist:"pool.ntp.org" /reliable:yes /update
tzutil /s "W. Europe Standard Time"  
# Allow pings in both directions  
Set-NetFirewallRule -Name "CoreNet-Diag-ICMP4-EchoRequest-In" -Enabled True  
Set-NetFirewallRule -Name "CoreNet-Diag-ICMP4-EchoRequest-Out" -Enabled True

Set-NetFirewallRule -Name "CoreNet-Diag-ICMP6-EchoRequest-In" -Enabled True  
Set-NetFirewallRule -Name "CoreNet-Diag-ICMP6-EchoRequest-Out" -Enabled True  
# RDP aktivieren  
Enable-PSRemoting -Force  
Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -value 0  
# Reboot  
Restart-Computer
# ---------- Script-Ende----------
```    

## WS2-Ansible
| interface          | ip             | lan segment |
| ------------------ | -------------- | ----------- |
| NAT-Interface      | DHCP           | -           |
| Ethernet1          | 192.168.1.3/24 | WAnsible    |

### Konfiguration
**Skript**
```shell
# ---------- Script-Anfang ----------  
# Set device name  
Rename-Computer -NewName 'WS2-Ansible' -Force  
# Rename network adapter
Rename-NetAdapter -Name 'Ethernet0' -NewName 'Internet'

Rename-NetAdapter -Name 'Ethernet1' -NewName 'WAnsible'  
# Setup dynamic for IPv4  
Set-NetIPInterface -InterfaceAlias 'WAnsible' -AddressFamily IPv4 -Dhcp Disabled  
New-NetIPAddress -InterfaceAlias 'WAnsible'-AddressFamily IPv4 -IPAddress '192.168.1.3' -PrefixLength 24 -DefaultGateway "192.168.1.1" 
# DNS-Client  
Set-DnsClientServerAddress -InterfaceAlias 'WAnsible' -ResetServerAddresses
Set-DnsClientServerAddress -InterfaceAlias 'WAnsible' -ServerAddresses ("192.168.1.1")
  
# Set timezone and time (timeserver)  
w32tm /config /syncfromflags:manual /manualpeerlist:"pool.ntp.org" /reliable:yes /update
tzutil /s "W. Europe Standard Time"  
# Allow pings in both directions  
Set-NetFirewallRule -Name "CoreNet-Diag-ICMP4-EchoRequest-In" -Enabled True  
Set-NetFirewallRule -Name "CoreNet-Diag-ICMP4-EchoRequest-Out" -Enabled True

Set-NetFirewallRule -Name "CoreNet-Diag-ICMP6-EchoRequest-In" -Enabled True  
Set-NetFirewallRule -Name "CoreNet-Diag-ICMP6-EchoRequest-Out" -Enabled True  
# RDP aktivieren  
Enable-PSRemoting -Force  
Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -value 0  
# Reboot  
Restart-Computer
# ---------- Script-Ende----------
```