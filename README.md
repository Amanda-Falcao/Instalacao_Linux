# Atividade Instalação Linux
Esta documentação irá descrever e detalhar o processo de instalação do Oracle Linux, na versão 8.6. Esta versão foi utilizada, pois apesar de que a 9.0 ser a mais recente, a 8.6 é a mais estável no momento.

O sistema operacional foi instalado através do software de virtualização VirtualBox, versão 6.1.36. 

#### Downloads
- Oracle Linux 8.6 - https://yum.oracle.com/oracle-linux-isos.html 
- Virtual Box 6.1.36 - https://www.virtualbox.org/wiki/Downloads

## Instalação
Segue abaixo as especificações da máquina virtual na qual a instalação foi realizada:

- Memória RAM - 4GB
- HD - 30GB
 
#

**1 - WHAT LANGUAGE WOULD YOU LIKE TO USE DURING THE INSTALLATION PROCESS?**

- English > English (United States)

**2 - INSTALLATION SUMMARY**
- LOCALIZATION

#### Keyboard > English(US)
#### Language Support > English(United States)
#### Time and Date > Americas/Sao Paulo timezone

- SOFTWARE

#### Installation Source > Local media
#### Software Selection > Minimal Install

- SYSTEM

#### Installation Destiny > Automatic Partionning Selection
#### KDUMP > Kdump is enabled
#### Network & Host >  Ethernet - on

- USER SETTINGS
#### Root Password > *definir senha para o usuário root*
#### User Creation > *definir novo usuário*


*Clicar em Begin Installation para iniciar a instalação*

*Após finalizar a intalação clicar em Reboot System*

## Configurar Rede
 Ir em configurações de rede e selecionar a opção: placa em modo Bridge.

No terminal, seguir os seguintes passos para a configuração do IP fixo de rede:

1- Verificar o status atual das interfaces de rede físicas do computador.
~~~
nmcli device status 
~~~
2- Verificar a configuração da interface de rede específica.
~~~
ip addr show dev enp0s3
~~~
3 – Para configurar o IP em uma interface de forma permanente, editar o arquivo /etc/sysconfig/network-scripts/ifcfg-enp0s3:
 ~~~
 vi /etc/sysconfig/network-scripts/ifcfg-enp0s3
~~~
***Alterações no arquivo:***

Alterar o BOOTPROTO de dhcp para none.
~~~
 BOOTPROTO=none
~~~
***Adicionar as seguintes linhas:***

- IPADDR0=192.168.18.20 (algum IP válido na sua rede)
- PREFIX0 = 24 (prefixo da sua rede)
- GATEWAY0=192.168.18.1 (gateway da sua rede)
- DNS1=192.168.18.1 (sistema de nome de domínio)
- DNS2=8.8.8.8 (sistema de nome de domínio)
- NETMASK=255.255.255.0 (máscara da sua rede)
- NETWORK=192.168.18.0 (sua rede)
- BROADCAST= 192.168.18.255

Salvar o arquivo e reiniciar a máquina, ou utilizar o comando:
~~~
systemctl restart NetworkManager
~~~

## Configuração SSH - relação de confiança

O SSH fornece a possibilidade de estabelecermos uma configuração de confiança entre hosts, para que você consiga utilizar um mesmo usuário em host distintos.

Passos a serem seguidos para a configuração:

1- Instalar o SSH
~~~
yum install openssh-server
~~~
2- Ir para a pasta HOME
~~~
cd ~
~~~
3- Gerar uma senha

~~~
ssh-keygen -b 1024 -t rsa
~~~

Nesta etapa pode-se definir algumas especificações, mas não é necessário digitar nada em nenhum campo, basta pressionar ENTER.

4- O comando cat possibilitará ver a sua chave pública que será utilizada.
~~~
cat .ssh/id_rsa.pub
~~~

5- Copiar a chave gerada para a outra VM
~~~
ssh-copy-id -i ~/.ssh/id_rsa.pub amanda@192.168.18.50
~~~
Substituir o "amanda" pelo usuário que deseja ter acesso, e o "192.168.18.50" pelo seu respectivo IP. Após isso insira a senha do usuário.

Após realizar esses passos, tem-se o acesso de confiança entre as duas máquinas sem a utilização de senhas. Caso queira, realize o mesmo processo na outra máquina, assim uma terá acesso a outra sem o uso de senha.

6 - Acessar sem senha
~~~
ssh amanda@192.168.18.50
~~~
