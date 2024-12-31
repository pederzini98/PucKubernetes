## Descrição
Este repositório contém a implementação de um sistema utilizando Kubernetes para gerenciar uma aplicação de adivinhação (guess game). A aplicação é composta por:

- **Frontend (React)**: Interface do usuário.
- **Backend (Flask)**: Lógica do jogo e API.
- **Banco de Dados (Postgres)**: Armazena informações persistentes do jogo.
- **NGINX**: Proxy reverso para balancear a carga entre instâncias do backend e servir arquivos estáticos.

## Requisitos
- Kubernetes local configurado (Minikube, MicroK8s ou Docker Desktop).
- Kubectl instalado.
- Repositório com as seguintes pastas e arquivos:
  - `frontend/`: Código da aplicação React.
  - `backend/`: Código da aplicação Flask.
  - `k8s/`: Arquivos YAML de configuração Kubernetes.

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

### 3. Build das Imagens Localmente
Use o Docker embutido no seu cluster Kubernetes (como Minikube) para construir e carregar as imagens diretamente:

#### Backend
```bash
cd backend
docker build -t backend:latest .
cd ..
```
#### Frontend
```bash
cd frontend
docker build -t frontend:latest .
cd ..
```

### 4. Deploy dos Componentes
#### Banco de Dados
```bash
kubectl apply -f k8s/postgres-statefulset.yml
kubectl apply -f k8s/postgres-service.yml
```
#### Backend
```bash
kubectl apply -f k8s/backend-deployment.yml
kubectl apply -f k8s/backend-hpa.yml
```
#### Frontend
```bash
kubectl apply -f k8s/frontend-deployment.yml
kubectl apply -f k8s/frontend-service.yml
```
#### NGINX
```bash
kubectl apply -f k8s/nginx-deployment.yml
kubectl apply -f k8s/nginx-configmap.yml
```

### 5. Verifique o Status
Use o seguinte comando para verificar os pods:
```bash
kubectl get pods
```
Certifique-se de que todos os pods estão em estado `Running`.

### 6. Acesse o Sistema
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
- `k8s/postgres-statefulset.yml`: Configuração do StatefulSet para o Postgres.
- `k8s/backend-deployment.yml`: Deployment do backend.
- `k8s/backend-hpa.yml`: Configuração do HPA para o backend.
- `k8s/frontend-deployment.yml`: Deployment do frontend.
- `k8s/frontend-service.yml`: Serviço para expor o frontend.
- `k8s/nginx-deployment.yml`: Deployment do NGINX.
- `k8s/nginx-configmap.yml`: Configuração do NGINX.

