# 2 Services

## 2.1 Configure Server Services

| CIS-punkt | Beskrivning | Status | Åtgärd | Verifiering | Datum |
|---|---|---|---|---|---|
| 2.1.1 | autofs-tjänster ej i bruk | **Redan uppfyllt** | Ej installerat | Kommando: `dpkg-query -s autofs &>/dev/null && echo "autofs is installed"`<br>Output: tom | 2026-09-12 |
| 2.1.2 | avahi daemon-tjänster ej i bruk | **Redan uppfyllt** | Ej installerat | Kommando: `dpkg-query -s avahi-daemon &>/dev/null && echo "avahi-daemon is installed"`<br>Output: tom | 2026-09-12 |
| 2.1.3 | dhcp server-tjänster (kea) ej i bruk | **Redan uppfyllt** | Ej installerat | Kommando: `dpkg-query -l \| awk '{print $2}' \| grep -Pio -- 'kea' &>/dev/null && echo "kea is installed"`<br>Output: tom | 2026-09-12 |
| 2.1.4 | dns server-tjänster (bind9) ej i bruk | **Redan uppfyllt** | Ej installerat | Kommando: `dpkg-query -s bind9 &>/dev/null && echo "bind9 is installed"`<br>Output: tom | 2026-09-12 |
| 2.1.5 | dnsmasq-tjänster ej i bruk | **Redan uppfyllt** | Ej installerat | Kommando: `dpkg-query -s dnsmasq &>/dev/null && echo "dnsmasq is installed"`<br>Output: tom | 2026-09-12 |
| 2.1.6 | ftp server-tjänster (vsftpd) ej i bruk | **Redan uppfyllt** | Ej installerat | Kommando: `dpkg-query -s vsftpd &>/dev/null && echo "vsftpd is installed"`<br>Output: tom | 2026-09-12 |
| 2.1.7 | ldap server-tjänster (slapd) ej i bruk | **Redan uppfyllt** | Ej installerat | Kommando: `dpkg-query -s slapd &>/dev/null && echo "slapd is installed"`<br>Output: tom | 2026-09-12 |
| 2.1.8 | message access server-tjänster (dovecot) ej i bruk | **Redan uppfyllt** | Ej installerat | Kommando: `dpkg-query -s dovecot-imapd &>/dev/null && echo "..." → tom`<br>Kommando: `dpkg-query -s dovecot-pop3d &>/dev/null && echo "..." → tom` | 2026-09-12 |
| 2.1.9 | network file system-tjänster ej i bruk | **Redan uppfyllt** | Ej installerat | Kommando: `dpkg-query -s nfs-kernel-server &>/dev/null && echo "nfs-kernel-server is installed"`<br>Output: tom | 2026-09-12 |
| 2.1.10 | nis server-tjänster (ypserv) ej i bruk | **Redan uppfyllt** | Ej installerat | Kommando: `dpkg-query -s ypserv &>/dev/null && echo "ypserv is installed"`<br>Output: tom | 2026-09-12 |
| 2.1.11 | print server-tjänster (cups) ej i bruk | **Redan uppfyllt** | Ej installerat | Kommando: `dpkg-query -s cups &>/dev/null && echo "cups is installed"`<br>Output: tom | 2026-09-12 |
| 2.1.12 | rpcbind-tjänster ej i bruk | **Redan uppfyllt** | Ej installerat | Kommando: `dpkg-query -s rpcbind &>/dev/null && echo "rpcbind is installed"`<br>Output: tom | 2026-09-12 |
| 2.1.13 | rsync-tjänster ej i bruk | **Åtgärdad** | rsync var installerat men ej planerad för användning, stoppade rsync.service och tog bort paketet med apt purge rsync -y | Kommando: `dpkg-query -s rsync &>/dev/null && echo "rsync is installed"`<br>Output: tom | 2026-09-12 |
| 2.1.14 | samba file server-tjänster ej i bruk | **Redan uppfyllt** | Ej installerat | Kommando: `dpkg-query -s samba &>/dev/null && echo "samba is installed"`<br>Output: tom | 2026-09-12 |
| 2.1.15 | snmp-tjänster ej i bruk | **Redan uppfyllt** | Ej installerat | Kommando: `dpkg-query -s snmpd &>/dev/null && echo "snmpd is installed"`<br>Output: tom | 2026-09-12 |
| 2.1.16 | telnet server-tjänster ej i bruk | **Redan uppfyllt** | Ej installerat | Kommando: `dpkg-query -l \| awk '{print $2}' \| grep -Pio -- '^telnetd\|^telnetd-ssl' &>/dev/null && echo "telnetd is installed"`<br>Output: tom | 2026-09-12 |
| 2.1.17 | tftp server-tjänster (tftpd-hpa) ej i bruk | **Redan uppfyllt** | Ej installerat | Kommando: `dpkg-query -s tftpd-hpa &>/dev/null && echo "tftpd-hpa is installed"`<br>Output: tom | 2026-09-12 |
| 2.1.18 | web proxy server-tjänster (squid) ej i bruk | **Redan uppfyllt** | Ej installerat | Kommando: `dpkg-query -s squid &>/dev/null && echo "squid is installed"`<br>Output: tom | 2026-09-12 |
| 2.1.19 | web server-tjänster ej i bruk | **Redan uppfyllt** | Ingen åtgärd, apache2 eller nginx ej installerade | Kommando: `dpkg-query -s apache2 &>/dev/null && echo "apache2 is installed" → tom`<br>Kommando: `dpkg-query -s nginx &>/dev/null && echo "nginx is installed" → tom` | 2026-09-12 |
| 2.1.20 | xinetd-tjänster ej i bruk | **Redan uppfyllt** | Ej installerat | Kommando: `dpkg-query -s xinetd &>/dev/null && echo "xinetd is installed"`<br>Output: tom | 2026-09-12 |
| 2.1.22 | Mail transfer agents konfigurerade för local-only mode | **Redan uppfyllt** | Ingen MTA installerad | CIS audit-script kördes → PASS, portar 25/465/587 ej lyssnande på icke-loopback, MTA ej i bruk | 2026-09-12 |
| 2.1.23 | Endast godkända tjänster lyssnar på nätverksgränssnitt | **Redan uppfyllt** | Ingen åtgärd, enbart nödvändiga tjänster lyssnar (systemd-resolve/DNS, systemd-network/DHCP, chronyd/NTP-kontroll — alla loopback-bundna, samt sshd på 0.0.0.0/[::]:22 för fjärråtkomst) | Kommando: `ss -plntu → 4 tjänster identifierade, samtliga granskade och godkända` | 2026-09-12 |

## 2.2 Configure Client Services

| CIS-punkt | Beskrivning | Status | Åtgärd | Verifiering | Datum |
|---|---|---|---|---|---|
| 2.2.1 | nis client ej installerat | **Redan uppfyllt** | Ej installerat | Kommando: `dpkg-query -s nis &>/dev/null && echo "nis is installed"`<br>Output: tom | 2026-09-13 |
| 2.2.2 | rsh client ej installerat | **Redan uppfyllt** | Ej installerat | Kommando: `dpkg-query -s rsh-client &>/dev/null && echo "rsh-client is installed"`<br>Output: tom | 2026-09-13 |
| 2.2.3 | talk client ej installerat | **Redan uppfyllt** | Ej installerat | Kommando: `dpkg-query -s talk &>/dev/null && echo "talk is installed"`<br>Output: tom | 2026-09-13 |
| 2.2.4 | telnet client ej installerat | **Åtgärdad** | inetutils-telnet var installerat, borttaget med apt purge telnet inetutils-telnet telnet-ssl -y | Kommando: `for pkg in telnet inetutils-telnet telnet-ssl; do dpkg-query -s "$pkg" 2>/dev/null \| grep -q '^Status: install ok installed' && echo "$pkg is installed"; done`<br>Output: tom | 2026-09-13 |
| 2.2.5 | ldap client ej installerat | **Redan uppfyllt** | Ej installerat | Kommando: `dpkg-query -s ldap-utils &>/dev/null && echo "ldap-utils is installed"`<br>Output: tom | 2026-09-13 |
| 2.2.6 | ftp client ej installerat | **Redan uppfyllt** | Ej installerat | Kommando: `for pkg in ftp tnftp atftp inetutils-ftp ftp-ssl; do dpkg-query -s "$pkg" ...; done`<br>Output: tom (samtliga fem) | 2026-09-13 |

## 2.3 Configure Time Synchronization

| CIS-punkt | Beskrivning | Status | Åtgärd | Verifiering | Datum |
|---|---|---|---|---|---|
| 2.3.1.1 | Endast en tidssync-demon i bruk | **Redan uppfyllt** | Ingen åtgärd, chrony är enabled och active, systemd-timesyncd är inaktiv | CIS audit-script → PASS: endast chrony.service enabled/active | 2026-09-13 |
| 2.3.2.1 | systemd-timesyncd konfigurerad med auktoriserad tidsserver | **N/A** | Servern använder chrony | Se 2.3.1.1 | 2026-09-13 |
| 2.3.2.2 | systemd-timesyncd enabled och running | **N/A** | Servern använder chrony | Se 2.3.1.1 | 2026-09-13 |
| 2.3.3.1 | chrony konfigurerad | **Redan uppfyllt** | Ingen åtgärd, pool-direktiv mot Ubuntus NTP-pooler (NTS-säkrat) redan konfigurerat | CIS audit-script → PASS: pool-rader i /etc/chrony/sources.d/ubuntu-ntp-pools.sources | 2026-09-13 |
| 2.3.3.2 | chrony körs som _chrony | **Redan uppfyllt** | Ingen åtgärd — chronyd körde redan korrekt som _chrony. Note: Ubuntus chronyd-starter.sh-wrapper körs som root men startar själva chronyd-binären som _chrony; standardkommandots regex matchade wrapper-scriptets namn och gav falskt FAIL vid första körningen | Kommando: `ps -ef \| grep '[/]usr/sbin/chronyd' \| awk '$1!="_chrony" { print $1 }'`<br>Output: tom | 2026-09-13 |
| 2.3.3.3 | chrony enabled och running | **Redan uppfyllt** | Ingen åtgärd | Kommando: `systemctl is-enabled chrony.service → enabled`<br>Kommando: `systemctl is-active chrony.service → active` | 2026-09-13 |

## 2.4 Job Schedulers

| CIS-punkt | Beskrivning | Status | Åtgärd | Verifiering | Datum |
|---|---|---|---|---|---|
| 2.4.1.1 | cron daemon enabled och active | **Redan uppfyllt** | Ingen åtgärd | Kommando: `systemctl list-unit-files \| awk '$1~/^crond?\.service/{print $2}' → enabled`<br>Kommando: `systemctl list-units \| awk '$1~/^crond?\.service/{print $3}' → active` | 2026-09-13 |
| 2.4.1.2 | Åtkomst till /etc/crontab konfigurerad | **Åtgärdad** | Var 644, ändrad till 600 med chown root:root /etc/crontab &amp;&amp; chmod og-rwx /etc/crontab | Kommando: `stat -Lc 'Access: (%a/%A) Uid: ( %u/ %U) Gid: ( %g/ %G)' /etc/crontab`<br>Output: Access: (600/-rw-------) Uid: ( 0/ root) Gid: ( 0/ root) | 2026-09-13 |
| 2.4.1.3 | Åtkomst till /etc/cron.hourly/konfigurerad | **Åtgärdad** | Var 755, ändrad till 700 med chown root:root /etc/cron.hourly/ &amp;&amp; chmod og-rwx /etc/cron.hourly/ | Kommando: `stat -Lc 'Access: (%a/%A) Uid: ( %u/ %U) Gid: ( %g/ %G)' /etc/cron.hourly/`<br>Output: Access: (700/drwx------) Uid: ( 0/ root) Gid: ( 0/ root) | 2026-09-13 |
| 2.4.1.4 | Åtkomst till /etc/cron.daily/konfigurerad | **Åtgärdad** | Var 755, ändrad till 700 med chown root:root /etc/cron.daily/ &amp;&amp; chmod og-rwx /etc/cron.daily/ | Kommando: `stat -Lc 'Access: (%a/%A) Uid: ( %u/ %U) Gid: ( %g/ %G)' /etc/cron.daily/`<br>Output: Access: (700/drwx------) Uid: ( 0/ root) Gid: ( 0/ root) | 2026-09-13 |
| 2.4.1.5 | Åtkomst till /etc/cron.weekly/konfigurerad | **Åtgärdad** | Var 755, ändrad till 700 med chown root:root /etc/cron.weekly/ &amp;&amp; chmod og-rwx /etc/cron.weekly/ | Kommando: `stat -Lc 'Access: (%a/%A) Uid: ( %u/ %U) Gid: ( %g/ %G)' /etc/cron.weekly/`<br>Output: Access: (700/drwx------) Uid: ( 0/ root) Gid: ( 0/ root) | 2026-09-13 |
| 2.4.1.6 | Åtkomst till /etc/cron.monthly/konfigurerad | **Åtgärdad** | Var 755, ändrad till 700 med chown root:root /etc/cron.monthly/ &amp;&amp; chmod og-rwx /etc/cron.monthly/ | Kommando: `stat -Lc 'Access: (%a/%A) Uid: ( %u/ %U) Gid: ( %g/ %G)' /etc/cron.monthly/`<br>Output: Access: (700/drwx------) Uid: ( 0/ root) Gid: ( 0/ root) | 2026-09-13 |
| 2.4.1.7 | Åtkomst till /etc/cron.yearly/konfigurerad | **Åtgärdad** | Var 755, ändrad till 700 med chown root:root /etc/cron.yearly/ &amp;&amp; chmod og-rwx /etc/cron.yearly/ | Kommando: `stat -Lc 'Access: (%a/%A) Uid: ( %u/ %U) Gid: ( %g/ %G)' /etc/cron.yearly/`<br>Output: Access: (700/drwx------) Uid: ( 0/ root) Gid: ( 0/ root) | 2026-09-13 |
| 2.4.1.8 | Åtkomst till /etc/cron.d/konfigurerad | **Åtgärdad** | Var 755, ändrad till 700 med chown root:root /etc/cron.d/ &amp;&amp; chmod og-rwx /etc/cron.d/ | Kommando: `stat -Lc 'Access: (%a/%A) Uid: ( %u/ %U) Gid: ( %g/ %G)' /etc/cron.d/`<br>Output: Access: (700/drwx------) Uid: ( 0/ root) Gid: ( 0/ root) | 2026-09-13 |
| 2.4.1.9 | Åtkomst till crontab konfigurerad | **Åtgärdad** | /etc/cron.allow saknades (/etc/cron.deny fanns inte heller, godkänt läge). Skapade /etc/cron.allow med chmod u-x,g-wx,o-rwx och chown root:crontab | Kommando: `stat -Lc 'Access: (%a/%A) Owner: (%U) Group: (%G)' /etc/cron.allow`<br>Output: Access: (640/-rw-r-----) Owner: (root) Group: (crontab) | 2026-09-13 |
| 2.4.2.1 | Åtkomst till at konfigurerad | **Åtgärdad** | /etc/at.allow saknades, skapad med korrekt ägare/behörighet. /etc/at.deny redan korrekt (640/root:daemon) | Kommando: `stat -Lc 'Access: (%a/%A) Owner: (%U) Group: (%G)' /etc/at.allow`<br>Output: Access: (640/-rw-r-----) Owner: (root) Group: (daemon) | 2026-09-13 |

