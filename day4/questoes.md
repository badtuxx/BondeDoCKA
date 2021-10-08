## DAY4

### Questão 1
Precisamos subir um container em um node master. Esse container tem que estar
rodando a imagem do nginx, o nome do pod é pod-web e o container é
container-web. Sua namespace será a catota.

### Resposta 1

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: pod-web
  name: pod-web
  namespace: catota
spec:
  containers:
  - image: nginx
    name: container-web
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
  tolerations:
  - effect: NoSchedule
    operator: Equal
    key: node-role.kubernetes.io/master
  nodeSelector:
    node-role.kubernetes.io/master: ""  
status: {}

```

```bash
kubectl create namespace catota
kubectl create -f opa.yaml
```


### Questão 2

### Resposta 2

```bash

```
