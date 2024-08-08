# ArgoCD

Instalando ArgoCD para utilização do recurso de entrega contínua.

- [Site Oficial](https://argoproj.github.io/cd/)
- [Documentação](https://argo-cd.readthedocs.io/en/stable/getting_started/)


### Instalação do ArgoCD no Kubernetes.

Criando Namespace
```
kubectl create namespace argocd
```

Instalando via Kubernetes
```
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

Verificando se os PODs do ArgoCD estão com Status Running
```
kubectl get pods -n argocd
```

### Instalando ArgoCD CLI

Instalando o binário do ArgoCD localmente na estação de trabalho, para poder realizar as configurações entre ArgoCD/Kubernetes.

```
curl -sSL -o argocd-linux-amd64 https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64

sudo install -m 555 argocd-linux-amd64 /usr/local/bin/argocd

rm argocd-linux-amd64
```

### Configurando o Kubernetes e ArgoCD

Expondo a porta localmente por Port-forward, para acesso ao serviço do ArgoCD no Kubernetes
```
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Autenticando no ArgoCD no Kubernetes via CLI
```
argocd login localhost:8080
```

User padrão ```admin``` e para recuperar a senha, basta consultar o secret do ArgoCD no Kubernetes
```
kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 -d
```

Recuperando o contexto do cluster Kubernetes
```
kubectl config current-context
```

Adicionando o cluster Kubernetes no ArgoCD
```
argocd cluster add <NOME_DO_CONTEXTO>
```

Para listar o cluster Kubernetes no ArgoCD
```
argocd cluster list
```

<br><br>

# Publicando as portas para acesso:

Traefik:
```
kubectl apply -f services/traefik-dashboard.yml
```
Recuperando a porta mapeada no host, exemplo:
```
kubectl get services --namespace traefik traefik-dashboard
```

Prometheus
```
kubectl apply -f services/prometheus.yml
```

ArgoCD
```
kubectl apply -f services/argocd-ui.yml
```

Adicionando chave de acesso aos repositórios do Git para o ArgoCD
```
kubectl apply -f secrets/argocd-private-ssh.yml
```
