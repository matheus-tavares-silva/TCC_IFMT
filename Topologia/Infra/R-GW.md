# Gateway Routers Configuration.

## These are the gateway's of this network.
#### Primary access configurations.

##### Here it will be added security parameters like user and password and ss h access:

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

##### Add the DNS address from local servers:

**R01,R02:**

    configure terminal
        ip domain-lookup
        ip name-server 10.53.5.3
        ip name-server 10.53.5.4

##### Add the NTP address from ntp.br servers:

**R01,R02:**

    configure terminal
        clock timezone EST -4 0
        ntp server 200.160.0.8
        ntp server 200.160.7.186

##### Add the interfaces address and Dot1Q encapsulation for access and management Vlan's and set the nat overload for internet access:

**R01:**

    configure terminal
        interface eth 0/0
            no shutdown
        interface eth 0/0.1
            encapsulation dot1Q 99
            ip address 172.99.99.21 255.255.255.224
            ip ospf 1 area 1
            ospf cost 64
        interface eth 0/0.2
            encapsulation dot1Q 172
            ip address 10.17.2.1 255.255.255.248
            ip ospf 1 area 1
            ip ospf hello-interval 1 
            ip ospf dead-interval 3
            ip nat inside
        exit
        access-list 1 permit any
        ip nat inside source list 1 interface eth 0/1 overload

**R02:**

    configure terminal
        interface eth 0/0
            no shutdown
        interface eth 0/0.1
            encapsulation dot1Q 99
            ip address 172.99.99.20 255.255.255.224
            ip ospf 1 area 1
            ospf cost 64
        interface eth 0/0.2
            encapsulation dot1Q 172
            ip address 10.17.2.2 255.255.255.248
            ip ospf 1 area 1
            ip ospf hello-interval 1 
            ip ospf dead-interval 3
            ip nat inside
        exit
        access-list 1 permit any
        ip nat inside source list 1 interface eth 0/1 overload

##### Add to the outer interface DHCP mode for internet connection. 

**R01,R02:**

    configure terminal
        interface eth 0/1
            ip address dhcp
            ip nat outside
            no shutdown
            exit

##### Add the OSPF parameters for intercommunication over local network devices.

**R01,R02:**

    configure terminal
        router ospf 1
            default-information originate always
            exit
