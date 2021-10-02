## DAY2

### Questão 1
Precisamos levantar algumas informações sobre o nosso cluster:
- Quantos nodes são workers?
- Quantos nodes são masters?
- Qual o Pod Network (CNI) que estamos utilizando?
- Qual o CIDR dos pods no segundo workers

Adicionar as informações colhidas no arquivo /opt/cluster_info.txt

<details>
  <summary><b>Resposta 1</b> <em>(clique para ver a resposta)</em></summary>

- Quantos nodes são workers?
```bash
kubectl get nodes
```

- Quantos nodes são masters?
```bash
kubectl get nodes
```

- Qual o Pod Network (CNI) que estamos utilizando?
```bash
kubectl get pods -n kube-system
```

```bash
ssh NODE
cd /etc/cni
ls -lha
```

- Qual o CIDR dos pods no segundo workers
```bash
kubectl get node -o jsonpath="{range .items[*]}{.metadata.name} {.spec.podCIDR}"
```

```bash
kubectl describe nodes seul-cool-05 | grep PodCIDR
```

- Qual é o nosso serviço de DNS para o nosso cluster?
```bash
kubectl get pods -n kube-system
```

</details>


### Questão 2
Precisamos criar um pod com as seguintes caracteristicas:

- Precisa ter um container rodando a imagem do Nginx, com um volume montado no
  diretorio html do nginx.

- Precisa ter um outro container rodando busybox e adicionando algum conteúdo
  ao arquivo /tmp/index.html

```bash
      => command: ["sh", "-c", "while true; do uname -a >> /tmp/index.html; date >> /tmp/index.html; sleep 2; done"]
```

- Precisamos ter um outro container rodando o busybox e executando o seguinte
  comando:
```bash
      => command: ["sh", "-c", "tail -f /tmp/index.html"]
```

<details>
  <summary><b>Resposta 2</b> <em>(clique para ver a resposta)</em></summary>

Criamos o arquivo pod.yaml e nele adicionamos os tres containers

```yaml
---
apiVersion: v1
kind: Pod
metadata:
  name: meu-pod
spec:
  containers:
    - name: container-1
      image: nginx
      ports:
        - containerPort: 80
      volumeMounts:
        - name: workdir
          mountPath: /usr/share/nginx/html
      resources:
        limits:
          memory: '1Gi'
          cpu: '800m'
        requests:
          memory: '700Mi'
          cpu: '400m'
    - name: container-2
      image: busybox
      command: ["sh", "-c", "while true; do uname -a >> /tmp/index.html; date >> /tmp/index.html; sleep 2; done"]
      volumeMounts:
        - name: workdir
          mountPath: /tmp/
    - name: container-3
      image: busybox
      command: ["sh", "-c", "tail -f /tmp/index.html"]
      volumeMounts:
        - name: workdir
          mountPath: /tmp/
  # These containers are run during pod initialization
  dnsPolicy: Default
  volumes:
    - name: workdir
      emptyDir: {}
```

Criando o pod
```bash
kubectl create -f pod.yaml
```

Verificando os logs par ver tudo funcionando

```bash
kubectl logs -f meu-pod container-1
kubectl logs -f meu-pod container-2
kubectl logs -f meu-pod container-3
kubectl exec -ti meu-pod -c container-1 -- bash
```
</details>


