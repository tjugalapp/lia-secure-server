# 1 NetBird

NetBird ger servern ett privat WireGuard-nätverk för administration. Ingen instruktion om vilket nätverk servern skulle anslutas till fanns, så ett eget NetBird Cloud-konto används tills vidare. Port 22 hölls öppen publikt tills en omstart hade visat att NetBird startar av sig själv, eftersom servern saknar konsolåtkomst. Den publika porten är nu stängd (se NB-9).  

## NB-1 — NetBird-klient installerad från signerat apt-repo

**Status:** Åtgärdad · Datum: 2026-09-28 · Påverkar CIS: 1.2.1.1, 1.2.1.3, 1.2.1.9, 1.2.1.11, 5.4.3.3  

**Åtgärd:** Lade till NetBirds repo som /etc/apt/sources.list.d/netbird.sources (deb822, https, Signed-By) och installerade paketet netbird. En första .list-fil flaggades av CIS-kontrollen 1.2.1.1, så källan skrevs om som .sources. Rättigheterna sattes till 644, eftersom umask 027 annars ger 640 och apt inte kan läsa nyckeln.  

**Verifiering:** `stat -Lc '%n %a %U:%G' /usr/share/keyrings/netbird-archive-keyring.gpg /etc/apt/sources.list.d/netbird.sources` → båda 644 root:root. apt-get update → Hit på pkgs.netbird.io utan fel. CIS 1.2.1.1 och 1.2.1.11 → tomt.  

## NB-2 — Servern ansluten till eget NetBird Cloud-nätverk

**Status:** Åtgärdad · Datum: 2026-09-28  

**Åtgärd:** Ansluten med engångs-setup key via netbird up (nyckeln dokumenteras inte). Servern fick NetBird-adressen <netbird-ip> på gränssnittet wt0.  

**Verifiering:** `netbird status` → Management: Connected, Signal: Connected, Relays: 4/4 Available. ip -br addr show wt0 → <netbird-ip>/16.  

## NB-3 — NetBird startar vid uppstart

**Status:** Redan uppfyllt · Datum: 2026-09-28  

**Åtgärd:** Ingen åtgärd, paketet aktiverar tjänsten vid installation.  

**Verifiering:** `systemctl is-enabled netbird; systemctl is-active netbird` → enabled / active.  

## NB-4 — NetBirds inbyggda SSH-server avstängd

**Status:** Redan uppfyllt · Datum: 2026-09-28 · Påverkar CIS: 5.1.1–5.1.24  

**Åtgärd:** Ingen åtgärd, avstängd som standard. Administration sker med OpenSSH, så CIS-inställningarna i kapitel 5 gäller fortsatt.  

**Verifiering:** `netbird status` → SSH Server: Disabled.  

## NB-5 — SSH via NetBird-adressen fungerar

**Status:** Verifierad · Datum: 2026-09-28 · Påverkar CIS: 5.1.x, 1.6.5  

**Åtgärd:** Ingen ändring. Testat från Windows-dator med NetBird ansluten.  

**Verifiering:** `ssh <användare>@<netbird-ip>` → inloggning lyckades, varningsbannern (1.6.5) visades.  

## NB-6 — SSH tillåten på wt0 i UFW

**Status:** Åtgärdad · Datum: 2026-09-29  

**Åtgärd:** Lade till UFW-regeln som tillåter inkommande TCP 22 på gränssnittet wt0 (IPv4 och IPv6) med kommentaren SSH via NetBird: ufw allow in on wt0 to any port 22 proto tcp comment 'SSH via NetBird'. Den publika port 22-regeln ligger kvar tills en omstart har verifierat åtkomsten.  

**Verifiering:** `ufw status numbered` → 22/tcp on wt0 ALLOW IN finns för IPv4 (regel 2) och IPv6 (regel 4), båda med kommentaren SSH via NetBird. Publika 22/tcp (regel 1 och 3) finns kvar. netbird status → NetBird IP: <netbird-ip>/16, Peers count: 1/1 Connected.  

## NB-7 — Omstart: NetBird startar av sig själv

**Status:** Verifierad · Datum: 2026-09-29 · Påverkar CIS: 5.4.3.3  

**Åtgärd:** Servern startades om med publika port 22 fortfarande öppen. Utöver NetBirds autostart (NB-3) kontrollerades att brandväggen, log_martians och umask 027 består efter omstart.  

**Verifiering:** `ufw status` → Status: active. systemctl is-active netbird → active. sysctl -n net.ipv4.conf.all.log_martians → 1. bash -lc umask → 0027.  

## NB-8 — SSH nåbar via både publik adress och NetBird efter omstart

**Status:** Verifierad · Datum: 2026-09-29 · Påverkar CIS: 5.1.x  

**Åtgärd:** Ingen ändring. Testat efter omstarten mot både <publik-ip> och <netbird-ip>.  

**Verifiering:** `ssh mot båda adresserna` → inloggning lyckades.  

## NB-9 — Publik SSH (port 22) stängd i UFW

**Status:** Åtgärdad · Datum: 2026-09-29  

**Åtgärd:** Tog bort de publika UFW-reglerna för 22/tcp (IPv4 och IPv6) efter att åtkomst via både publik adress och NetBird verifierats efter omstart (NB-8). Kvar är endast 22/tcp på wt0 (NB-6). Administration sker nu enbart via NetBird-adressen <netbird-ip>.  

**Verifiering:** `ufw status numbered` → endast 22/tcp on wt0 ALLOW IN kvar, regel 1 (IPv4) och regel 2 (IPv6), båda med kommentaren SSH via NetBird. ssh <användare>@<netbird-ip> → inloggning lyckades, varningsbannern visades. ssh <användare>@<publik-ip> → Connection timed out.  
