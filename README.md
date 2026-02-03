# 🚀 n8n Ultimate Local Setup: Windows + Docker + WSL2

![n8n Banner](https://raw.githubusercontent.com/n8n-io/n8n/master/assets/n8n-logo.png)

![Docker](https://img.shields.io/badge/docker-%230db7ed.svg?style=for-the-badge&logo=docker&logoColor=white)
![Ubuntu](https://img.shields.io/badge/Ubuntu-E94331?style=for-the-badge&logo=ubuntu&logoColor=white)
![Windows 11](https://img.shields.io/badge/Windows%2011-%230079d5.svg?style=for-the-badge&logo=Windows%2011&logoColor=white)
![n8n](https://img.shields.io/badge/n8n-FF6D5B?style=for-the-badge&logo=n8n&logoColor=white)

Este guia fornece o passo a passo técnico para transformar seu notebook em uma poderosa central de automação, garantindo **persistência de dados** e **performance nativa** via WSL2.

---

## 📋 Sumário
* [0. Checkup de Hardware](#0-checkup-de-hardware)
* [1. Preparando a Base (WSL2)](#1-preparando-a-base-wsl2)
* [2. Instalando o Motor (Docker)](#2-instalando-o-motor-docker)
* [3. Configurando o n8n](#3-configurando-o-n8n)
* [4. Inicialização](#4-inicialização)
* [5. Guia de Manutenção](#5-guia-de-manutenção)
* [⚠️ Riscos e Falhas](#-riscos-e-falhas)

---

## 🔍 0. Checkup de Hardware
Antes de começar, precisamos garantir que seu processador pode criar "mundos virtuais".

1.  Pressione `Ctrl + Shift + Esc` (**Gerenciador de Tarefas**).
2.  Acesse a aba **Desempenho** > **CPU**.
3.  Confirme se **Virtualização: Habilitado** está visível.

> [!CAUTION]
> **Está desabilitado?** Você deve reiniciar o PC, entrar na BIOS (geralmente teclas F2, F10 ou DEL) e ativar **VT-x (Intel)** ou **AMD-V**. Sem isso, o Docker não iniciará.

---

## 🏗️ 1. Preparando a Base: WSL 2 e Ubuntu
O WSL2 permite rodar o Linux dentro do Windows sem perda de performance.

1.  Abra o **PowerShell** como Administrador.
2.  Execute o comando:
    ```bash
    wsl --install
    ```
3.  🔄 **Reinicie seu computador imediatamente.**
4.  Após o boot, o Ubuntu abrirá uma janela preta. Escolha um **usuário** e **senha** (você usará isso para comandos administrativos no Linux).

---

## 🐳 2. Instalando o Motor: Docker Desktop
O Docker é o que permite ao n8n rodar de forma isolada e segura.

1.  Baixe o [Docker Desktop para Windows](https://www.docker.com/products/docker-desktop/).
2.  **Importante:** Na instalação, verifique se a opção **"Use the WSL 2 based engine"** está marcada.
3.  Abra o Docker Desktop e espere o ícone da baleia (na barra de tarefas) ficar estável.
4.  Valide no seu terminal:
    ```bash
    docker --version
    ```

---

## ⚙️ 3. Configurando o n8n com Persistência
Não rodaremos o n8n solto. Criaremos uma estrutura organizada para que você **nunca perca seus fluxos**.

1.  Crie sua pasta de trabalho:
    ```bash
    mkdir n8n-docker && cd n8n-docker
    ```
2.  Crie um arquivo chamado `docker-compose.yml` e cole o código abaixo:

```yaml
version: '3.8'
services:
  n8n:
    image: n8nio/n8n:latest
    container_name: n8n_app
    restart: always
    ports:
      - "5678:5678"
    environment:
      - N8N_HOST=localhost
      - N8N_PORT=5678
      - N8N_PROTOCOL=http
      - NODE_ENV=production
      - WEBHOOK_URL=http://localhost:5678/
    volumes:
      - ./n8n_data:/home/node/.n8n
```
> 💡 **Nota do Especialista:** O campo `volumes` acima cria uma pasta `n8n_data` no seu HD. Se o container "quebrar", suas automações estão salvas ali.

---

## 🚀 4. Inicialização
Na pasta onde você criou o arquivo, execute:

```bash
docker-compose up -d
```
* `up`: Sobe o serviço.
* `-d`: Roda em "background" (você pode fechar o terminal).

**Acesse agora:** [http://localhost:5678](http://localhost:5678) 🌐

---

## 🛠️ 5. Guia de Manutenção
Comandos essenciais para o seu dia a dia:

| Objetivo | Comando |
| :--- | :--- |
| **Parar o n8n** | `docker-compose stop` |
| **Verificar Erros (Logs)** | `docker-compose logs -f` |
| **Atualizar o n8n** | `docker-compose pull && docker-compose up -d` |
| **Ver se está rodando** | `docker ps` |

---

## ⚠️ Riscos e Falhas Comuns

* **🚫 Porta 5678 Ocupada:** Se o n8n não abrir, outro programa está usando esta porta. Feche o outro programa ou mude a porta no arquivo `yml`.
* **🐢 Consumo de RAM:** O Docker Desktop + WSL2 podem consumir 2GB a 4GB de RAM facilmente. Se o notebook travar, considere fechar abas do Chrome.
* **🌐 Webhooks Externos:** Se você configurar um bot de WhatsApp ou Telegram, o `localhost` não será visto por eles. Você precisará de um **Tunnel** (ngrok ou Cloudflare) para tornar seu n8n público.
* **🚫 Localhost Não acessa:** Mate a instância atual: No terminal Ubuntu, rode: `docker compose down` 
Habilite a Integração no Docker Desktop:
Abra o painel do Docker Desktop no Windows
Vá em **Settings** (ícone da engrenagem).
Navegue até **Resources** > **WSL Integration**.
Garanta que a opção **"Enable integration with my default WSL distro"** esteja marcada.
Ative a chave específica para o seu Ubuntu.
Clique em **Apply & Restart**.
Rode SEM o sudo: Volte ao terminal do Ubuntu e tente rodar o comando sem a palavra sudo:
Bash `docker compose up -d`
Se o comando funcionar sem sudo, o Docker Desktop assumiu o controle. Agora, o endereço  ***http://localhost:5678*** funcionará magicamente no seu Chrome do Windows

---

**Desenvolvido com foco em estabilidade e segurança. Boa automação!** 🤖