# Switch L3 Core Configuration.

## These are the cores devices of this network.
#### Primary access configurations.

##### Here it will be added security parameters like user and password and ssh access:

**SW01:**

    enable
        configure terminal
            hostname SW01-CORE
            ip domain-name sw01core.local
            ip ssh rsa keypair-name tccmatheus
            crypto key generate rsa usage-keys label tccmatheus modulus 1024
            ip ssh time-out 120
            ip ssh version 2
            ip ssh authentication-retries 3
            service password-encryption
            username admin privilege 15 secret tccmatheus

            line console 0
                !session-limit 2
                session-timeout 180
                !session-disconnect-warning 30
                login local
                exit
            line aux 0
                !session-limit 2
                session-timeout 120
                !session-disconnect-warning 30
                login local
                exit
            line vty 0 2
                login local
                transport input ssh

**SW02:**

    enable
        configure terminal
            hostname SW02-CORE
            ip domain-name sw02core.local
            ip ssh rsa keypair-name tccmatheus
            crypto key generate rsa usage-keys label tccmatheus modulus 1024
            ip ssh time-out 120
            ip ssh version 2
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
        vlan 173
            name VLAN-173-INTERNET
            exit

##### Add the respective IPs for SVI to permit the internetwork access and local management.

**SW01:**

    configure terminal
        interface vlan 535
            ip address 10.53.5.13 255.255.255.240
            description SERVICES-535
            ip ospf dead-interval 1
            ip ospf dead-interval minimal hello-multiplier 5
            no shutdown
            exit
        interface vlan 339
            ip address 192.33.9.125 255.255.255.128
            ip helper-address 10.53.5.1
            standby 1 192.33.9.123
            standby 1 priority 200
            standby 1 preempt
            standby 1 name vl605-one
            standby 2 ip 192.33.9.124
            standby 2 name vl605-two
            standby 1 timers msec 500 1
            standby 2 timers msec 500 1
            description EMPLOYEES-339
            ip ospf dead-interval 1
            ip ospf dead-interval minimal hello-multiplier 5
            no shutdown
            exit
        interface vlan 605
            ip address 192.60.5.125 255.255.255.128
            ip helper-address 10.53.5.1
            standby 1 ip 192.60.5.123
            standby 1 priority 200
            standby 1 preempt
            standby 1 name vl605-one
            standby 2 ip 192.60.5.124
            standby 2 name vl605-two
            standby 1 timers msec 500 1
            standby 2 timers msec 500 1
            description COSTUMERS-605
            ip ospf dead-interval 1
            ip ospf dead-interval minimal hello-multiplier 5
            no shutdown
            exit
        interface vlan 172
            ip address 10.17.2.5 255.255.255.248
            description INTERNET-172
            ip ospf dead-interval 1
            ip ospf dead-interval minimal hello-multiplier 5
            no shutdown
            exit
        interface vlan 173
            ip address 10.17.3.5 255.255.255.248
            description INTERNET-173
            ip ospf dead-interval 1
            ip ospf dead-interval minimal hello-multiplier 5
            no shutdown
            exit

**SW02:**

    configure terminal
        interface vlan 535
            ip address 10.53.5.14 255.255.255.240
            description SERVICES-535

            ip ospf dead-interval 1
            ip ospf dead-interval minimal hello-multiplier 5
            no shutdown
            exit
        interface vlan 339
            ip address 192.33.9.126 255.255.255.128
            ip helper-address 10.53.5.2
            standby 1 ip 192.33.9.123
            standby 1 name vl605-one
            standby 2 ip 192.33.9.124
            standby 2 priority 200
            standby 2 preempt
            standby 2 name vl605-two
            standby 1 timers msec 500 1
            standby 2 timers msec 500 1
            description EMPLOYEES-339  
            ip ospf dead-interval 1
            ip ospf dead-interval minimal hello-multiplier 5    
            no shutdown
            exit
        interface vlan 605
            ip address 192.60.5.126 255.255.255.128
            ip helper-address 10.53.5.2
            standby 1 ip 192.60.5.123
            standby 1 name vl605-one
            standby 2 ip 192.60.5.124
            standby 2 priority 200
            standby 2 preempt
            standby 2 name vl605-two
            standby 1 timers msec 500 1
            standby 2 timers msec 500 1
            description COSTUMERS-605
            ip ospf dead-interval 1
            ip ospf dead-interval minimal hello-multiplier 5
            no shutdown
            exit
        interface vlan 172
            ip address 10.17.2.6 255.255.255.248
            description INTERNET-172
            ip ospf dead-interval 1
            ip ospf dead-interval minimal hello-multiplier 5
            no shutdown
            exit
        interface vlan 173
            ip address 10.17.3.6 255.255.255.248
            description INTERNET-173
            ip ospf dead-interval 1
            ip ospf dead-interval minimal hello-multiplier 5
            no shutdown
            exit

##### Add the LACP configuration to increase traffic between the core devices:

**SW01:**

    configure terminal
        interface range eth 3/2 - 3
            switchport
            channel-group 1 mode active
            channel-protocol lacp
            switchport trunk encapsulation dot1q
            switchport mode trunk
            switchport trunk allowed vlan 535,339,605,172,173
            exit
        interface port-channel 1
            switchport
            description TRUNK-LACP-SW01CORE
            switchport trunk encapsulation dot1q
            switchport mode trunk
            switchport trunk allowed vlan 535,339,605,172,173
            exit

**SW02:**

    configure terminal
        interface range eth 3/2 - 3
            switchport
            channel-group 1 mode active
            channel-protocol lacp
            switchport trunk encapsulation dot1q
            switchport mode trunk
            switchport trunk allowed vlan 535,339,605,172,173
            exit
        interface port-channel 1
            switchport
            description TRUNK-LACP-SW02CORE
            switchport trunk encapsulation dot1q
            switchport mode trunk
            switchport trunk allowed vlan 535,339,605,172,173
            exit

##### Add the VLAN that will pruned in trunk, allowing only the necessary for management and interconnection: 

**SW01,SW02:**

    configure terminal
        interface eth 0/0
            switchport
            switchport trunk encapsulation dot1q
            switchport mode trunk
            switchport trunk allowed vlan 172,173
            exit
         interface eth 0/1
            switchport
            switchport trunk encapsulation dot1q
            switchport mode trunk
            switchport trunk allowed vlan 172,173
            exit
        interface range eth 0/2 - 3, eth 1/0 - 3, eth 2/0 - 2
            switchport
            switchport mode access
            switchport access vlan 535
            exit
        interface range eth 2/3, eth 3/0 - 1
            switchport
            switchport trunk encapsulation dot1q
            switchport mode trunk
            switchport trunk allowed vlan 99,339,605
            exit

##### Add the OSPF id for correct working of protocol:

**SW01:**

    configure terminal
        router ospf 1
            router-id 10.0.0.3
            network 10.53.5.0 255.255.255.240 area 1
            network 192.33.9.0 255.255.255.128 area 1
            network 192.60.5.0 255.255.255.128 area 1
            network 10.17.2.0 255.255.255.248 area 1
            network 10.17.3.0 255.255.255.248 area 1
            exit

**SW02:**

   configure terminal
        router ospf 1
            router-id 10.0.0.4
            network 10.53.5.0 255.255.255.240 area 1
            network 192.33.9.0 255.255.255.128 area 1
            network 192.60.5.0 255.255.255.128 area 1
            network 10.17.2.0 255.255.255.248 area 1
            network 10.17.3.0 255.255.255.248 area 1
            exit

##### Add the PVSTP configuration to prevent loopings on the network: 

**SW01,SW02:**

    configure terminal
        spanning-tree mode rapid-pvst
        spanning-tree vlan 339,605,535