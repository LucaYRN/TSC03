# 🐧 Auditoria e Configuração - Ubuntu Server 26.04.1 LTS

**Unidade Curricular:** UC8 - Planejar e executar auditoria de sistemas.
**Data de Conclusão:** 30/10/2026
**Docente:** Robson Vaamonde
**Autor(a):** Luca Navarro

Este repositório documenta todo o processo de instalação, configuração, atualização e auditoria de um servidor GNU/Linux utilizando o Ubuntu Server 26.04.1 LTS, integrado com Git, GitHub e ferramentas de IA (ChatGPT e Claude).

---

## 🛠️ 1. Atualização do Sistema
Após a instalação da máquina virtual, o primeiro passo foi garantir que todos os pacotes nativos estivessem atualizados.

```bash
# Atualizar as listas do repositório
sudo apt update

# Atualizar os pacotes instalados
sudo apt upgrade

# Atualizar distribuição e kernel
sudo apt dist-upgrade

# Limpar pacotes e cache desnecessários
sudo apt autoremove
sudo apt autoclean
sudo apt clean

# Reiniciar o servidor para aplicar mudanças
sudo systemctl reboot
```

---

## 🌐 2. Configuração de Rede (Netplan)
Configuração de IP estático utilizando a ferramenta nativa `netplan`. 

**Arquivo editado:** `/etc/netplan/00-installer-config.yaml`

```yaml
network:
  ethernets:
    enp0s3:
      dhcp4: false
      dhcp6: false
      match:
        macaddress: 08:00:27:89:46:28
      set-name: enp0s3
      addresses:
        - 10.24.86.221/24
      routes:
        - to: default
          via: 10.24.86.1
      nameservers:
        addresses:
          - 8.8.8.8
          - 8.8.4.4
        search: [senac.br]
```

**Aplicação das configurações:**
```bash
sudo netplan --debug apply
```

<img width="738" height="197" alt="image" src="https://github.com/user-attachments/assets/17c31966-0252-42f1-9019-646fccf21e12" />


---

## 🔍 3. Levantamento e Auditoria do Servidor (Comandos Nativos)
Coleta de informações de hardware, rede e sistema sem a instalação de pacotes de terceiros.

| Objetivo | Comando Nativo Utilizado |
| :--- | :--- |
| **Versão do SO** | `cat /etc/os-release` |
| **Nome do Servidor** | `hostnamectl` |
| **Versão do Kernel** | `uname -a` |
| **Discos e Partições** | `lsblk -f` e `df -h` |
| **Processador (CPU)** | `lscpu` |
| **Memória RAM** | `more /proc/meminfo` |
| **Processos Ativos** | `ps aux` |
| **Serviços Rodando** | `systemctl --type=service --state=running` |
| **Conexões de Rede** | `ss -tulpan` |

---

## ⏱️ 4. Passo a Passo do Último Dia (30/10/2026)

O foco da aula final foi a auditoria de logs e correção de data/hora.

### A. Correção da Data/Hora com NTP.br
Para corrigir a data/hora sincronizando com o NTP.br através das ferramentas nativas do sistema.

```bash
# 1. Configurar o fuso horário para São Paulo
sudo timedatectl set-timezone America/Sao_Paulo

# 2. Configurar o arquivo do serviço nativo de tempo
sudo nano /etc/systemd/timesyncd.conf
# Descomentar e alterar a linha NTP para: NTP=a.ntp.br b.ntp.br c.ntp.br

# 3. Reiniciar o serviço e forçar a ativação do NTP
sudo systemctl restart systemd-timesyncd
sudo timedatectl set-ntp true

# 4. Validar o status da sincronização
timedatectl timesync-status
```

### B. Análise de Logs do Sistema
Utilizamos as ferramentas nativas para navegar no diretório de eventos `/var/log` e o utilitário do systemd para buscar anomalias.

```bash
# Acessar o diretório de logs padrão
cd /var/log

# Monitorar logs de autenticação do sistema
sudo tail -f auth.log
sudo cat -n /var/log/syslog

# Usar journalctl para auditoria focada no serviço SSH desde o início do dia
sudo journalctl -u ssh --since "today"

# Filtrar tentativas de acesso (sucesso, falha ou usuário inválido)
sudo journalctl -u ssh | grep -E "Failed|Accepted|Invalid user"
```

<img width="789" height="285" alt="image" src="https://github.com/user-attachments/assets/9f07bc6d-48a5-42ee-a6b1-d718d840c0e4" />


---

## 💻 5. Integração com GitHub (Via Git Bash / VSCode)
O processo de versionamento do relatório e das configurações elaboradas.

```bash
# Verificar os arquivos modificados
git status

# Adicionar todas as modificações da auditoria para versionamento
git add .

# Criar o ponto de salvamento na linha do tempo
git commit -m "Finalização do relatório de auditoria e correção de logs"

# Enviar os arquivos locais para o repositório remoto no GitHub
git push
```
<img width="493" height="92" alt="image" src="https://github.com/user-attachments/assets/2daa820d-7b7e-4cae-8d7f-edc555c27092" />
