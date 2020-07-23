# TCC - Matheus Tavares
## Redes de Alta Disponibilidade

### Objetivo

Este trabalho foi efetuado com o objetivo de concluir o curso de redes de computadores do Instituto Federal de Educação de Mato Grosso (IFMT), O cenário reproduzido neste trabalho remete a uma rede de alta diponibilidade voltado para os dispositivos da Cisco Networking, apesar de serem utilizados diversos protocolos não proprietários.

O trabalho está dividio em duas partes para melhor compreenção:

**Infraestrutura**

Na infraestrutura onde foi estrutura toda a topologia da rede onde feito a estruturação "física" do projeto e como ficariam alocados os dispositivos de redes.

**Serviços**

Base de serviços que não foram atribuidas aos dispositivos de redes, optei pode levantar dois servidores com Alpine linux para o serviços de DHCP e DNS.

### Apêndice

1. Infraestrutura
    1. [Routers](https://github.com/s0berano/TCC_IFMT/blob/master/Infraestrutura/R-GW.md)
    2. [Switchs Núcleo](https://github.com/s0berano/TCC_IFMT/blob/master/Infraestrutura/SW-ACCESS.md)
    3. [Switchs Distribuição/Acesso](https://github.com/s0berano/TCC_IFMT/blob/master/Infraestrutura/SW-CORE.md)

2. Serviços
    1. [DNS](https://github.com/s0berano/TCC_IFMT/blob/master/Servi%C3%A7os/DNS/SRV-DNS.md)
    2. [DHCP](https://github.com/s0berano/TCC_IFMT/blob/master/Servi%C3%A7os/DHCP/SRV-DHCP.md)


#### Configuração da interface bridge no linux:

##### Configure a uma interface bridge no netplan:

    network:
    version: 2
    renderer: NetworkManager
    ethernets:
        eno1:
        [...]
    bridges:
        br0:
        dhcp4: false
        interfaces: [eno1]

#### Adicione a interface bridge ao libvirt, crie um arquivo temporário nomeado de br0.xml em /tmp:

    <network>
        <name>br0</name>
            <forward mode='bridge'/>
        <bridge name='br0'/>
    </network>


#### Adicione essa nova rede com o comando 'virsh':

    virsh net-define /tmp/br0.xml
    virsh net-start br0
    virsh net-autostart br0

#### Por último confirme se tudo está funcionando:

    virsh net-list --all