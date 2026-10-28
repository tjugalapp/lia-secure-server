# Kubernetes-manifest (K3s på its1)

Filerna är de som körs på servern i `/opt/k3s`. De innehåller inga hemligheter.
Secrets (`its1-tls` och `its1ssl-tls`) skapas i klustret och ligger inte i repot.

| Fil | Vad den gör |
|---|---|
| `01-namespace.yaml` | Namespace `yatest` |
| `02-traefik-gateway-config.yaml` | Aktiverar Gateway API i K3s Traefik, med listeners `web`, `websecure` (its1) och `websecure-ssl` (its1ssl) |
| `40-site.yaml` | Sajten: Deployment (3 repliker av `yeasy/simple-web`) och Service på port 80 |
| `50-gateway.yaml` | HTTPRoute för `its1.example.com` (via Cloudflare) |
| `51-its1ssl.yaml` | HTTPRoute för `its1ssl.example.com` (direkt, utan Cloudflare) |
| `60-cert-manager.yaml` | Installerar cert-manager via K3s helm-controller, med Gateway API-stöd |
| `61-clusterissuer-staging.yaml` | Let's Encrypt staging, användes för första testet |
| `63-clusterissuer-prod.yaml` | Let's Encrypt produktion |
| `62-certificate.yaml` | Certifikat för `its1ssl.example.com` (utfärdas av produktionsissuern) |

## Ordning

```bash
sudo k3s kubectl apply -f 01-namespace.yaml
sudo k3s kubectl apply -f 40-site.yaml
sudo k3s kubectl apply -f 02-traefik-gateway-config.yaml
sudo k3s kubectl apply -f 50-gateway.yaml
sudo k3s kubectl apply -f 60-cert-manager.yaml        # vänta tills tre poddar körs i cert-manager
sudo k3s kubectl apply -f 63-clusterissuer-prod.yaml
sudo k3s kubectl apply -f 62-certificate.yaml
sudo k3s kubectl apply -f 51-its1ssl.yaml
```

Obs: `02-traefik-gateway-config.yaml` pekar på secreten `its1-tls` (självsignerat certifikat för its1,
skapat med openssl, se K3S-5 i dokumentationen) och på `its1ssl-tls` (skapas av cert-manager).
`its1-tls` måste finnas före listenern `websecure` fungerar.
För att testa mot Let's Encrypt staging först: använd `61-clusterissuer-staging.yaml` och byt `issuerRef` i `62-certificate.yaml`.
