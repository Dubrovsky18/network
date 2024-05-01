WBR# sh running-config 

hostname WBR

object-group service ssh
port-range 22
exit

object-group service dhcp_server
port-range 67
exit

object-group service dhcp_client
port-range 68
exit

object-group service ntp
port-range 123
exit

syslog max-files 3
syslog file-size 512
syslog file tmpsys:syslog/default info

username admin
password encrypted $6$ApqtH9yNpiCXBABp$HwWaFo9BGqab2vBn3q1uFZ3fCRcK113MTYROPD 1f6c450sqRLqp9.HYmxtvHEQGGM4Jds/9JJ9sD4FBkLEX/ 
exit



boot host auto-config
vlan 2
exit

security zone trusted
exit

security zone untrusted
exit


birtidge 1
vian 1
security-zone trusted
ip address 192.168.1.1/24
enable
exit 

bridge 2
vlan 2
security-zone untrusted
ip address dhcp
enable
exit

interface gigabitethernet 1/0/1
description "ISP1"
exit
interface gigabitethernet 1/0/2
description "to_SW02"
ip firewall disable
exit
interface gigabitethernet 1/0/2.12
exit
interface gigabitethernet 1/0/2.100
description "mgmt"
ip firewall disable
ip address 172.16.10.1/29
exit
interface gigabitethernet 1/0/2.110
ip firewall disable
ip address 172.16.11.1/29
exit
interface gigabitethernet 1/0/2.120
ip firewall disable
ip address 172.16.12.1/29
exit
interface gigabitethernet 1/0/2.200
ip firewall disable
ip address 172.16.20.1/29
exit
interface gigabitethernet 1/0/2.210
ip firewall disable
ip address 172.16.21.1/29
exit
interface gigabitethernet 1/0/2.220
ip firewall disable
ip address 172.16.22.1/29
exit
interface gigabitethernet 1/0/3
description "to_SW01" 
ip firewall disable
exit
Interface gigabitethernet 1/0/3.10 
ip firewall disable
ip address 192.168.10.1/29
exit
Interface gigabitethernet 1/0/3.11
ip firewall disable
ip address 192.168.11.1/29
exit
interface gigabitethernet 1/0/3.12
ip firewall disable
ip address 192.168.12.1/29
exit
interface gigabitethernet 1/0/3.20
ip firewall disable
ip address 192.168.20.1/29
exit
interface gigabitethernet 1/0/3.21
ip firewall disable
ip address 192.168.21.1/29
exit
interface gigabitethernet 1/0/3.22
ip firewall disable
ip address 192.168.22.1/29
exit
interface gigabitethernet 1/0/4
mode switchport
exit

security zone-pair trusted untrusted
rule 1
action permit
enable
exit
exit

security zone-pair trusted trusted
rule 1
action permit
enable
exit
exit

security zone-pair trusted self
rule 10
action permit
match protocol tcp
metch destination-port ssh
enable
exit

rule 20
action permit
match protocol icmp
enable
exit

rule 30
action permit
match protocol udp
match source-port dhcp_client
match destination-port dhcp_server
enable
exit

rule 40
action permit
match protocol udp
match destination-port ntp
enable
exit
exit

security zone-pair untrusted self
rule 1
action permit
match protocol udp
match source-port dhcp_server
match destination-port dhcp_client
enable
exit
exit

security passwords default-expired
nat source
ruleset factory
to zone untrusted
rule 10
description "replace 'source ip' by outgoing interface ip address"
action source-nat interface
enable
exit
exit
exit

ip dhcp-server
ip dhcp-server pool lan-pool
network 192.168.1.0/24
address-range 192.168.1.2-192.168.1.254
default-router 192.168.1.1
exit
ip dhcp-server pool AP
network 192.168.12.0/29
address-range 192.168.12.2-192.168.12.6
default-router 192.168.12.1
option 43 ip-address 192.168.11.3
exit
ip dhcp-server pool Users
network 192.168.20.0/24
address-range 192.168.20.2-192.168.20.254
default-router 192.168.20.1
exit

ip route 0.0.0.0/0 interface gigabitethernet 1/0/1

ip ssh server

ntp enable
ntp broadcast-client enable
