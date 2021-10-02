## DAY3

### Questão 1
Criar um pod estatico utilizando a imagem do nginx.

### Resposta 1
Para criar um pod estatico, voce precisa adicionar o manifesto de criação do pod desejado, dentro do diretório /etc/kubernetes/manifests, conforme abaixo: 

```bash
cd /etc/kubernetes/manifests
k run giropops --image nginx -o yaml --dry-run=client > meu-pod-estatico.yaml
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

### Questão 2
O nosso gerente está assustado, pois conversando com o gerente de uma outra
empresa, ficou sabendo que aconteceu uma indisponibilidade no ambiente
Kubernetes de lá por conta de certificados expirados.
Ele está demasiadamente preocupado.
Ele quer que tenhamos a certeza de que nosso cluster não corre esse perigo,
portanto, adicione no arquivo /tmp/meus-certificados.txt todos eles e suas
datas de expiração.

### Resposta 2
Os certificados, por padrao, ficam no diretório /etc/kubernetes/pki. Para que
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
kubeadm certs check-expiration >> /tmp/meus-certificados.txt
```

### Questão 3
Pois bem, vimos que precisamos atualizar o nosso cluster imediatamente, sem
trazer nenhum indisponibilidade para o ambiente. Como devemos proceder?



### Resposta 3
Podemos utilizar o comando kubeadm certs para visualizar as datas corretas e
tbm para realizar sua renovação. Conforme estamos fazendo abaixo:

```bash
kubeadm certs renew all
```

Lembrando a importancia de realizar o procedimento em todos os nodes master.
Lembre se restartar o apiserver, controller, scheduller e o etcd.
Para isso, você pode utilizar o comando docker stop, de dentro do node que está
sendo atualizado.


