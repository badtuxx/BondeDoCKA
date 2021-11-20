## DAY7

### Questão 1
Precisamos subir um pod, fácil não?
Porém esse pod somente poderá ficar disponível quando um determinado service
estiver no ar.

O serviço deverá ser um simples Nginx.

O pod, nós teremos mais detalhes durante a resolução.

<details>
  <summary><b>Resposta 1</b> <em>(clique para ver a resposta)</em></summary>

Criar o seguinte pod utilizando as probes readiness e liveness

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: giropops
  name: giropops
spec:
  containers:
  - image: nginx
    name: giropops
    ports:
    - containerPort: 80
    resources: {}
    livenessProbe:
      exec:
        command:
        - 'true'
    readinessProbe:
      exec:
        command:
        - sh
        - -c
        - 'curl http://my-nginx:80'      
  dnsPolicy: ClusterFirst
  restartPolicy: Always
```

```bash
kubectl create -f pod-que-espera-por-um-service.yaml
```

Agora vamos criar o pod e o service que o primeiro pod está esperando para
ficar disponível:

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: my-nginx
  name: my-nginx
spec:
  containers:
  - image: nginx
    name: my-nginx
    ports:
    - containerPort: 80
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
```

```bash
kubectl create -f pod-que-o-outro-pod-esta-esperando.yaml
kubectl expose pod my-nginx
kubectl get pods
kubectl describe pods giropops
```
</details>
