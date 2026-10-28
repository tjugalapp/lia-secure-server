# Skript

## cloudflare-443-update

Bash-skript som hanterar brandväggsfiltret för webbtrafik (nftables, tabellen `inet cf_filter`).

1. Hämtar Cloudflares IP-listor (`ips-v4`, `ips-v6`) och kontrollerar att de är giltiga CIDR-block
   (minst 10 v4 och 5 v6). Annars används senast sparade lista i `/var/lib/cloudflare-ips`.
2. Skriver om nftables-tabellen och laddar den i en enda transaktion (`nft -c` testar först).
3. Gäller bara trafik som kommer in på det publika kortet (`eth0`).
4. Cloudflares adresser släpps igenom på 443. Övrig trafik på 80 och 443 **räknas men blockeras inte**,
   eftersom `its1ssl.example.com` ska kunna nås direkt och Let's Encrypt måste nå port 80 (se LE-4).

Installation på servern: `/usr/local/sbin/cloudflare-443-update` (root:root, 750).
Körs varje natt kl. 03:17 via `/etc/cron.d/cloudflare-443` och vid uppstart via `cloudflare-443.service`.

Kontrollera filtret: `sudo nft list chain inet cf_filter pre`

Varför nftables och inte UFW: trafik som K3s skickar vidare (DNAT/FORWARD) träffas inte av UFW:s INPUT-regler,
så ett UFW-baserat skript hade inte påverkat sajten (se CF-1 och CF-4).
