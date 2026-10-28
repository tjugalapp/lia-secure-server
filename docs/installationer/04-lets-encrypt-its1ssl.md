# 4 Let’s Encrypt (its1ssl)

its1ssl.example.com är en andra sajt som inte går via Cloudflare utan nås direkt på servern, med ett certifikat från Let’s Encrypt som cert-manager i klustret utfärdar och förnyar. its1.example.com är oförändrad och går fortfarande via Cloudflare.  

## LE-1 — cert-manager installerat i klustret

**Status:** Verifierad · Datum: 2026-10-12  

**Åtgärd:** cert-manager v1.21.2 installerades med K3s inbyggda helm-controller (en HelmChart-resurs i kube-system, mål-namespace cert-manager) med stöd för Gateway API aktiverat. Gateway API-CRD:erna kommer redan med K3s och installerades därför inte separat. Manifesten ligger i /opt/k3s på servern.  

**Verifiering:** `kubectl get pods -n cert-manager` → tre poddar (cert-manager, cainjector, webhook) med status Running.  

## LE-2 — ClusterIssuer och certifikat för its1ssl

**Status:** Verifierad · Datum: 2026-10-12  

**Åtgärd:** En ClusterIssuer mot Let’s Encrypt (ACME, HTTP-01 via en tillfällig HTTPRoute på Gateway-listenern web) och en Certificate för its1ssl.example.com i kube-system skapades. Certifikatet utfärdades först mot Let’s Encrypt staging för att undvika rate limits, och därefter mot produktion genom att byta issuer, ta bort secreten its1ssl-tls och utfärda på nytt.  

**Verifiering:** Certificate its1ssl-tls → READY True. Staging-certifikatet hade issuer ”(STAGING) Dastardly Durum YR1”. Produktionscertifikatet har subject CN=its1ssl.example.com, issuer Let’s Encrypt (CN=YR2), giltigt 2026-10-12 till 2027-01-10.  

## LE-3 — Egen listener och route för its1ssl

**Status:** Verifierad · Datum: 2026-10-12  

**Åtgärd:** En ny HTTPS-listener, websecure-ssl, med hostname its1ssl.example.com och secreten its1ssl-tls lades till i Traefiks Gateway via HelmChartConfig (02-traefik-gateway-config.yaml). Den befintliga listenern websecure med its1-tls för its1 lämnades oförändrad. En HTTPRoute, its1ssl, i namespace yatest kopplades till web och websecure-ssl och pekar på samma tjänst som its1. A-posten för its1ssl skapades av den som ansvarar för DNS hos företaget och går inte genom Cloudflare.  

**Verifiering:** `curl -i https://its1ssl.example.com/ från administratörens dator, utan -k` → HTTP/1.1 200 OK, utan certifikatfel. Servern svarar med certifikatet för its1ssl från Let’s Encrypt direkt. curl -i https://its1.example.com/ → HTTP/1.1 200 OK (via Cloudflare, oförändrad).  

## LE-4 — Filtret för port 80 och 443 ändrat så att direkt trafik släpps igenom

**Status:** Avvikelse · Datum: 2026-10-12  

**Åtgärd:** För att its1ssl ska kunna nås direkt av webbläsare, och för att Let’s Encrypt ska kunna validera och förnya certifikatet via HTTP-01 på port 80, ändrades skriptet /usr/local/sbin/cloudflare-443-update. Raden som blockerade (drop) all trafik till port 80 och 443 som inte kom från Cloudflare ersattes av en rad som bara räknar den (counter). Cloudflares IP-listor hämtas och laddas fortfarande varje natt. Avvikelsen är att its1.example.com inte längre är låst till Cloudflare på IP-nivå, eftersom servern kan nås direkt på port 80 och 443. Ändringen ersätter de tillfälliga reglerna för port 80 i CF-6, och kräver ingen manuell regel som försvinner vid nästa körning. En säkerhetskopia av skriptet ligger i samma mapp (.bak).  

**Verifiering:** Skriptet kördes: ”Cloudflare-filter uppdaterat: 15 v4, 7 v6”. nft list chain inet cf_filter pre → sista regeln är ”tcp dport { 80, 443 } counter … comment ”icke-Cloudflare, tillaten (its1ssl)””, utan drop. curl -i https://its1ssl.example.com/ och https://its1.example.com/ efter ändringen → HTTP/1.1 200 OK för båda.  
