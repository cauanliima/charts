# charts

## Criando cluster com k3s

### Instalação ferramentas
```
apt update -y
apt install curl wget -y
```

### Instalação k3s
```
curl -sfL https://get.k3s.io | sh -
```

### Instação kubectl
```
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
mv kubectl /usr/local/bin/
```

### Instalação Helm
```
wget https://get.helm.sh/helm-v3.14.2-linux-amd64.tar.gz
tar -zxvf helm-v3.14.2-linux-amd64.tar.gz
rm helm-v3.14.2-linux-amd64.tar.gz
mv  linux-amd64/helm /usr/local/bin/
rm -r linux-amd64
```

### Instalação CLI-argocd
```
wget https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
chmod +x argocd-linux-amd64
mv argocd-linux-amd64 /usr/local/bin/argocd
```

### Acessando cluster
```
mkdir ~/.kube
cp -a /etc/rancher/k3s/k3s.yaml ~/.kube/config
kubectl get nodes
```

## Tipos de charts

### Todo versionado                              - uptimekuma      - instalação com helm
```
helm repo add helm-l3st86 https://charts.l3st-tech.com/
helm pull --untar helm-l3st86/uptimekuma
cd uptimekuma
helm upgrade --install --create-namespace --namespace monitoramento uptimekuma .
```

### Versionando apenas o values                  - redis           - instalação via helm
```
helm repo add bitnami https://charts.bitnami.com/bitnami
cd redis
helm show values bitnami/redis > values.yaml
helm upgrade --install redis --create-namespace --namespace redis bitnami/redis --version 20.3.0 --values values.yaml
```

### Subchart                                     - argocd          - instalação com helm
```
cd argocd
helm dependency build
helm upgrade --install argocd --create-namespace --namespace argo .
```

### Subchart                                     - chartmuseum     - instalação via ide argo


### Subchart                                     - coroot          - instalação via CLI-argocd
```
ARGOCD_URL=""
ARGOCD_PASSWORD="password"
REPO=""

argocd login $ARGOCD_URL --username  admin --password $ARGOCD_PASSWORD --grpc-web
argocd app create coroot --repo $REPO --path . --values values.yaml \
      --dest-namespace monitoramento --dest-server https://kubernetes.default.svc \
      --sync-policy automated --upsert --grpc-web  --self-heal --auto-prune --loglevel="debug"
```

### Helm via manifest kubernetes                 - traefik
