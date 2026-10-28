# Härdning och K3s på en Ubuntu-server (LIA-projekt)

Dokumentation från mitt LIA-projekt hos Systementor AB. Tillsammans med
två klasskamrater satte jag upp och härdade en Ubuntu 26.04-server (VPS)
som ska kunna köra företagets webbtjänster.

> Det här är en rensad kopia av projektets dokumentation. Domäner,
> IP-adresser och användarnamn är utbytta, till exempel mot
> `its1.example.com` och `<publik-ip>`.

## Vad vi gjorde

- **Härdade servern enligt CIS Ubuntu Linux 26.04 LTS Benchmark,
  Level 1 - Server.** Varje punkt har status, åtgärd, kommando och
  resultat, 256 punkter totalt.
- **Körde Lynis som en oberoende kontroll** (hardening index 72).
- **Stängde SSH mot internet.** Administration sker bara via NetBird
  (WireGuard).
- **Byggde ett nftables-filter** som släpper in webbtrafik från
  Cloudflares IP-adresser. Listan uppdateras automatiskt varje natt.
- **Satte upp K3s** med Traefik och Gateway API, med en sajt som kör i
  3 repliker.
- **Lade till en andra sajt** med ett Let's Encrypt-certifikat som
  cert-manager utfärdar och förnyar automatiskt.

## Avvägningar

Några CIS-rekommendationer följde vi medvetet inte, eftersom de krockar
med Kubernetes eller med hur servern drivs. Varje avvikelse står
motiverad i respektive punkt.

- **IP forwarding är på** (CIS 3.3.1.1). K3s behöver det för
  poddnätverket.
- **`rp_filter` är 2 i stället för 1** (CIS 3.3.1.12), för att inte
  riskera routningsproblem med Kubernetes nätverk.
- **Inget bootloader-lösenord** (CIS 1.4.1). Vi har ingen konsolåtkomst
  till servern, och en tidigare server blev oåtkomlig efter en omstart,
  troligen på grund av den inställningen.
- **Port 80 och 443 är öppna även för andra än Cloudflare**, så att den
  andra sajten kan nås direkt och Let's Encrypt kan förnya certifikatet
  (LE-4 i [04-lets-encrypt-its1ssl.md](docs/installationer/04-lets-encrypt-its1ssl.md)).

## Saker som krånglade

- **UFW stoppade inte trafiken till K3s.** Trafik som K3s skickar vidare
  med DNAT går genom `FORWARD`, inte `INPUT`, så UFW-regler hade ingen
  effekt. Därför byggdes filtret i nftables i stället (CF-1 i
  [02-cloudflare.md](docs/installationer/02-cloudflare.md)).
- **Sajten gav plötsligt fel 522 via Cloudflare.** Räknare i filtret
  visade att Cloudflare anslöt på port 80 i stället för 443. TLS-läget i
  Cloudflare hade ändrats från Full till Flexible (CF-6 i samma fil).
- **CIS egna kontrollkommandon gav ibland fel svar.** Ett trasigt
  escape-tecken gav falskt godkänt för `/etc/issue` (1.6.2), och en regex
  som träffade fel process gav falskt underkänt för chrony (2.3.3.2).

## Innehåll

| Mapp | Innehåll |
|---|---|
| [`docs/hardening`](docs/hardening) | CIS-härdningen, ett kapitel per fil |
| [`docs/installationer`](docs/installationer) | NetBird, Cloudflare, K3s och Let's Encrypt, steg för steg med verifiering |
| [`k8s`](k8s) | Kubernetes-manifesten, i den ordning de körs |
| [`scripts`](scripts) | Skriptet som uppdaterar brandväggsfiltret med Cloudflares IP-adresser |

## Hemligheter

Inga nycklar, tokens, kubeconfig eller certifikat ligger i repot (se
`.gitignore`).
