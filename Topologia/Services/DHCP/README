[Infos]
    # Instalação e configuração do serviço DHCP no Alpine Linux 3.10.2.

    [Network]
        [SRV01]
            Address:.....10.53.5.1
            Netmask:.....255.255.255.240
            Gateway:.....10.53.5.13

        printf "%b\n"\
            "auto lo"\
            "iface lo inet loopback\n"\
            "auto eth0"\
            "iface eth0 inet static"\
            "   address 10.53.5.1"\
            "   netmask 255.255.255.240"\
            "   gateway 10.53.5.13" > /etc/network/interfaces && rc-service networking restart

        [SRV02]
            Address:.....10.53.5.2
            Netmask:.....255.255.255.240
            Gateway:.....10.53.5.14

        printf "%b\n"\
            "auto lo"\
            "iface lo inet loopback\n"\
            "auto eth0"\
            "iface eth0 inet static"\
            "   address 10.53.5.2"\
            "   netmask 255.255.255.240"\
            "   gateway 10.53.5.13" > /etc/network/interfaces && rc-service networking restart

[Guides]
    https://www.hiroom2.com/2017/08/22/alpinelinux-3-6-dhcp-en/

[Base]
    # Configurar senha para o root:

        passwd root

    # Alterar o hostname do servidor para melhor identificação:
        [SRV01]
            echo SRV01-DHCP > /etc/hostname ; rc-service hostname restart
        [SRV02]
            echo SRV02-DHCP > /etc/hostname ; rc-service hostname restart

     # Alterar os DNS's do servidor:

        printf "%b\n"\
                "nameserver 10.53.5.3"\
                "nameserver 10.53.5.4"\
                "nameserver 1.1.1.1" > /etc/resolv.conf

[Service]
     # Atualizar os repositórios e instalar o serviço nsd:

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

    # Criar o arquivo de configuração do dhcpd:

        touch /etc/dhcp/dhcpd.conf

    # Editar o arquivo de acordo com as configurações do arquivo './dhcpd.conf':

    # Habilitar e adicionar o serviço para inicialização com o sistema:

        rc-service dhcpd start ; rc-update add dhcpd

    # Para verificar a lista de clientes no DHCP:
        tail -f /var/lib/dhcp/dhcpd.leases
