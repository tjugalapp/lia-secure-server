# 6 Logging and Auditing

## 6.1.1 Configure journald

| CIS-punkt | Beskrivning | Status | Åtgärd | Verifiering | Datum |
|---|---|---|---|---|---|
| 6.1.1.1.1 | systemd-journald-tjänsten ska vara aktiv | **Redan uppfyllt** | Ingen åtgärd. Tjänsten var aktiv från grundinstallation. Uniten är static och saknar [Install]-sektion, vilket gör att is-enabled korrekt svarar "static" — förväntat beteende | Kommando: `systemctl is-active systemd-journald.service → active`<br>Kommando: `systemctl is-enabled systemd-journald.service → static` | 2026-09-14 |
| 6.1.1.1.2 | systemd-journal-remote ska inte användas | **Redan uppfyllt** | Ingen åtgärd. Paketet är inte installerat på servern | Kommando: `dpkg-query -s systemd-journal-remote &>/dev/null && echo "INSTALLERAT" \|\| echo "EJ INSTALLERAT"`<br>Output: EJ INSTALLERAT | 2026-09-14 |
| 6.1.1.1.3 | journald ska vara konfigurerad att skicka loggar till rsyslog | **Redan uppfyllt** | Ingen åtgärd. ForwardToSyslog är satt till "yes" som standardvärde | Kommando: `systemd-analyze cat-config systemd/journald.conf \| grep -P -- '^\h*ForwardToSyslog\b'`<br>Output: ForwardToSyslog=yes | 2026-09-14 |
| 6.1.1.1.4 | Åtkomst till journald-loggfiler ska vara konfigurerad | **Redan uppfyllt** | Ingen åtgärd. Mapp och filer ägs av root:systemd-journal med rättigheter 2755/640 — matchar CIS godkända variant med säkerhetsgrupp | Kommando: `stat -c "%n %U:%G %a" /var/log/journal`<br>Output: /var/log/journal root:systemd-journal 2755<br>Kommando: `find /var/log/journal -type f -exec stat -c "%n %U:%G %a" {} \;`<br>Output: samtliga filer root:systemd-journal 640 (9 filer) | 2026-09-14 |
| 6.1.1.1.5 | journald log file rotation ska vara konfigurerad | **Åtgärdad** | Redigerade /etc/systemd/journald.conf, satte SystemMaxUse=500M och MaxRetentionSec=1month, startade om systemd-journald | Kommando: `systemd-analyze cat-config systemd/journald.conf \| grep -P -- '^\h*(SystemMaxUse\|MaxRetentionSec)\h*='`<br>Output: SystemMaxUse=500M / MaxRetentionSec=1month | 2026-09-14 |
| 6.1.1.1.6 | journald Storage ska vara konfigurerad | **Åtgärdad** | Redigerade /etc/systemd/journald.conf, satte Storage=persistent (var tidigare implicit "auto"), startade om systemd-journald | Kommando: `systemd-analyze cat-config systemd/journald.conf \| grep -P -- '^\h*Storage\h*='`<br>Output: Storage=persistent | 2026-09-14 |
| 6.1.1.1.7 | journald Compress ska vara konfigurerad | **Åtgärdad** | Redigerade /etc/systemd/journald.conf, satte Compress=yes (var tidigare implicit standard), startade om systemd-journald | Kommando: `systemd-analyze cat-config systemd/journald.conf \| grep -P -- '^\h*Compress\h*='`<br>Output: Compress=yes | 2026-09-14 |

## 6.1.2 Configure rsyslog

| CIS-punkt | Beskrivning | Status | Åtgärd | Verifiering | Datum |
|---|---|---|---|---|---|
| 6.1.2.1 | rsyslog ska vara installerat | **Redan uppfyllt** | Ingen åtgärd. rsyslog var redan installerat på servern | Kommando: `dpkg-query -s rsyslog &>/dev/null && echo "INSTALLERAT" \|\| echo "EJ INSTALLERAT"`<br>Output: INSTALLERAT | 2026-09-14 |
| 6.1.2.2 | rsyslog-tjänsten ska vara enabled och active | **Redan uppfyllt** | Ingen åtgärd. Tjänsten var redan enabled och active | Kommando: `systemctl is-enabled rsyslog.service → enabled`<br>Kommando: `systemctl is-active rsyslog.service → active` | 2026-09-14 |
| 6.1.2.3 | rsyslog log file creation mode ska vara konfigurerad | **Redan uppfyllt** | Ingen åtgärd. $FileCreateMode 0640 var redan satt i /etc/rsyslog.conf | Kommando: `grep -rP -- '^\h*\$?FileCreateMode\h+0?[0-9]+' /etc/rsyslog.conf /etc/rsyslog.d/*.conf`<br>Output: /etc/rsyslog.conf:$FileCreateMode 0640 | 2026-09-14 |
| 6.1.2.4 | rsyslog-loggning ska vara konfigurerad (Manual) | **Redan uppfyllt** | Ingen åtgärd. Standardkonfigurationen i /etc/rsyslog.d/50-default.conf täcker auth/authpriv, kern, mail samt en generell catch-all-regel. Bedömt tillräcklig av gruppen | Grep-sökning i rsyslog.conf/rsyslog.d<br>Output: 5 regler (auth.log, syslog, kern.log, mail.log, mail.err) | 2026-09-14 |
| 6.1.2.6 | rsyslog ska inte ta emot loggar från fjärrklienter | **Redan uppfyllt** | Ingen åtgärd. Den enda träffen (imudp) var utkommenterad och alltså inaktiv | Grep-sökning efter imtcp/imudp/imrelp-moduler<br>Output: /etc/rsyslog.conf:#module(load="imudp") (utkommenterad) | 2026-09-14 |
| 6.1.2.7 | logrotate ska vara konfigurerat (Manual) | **Redan uppfyllt** | Ingen åtgärd. logrotate installerat, standardkonfiguration roterar veckovis med 4 veckors historik och komprimering för syslog, mail.log, kern.log, auth.log, user.log och cron.log | Kommando: `cat /etc/logrotate.d/rsyslog`<br>Output: rotate 4, weekly, compress, delaycompress | 2026-09-14 |

