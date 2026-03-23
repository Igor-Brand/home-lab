# home-lab

------------------------------

 ##Laboratório de Pentest: Configuração de Rede Isolada
Este guia descreve como configurar o endereçamento IP estático para um ambiente de laboratório controlado (Rede Interna) utilizando Kali Linux, Metasploitable 3 e Ubuntu (Monitoramento).
🌐 Topologia da Rede

* Rede: 192.168.1.0/24
* Modo de Rede: Rede Interna (Internal Network)
* Modo Promíscuo: Permitir Tudo (Allow All)

| Máquina | Sistema Operacional | Endereço IP |
|---|---|---|
| Atacante | Kali Linux | 192.168.1.10 |
| Alvo 1 | Metasploitable 3 (Linux) | 192.168.1.11 |
| Alvo 2 | Metasploitable 3 (Windows) | 192.168.1.12 |
| Monitor | Ubuntu Server/Desktop | 192.168.1.13 |

------------------------------
🛠️ Passo a Passo da Configuração1. Preparação no VirtualBox
Antes de ligar as máquinas, em Configurações > Rede de cada VM:

   1. Selecione Conectado a: Rede Interna.
   2. Nomeie a rede como lab-pentest (deve ser igual em todas).
   3. Em Avançado, altere o Modo Promíscuo para Permitir Tudo.

2. Configurando o Kali Linux (.10)
No terminal do Kali, identifique sua interface (ex: eth0) e aplique:

sudo ip addr flush dev eth0
sudo ip addr add 192.168.1.10/24 dev eth0
sudo ip link set eth0 up

3. Configurando o Metasploitable 3 Windows (.12)
Abra o CMD como Administrador e execute:

### Define o IP estático (certifique-se que o nome da interface é "Ethernet")
```
netsh interface ip set address name="Ethernet" static 192.168.1.12 255.255.255.0
```

### Desativa o Firewall para permitir testes de ping e scan
```
netsh advfirewall set allprofiles state off
```

4. Configurando o Ubuntu de Monitoramento (.13)
No Ubuntu, as interfaces costumam ter nomes como enp0s3. Identifique com ip link e aplique:

### Impede que o NetworkManager resete a configuração
```
sudo nmcli device set enp0s3 managed no
```
### Configura o IP e ativa o modo promíscuo para captura de tráfego
```
sudo ip addr add 192.168.1.13/24 dev enp0s3
sudo ip link set enp0s3 up
sudo ip link set enp0s3 promisc on
```
------------------------------
🧪 Testando a Conectividade
A partir do Kali Linux, verifique se todos os alvos estão visíveis:

### Teste de ping individual
```
ping -c 3 192.168.1.11
```
```
ping -c 3 192.168.1.12
```
```
ping -c 3 192.168.1.13
```
### Varredura rápida de rede
```
sudo nmap -sn 192.168.1.0/24
```
------------------------------


