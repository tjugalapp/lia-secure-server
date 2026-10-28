# 4 Host Based Firewall

## 4.1 Configure Uncomplicated Firewall (UFW)

| CIS-punkt | Beskrivning | Status | Åtgärd | Verifiering | Datum |
|---|---|---|---|---|---|
| 4.1.1 | UFW ska vara installerat | **Redan uppfyllt** | Ingen åtgärd. UFW var redan installerat på den nya servern | Kommando: `dpkg-query -s ufw &>/dev/null && echo "INSTALLERAT" \|\| echo "EJ INSTALLERAT"`<br>Output: INSTALLERAT | 2026-09-08 |
| 4.1.2 | ufw-tjänsten ska vara konfigurerad (enabled, active) | **Åtgärdad** | Lade till regel för SSH (port 22/tcp) innan aktivering för att undvika utelåsning, aktiverade sedan UFW med "ufw --force enable" | Kommando: `systemctl is-enabled ufw.service; systemctl is-active ufw.service;`<br>ufw status<br>Output: enabled / active / Status: active. Regel: 22/tcp ALLOW Anywhere (IPv4 + IPv6) | 2026-09-14 |
| 4.1.3 | Standardpolicy för inkommande trafik ska vara konfigurerad | **Redan uppfyllt** | Ingen åtgärd. Standardpolicyn deny (incoming) var redan satt (UFW:s standardvärde) | Kommando: `ufw status verbose \| grep -P -- '^Default:'`<br>Output: Default: deny (incoming), allow (outgoing), deny (routed) | 2026-09-14 |
| 4.1.5 | Standardpolicy för routad trafik ska vara konfigurerad | **Redan uppfyllt** | Ingen åtgärd. Standardpolicyn deny (routed) var redan satt (UFW:s standardvärde) | Kommando: `ufw status verbose \| grep -w routed`<br>Output: Default: deny (incoming), allow (outgoing), deny (routed) | 2026-09-14 |

