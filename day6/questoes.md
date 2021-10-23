## DAY6

### Questão 1

O nosso gerente observou no dashboard do Lens que um dos nossos nodes não está
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
vim /etc/systemd/system/kubelet.service.d/10-kubeadm.conf # ARRUMAR O PATH DO
# systemctl edit --full kubelet # ainda podemos usar esse comando ao inves de
# alterar o arquivo
BINARIO DO KUBELET
systemctl daemon-reload
systemctl restart kubelet
systemctl status kubelet
journalctl -u kubelet
```
</details>

### Questão 2

<details>
  <summary><b>Resposta 2</b> <em>(clique para ver a resposta)</em></summary>
  
```bash

```
</details>
