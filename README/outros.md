# Outros

# BUG FIX

Contour Ingress com status de Processing na verificação de integridade do ArgoCD.

- Artigo: https://rodrigolira.eti.br/contour-ingress-com-status-de-processing-na-verificacao-de-integridade-do-argocd/

- Oficial Issues: https://github.com/argoproj/argo-cd/issues/1704

Resumo para resolver o bug.

Editar o configmap do argocd-cm
```
kubectl -n argocd edit configmap argocd-cm
```

Adicione as linhas abaixo:
```
data:
  resource.customizations: |
    networking.k8s.io/Ingress:
      health.lua: |
        hs = {}
        hs.status = "Healthy"
        return hs
```
