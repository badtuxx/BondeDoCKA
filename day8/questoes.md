## DAY8

### Questão 1
Hoje o nosso gerente pediu para que fiquemos confortáveis com o gerenciamento
de contextos do nossos clusters.
Ele está com medo de que executemos algo em ulgum cluster errado, e assim
deixando o nosso dia muito mais chatiante!

<details>
  <summary><b>Resposta 1</b> <em>(clique para ver a resposta)</em></summary>

Criamos dois clusters, para que pudessemos brincar com os contextos. Para criar
os cluster, nós utilizamos o Kind, e para criar o cluster, nós estamos
utilizando um arquivo template, conforme abaixo:


```bash
vim kind-cluster-1.yaml
```

```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
- role: worker
- role: worker
```

```bash
kind create cluster --config kind-cluster-1.yaml
```

Criando o nosso segundo cluster:

```bash
vim kind-cluster-2.yaml
```

```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
- role: worker
```

```bash
kind create cluster --config kind-cluster-2.yaml
```

Agora que os nossos clusters já estão criados, bora brincar com os contextos.

Para visualizar os contextos, utilize o comando abaixo:

```bash
kubectl config get-contexts
```

Para selecionar determinado contexto, utilize:

```bash
kubectl config use-context CONTEXTO_DESEJADO
```

Vale lembrar que os contextos estão definidos no seu arquivo config, na maioria
dos casos no $HOME/.kube/config.


</details>


### Questão 02
Precisamos criar um pod com o Nginx rodando no cluster lt-01, já no cluster
giropops-01, nós precisamos ter um deployment do Nginx e um service apontando
para esse deployment.
Os containers deverão ter o mesmo nome em todos os cluster e estarem rodando no
namespace strigus.


<details>
  <summary><b>Resposta 1</b> <em>(clique para ver a resposta)</em></summary>


```bash
kubectl config current-context
kubectl config use-context kind-lt-01
```

```bash
kubectl run --image nginx strigus-01 --port 80 --namespace strigus --dry-run=client -o yaml  > meu_pod.yaml
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: strigus-01
  name: strigus-01
  namespace: strigus
spec:
  containers:
  - image: nginx
    name: strigus-01
    ports:
    - containerPort: 80
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

```bash
kubectl create ns strigus
kubectl create -f meu_pod.yaml
```


```bash
kubectl config current-context
kubectl config use-context kind-giropops-01
```

```bash
kubectl create deployment giropops --image nginx --port 80 --namespace strigus --dry-run=client -o yaml > meu_deployment.yaml
```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: giropops
  name: giropops
  namespace: strigus
spec:
  replicas: 1
  selector:
    matchLabels:
      app: giropops
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: giropops
    spec:
      containers:
      - image: nginx
        name: strigus-01
        ports:
        - containerPort: 80
        resources: {}
status: {}
```

```bash
kubectl create ns strigus
kubectl create -f meu_deployment.yaml
kubectl expose deployment --namespace strigus giropops
```

</details>
