# argocd-demo

Demo de GitOps con **ArgoCD + k3s** corriendo en un servidor casero (laptop Ubuntu).

## Estructura

```
apps/hola-gitops/      # Manifiestos de la app (Kustomize)
  index.html           # Contenido de la página (edítalo para probar el sync)
  kustomization.yaml
  deployment.yaml      # nginx sin root, FS read-only, límites de recursos
  service.yaml
  cloudflared.yaml     # Túnel Cloudflare (sin abrir puertos) para ver la página desde cualquier lado
argocd/
  hola-gitops.yaml     # Definición de la Application en ArgoCD
```

## Registrar la app (una sola vez, en el servidor)

```bash
kubectl apply -f https://raw.githubusercontent.com/TU-USUARIO/argocd-demo/main/argocd/hola-gitops.yaml
```

## Ver la página

```bash
kubectl logs -n demo deploy/cloudflared | grep -o 'https://[a-z0-9-]*\.trycloudflare\.com'
```

> La URL cambia si el pod de cloudflared se reinicia. Para URL fija se necesita un *named tunnel* con dominio en Cloudflare.

## Pruebas

1. **Deploy por Git:** cambia `v1` → `v2` en `index.html`, commit + push. ArgoCD sincroniza (≤3 min o botón *Refresh*).
2. **Self-heal:** `kubectl delete deploy hola-gitops -n demo` → ArgoCD lo recrea.
3. **Rollback:** `git revert HEAD` + push → vuelve a `v1`.

---
VC.DevAI · DevSecOps · Azure · IA · Automatizaciones