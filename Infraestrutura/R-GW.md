# Configurações dos router.
## Configuração dos dispositivos de acesso externo a rede.

##### Configuração base de segurança e acesso via ssh:

**R01:**

    enable
        configure terminal
            no service config
            hostname R01-GW
            ip domain-name r01gw.local
            crypto key generate rsa 
            
            ip ssh time-out 120
            ip ssh authentication-retries 3
            service password-encryption
            username admin privilege 15 secret tccmatheus

            line console 0
                session-limit 2
                session-timeout 180
                session-disconnect-warning 30
                login local
                exit
            line aux 0
                session-limit 2
                session-timeout 120
                session-disconnect-warning 30
                login local
                exit
            line vty 0 2
                login local
                transport input ssh
                exit

**R02:**

    enable
        configure terminal
            no service config
            hostname R02-GW
            ip domain-name r02gw.local
            crypto key generate rsa 
            
            ip ssh time-out 120
            ip ssh authentication-retries 3
            service password-encryption
            username admin privilege 15 secret tccmatheus

            line console 0
                session-limit 2
                session-timeout 180
                session-disconnect-warning 30
                login local
                exit
            line aux 0
                session-limit 2
                session-timeout 120
                session-disconnect-warning 30
                login local
                exit
            line vty 0 2
                login local
                transport input ssh
                exit

##### Configuração dos DNS locais:

**R01,R02:**

    configure terminal
        ip domain-lookup
        ip name-server 10.53.5.3
        ip name-server 10.53.5.4

##### Configuração dos servidores NTP com base nos servidores do ntp.br:

**R01,R02:**

    configure terminal
        clock timezone EST -4 0
        ntp server 200.160.0.8
        ntp server 200.160.7.186  

##### Configuração das interfaces encapsuladas e do NAT:

**R01:**

    configure terminal
        interface eth 0/1
            ip address dhcp
            ip nat outside
            no shutdown
        interface eth 0/0
            no shutdown
        interface eth 0/0.1
            encapsulation dot1Q 172
            ip address 10.17.2.1 255.255.255.248
            ip ospf dead-interval 1
            ip ospf dead-interval minimal hello-multiplier 5
            ip nat inside
            exit
        interface eth 0/2
            no shutdown
        interface eth 0/2.1
            encapsulation dot1Q 173
            ip address 10.17.3.1 255.255.255.248
            ip ospf dead-interval 1
            ip ospf dead-interval minimal hello-multiplier 5
            ip ospf cost 64
            ip nat inside
            exit
        access-list 1 permit any
        ip nat inside source list 1 interface eth 0/1 overload

**R02:**

    configure terminal
        interface eth 0/1
            ip address dhcp
            ip nat outside
            no shutdown
        interface eth 0/0
            no shutdown
        interface eth 0/0.1
            encapsulation dot1Q 172
            ip address 10.17.2.2 255.255.255.248
            ip ospf dead-interval 1
            ip ospf dead-interval minimal hello-multiplier 5
            ip nat inside
        interface eth 0/2
            no shutdown
        interface eth 0/2.1
            encapsulation dot1Q 173
            ip address 10.17.3.2 255.255.255.248
            ip ospf dead-interval 1
            ip ospf dead-interval minimal hello-multiplier 5
            ip ospf cost 64
            ip nat inside
            exit
        access-list 1 permit any
        ip nat inside source list 1 interface eth 0/1 overload

##### Configuração das redes OSPF:

**R01:**

    configure terminal
        router ospf 1
            router-id 10.0.0.1
            network 10.17.2.0 255.255.255.248 area 1
            network 10.17.3.0 255.255.255.248 area 1
            default-information originate always
            exit

**R02:**

    configure terminal
        router ospf 1
            router-id 10.0.0.2
            network 10.17.2.0 255.255.255.248 area 1
            network 10.17.3.0 255.255.255.248 area 1
            default-information originate always
            exit
