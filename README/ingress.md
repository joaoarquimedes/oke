# Instalação do Ingress

Instalação e configuração da infraestrutura Ingress com Traefik.

Documentações:

- Instalação: https://github.com/traefik/traefik-helm-chart
- Exemplos configurações: https://github.com/traefik/traefik-helm-chart/blob/master/EXAMPLES.md
- Arquivo oficial: https://github.com/traefik/traefik-helm-chart/blob/master/traefik/values.yaml
- Requisito: [https://helm.sh/](https://helm.sh/)

<br>

Primeiro definindo um namespace para agrupar todos os recursos referentes ao Ingress Traefik.

```
kubectl apply -f ingress/namespace.yml
```
<br>

Via **helm**, adicionar o repositório do traefik
```
helm repo add traefik https://helm.traefik.io/traefik
```

Verificar/Atualizar o repositório
```
helm repo update
```
<br>


Verificar versões
```
# Versão corrente
helm search repo traefik

# Versões anteriores
helm search repo traefik --versions
```
<br>

No arquivo (`ingress/values.yaml`) oficial de instalação do [**Traefik**](https://github.com/traefik/traefik-helm-chart/blob/master/traefik/values.yaml) via **helm**, foram adicionados anotações para configurações do **LoadBalancer** no OCI, especificando por exemplo, o tipo do loadbalancer e limites do Shape.
```
service:
...
  annotations: 
    oci.oraclecloud.com/load-balancer-type: "lb" 
    service.beta.kubernetes.io/oci-load-balancer-shape: "flexible" 
    service.beta.kubernetes.io/oci-load-balancer-shape-flex-min: "10" 
    service.beta.kubernetes.io/oci-load-balancer-shape-flex-max: "100" 
...
```
<br>

No arquivo `ingress/values.yaml`, foram adicionados os argumentos extras:
```
additionalArguments:
  - "--serverstransport.insecureskipverify=true"
```
<br>

No arquivo `ingress/values.yaml`, foram especificados valores para gravações dos logs:
```
...
logs:
  general:
    level: INFO
  access:
    enabled: true
    filePath: "/data/storage/log/traefik/access.log"
    bufferingSize: 100
```
<br>

Outras alterações no `ingress/values.yaml`:
```
metrics:
  prometheus:
    entryPoint: metrics
    buckets: "0.1,0.3,1.2,5.0"

...

deployment:
...
  initContainers:
  - name: volume-permissions
    image: busybox:latest
    command: ["sh", "-c", "mkdir /data/storage; chown -R 65532:65532 /data/storage"]
    securityContext:
      runAsNonRoot: false
      runAsGroup: 0
      runAsUser: 0
    volumeMounts:
      - name: data
        mountPath: /data

...

persistence:
  enabled: true
  name: data
  accessMode: ReadWriteMany
  size: 10Gi
  storageClass: oci-fss
  path: /data

...

certResolvers:
  letsencrypt:
    email: suporte@client.com.br
    storage: /data/storage/acme.json
```

<br>

Comando para instalação do traefik
```
helm install --namespace=traefik -f ingress/values.yaml traefik traefik/traefik
```

Comando para atualização do traefik. Aplicar alterações
```
helm upgrade --namespace=traefik -f ingress/values.yaml traefik traefik/traefik
```