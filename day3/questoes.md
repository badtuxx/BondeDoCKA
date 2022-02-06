## DAY3

### Questão 1
Criar um pod estático utilizando a imagem do nginx.

<details>
  <summary><b>Resposta 1</b> <em>(clique para ver a resposta)</em></summary>
Para criar um pod estatico, voce precisa adicionar o manifesto de criação do pod desejado, dentro do diretório /etc/kubernetes/manifests, conforme abaixo: 

```bash
cd /etc/kubernetes/manifests
k run giropops --image nginx -o yaml --dry-run=client | sudo tee meu-pod-estatico.yaml
k get pods -A | grep giropops
docker ps -a |grep giropops
```

O arquivo terá o conteúdo abaixo:

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
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

Referências:
* https://blog.vikki.in/creating-static-pod-in-kubernetes/
* https://kubernetes.io/docs/tasks/configure-pod-container/static-pod/

</details>

### Questão 2
O nosso gerente está assustado, pois conversando com o gerente de uma outra
empresa, ficou sabendo que aconteceu uma indisponibilidade no ambiente
Kubernetes de lá por conta de certificados expirados.
Ele está demasiadamente preocupado.
Ele quer que tenhamos a certeza de que nosso cluster não corre esse perigo,
portanto, adicione no arquivo /tmp/meus-certificados.txt todos eles e suas
datas de expiração.

<details>
  <summary><b>Resposta 2</b> <em>(clique para ver a resposta)</em></summary>
Os certificados, por padrão, ficam no diretório /etc/kubernetes/pki. Para que
você possa verificar a data de expiração, você pode utilizar o comando openssl,
conforme abaixo:

```bash
cd /etc/kubernetes/pki
openssl x509 -noout -text -in apiserver.crt | grep -i "not after"
```

Lembrar de adicionar a data de expiração no arquivo solicitado na questão.

Caso queira fazer de uma forma mais bonitinha, e automagicamente pegar as datas
e já adicionar ao arquivo, faça conforme abaixo:

```bash
find /etc/kubernetes/pki/ -iname "apiserver*crt" -exec openssl x509 -noout -subject -enddate -in {} \; >> /tmp/meus-certificados.txt
```

Para facilitar a nossa vida, podemos utilizar o kubeadm certs, conforme abaixo:

```bash
sudo kubeadm certs check-expiration >> /tmp/meus-certificados.txt
```
</details>

### Questão 3
Pois bem, vimos que precisamos atualizar o nosso cluster imediatamente, sem
trazer nenhum indisponibilidade para o ambiente. Como devemos proceder?

<details>
  <summary><b>Resposta 3</b> <em>(clique para ver a resposta)</em></summary>

Podemos utilizar o comando kubeadm certs para visualizar as datas corretas e
também para realizar sua renovação. Conforme estamos fazendo abaixo:

```bash
sudo kubeadm certs renew all
```

Lembrando a importância de realizar o procedimento em todos os nodes master.
Lembre se restartar o kube-apiserver, kube-controller-manager, kube-scheduller e o etcd.
Para isso, você pode utilizar o comando docker stop, de dentro do node que está
sendo atualizado.

```bash
#-------------- Pesquisando pelo conteiner do kube-apiserver
docker ps | grep -i kube-apiserver

# Exemplo da resposta
3c277bd8272d   d35b182b4200           "kube-apiserver --ad…"   43 minutes ago   Up 43 minutes             k8s_kube-apiserver_kube-apiserver-master_kube-system_0b2d4f0f571034198d5ee7cd37deb292_3
8c31374d6b1c   k8s.gcr.io/pause:3.5   "/pause"                 43 minutes ago   Up 43 minutes             k8s_POD_kube-apiserver-master_kube-system_0b2d4f0f571034198d5ee7cd37deb292_3

# Exemplo de um restart do conteiner do kube-apiserver
docker restart 3c277bd8272d

#-------------- Pesquisando pelo conteiner do kube-controller-manager
docker ps | grep -i kube-controller-manager

# Exemplo da resposta
343af5293b5f   3618e4ab750f           "kube-controller-man…"   About a minute ago   Up About a minute             k8s_kube-controller-manager_kube-controller-manager-master_kube-system_234b6b0a88f29ae670a32e71f28d335f_4
178be563db9d   k8s.gcr.io/pause:3.5   "/pause"                 46 minutes ago       Up 46 minutes                 k8s_POD_kube-controller-manager-master_kube-system_234b6b0a88f29ae670a32e71f28d335f_3

# Exemplo de um restart do conteiner do kube-controller-manager
docker restart 343af5293b5f

#-------------- Pesquisando pelo conteiner do kube-scheduler
docker ps | grep -i kube-scheduler

# Exemplo da resposta
6de306bc007c   9fe44a6192d1           "kube-scheduler --au…"   2 minutes ago    Up 2 minutes              k8s_kube-scheduler_kube-scheduler-master_kube-system_5407c93d3f7e4e48c5d2e3470cc8e217_4
943f069d5134   k8s.gcr.io/pause:3.5   "/pause"                 46 minutes ago   Up 46 minutes             k8s_POD_kube-scheduler-master_kube-system_5407c93d3f7e4e48c5d2e3470cc8e217_3

# Exemplo de um restart do conteiner do kube-scheduler
docker restart 6de306bc007c

#-------------- Pesquisando pelo conteiner do etcd
docker ps | grep -i etcd

# Exemplo da resposta
f8b9e21ea176   004811815584           "etcd --advertise-cl…"   47 minutes ago   Up 47 minutes             k8s_etcd_etcd-master_kube-system_ccc606cd086ca07519d82deaded80eb7_3
4f4f8a830fff   k8s.gcr.io/pause:3.5   "/pause"                 47 minutes ago   Up 47 minutes             k8s_POD_etcd-master_kube-system_ccc606cd086ca07519d82deaded80eb7_3

# Exemplo de um restart do conteiner do etcd
docker restart f8b9e21ea176
```

Referências:

* https://stackoverflow.com/questions/67643559/what-is-the-best-practice-to-rotate-kubernetes-certificates
* https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-certs/
</details>

