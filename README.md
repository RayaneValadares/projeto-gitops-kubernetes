<h1 align="left">
  <img src="https://argo-cd.readthedocs.io/en/stable/assets/logo.png" 
       alt="ArgoCD" width="60" style="vertical-align:middle; margin-right:10px;">
  GitOps na Prática - "Online Boutique"
</h1>

![ARGOCD](https://img.shields.io/badge/Argo%20CD-1e0b3e?style=for-the-badge&logo=argo&logoColor=#d16044)  ![KUBERNETES](https://img.shields.io/badge/Kubernetes-3069DE?style=for-the-badge&logo=kubernetes&logoColor=white)  ![RANCHER](https://img.shields.io/badge/Rancher-0075A8?style=for-the-badge&logo=rancher&logoColor=white)  


Este projeto implementa o uso prático da metodologia **GitOps** para automatizar o deploy da aplicação **Online Boutique** (um exemplo de microserviços do Google Cloud) em um cluster, utilizando o **ArgoCD** como ferramenta de controle de entrega contínua.

---

## 🧩 Objetivo do Projeto
Implementar uma pipeline GitOps completa, onde qualquer alteração no repositório Git reflita automaticamente no ambiente Kubernetes, garantindo **sincronização, versionamento e reprodutibilidade**.

---

## ✅ Entregas Realizadas
- **Repositório Git configurado** com manifests YAML organizados.
- **Instalação e configuração do ArgoCD** no cluster Kubernetes local.
- **Criação e vinculação do repositório Git** dentro do ArgoCD.
- **Sincronização automática da aplicação** com os manifests do repositório.
- **Implantação e acesso ao frontend** da Online Boutique via `kubectl port-forward`.
- *(Opcional)* Customização de parâmetros nos manifests, como alteração do número de réplicas.

---

## ⚙️ Pré-requisitos
Antes de iniciar, verifique se os seguintes componentes estão instalados e configurados:

| Ferramenta | Descrição |
|-------------|------------|
| **Git** | Controle de versão dos manifests. |
| **Docker** | Motor de contêineres. Deve estar integrado ao WSL2. |
| **kubectl** | CLI para comunicação com o cluster. |
| **Conta GitHub** | Hospeda o repositório GitOps. |
| **Personal Access Token (PAT)** | Permite o acesso do ArgoCD ao repositório. |
| **VS Code** | Editor de código (com terminal PowerShell ou WSL). |

---

## 🚀 Guia de Execução
 
### 1️⃣ Iniciar o Ambiente
Certifique-se de que o **Docker Desktop** está ativo e com o **Kubernetes habilitado**.

> ⚙️ O Docker Desktop já cria e gerencia automaticamente um cluster Kubernetes local.

Verifique se o cluster está ativo:
```bash
kubectl get nodes
```

Clone este repositório:

```bash
git clone https://github.com/seu-usuario/Projeto-GitOps-K8s.git
cd Projeto-GitOps-K8s
```

- Fork e repositório GitHub: Fork do repositório oficial: a. https://github.com/GoogleCloudPlatform/microservices-demo

<img width="1406" height="916" alt="google" src="https://github.com/user-attachments/assets/eb8cc62e-b8fb-45f8-83c8-7ec2c3ef5281" />

<br>
<br>

### 2️⃣ Estrutura do Repositório
A estrutura utilizada neste projeto segue o padrão:

```bash
Projeto-GitOps-K8s/
└── k8s/
    └── online-boutique.yaml
```
Este arquivo YAML contém todos os manifests necessários para a aplicação.

<br>
<br>

### 3️⃣ Estrutura do Repositório
Crie o namespace para o ArgoCD:

```bash
kubectl create namespace argocd
```

<img width="991" height="936" alt="create" src="https://github.com/user-attachments/assets/76d720d9-5054-41dd-841f-fc52a37363cc" />

Aplique o manifest oficial de instalação:

```bash
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

Depois, exponha o serviço do ArgoCD localmente (mantenha esse terminal aberto):

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

<img width="772" height="513" alt="port-forward" src="https://github.com/user-attachments/assets/9d8e9d82-552d-43e7-8f05-c1ff572d951c" />

<br>
<br>

4️⃣ Acessar o Painel do ArgoCD

Recupere a senha inicial do usuário admin:

```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
```

- Acesse pelo navegador:
🔗 https://localhost:8081

Ignore o alerta de segurança e entre com:

Usuário: admin

Senha: (a obtida no comando anterior)

<br>
<br>

5️⃣ Conectar Repositório no ArgoCD

No painel do ArgoCD:

- Vá em Settings → Repositories → + Connect Repo

- Selecione HTTPS como método.

- Informe:

  Repository URL: https://github.com/seu-usuario/Projeto-GitOps-K8s.git

  Username: seu usuário GitHub

  Password: seu Personal Access Token (PAT)

<img width="1708" height="742" alt="argocd" src="https://github.com/user-attachments/assets/f9f474b7-a8c9-405e-aad6-d2e91de78e46" />

- Clique em CONNECT e confirme o status “Successful”.

<br>
<br>

6️⃣ Criar Aplicação no ArgoCD

- Vá em Applications → + NEW APP

- Configure os campos:
	
| Campo | Valor |
|-------------|------------|
| **Application Name** | online-boutique |
| **Project Name** | default |
| **Repository URL** | (o seu repositório conectado) |
| **Revision** | HEAD |
| **Path** | k8s |
| **Cluster** | in-cluster |
| **Namespace** | default |

- Clique em CREATE.

<img width="819" height="367" alt="5argocd" src="https://github.com/user-attachments/assets/c5d2db95-b6f4-4009-bbfd-7d8443b71cfa" />

<Br>
<BR>

7️⃣ Sincronizar a Aplicação

Abra a aplicação criada e clique em SYNC → SYNCHRONIZE.
O ArgoCD fará o deploy automático de todos os recursos definidos no manifest.

Aguarde até o status mudar para Healthy e Synced ✅

<br>
<br>

8️⃣ Acessar o Frontend

Execute o port-forward para acessar a aplicação:

```bash
kubectl port-forward svc/frontend -n default 8080:80
```

Acesse no navegador:
🌐 http://localhost:8080

<img width="1653" height="922" alt="boutique" src="https://github.com/user-attachments/assets/36be3431-5ef7-4d52-be85-93d79da3c6fe" />

A aplicação Online Boutique deve estar rodando corretamente 🎉

<br>
<br>

🔧 Customização GitOps

Como demonstração de controle GitOps, altere o número de réplicas do serviço loadgenerator no arquivo k8s/online-boutique.yaml, por exemplo:

```bash
spec:
  replicas: 2
```

Após commitar e enviar para o GitHub, o ArgoCD detectará a mudança e sincronizará automaticamente o novo estado no cluster.

<br>
<br>

🧩 Tecnologias Utilizadas

<p align="center">
  <img src="https://img.shields.io/badge/GitOps-Automation-1F6FEB?style=flat-square&logo=git&logoColor=white" alt="GitOps Badge"/>
  <img src="https://img.shields.io/badge/ArgoCD-Continuous_Deployment-EB6E34?style=flat-square&logo=argo&logoColor=white" alt="ArgoCD Badge"/>
  <img src="https://img.shields.io/badge/Kubernetes-Orchestration-326CE5?style=flat-square&logo=kubernetes&logoColor=white" alt="Kubernetes Badge"/>
  <img src="https://img.shields.io/badge/GitHub-Repository-181717?style=flat-square&logo=github&logoColor=white" alt="GitHub Badge"/>
  <img src="https://img.shields.io/badge/Docker-Containerization-2496ED?style=flat-square&logo=docker&logoColor=white" alt="Docker Badge"/>
</p>

---

![Version](https://img.shields.io/badge/version-1.1.1-blue?style=flat-square)
![Maintainer](https://img.shields.io/badge/maintainer-Rayane%20Vitória%20Valadares-purple?style=flat-square)

---

🏁 Conclusão

Este projeto mostra na prática como o GitOps, aliado ao ArgoCD, simplifica a entrega contínua de aplicações Kubernetes.
A abordagem garante automação, rastreabilidade e consistência, permitindo que todo o ciclo de deploy seja versionado e auditável diretamente pelo Git.



