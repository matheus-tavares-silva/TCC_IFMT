
# Instalação e configuração do serviço DHCP no Alpine Linux 3.10.2.
## Configurações para o servidor DHCP

##### Configuração das interfaces de rede:

**SRV01:**
    
    printf "%b\n"\
        "auto lo"\
        "iface lo inet loopback\n"\
        "auto eth0"\
        "iface eth0 inet static"\
        "   address 10.53.5.1"\
        "   netmask 255.255.255.240"\
        "   gateway 10.53.5.13" > /etc/network/interfaces && rc-service networking restart

**SRV02:**

    printf "%b\n"\
        "auto lo"\
        "iface lo inet loopback\n"\
        "auto eth0"\
        "iface eth0 inet static"\
        "   address 10.53.5.2"\
        "   netmask 255.255.255.240"\
        "   gateway 10.53.5.13" > /etc/network/interfaces && rc-service networking restart

##### Configurar senha para o root:

**SRV03,SRV04:**

    passwd root

##### Alterar o hostname do servidor para melhor identificação:
        
**SRV01:**

    echo SRV01-DHCP > /etc/hostname ; rc-service hostname restart

**SRV02:**

    echo SRV02-DHCP > /etc/hostname ; rc-service hostname restart

##### Alterar os DNS's do servidor para resolução de nomes internos e externos:

**SRV01,SRV02:**

    printf "%b\n"\
            "nameserver 10.53.5.3"\
            "nameserver 10.53.5.4"\
            "nameserver 1.1.1.1" > /etc/resolv.conf

##### Atualizar os repositórios e instalar o serviço nsd:

**SRV01,SRV02:** 

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
        "#http://dl-cdn.alpinelinux.org/alpine/edge/testing" > /etc/apk/repositories && apk update && apk add dhcp

##### Criar o arquivo de configuração do dhcpd:

**SRV01,SRV02:**

    touch /etc/dhcp/dhcpd.conf

##### Editar o arquivo de acordo com as configurações do arquivo './dhcpd.conf':

**SRV01:**

Arquivo: [dhcpd-srv01.conf](https://github.com/s0berano/TCC_IFMT/tree/master/Serviços/DHCP/dhcpd-srv01.conf)

**SRV02:**

Arquivo: [dhcpd-srv02.conf](https://github.com/s0berano/TCC_IFMT/tree/master/Serviços/DHCP/dhcpd-srv02.conf)

##### Habilitar e adicionar o serviço para inicialização com o sistema:

**SRV01,SRV02:**

    rc-service dhcpd start ; rc-update add dhcpd

##### Para verificar a lista de clientes no DHCP:

**SRV01,SRV02:**

    tail -f /var/lib/dhcp/dhcpd.leases
