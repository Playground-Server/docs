### \[UNUSED\] Installing Mikrotik CHR on Vultr (Using Cloudflared Now)

Please refer to this page for mikrotik on vultr https://www.vultr.com/docs/configure-a-mikrotik-chr-router-with-a-virtual-private-cloud-vpc/.

### Basic Security Configuration

This configuration applied on both mikrotik (local and cloud).

```routeros
# Password
/password
# Remove/disable admin and create new user
/user add name=leon password=your-strong-password group=full
/user remove admin
# Disable unused service port (api might be opened again).
/ip service disable telnet,ftp,www,www-ssl,api,api-ssl
# Change ssh port
/ip service set ssh port=2209
# Disable neighbor discovery
/ip neighbor discovery-settings set discover-interface-list=none 
# Disable unused interface
/interface set x disabled=yes 
```

Lately our public cloud router bruteforced by random people, in that case we need Bruteforce prevention. This configuration will prevent a SSH and FTP brute forcer to be banned for 10 days after repetitive attempts and put them on the table of shame.

```routeros
/ip firewall filter
# FTP
add chain=input protocol=tcp dst-port=21 src-address-list=ftp_blacklist action=drop \
comment="drop ftp brute forcers"

add chain=output action=accept protocol=tcp content="530 Login incorrect" dst-limit=1/1m,9,dst-address/1m

add chain=output action=add-dst-to-address-list protocol=tcp content="530 Login incorrect" \
address-list=ftp_blacklist address-list-timeout=3h

# SSH
add chain=input protocol=tcp dst-port=2209 src-address-list=ssh_blacklist action=drop \
comment="drop ssh brute forcers" disabled=no

add chain=input protocol=tcp dst-port=2209 connection-state=new \
src-address-list=ssh_stage3 action=add-src-to-address-list address-list=ssh_blacklist \
address-list-timeout=10d comment="" disabled=no

add chain=input protocol=tcp dst-port=2209 connection-state=new \
src-address-list=ssh_stage2 action=add-src-to-address-list address-list=ssh_stage3 \
address-list-timeout=1m comment="" disabled=no

add chain=input protocol=tcp dst-port=2209 connection-state=new src-address-list=ssh_stage1 \
action=add-src-to-address-list address-list=ssh_stage2 address-list-timeout=1m comment="" disabled=no

add chain=input protocol=tcp dst-port=2209 connection-state=new action=add-src-to-address-list \
address-list=ssh_stage1 address-list-timeout=1m comment="" disabled=no
```

### DNS Server

Add a Mikrotik firewall rule that forces all LAN devices to direct all DNS queries to the Mikrotik DNS server.

```routeros
ip firewall nat add chain=dstnat action=redirect to-ports=53 protocol=udp dst-port=53 to-address=<internal-ip-addrs> comment="DIRECT ALL DNS REQUESTS TO MIKROTIK INTERNAL DNS SERVER."
```

### REST API

#### Upgrade Firmware

Starting from **RouterOS v7.1beta4**, it is implemented as a JSON wrapper interface of the console [API](https://help.mikrotik.com/docs/display/ROS/API). It allows to create, read, update and delete resources and call arbitrary console commands. And our firmware is still on version 6.49.8. So we need to upgrade it.

Following this tutorial is easy https://citraweb.com/artikel_lihat.php?id=126.

#### Enabling REST API

In Winbox, Go to **IP \> Services**. Then enable **www-ssl** and set the Availability to our subnet.

![image.png](img/mikrotik/01.png)

And open terminal and test it.

```bash
curl -k -u ouruser: https://mikrotik.playground.lokal/rest/system/resource
Enter host password for user 'ouruser':
{"architecture-name":"mmips","board-name":"hEX","build-time":"2024-06-07 12:49:11","cpu":"MIPS 1004Kc V2.15","cpu-count":"4","cpu-frequency":"880","cpu-load":"4","factory-software":"6.46.3","free-hdd-space":"4415488","free-memory":"204107776","platform":"MikroTik","total-hdd-space":"16777216","total-memory":"268435456","uptime":"2d11h36m31s","version":"7.15.1 (stable)","write-sect-since-reboot":"6410","write-sect-total":"1036240"}
```

### References

* \[UNUSED\] https://www.vultr.com/docs/configure-a-mikrotik-chr-router-with-a-virtual-private-cloud-vpc/
* https://billysoftacademy.com/how-to-use-a-mikrotik-router-as-a-dns-server-and-add-static-dns-records-for-internal-servers-in-your-lan-network/
* https://wiki.mikrotik.com/wiki/Manual:Securing_Your_Router
* https://wiki.mikrotik.com/wiki/Bruteforce_login_prevention
* https://help.mikrotik.com/docs/display/ROS/REST+API#RESTAPI-GET