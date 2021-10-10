## DAY4

### Questão 1
Precisamos subir um container em um node master. Esse container tem que estar
rodando a imagem do nginx, o nome do pod é pod-web e o container é
container-web. Sua namespace será a catota.

<details>
  <summary><b>Resposta 1</b> <em>(clique para ver a resposta)</em></summary>

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
</details>

### Questão 2
Precisamos de algumas informaçoes do nosso cluster e dos pods que lá estão.
Portanto, precisamos do seguinte:
- Adicione todos os pods do cluster por ordem de criação, dentro do arquivo
  /tmp/pods.txt
- Remova um pod do weave, verifique os eventos e os adicione no arquivo /tmp/eventos.txt
- Liste todos os pods que estao em execução no seul-cool-5 e os adicione no
  arquivo /tmp/pods-node-05.txt



<details>
  <summary><b>Resposta 2</b> <em>(clique para ver a resposta)</em></summary>
  
```bash
kubectl get pods --sort-by=.metadata.creationTimestamp -A -o name > /tmp/pods.txt
kubectl get events --all-namespaces --sort-by=.metadata.creationTimestamp > /tmp/eventos.txt
kubectl get pods --all-namespaces --field-selector spec.nodeName=seul-cool-05 -o name > /tmp/pods-node-05.txt
```
</details>
