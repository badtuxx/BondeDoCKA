## DAY6

### Questão 1

O nosso gerente observou no dashboard do [Lens](https://k8slens.dev/) que um dos nossos nodes não está
bem. Temos algum problema com o nosso cluster e precisamos resolver agora. 

<details>
  <summary><b>Resposta 1</b> <em>(clique para ver a resposta)</em></summary>

```bash
kubectl get nodes
ssh node_com_problema
ps -ef | grep kubelet
docker ps
systemctl status kubelet
journalctl -u kubelet
whereis kubelet
sudo vim /etc/systemd/system/kubelet.service.d/10-kubeadm.conf # ARRUMAR O PATH DO
# sudo systemctl edit --full kubelet # ainda podemos usar esse comando ao inves de
# alterar o arquivo
BINARIO DO KUBELET
systemctl daemon-reload
systemctl restart kubelet
systemctl status kubelet
journalctl -u kubelet
```
</details>

### Questão 2

Temos um secret com o nome e senha de um usuário que nossa aplicação irá
utilizar, precisamos colocar esse secret em um pod.\
Detalhe: Esse secret deve se tornar uma variável de ambiente dentro do container.

<details>
  <summary><b>Resposta 2</b> <em>(clique para ver a resposta)</em></summary>

Criando o arquivo de secrets e, em seguida, criando a secret:
  
```bash
kubectl create secret generic credentials --from-literal user=jeferson \
--from-literal password=giropops --dry-run=client -o yaml > meu_secret.yaml

kubectl create -f meu_secret.yaml
```

Visualizando a secret:
  
```bash
kubectl get secrets -n default
kubectl get secrets credentials -n default
kubectl describe secrets credentials -n default
```

Criando o arquivo do pod que fará uso da secret para criar variáveis de ambiente:

```bash
kubectl run giropops --image nginx --dry-run=client -o yaml > pod_com_secret.yaml       
```

Conteúdo final do arquivo do pod:

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: giropops
  name: giropops
spec:
  containers:
  - image: nginx
    name: giropops
    resources: {}
    env:
    - name: MEU_USER
      valueFrom:
        secretKeyRef:
          name: credentials
          key: user
    - name: MEU_PASSWORD
      valueFrom:
        secretKeyRef:
          name: credentials
          key: password
    volumeMounts:
    - name: credentials
      mountPath: /opt/giropops
  dnsPolicy: ClusterFirst
  restartPolicy: Always
  volumes:
  - name: credentials
    secret:
      secretName: credentials
```

Criando o pod:

```bash
kubectl create -f pod_com_secret.yaml
```

Visualizando as variáveis de ambiente do pod:

```bash
kubectl exec -it giropops -n default -- env
```
  
</details>
