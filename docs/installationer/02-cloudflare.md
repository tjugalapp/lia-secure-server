# 2 Cloudflare

Trafiken till its1.example.com (<publik-ip>) går via Cloudflare. På servern ska därför endast webbtrafik på port 443 tillåtas från Cloudflares IP-adresser, och listan ska uppdateras automatiskt varje natt. För att its1ssl ska kunna nås direkt släpps trafik på port 80 och 443 senare igenom även från andra källor än Cloudflare, se CF-4 och LE-4.  

## CF-1 — Cloudflare-filter för port 443 (nftables)

**Status:** Åtgärdad · Datum: 2026-09-29  

**Åtgärd:** Skapade /usr/local/sbin/cloudflare-443-update (root:root, 750). Scriptet hämtar Cloudflares IP-listor (ips-v4 och ips-v6), kontrollerar att de är giltiga CIDR-block (minst 10 v4 och 5 v6, annars används senast sparade i /var/lib/cloudflare-ips) och laddar nftables-tabellen inet cf_filter i hooken prerouting (priority raw). Tabellen släpper igenom TCP 443 från Cloudflares intervall och droppar övrig trafik mot 443. lo och wt0 är undantagna. Tabellen byts ut i en och samma transaktion vid varje körning. Valdes framför UFW-regler eftersom trafik som DNAT:as av K3s går via FORWARD och därför inte träffas av UFW:s INPUT-regler. Port 22 påverkas inte. Reglerna har ändrats senare, se CF-4 och LE-4.  

**Verifiering:** Scriptet → Cloudflare-filter uppdaterat: 15 v4, 7 v6. nft list chain inet cf_filter pre → fyra regler (lo/wt0 return, cf4 return, cf6 return, drop). Test-NetConnection <publik-ip> -Port 443 från Windows-dator → TcpTestSucceeded: False, räknaren på drop-regeln 5 paket.  

## CF-2 — Cloudflare-filtret laddas vid uppstart

**Status:** Åtgärdad · Datum: 2026-09-29  

**Åtgärd:** Skapade systemd-tjänsten cloudflare-443.service (oneshot, efter network-online.target) som kör cloudflare-443-update vid boot, eftersom nftables-tabellen inte finns kvar efter omstart. Tjänsten aktiverades med systemctl enable.  

**Verifiering:** `systemctl is-enabled cloudflare-443.service` → enabled.  

## CF-3 — Nattligt cron-jobb för Cloudflare-filtret

**Status:** Åtgärdad · Datum: 2026-09-29  

**Åtgärd:** Skapade /etc/cron.d/cloudflare-443 som kör cloudflare-443-update som root kl. 03:17 varje natt. Utskriften skickas till journalen med taggen cloudflare-443 (logger -t). Rättigheterna sattes till 644, eftersom umask 027 annars ger 640.  

**Verifiering:** `sudo ls -l /etc/cron.d/cloudflare-443` → -rw-r--r-- 1 root root. journalctl -t cloudflare-443 → körningar kl. 03:17 den 30 september och den 1 oktober, båda med Cloudflare-filter uppdaterat: 15 v4, 7 v6. journalctl -u cron visar motsvarande CRON-rader för root.  

## CF-4 — Filtret begränsat till publika kortet och port 80 stängd

**Status:** Åtgärdad · Datum: 2026-10-01  

**Åtgärd:** Ändrade /usr/local/sbin/cloudflare-443-update inför K3s. Reglerna gäller nu bara trafik som kommer in på det publika kortet eth0 (iifname != "eth0" return), i stället för att undanta lo och wt0. Utan ändringen hade filtret även droppat utgående trafik på 80 och 443 från K3s-poddar, eftersom den passerar prerouting med en källadress som inte tillhör Cloudflare. Drop-regeln utökades från port 443 till port 80 och 443. K3s ServiceLB exponerar annars port 80 via DNAT förbi UFW, och uppgiften tillåter endast port 443 från Cloudflares adresser. Port 80 kan därför inte nås utifrån, men väl via wt0 (NetBird). Den här blockeringen togs senare bort för att its1ssl ska nås direkt, se LE-4.  

**Verifiering:** Scriptet → Cloudflare-filter uppdaterat: 15 v4, 7 v6. nft list chain inet cf_filter pre → fyra regler: iifname != "eth0" return, cf4 return, cf6 return och drop för tcp dport { 80, 443 }.  

## CF-5 — Sajten nås via Cloudflare

**Status:** Verifierad · Datum: 2026-10-01  

**Åtgärd:** Ingen ändring på servern. DNS-posten för its1.example.com går via Cloudflare (proxy på), och trafiken når servern på port 443 från Cloudflares adresser. De tillfälliga testreglerna för administratörens dator och raden i hosts-filen togs bort före testet.  

**Verifiering:** `nslookup its1.example.com` → Cloudflares adresser (188.114.96.1, 188.114.97.1, 2a06:98c1:3120::1, 2a06:98c1:3121::1), inte serverns <publik-ip>. 188.114.96.0/20 och 2a06:98c0::/29 finns i filtrets listor (/var/lib/cloudflare-ips). curl -i https://its1.example.com/ från administratörens dator, utan hosts-fil och utan -k → HTTP/1.1 200 OK, Server: cloudflare, sidan visas från webbservern 10.42.0.16.  

## CF-6 — 522 via Cloudflare och tillfällig regel för port 80

**Status:** Avvikelse · Datum: 2026-10-08  

**Åtgärd:** https://its1.example.com gav felet 522 (Connection timed out) från Cloudflare, trots att sajten fungerat tidigare. Servern var frisk: uptime 8 dagar, alla poddar Running eller Completed, och filtret var laddat och uppdaterat varje natt. Tillfälliga räknare i filtret visade att Cloudflare anslöt på port 80 över IPv4 (37 paket) och inte på 443 (0 paket). Orsaken var att SSL/TLS-läget i Cloudflare hade ändrats från Full till Flexible, vilket gör att Cloudflare ansluter till servern över HTTP på port 80, som filtret stänger. För att sajten skulle fungera igen lades två tillfälliga regler in i tabellen cf_filter som släpper igenom Cloudflares adresser på port 80 (IPv4 och IPv6). Det avviker från kravet att bara port 443 ska släppas in från Cloudflare, och reglerna försvinner när filterscriptet körs nästa gång (cron kl. 03:17) eller när det körs manuellt.  

**Verifiering:** `nft list chain inet cf_filter pre med räknare per portfamilj` → cf80v4: 37 paket, cf80v6: 0, cf443v4: 0, cf443v6: 0. Efter att reglerna lagts in svarade https://its1.example.com med sajten igen. SSL/TLS-läget i Cloudflare bekräftades vara Flexible, tidigare Full. Kvar att göra: läget ska sättas tillbaka till Full för its1. De tillfälliga reglerna för port 80 behövs inte längre, eftersom filtret har ändrats permanent (se LE-4).  
