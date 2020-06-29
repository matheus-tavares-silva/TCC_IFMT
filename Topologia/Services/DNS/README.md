[Infos]
    * Instalação e configuração do serviço DNS no Alpine Linux 3.10.2.

    [Network]
        [SRV03]
            Address:.....10.53.5.3
            Netmask:.....255.255.255.240
            Gateway:.....10.53.5.13

        printf "%b\n"\
            "auto lo"\
            "iface lo inet loopback\n"\
            "auto eth0"\
            "iface eth0 inet static"\
            "   address 10.53.5.3"\
            "   netmask 255.255.255.240"\
            "   gateway 10.53.5.13" > /etc/network/interfaces && rc-service networking restart
        
        [SRV04]
            Address:.....10.53.5.4
            Netmask:.....255.255.255.240
            Gateway:.....10.53.5.14

         printf "%b\n"\
            "auto lo"\
            "iface lo inet loopback\n"\
            "auto eth0"\
            "iface eth0 inet static"\
            "   address 10.53.5.4"\
            "   netmask 255.255.255.240"\
            "   gateway 10.53.5.14" > /etc/network/interfaces && rc-service networking restart

[Guides]
    https://wiki.alpinelinux.org/wiki/Setting_up_nsd_DNS_server
    https://linux.die.net/man/5/nsd.conf
    https://calomel.org/nsd_dns.html

[Base]
    # Configurar senha para o root:

        passwd root

    # Alterar o hostname do servidor para melhor identificação:

        [SRV03]
            echo SRV03-NSD > /etc/hostname && rc-service hostname restart
        [SRV04]
            echo SRV04-NSD > /etc/hostname && rc-service hostname restart

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
                "#http://dl-cdn.alpinelinux.org/alpine/edge/testing" > /etc/apk/repositories && apk update && apk add nsd

    # Criar o arquivo de configuração do nsd vazio e alterar o padrão:

        cd /etc/nsd && touch nsd.conf

    # Executar o comando para gerar os arquvios necessários para o funcionamento do serviço:

        nsd-control-setup

    # Editar o arquivo de acordo com as configurações do arquivo './nsd.conf':

        * Notar que existem diferenças entre os arquivos do SRV03 e SRV04.

    # Criar arquivo para pasta para os aquivos de zona:
        * As zonas são configuradas apenas no servidor princiapal, no caso o SRV03:

        mkdir /etc/nsd/zones \
            && cd /etc/nsd/zones \
            && touch local.zone.foward \
            && touch 5.53.10.zone.reverse \
            && touch 99.99.172.zone.reverse

    # Habilitar e adicionar o serviço para inicialização com o sistema:

        rc-service nsd start && rc-update add nsd ; tailf -f /var/log/nsd.log &


        