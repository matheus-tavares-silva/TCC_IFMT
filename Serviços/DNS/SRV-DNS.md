
# Instalação e configuração do serviço DNS no Alpine Linux 3.10.2.
## Configurações para o servidor DNS

##### Configuração das interfaces de rede:

**SRV03:**

    printf "%b\n"\
        "auto lo"\
        "iface lo inet loopback\n"\
        "auto eth0"\
        "iface eth0 inet static"\
        "   address 10.53.5.3"\
        "   netmask 255.255.255.240"\
        "   gateway 10.53.5.13" > /etc/network/interfaces && rc-service networking restart
        
**SRV04:**

    printf "%b\n"\
        "auto lo"\
        "iface lo inet loopback\n"\
        "auto eth0"\
        "iface eth0 inet static"\
        "   address 10.53.5.4"\
        "   netmask 255.255.255.240"\
        "   gateway 10.53.5.14" > /etc/network/interfaces && rc-service networking restart

##### Configurar senha para o root:

**SRV03,SRV04:**

    passwd root

##### Alterar o hostname do servidor para melhor identificação:

**SRV03:**

    echo SRV03-NSD > /etc/hostname && rc-service hostname restart

**SRV04:**
    
    echo SRV04-NSD > /etc/hostname && rc-service hostname restart

##### Alterar os DNS's do servidor para resolução de nomes internos e externos:

**SRV03,SRV04:**

    printf "%b\n"\
            "nameserver 10.53.5.3"\
            "nameserver 10.53.5.4"\
            "nameserver 1.1.1.1" > /etc/resolv.conf


    
##### Atualizar os repositórios e instalar o serviço nsd:

**SRV03,SRV04:**

    printf "%b\n"\
        "#/media/cdrom/apks"\
        "/v3.10/main"\
        "/v3.10/community"\
        "/edge/main"\
        "/edge/community"\
        "#/edge/testing\n"\
        "http://dl-cdn.alpinelinux.org/alpine/v3.10/main"\
        "http://dl-cdn.alpinelinux.org/alpine/v3.10/community"\
        "http://dl-cdn.alpinelinux.org/alpine/edge/main"\
        "http://dl-cdn.alpinelinux.org/alpine/edge/community"\
        "#http://dl-cdn.alpinelinux.org/alpine/edge/testing" > /etc/apk/repositories && apk update && apk add nsd

##### Criar o arquivo de configuração do nsd vazio e alterar o padrão:

**SRV03,SRV04:**

    cd /etc/nsd && touch nsd.conf

##### Executar o comando para gerar os arquvios necessários para o funcionamento do serviço:

**SRV03,SRV04:**

    nsd-control-setup

##### Editar o arquivo de acordo com as configurações do arquivo './nsd.conf':

**SRV03:**

Arquivo: [dhcpd-srv01.conf](https://github.com/s0berano/TCC_IFMT/tree/master/Serviços/DNS/nsd-srv03.conf)

**SRV04:**

Arquivo: [dhcpd-srv02.conf](https://github.com/s0berano/TCC_IFMT/tree/master/Serviços/DNS/nsd-srv04.conf)

##### Criar arquivo para pasta para os aquivos de zona:

**SRV03:**

    mkdir /etc/nsd/zones \
        && cd /etc/nsd/zones \
        && touch local.zone.foward \
        && touch 5.53.10.zone.reverse \

##### Habilitar e adicionar o serviço para inicialização com o sistema:

**SRV03,SRV04**

    rc-service nsd start && rc-update add nsd ; tailf -f /var/log/nsd.log &


        