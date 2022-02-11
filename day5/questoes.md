## DAY5

### Questão 1
O nosso gerente solicitou que seja feita agora, um backup/snapshot do nosso
ETCD. Ele ficou muito assustado em saber que se perdermos o ETCD, perderemos o
nosso cluster e, consequentemente, a nossa tranquilidade! Portanto, bora fazer
esse snapshot imediatamente!

<details>
  <summary><b>Resposta 1</b> <em>(clique para ver a resposta)</em></summary>

```bash
ssh node-master # Um dos nodes onde o ETCD está em execução.
cd /etc/kubernetes/manifests
sudo cat etcd.yaml
sudo grep etcd kube-apiserver.yaml

# Com essas informaçoes, já podemos criar o nosso snapshot
sudo apt install -y etcd-client
sudo ETCDCTL_API=3 etcdctl snapshot save $HOME/snap_do_gerente.db --key /etc/kubernetes/pki/apiserver-etcd-client.key --cacert /etc/kubernetes/pki/etcd/ca.crt --cert /etc/kubernetes/pki/apiserver-etcd-client.crt

```
</details>

### Questão 2
Muito bem, o gerente está feliz, mas não perfeitamente explendido em sua
felicidade! A pergunta do gerente foi a seguinte: Você já fez o restore para
testar o nosso snapshot? EU QUERO TESTAR AGORA!

<details>
  <summary><b>Resposta 2</b> <em>(clique para ver a resposta)</em></summary>
  
```bash
ETCDCTL_API=3 etcdctl snapshot restore $HOME/snap_do_gerente.db --data-dir /tmp/etcd-test
ls -lR /tmp/etcd-test
```
</details>
