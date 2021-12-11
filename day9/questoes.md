## DAY9

### Questão 1

Nosso gerente precisa reportar para o nosso diretor, quais as namespaces que
nós temos hoje em produção.
Salvar a lista de namespaces no arquivo /tmp/giropops-k8s.txt

<details>
  <summary><b>Resposta 1</b> <em>(clique para ver a resposta)</em></summary>

Para gerar a lista de todas as namespaces disponíveis e já adiciona-lá no
arquivo indicado.

```bash
k get ns --no-headers -o custom-columns=":metadata.name" > /tmp/giropops-k8s.txt
```
</details>


### Questão 2
Precisamos criar um pod chamado web e utilizando a imagem do Nginx na versão 1.21.4. O pod deverá
ser criado no namespace web-1 e o container deverá se chamar meu-container-web.
O nosso gerente pediu para que seja criado um script que retorne o status desse
pod que iremos criar.
O nome do script é /tmp/script-do-gerente-toskao.sh

<details>
  <summary><b>Resposta 2</b> <em>(clique para ver a resposta)</em></summary>

Para criar o nosso yaml que dará origem ao pod solicitado, precisamos executar
o comando abaixo:

```bash
k run web --image nginx:1.21.4 -n web-1 --dry-run=client -o yaml > pod.yaml
```

Agora, apenas edite o arquivo e adicione o nome do container conforme
solicitado:

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: web
  name: web
  namespace: web-1
spec:
  containers:
  - image: nginx:1.21.4
    name: meu-container-web
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

Para criar o script que mostrará o status do pod em questão, faça:

```bash
echo 'k get pods -n web-1 --no-headers -o custom-columns=":metadata.name, :status.phase"' > /tmp/script-do-gerente-toskao.sh
```

Verificando os logs par ver tudo funcionando

```bash
kubectl logs -f meu-pod container-1
kubectl logs -f meu-pod container-2
kubectl logs -f meu-pod container-3
kubectl exec -ti meu-pod -c container-1 -- bash
```
</details>


### Questão 3

Criamos o pod do Nginx, parabéns! 
TASK-1
Portanto, agora precisamos mudar a versão do Nginx para a versão 1.18.0, pois o
nosso gerente viu um artigo no Medium e disse que agora temos que usar essa
versão e ponto.

<details>
  <summary><b>Resposta 2</b> <em>(clique para ver a resposta)</em></summary>

```bash
k edit pods -n web-1 web
```

TASK-2
Precisamos criar um deployment no lugar do nosso pod do Nginx

```bash
k create deployment web --image nginx:1.20.2 --dry-run=client -o yaml > deployment.yaml
```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: web
  name: web
  namespace: web-1
spec:
  replicas: 1
  selector:
    matchLabels:
      app: web
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: web
    spec:
      containers:
      - image: nginx:1.20.2
        name: meu-container-web
        resources: {}
status: {}
```

TASK-3
Precisamos utilizar o Nginx com a imagem do Alpine, pq o gerente leu um outro
artigo no Medium.

```bash
k edit deployment -n web-1 web
```

TASK-4
Precisamos realizar o rollback do nosso deployment web

```bash
k rollout history deployment -n web-1 web
k rollout history deployment -n web-1 web --revision=1
k rollout history deployment -n web-1 web --revision=2
k rollout undo deployment -n web-1 web --to-revision=1
```
</details>
