# 3 Network

## 3.1 Configure Network Devices

| CIS-punkt | Beskrivning | Status | Åtgärd | Verifiering | Datum |
|---|---|---|---|---|---|
| 3.1.1 | IPv6-status identifierad | **Identifierad** | IPv6 är aktiverat och används (SSH lyssnar på [::]:22). Beslut: behåll aktiverat, kommande IPv6-kontroller ska tillämpas | CIS audit-script → "IPv6 is enabled" | 2026-09-13 |
| 3.1.2 | Trådlösa gränssnitt ej tillgängliga | **Redan uppfyllt** | Inga trådlösa NIC:s installerade | CIS audit-script → "System has no wireless NICs installed" | 2026-09-13 |
| 3.1.3 | Bluetooth-tjänster ej i bruk | **Redan uppfyllt** | Ej installerat | Kommando: `dpkg-query -s bluez &>/dev/null && echo "bluez is installed"`<br>Output: tom | 2026-09-13 |

## 3.2 Configure Network Kernel Modules

| CIS-punkt | Beskrivning | Status | Åtgärd | Verifiering | Datum |
|---|---|---|---|---|---|
| 3.2.1 | atm kernel-modul ej tillgänglig | **Åtgärdad** | Modulen fanns tillgänglig, ej blockerad. Blockerad med modprobe -r atm, .conf-fil skapad i /etc/modprobe.d/ | Kommando: `lsmod \| grep 'atm' → tom`<br>Kommando: `modprobe --showconfig \| grep -P -- '\b(install\|blacklist)\h+atm\b' → blacklist atm, install atm /bin/false` | 2026-09-13 |
| 3.2.2 | can kernel-modul ej tillgänglig | **Åtgärdad** | Modulen fanns tillgänglig, ej blockerad. Blockerad med modprobe -r can, .conf-fil skapad i /etc/modprobe.d/ | Kommando: `lsmod \| grep 'can' → tom`<br>Kommando: `modprobe --showconfig \| grep -P -- '\b(install\|blacklist)\h+can\b' → blacklist can, install can /bin/false` | 2026-09-13 |
| 3.2.3 | dccp kernel-modul ej tillgänglig | **Redan uppfyllt** | Modul ej tillgänglig på systemet | Audit-script (steg 1) → tom | 2026-09-13 |
| 3.2.4 | rds kernel-modul ej tillgänglig | **Åtgärdad** | Modulen fanns tillgänglig, ej blockerad. Blockerad med modprobe -r rds, .conf-fil skapad i /etc/modprobe.d/ | Kommando: `lsmod \| grep 'rds' → tom`<br>Kommando: `modprobe --showconfig \| grep -P -- '\b(install\|blacklist)\h+rds\b' → blacklist rds, install rds /bin/false` | 2026-09-13 |
| 3.2.5 | sctp kernel-modul ej tillgänglig | **Åtgärdad** | Modul fanns tillgänglig, ej blockerad. Blockerad med modprobe -r sctp, .conf-fil skapad i /etc/modprobe.d/. Note: SCTP kan i sällsynta fall användas av vissa Kubernetes NetworkPolicies — spårbart via modprobe - -showconfig om problem uppstår senare | Kommando: `lsmod \| grep 'sctp' → tom`<br>Kommando: `modprobe --showconfig \| grep -P -- '\b(install\|blacklist)\h+sctp\b' → blacklist sctp, install sctp /bin/false` | 2026-09-14 |
| 3.2.6 | tipc kernel-modul ej tillgänglig | **Åtgärdad** | Modulen fanns tillgänglig, ej blockerad. Blockerad med modprobe -r tipc, .conf-fil skapad i /etc/modprobe.d/ | Kommando: `lsmod \| grep 'tipc' → tom`<br>Kommando: `modprobe --showconfig \| grep -P -- '\b(install\|blacklist)\h+tipc\b' → blacklist tipc, install tipc /bin/false` | 2026-09-13 |
| 3.2.7 | Oanvända nätverksprotokoll-moduler ej tillgängliga | **Åtgärdad** | Full modulinventering kördes (400+ moduler). 66 exotiska/oanvända protokoll (appletalk, ax25, bluetooth, 9pnet, vsock, caif, can-varianter m.fl.) blockerade i /etc/modprobe.d/60-cis-network-disabled.conf. Docker/K8s-relevanta moduler (bridge, netfilter, ip_vs, tunneling, xfrm) medvetet exkluderade | Kommando: `grep -c 'install\|blacklist' /etc/modprobe.d/60-cis-network-disabled.conf → 132`<br>Kommando: `lsmod \| grep -E 'vsock\|bluetooth\|9pnet' → tom` | 2026-09-14 |

## 3.3.1 Configure IPv4 Kernel Parameters

| CIS-punkt | Beskrivning | Status | Åtgärd | Verifiering | Datum |
|---|---|---|---|---|---|
| 3.3.1.1 | net.ipv4.ip_forward konfigurerat | **Medveten avvikelse** | CIS rekommenderar 0, satt till 1 proaktivt inför planerad Docker/Kubernetes-installation (kräver IP forwarding för container-/pod-nätverk) i /etc/sysctl.d/60-ipv4_sysctl.conf | Kommando: `sysctl net.ipv4.ip_forward → net.ipv4.ip_forward = 1` | 2026-09-14 |
| 3.3.1.2 | net.ipv4.conf.all.forwarding konfigurerat | **Medveten avvikelse** | Se 3.3.1.1 | Kommando: `sysctl net.ipv4.conf.all.forwarding → net.ipv4.conf.all.forwarding = 1` | 2026-09-14 |
| 3.3.1.3 | net.ipv4.conf.default.forwarding konfigurerat | **Medveten avvikelse** | Se 3.3.1.1 | Kommando: `sysctl net.ipv4.conf.default.forwarding → net.ipv4.conf.default.forwarding = 1` | 2026-09-15 |
| 3.3.1.4 | net.ipv4.conf.all.send_redirects konfigurerat | **Åtgärdad** | Var 1, satt till 0 i /etc/sysctl.d/60-ipv4_sysctl.conf | Kommando: `sysctl net.ipv4.conf.all.send_redirects → net.ipv4.conf.all.send_redirects = 0` | 2026-09-15 |
| 3.3.1.5 | net.ipv4.conf.default.send_redirects konfigurerat | **Åtgärdad** | Var 1, satt till 0 i /etc/sysctl.d/60-ipv4_sysctl.conf | Kommando: `sysctl net.ipv4.conf.default.send_redirects → net.ipv4.conf.default.send_redirects = 0` | 2026-09-15 |
| 3.3.1.6 | net.ipv4.icmp_ignore_bogus_error_responses konfigurerat | **Åtgärdad** | Redan 1 (kernel default), säkrat explicit i /etc/sysctl.d/60-ipv4_sysctl.conf | Kommando: `sysctl net.ipv4.icmp_ignore_bogus_error_responses → net.ipv4.icmp_ignore_bogus_error_responses = 1` | 2026-09-15 |
| 3.3.1.7 | net.ipv4.icmp_echo_ignore_broadcasts konfigurerat | **Åtgärdad** | Redan 1 (kernel default), säkrat explicit i /etc/sysctl.d/60-ipv4_sysctl.conf | Kommando: `sysctl net.ipv4.icmp_echo_ignore_broadcasts → net.ipv4.icmp_echo_ignore_broadcasts = 1` | 2026-09-15 |
| 3.3.1.8 | net.ipv4.conf.all.accept_redirects konfigurerat | **Åtgärdad** | Redan 0, säkrat explicit i /etc/sysctl.d/60-ipv4_sysctl.conf | Kommando: `sysctl net.ipv4.conf.all.accept_redirects → net.ipv4.conf.all.accept_redirects = 0` | 2026-09-16 |
| 3.3.1.9 | net.ipv4.conf.default.accept_redirects konfigurerat | **Åtgärdad** | Redan 0, säkrat explicit i /etc/sysctl.d/60-ipv4_sysctl.conf | Kommando: `sysctl net.ipv4.conf.default.accept_redirects → net.ipv4.conf.default.accept_redirects = 0` | 2026-09-16 |
| 3.3.1.10 | net.ipv4.conf.all.secure_redirects konfigurerat | **Åtgärdad** | Var 1, satt till 0 i /etc/sysctl.d/60-ipv4_sysctl.conf | Kommando: `sysctl net.ipv4.conf.all.secure_redirects → net.ipv4.conf.all.secure_redirects = 0` | 2026-09-16 |
| 3.3.1.11 | net.ipv4.conf.default.secure_redirects konfigurerat | **Åtgärdad** | Var 1, satt till 0 i /etc/sysctl.d/60-ipv4_sysctl.conf | Kommando: `sysctl net.ipv4.conf.default.secure_redirects → net.ipv4.conf.default.secure_redirects = 0` | 2026-09-16 |
| 3.3.1.12 | net.ipv4.conf.all.rp_filter konfigurerat | **Medveten avvikelse** | CIS rekommenderar 1, behåller 2 för att undvika potentiella routningsproblem med Docker/Kubernetes CNI-plugins | Kommando: `sysctl net.ipv4.conf.all.rp_filter → net.ipv4.conf.all.rp_filter = 2` | 2026-09-16 |
| 3.3.1.13 | net.ipv4.conf.default.rp_filter konfigurerat | **Medveten avvikelse** | Se 3.3.1.12 | Kommando: `sysctl net.ipv4.conf.default.rp_filter → net.ipv4.conf.default.rp_filter = 2` | 2026-09-16 |
| 3.3.1.14 | net.ipv4.conf.all.accept_source_route konfigurerat | **Åtgärdad** | Redan 0, säkrat explicit i /etc/sysctl.d/60-ipv4_sysctl.conf | Kommando: `sysctl net.ipv4.conf.all.accept_source_route → net.ipv4.conf.all.accept_source_route = 0` | 2026-09-16 |
| 3.3.1.15 | net.ipv4.conf.default.accept_source_route konfigurerat | **Redan uppfyllt** | Ingen åtgärd, redan permanent satt till 0 av 55-network-security.conf | Kommando: `sysctl net.ipv4.conf.default.accept_source_route → net.ipv4.conf.default.accept_source_route = 0` | 2026-09-16 |
| 3.3.1.16 | net.ipv4.conf.all.log_martians konfigurerat | **Åtgärdad** | Var 0, satt permanent till 1 i /etc/sysctl.d/60-ipv4_sysctl.conf | Kommando: `sysctl net.ipv4.conf.all.log_martians → net.ipv4.conf.all.log_martians = 1` | 2026-09-16 |
| 3.3.1.17 | net.ipv4.conf.default.log_martians konfigurerat | **Åtgärdad** | Var 0, satt permanent till 1 i /etc/sysctl.d/60-ipv4_sysctl.conf | Kommando: `sysctl net.ipv4.conf.default.log_martians → net.ipv4.conf.default.log_martians = 1` | 2026-09-16 |
| 3.3.1.18 | net.ipv4.tcp_syncookies konfigurerat | **Åtgärdad** | Redan 1, satt permanent i /etc/sysctl.d/60-ipv4_sysctl.conf | Kommando: `sysctl net.ipv4.tcp_syncookies → net.ipv4.tcp_syncookies = 1` | 2026-09-16 |
| 3.3.1.19 | net.ipv4.conf.all.route_localnet konfigurerat | **Åtgärdad** | Redan 0, säkrat explicit i /etc/sysctl.d/60-ipv4_sysctl.conf. Note: kan behöva sättas till 1 senare om Kubernetes kube-proxy NodePort-åtkomst via localhost krånglar | Kommando: `sysctl net.ipv4.conf.all.route_localnet → net.ipv4.conf.all.route_localnet = 0` | 2026-09-16 |

## 3.3.2 Configure IPv6 Kernel Parameters

| CIS-punkt | Beskrivning | Status | Åtgärd | Verifiering | Datum |
|---|---|---|---|---|---|
| 3.3.2.1 | net.ipv6.conf.all.forwarding konfigurerat | **Åtgärdad** | Redan 0, säkrat explicit i /etc/sysctl.d/60-ipv6_sysctl.conf. Note: kan behöva sättas till 1 om IPv6 dual-stack aktiveras i Kubernetes senare | Kommando: `sysctl net.ipv6.conf.all.forwarding → net.ipv6.conf.all.forwarding = 0` | 2026-09-16 |
| 3.3.2.2 | net.ipv6.conf.default.forwarding konfigurerat | **Åtgärdad** | Redan 0, säkrat explicit i /etc/sysctl.d/60-ipv6_sysctl.conf | Kommando: `sysctl net.ipv6.conf.default.forwarding → net.ipv6.conf.default.forwarding = 0` | 2026-09-16 |
| 3.3.2.3 | net.ipv6.conf.all.accept_redirects konfigurerat | **Åtgärdad** | Redan 0, säkrat explicit i /etc/sysctl.d/60-ipv6_sysctl.conf | Kommando: `sysctl net.ipv6.conf.all.accept_redirects → net.ipv6.conf.all.accept_redirects = 0` | 2026-09-16 |
| 3.3.2.4 | net.ipv6.conf.default.accept_redirects konfigurerat | **Åtgärdad** | Redan 0, säkrat explicit i /etc/sysctl.d/60-ipv6_sysctl.conf | Kommando: `sysctl net.ipv6.conf.default.accept_redirects → net.ipv6.conf.default.accept_redirects = 0` | 2026-09-16 |
| 3.3.2.5 | net.ipv6.conf.all.accept_source_route konfigurerat | **Åtgärdad** | Redan 0, säkrat explicit i /etc/sysctl.d/60-ipv6_sysctl.conf | Kommando: `sysctl net.ipv6.conf.all.accept_source_route → net.ipv6.conf.all.accept_source_route = 0` | 2026-09-16 |
| 3.3.2.6 | net.ipv6.conf.default.accept_source_route konfigurerat | **Åtgärdad** | Redan 0, säkrat explicit i /etc/sysctl.d/60-ipv6_sysctl.conf | Kommando: `sysctl net.ipv6.conf.default.accept_source_route → net.ipv6.conf.default.accept_source_route = 0` | 2026-09-16 |
| 3.3.2.7 | net.ipv6.conf.all.accept_ra konfigurerat | **Åtgärdad** | Var 1, satt till 0 permanent i /etc/sysctl.d/60-ipv6_sysctl.conf. Bekräftat säkert: IPv6-adress och default-route är statiskt konfigurerade i /etc/netplan/50-cloud-init.yaml, ej beroende av SLAAC/Router Advertisement | Kommando: `sysctl net.ipv6.conf.all.accept_ra → net.ipv6.conf.all.accept_ra = 0` | 2026-09-16 |
| 3.3.2.8 | net.ipv6.conf.default.accept_ra konfigurerat | **Åtgärdad** | Var 1, satt till 0 permanent i /etc/sysctl.d/60-ipv6_sysctl.conf | Kommando: `sysctl net.ipv6.conf.default.accept_ra → net.ipv6.conf.default.accept_ra = 0` | 2026-09-16 |

