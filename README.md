# home-lab

------------------------------



🛡️ ## Laboratório de CyberSecurity: Monitoramento Estruturado com Wazuh
Este guia detalha a configuração de um ambiente de rede isolada para práticas de Pentest e Defesa (Blue Team), utilizando o Wazuh como central de eventos.
🌐 Topologia da Rede (Rede Interna)

* Segmento: 192.168.1.0/24
* Virtualizador: VirtualBox (Rede Interna: lab-pentest)

| Máquina | Função | IP Estático |
|---|---|---|
| Kali Linux | Atacante (Red Team) | 192.168.1.10 |
| Metasploitable 3 | Alvo Vulnerável | 192.168.1.11 |
| Wazuh Server | Servidor de Logs (SIEM) | 192.168.1.13 |
| Ubuntu Ops | Centro de Operações | 192.168.1.14 |

------------------------------
🛠️ 1. Configuração do Hardware Virtual (VirtualBox)
Antes de iniciar os sistemas, todas as VMs devem estar no mesmo "cabo virtual".

   1. Vá em Configurações > Rede de cada máquina.
   2. Selecione Rede Interna e nomeie como lab-pentest.
   3. Em Avançado, altere o Modo Promíscuo para Permitir Tudo.

📸 [ADICIONE AQUI: Print das configurações de rede do VirtualBox]

------------------------------
⚙️ 2. Configurando o Servidor Wazuh (.13)
No terminal da VM do Wazuh, vamos fixar o IP para que os agentes saibam onde enviar os logs.

### Identifique a placa (ex: eth0 ou enp0s3)
ip link show
### Aplique o IP manual
sudo ip addr flush dev eth0
sudo ip addr add 192.168.1.13/24 dev eth0
sudo ip link set eth0 up
### Impeça que o sistema remova o IP
sudo nmcli device set eth0 managed no

📸 [ADICIONE AQUI: Print do comando 'ip addr' no servidor Wazuh]

------------------------------
🖥️ 3. Configurando o Ubuntu Ops (.14)
Esta máquina será o seu Centro de Operações. Nela, você acessará o Dashboard e monitorará o tráfego em tempo real.

# Configuração de IP e Modo Promíscuo
sudo nmcli device set enp0s3 managed no
sudo ip addr add 192.168.1.14/24 dev enp0s3
sudo ip link set enp0s3 up
sudo ip link set enp0s3 promisc on

Para acessar o painel do Wazuh, abra o navegador no Ubuntu e digite: https://192.168.1.13.

📸 [ADICIONE AQUI: Print do navegador no Ubuntu acessando o Dashboard do Wazuh]

------------------------------
📡 4. Instalando o Agente no Alvo (Metasploitable)
Para que o monitoramento funcione, precisamos instalar o "espião" (Agente) no Metasploitable apontando para o servidor .13.
No Metasploitable (Linux):

sudo WAZUH_MANAGER='192.168.1.13' dpkg -i wazuh-agent.deb
sudo systemctl start wazuh-agent

No Metasploitable (Windows - PowerShell):

.\wazuh-agent.msi /q WAZUH_MANAGER='192.168.1.13'
NET START Wazuh

📸 [ADICIONE AQUI: Print da lista de Agentes no Wazuh mostrando o status 'Active']

------------------------------
🧪 5. Teste de Conectividade Geral
A partir do Kali Linux (.10), valide se toda a infraestrutura está respondendo:

### Teste de alcance
ping -c 2 192.168.1.11 (Alvo)
ping -c 2 192.168.1.13 (Wazuh)
ping -c 2 192.168.1.14 (Ubuntu Ops)

------------------------------





