# 7 System Maintenance

## 7.1 Configure system file and directory access

| CIS-punkt | Beskrivning | Status | Åtgärd | Verifiering | Datum |
|---|---|---|---|---|---|
| 7.1.1 | Åtkomst till /etc/passwd konfigurerad | **Redan uppfyllt** | Ingen åtgärd | Kommando: `stat -Lc 'Access: (%#a/%A) Uid: ( %u/ %U) Gid: ( %g/ %G)' /etc/passwd`<br>Output: Access: (0644/-rw-r--r--) Uid: ( 0/ root) Gid: ( 0/ root) | 2026-09-16 |
| 7.1.2 | Åtkomst till /etc/passwd-konfigurerad | **Redan uppfyllt** | Ingen åtgärd | Kommando: `stat -Lc 'Access: (%#a/%A) Uid: ( %u/ %U) Gid: ( %g/ %G)' /etc/passwd-`<br>Output: Access: (0644/-rw-r--r--) Uid: ( 0/ root) Gid: ( 0/ root) | 2026-09-16 |
| 7.1.3 | Åtkomst till /etc/group konfigurerad | **Redan uppfyllt** | Ingen åtgärd | Kommando: `stat -Lc 'Access: (%#a/%A) Uid: ( %u/ %U) Gid: ( %g/ %G)' /etc/group`<br>Output: Access: (0644/-rw-r--r--) Uid: ( 0/ root) Gid: ( 0/ root) | 2026-09-16 |
| 7.1.4 | Åtkomst till /etc/group-konfigurerad | **Redan uppfyllt** | Ingen åtgärd | Kommando: `stat -Lc 'Access: (%#a/%A) Uid: ( %u/ %U) Gid: ( %g/ %G)' /etc/group-`<br>Output: Access: (0644/-rw-r--r--) Uid: ( 0/ root) Gid: ( 0/ root) | 2026-09-16 |
| 7.1.5 | Åtkomst till /etc/shadow konfigurerad | **Redan uppfyllt** | Ingen åtgärd | Kommando: `stat -Lc 'Access: (%#a/%A) Uid: ( %u/ %U) Gid: ( %g/ %G)' /etc/shadow`<br>Output: Access: (0640/-rw-r-----) Uid: ( 0/ root) Gid: ( 42/ shadow) | 2026-09-16 |
| 7.1.6 | Åtkomst till /etc/shadow-konfigurerad | **Redan uppfyllt** | Ingen åtgärd | Kommando: `stat -Lc 'Access: (%#a/%A) Uid: ( %u/ %U) Gid: ( %g/ %G)' /etc/shadow-`<br>Output: Access: (0640/-rw-r-----) Uid: ( 0/ root) Gid: ( 42/ shadow) | 2026-09-16 |
| 7.1.7 | Åtkomst till /etc/gshadow konfigurerad | **Redan uppfyllt** | Ingen åtgärd | Kommando: `stat -Lc 'Access: (%#a/%A) Uid: ( %u/ %U) Gid: ( %g/ %G)' /etc/gshadow`<br>Output: Access: (0640/-rw-r-----) Uid: ( 0/ root) Gid: ( 42/ shadow) | 2026-09-16 |
| 7.1.8 | Åtkomst till /etc/gshadow-konfigurerad | **Redan uppfyllt** | Ingen åtgärd | Kommando: `stat -Lc 'Access: (%#a/%A) Uid: ( %u/ %U) Gid: ( %g/ %G)' /etc/gshadow-`<br>Output: Access: (0640/-rw-r-----) Uid: ( 0/ root) Gid: ( 42/ shadow) | 2026-09-16 |
| 7.1.9 | Åtkomst till /etc/shells konfigurerad | **Redan uppfyllt** | Ingen åtgärd | Kommando: `stat -Lc 'Access: (%#a/%A) Uid: ( %u/ %U) Gid: ( %g/ %G)' /etc/shells`<br>Output: Access: (0644/-rw-r--r--) Uid: ( 0/ root) Gid: ( 0/ root) | 2026-09-16 |
| 7.1.10 | Åtkomst till /etc/security/opasswd konfigurerad | **Redan uppfyllt** | Ingen åtgärd | Kommando: `stat -Lc '%n Access: (%#a/%A) Uid: ( %u/ %U) Gid: ( %g/ %G)' /etc/security/opasswd`<br>Output: Access: (0600/-rw-------) Uid: ( 0/ root) Gid: ( 0/ root). Samma för opasswd.old | 2026-09-16 |
| 7.1.11 | World writable filer och kataloger säkrade | **Redan uppfyllt** | Ingen åtgärd | CIS audit-script → "No world writable files exist" + "Sticky bit is set on world writable directories" | 2026-09-16 |
| 7.1.12 | Inga filer eller kataloger utan ägare och grupp | **Redan uppfyllt** | Ingen åtgärd | CIS audit-script → PASS: inga unowned/ungrouped filer eller kataloger | 2026-09-16 |
| 7.1.13 | SUID/SGID-filer granskade | **Granskad, godkänd** | Ingen åtgärd. 15 SUID + 7 SGID-filer identifierade och granskade. Två filer (sudo-rs-paketets su/sudo i /usr/lib/cargo/bin/) och en (/usr/bin/sudo.ws) initialt flaggade som misstänkta pga ovanliga sökvägar/namn, verifierade som legitima via dpkg -S (tillhör paketen sudo-rs respektive sudo) | CIS audit-script → 15 SUID + 7 SGID filer listade dpkg -S bekräftade samtliga flaggade filer tillhör legitima installerade paket | 2026-09-16 |

## 7.2 Local Users and Group Settings

| CIS-punkt | Beskrivning | Status | Åtgärd | Verifiering | Datum |
|---|---|---|---|---|---|
| 7.2.1 | Konton i /etc/passwd använder shadow-lösenord | **Redan uppfyllt** | Ingen åtgärd | Kommando: `awk -F: '($2 != "x" ) { print "User: \"" $1 "\" is not set to shadowed passwords"}' /etc/passwd`<br>Output: tom | 2026-09-16 |
| 7.2.2 | /etc/shadow lösenordsfält ej tomma | **Redan uppfyllt** | Ingen åtgärd | Kommando: `sudo awk -F: '($2 == "" ) { print $1 " does not have a password"}' /etc/shadow`<br>Output: tom | 2026-09-16 |
| 7.2.3 | Alla grupper i /etc/passwd finns i /etc/group | **Redan uppfyllt** | Ingen åtgärd | CIS audit-script → tom | 2026-09-16 |
| 7.2.4 | shadow-gruppen är tom | **Redan uppfyllt** | Ingen åtgärd | Kommando: `awk -F: '($1=="shadow") {print $NF}' /etc/group`<br>Output: tom | 2026-09-16 |
| 7.2.5 | Inga dubbletta UID:er | **Redan uppfyllt** | Ingen åtgärd | CIS audit-script → tom | 2026-09-16 |
| 7.2.6 | Inga dubbletta GID:er | **Redan uppfyllt** | Ingen åtgärd | CIS audit-script → tom | 2026-09-16 |
| 7.2.7 | Inga dubbletta användarnamn | **Redan uppfyllt** | Ingen åtgärd | CIS audit-script → tom | 2026-09-16 |
| 7.2.8 | Inga dubbletta gruppnamn | **Redan uppfyllt** | Ingen åtgärd | CIS audit-script → tom | 2026-09-16 |
| 7.2.9 | Lokala interaktiva användares hemkataloger konfigurerade | **Redan uppfyllt** | Ingen åtgärd | CIS audit-script → PASS: alla hemkataloger existerar med mode 750 eller striktare, korrekt ägarskap | 2026-09-16 |
| 7.2.10 | Lokala interaktiva användares dot-filer konfigurerade | **Redan uppfyllt** | Ingen åtgärd | CIS audit-script (körd med sudo, sparad som fil) → PASS: samtliga användares hemkataloger kontrollerade | 2026-09-16 |
| 7.2.11 | Lokala interaktiva användares dot-kataloger konfigurerade | **Redan uppfyllt** | Ingen åtgärd | CIS audit-script (med sudo, sparad som fil) → PASS | 2026-09-16 |

