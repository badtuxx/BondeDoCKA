## DAY1

### Questão 1
Criar um pod utilizando a imagem do Nginx 1.18.0, com o nome de giropops no namespace strigus.
<details> 
  <summary><b>Resposta 1</b> <em>(clique para ver a resposta)</em></summary>

Nesse caso, temos duas formas.
A primeira, utilizando somente a linha de comando:

```bash
kubectl run giropops --image nginx:1.18.0 --port 80 --namespace strigus
```

A segunda, e a mais recomendada. Eu acho ela mais recomendada pelo fato de você
poder analisar com mais tranquilidade o que você está criando. Mas é a minha
opinião apenas. :)

```bash
kubectl run giropops --image nginx:1.18.0 --port 80 --namespace strigus
--dry-run=client -o yaml > pod.yaml

kubectl create -f pod.yaml
```
</details>

### Questão 2
Aumentar a quantidade de réplicas do deployment girus, que está utilizando a imagem do nginx 1.18.0, para 3 replicas. O deployment está no namespace strigus.

<details>
  <summary><b>Resposta 2</b> <em>(clique para ver a resposta)</em></summary>

```bash
kubectl scale deployment -n strigus girus --replicas 3
```

```bash
kubectl edit deployment -n strigus girus # lá dentro, alteramos a qtde de
replicas e saimos.
```

```bash
kubectl create deployment girus --image nginx:1.18.0 --port 80 --namespace strigus --replicas 3  --dry-run=client -o yaml > deployment2.yaml

kubectl apply -f deployment2.yaml
```
</details>

### Questão 2
Precisamos atualizar a versão do Nginx do Pod giropops. Ele está na versão 1.18.0 e precisamos atualizar para versão 1.21.1
<details>
  <summary><b>Resposta 3</b> <em>(clique para ver a resposta)</em></summary>

```bash
kubectl edit pod -n strigus giropops # lá mudamos a versão do Nginx
```

```bash
kubectl set image pod giropops -n strigus web=nginx:1.21.0
```

```bash
kubectl get pods -n strigus giropops -o yaml > pod4.yaml
# Lembre-se de remover tudo o que não é necessario.

kubectl apply -f pod4.yaml
```
</details>