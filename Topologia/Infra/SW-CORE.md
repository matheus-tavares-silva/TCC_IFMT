# Switch L3 Core Configuration.

## These are the cores devices of this network.
#### Primary access configurations.

##### Here it will be added security parameters like user and password and ssh access:

**SW01:**

enable
    configure terminal
        hostname SW01-CORE
        ip domain-name sw01core.local
        crypto key generate rsa usage-keys label tccmatheus modulus 1024
        service password-encryption
        username admin privilege 15 secret tccmatheus
        login block-for 10 attempts 5 within 50

        line console 0
            session-timeout 180
            login local
            exit
        line aux 0
            session-timeout 120
            login local
            exit
        line vty 0 2
            login local
            transport input all
            exit

**SW02:**

    enable
        configure terminal
            hostname SW02-CORE
            ip domain-name sw02core.local
            crypto key generate rsa usage-keys label tccmatheus modulus 1024
            service password-encryption
            username admin privilege 15 secret tccmatheus
            login block-for 10 attempts 5 within 50

            line console 0
                session-timeout 180
                login local
                exit
            line aux 0
                session-timeout 120
                login local
                exit
            line vty 0 2
                login local
                transport input all
                exit

##### Add the DNS address from local servers:

**SW01,SW02:**

    configure terminal
        ip domain-lookup
        ip name-server 10.53.5.3
        ip name-server 10.53.5.4

##### Add the NTP address from ntp.br servers:

**SW01,SW02:**

    configure terminal
        clock timezone EST -4 0
        ntp server 200.160.0.8
        ntp server 200.160.7.186

##### Add the main Vlan's of the local network:         

**SW01,SW02:**

    configure terminal
        vlan 535
            name VLAN-535-SERVICES
            exit
        vlan 339
            name VLAN-339-EMPLOYEES
            exit
        vlan 605
            name VLAN-605-COSTUMERS
            exit
        vlan 172
            name VLAN-172-INTERNET
            exit

##### Add the respective IPs for SVI to permit the internetwork access and local management.

**SW01:**

    configure terminal
        interface lo 0
            ip address 10.1.1.3 255.255.255.255
        interface vlan 535
            ip address 10.53.5.13 255.255.255.240
            ip ospf 1 area 1
            description SERVICES-535
            no shutdown
            exit
        interface vlan 339
            ip address 192.33.9.125 255.255.255.128
            ip ospf 1 area 1
            ip helper-address 10.53.5.1
            description EMPLOYEES-339
            no shutdown
            exit
        interface vlan 605
            ip address 192.60.5.125 255.255.255.128
            ip ospf 1 area 1
            ip helper-address 10.53.5.1
            standby 1 ip 192.60.5.124 ! Experimental Feature
            standby 1 priority 110
            description COSTUMERS-605
            no shutdown
            exit
        interface vlan 172
            ip address 10.17.2.5 255.255.255.248
            ip ospf 1 area 1
            description INTERNET-172
            no shutdown
            exit

**SW02:**

    configure terminal
        interface lo 0
            ip address 10.1.1.4 255.255.255.255
        interface vlan 535
            ip address 10.53.5.14 255.255.255.240
            ip ospf 1 area 1
            description SERVICES-535
            no shutdown
            exit
        interface vlan 339
            ip address 192.33.9.126 255.255.255.128
            ip ospf 1 area 1
            ip helper-address 10.53.5.2
            description EMPLOYEES-339           
            no shutdown
            exit
        interface vlan 605
            ip address 192.60.5.126 255.255.255.128
            ip ospf 1 area 1
            ip helper-address 10.53.5.2
            standby 1 ip 192.60.5.124 ! Experimental Feature
            standby 1 priority 120
            standby 1 preempt
            description COSTUMERS-605
            no shutdown
            exit
        interface vlan 172
            ip address 10.17.2.6 255.255.255.248
            ip ospf 1 area 1
            description INTERNET-172
            no shutdown
            exit

##### Add the LACP configuration to increase traffic between the core devices:

**SW01:**

    configure terminal
        interface range eth 3/2 - 3
            channel-group 1 mode active
            channel-protocol lacp
            switchport trunk encapsulation dot1q
            switchport mode trunk
            switchport trunk allowed vlan 99,535,339,605,172
            exit
        interface port-channel 1
            description TRUNK-LACP-SW01CORE
            switchport trunk encapsulation dot1q
            switchport mode trunk
            switchport trunk allowed vlan 99,535,339,605,172
            exit

**SW02:**

    configure terminal
        interface range eth 3/2 - 3
            channel-group 1 mode active
            channel-protocol lacp
            switchport trunk encapsulation dot1q
            switchport mode trunk
            switchport trunk allowed vlan 99,535,339,605,172
            exit
        interface port-channel 1
            description TRUNK-LACP-SW02CORE
            switchport trunk encapsulation dot1q
            switchport mode trunk
            switchport trunk allowed vlan 99,535,339,605,172
            exit

##### Add the VLAN that will pruned in trunk, allowing only the necessary for management and interconnection: 

**SW01,SW02:**

    configure terminal
        interface eth 0/0
            switchport trunk encapsulation dot1q
            switchport mode trunk
            switchport trunk allowed vlan 99,172
            exit
        interface eth 3/0
            switchport mode access
            switchport access vlan 99
            exit
        interface range eth 0/2 - 3, eth 1/0 - 3, eth 2/0 - 2
            switchport mode access
            switchport access vlan 535
            exit
        interface range eth 2/3, eth 3/0 - 1
            switchport trunk encapsulation dot1q
            switchport mode trunk
            switchport trunk allowed vlan 99,339,605
            exit


##### Add the PVSTP configuration to prevent loopings on the network: 

**SW01,SW02:**

    configure terminal
        spanning-tree mode rapid-pvst
        spanning-tree vlan 339,605,535