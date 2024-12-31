# README: Configuração de Sistema com Kubernetes

## Descrição
Este repositório contém a implementação de um sistema utilizando Kubernetes para gerenciar uma aplicação de adivinhação (guess game). A aplicação é composta por:

- **Frontend (React)**: Interface do usuário.
- **Backend (Flask)**: Lógica do jogo e API.
- **Banco de Dados (Postgres)**: Armazena informações persistentes do jogo.
- **NGINX**: Proxy reverso para balancear a carga entre instâncias do backend e servir arquivos estáticos.

## Requisitos
- Kubernetes local configurado (Minikube, MicroK8s ou Docker Desktop).
- Kubectl instalado.
- Imagens Docker disponíveis no Docker Hub:
  - Backend Flask: `<docker_hub_username>/backend:latest`
  - Frontend React: `<docker_hub_username>/frontend:latest`

## Estrutura de Componentes

1. **Backend**:
   - Deployment com 2 réplicas e suporte a escalonamento automático (HPA).
   - Exposição interna via ClusterIP.

2. **Frontend**:
   - Deployment com 1 réplica.
   - Exposto via Service NodePort na porta `32000`.

3. **Banco de Dados (Postgres)**:
   - StatefulSet para persistência de dados.
   - Armazenamento em PersistentVolumeClaim (PVC).

4. **NGINX**:
   - Proxy reverso configurado via ConfigMap.
   - Deployment com 1 réplica e exposição interna.

## Instalação

### 1. Clone o Repositório
```bash
git clone <url-do-repositorio>
cd <nome-do-repositorio>
```

### 2. Suba o Cluster Kubernetes
Certifique-se de que seu cluster Kubernetes está ativo. Por exemplo:
```bash
minikube start
```

### 3. Deploy dos Componentes
#### Banco de Dados
```bash
kubectl apply -f postgres-statefulset.yml
kubectl apply -f postgres-service.yml
```
#### Backend
```bash
kubectl apply -f backend-deployment.yml
kubectl apply -f backend-hpa.yml
```
#### Frontend
```bash
kubectl apply -f frontend-deployment.yml
kubectl apply -f frontend-service.yml
```
#### NGINX
```bash
kubectl apply -f nginx-deployment.yml
kubectl apply -f nginx-configmap.yml
```

### 4. Verifique o Status
Use o seguinte comando para verificar os pods:
```bash
kubectl get pods
```
Certifique-se de que todos os pods estão em estado `Running`.

### 5. Acesse o Sistema
O frontend estará acessível via NodePort:
```bash
minikube service frontend-service
```
Ou utilize o port-forward:
```bash
kubectl port-forward service/frontend-service 3000:3000
```
Acesse o navegador em `http://localhost:3000`.

## Escalonamento Automático
O HPA para o backend está configurado com base na utilização de CPU. Para testar o HPA:
1. Gere carga no backend.
2. Verifique o HPA:
   ```bash
   kubectl get hpa
   ```
3. Observe o aumento ou redução de réplicas.

## Estrutura de Arquivos
- `postgres-statefulset.yml`: Configuração do StatefulSet para o Postgres.
- `backend-deployment.yml`: Deployment do backend.
- `backend-hpa.yml`: Configuração do HPA para o backend.
- `frontend-deployment.yml`: Deployment do frontend.
- `frontend-service.yml`: Serviço para expor o frontend.
- `nginx-deployment.yml`: Deployment do NGINX.
- `nginx-configmap.yml`: Configuração do NGINX.

## Notas Adicionais
- Substitua `<docker_hub_username>` nos arquivos YAML pelas suas imagens Docker no Docker Hub.
- Certifique-se de que as imagens Docker estão atualizadas e disponíveis.
- Utilize o comando `kubectl describe <resource>` para diagnosticar problemas em qualquer componente.

## Bônus: Helm Charts
O projeto pode ser implementado utilizando Helm Charts para maior flexibilidade e automação. Inclua os diretórios `charts/frontend`, `charts/backend`, `charts/postgres`, e `charts/nginx` para configurar os serviços com valores parametrizados.

---
**Autor:** Seu Nome Aqui  
**Data:** Mês/Ano

