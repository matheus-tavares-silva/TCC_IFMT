# Switch L3 Core Configuration.

## These are the access devices of this network.
#### Primary access configurations.

##### Here it will be added security parameters like user and password and ssh access:

**SW03:**

    enable
        configure terminal
            hostname SW03-ACCESS
            no ip routing
            ip domain-name sw03access.local
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

**SW04:**

    enable
        configure terminal
            hostname SW04-ACCESS
            no ip routing
            ip domain-name sw04access.local
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

**SW05:**

    enable
        configure terminal
            hostname SW05-ACCESS
            no ip routing
            ip domain-name sw05access.local
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

**SW03,SW04,SW05:**

    configure terminal
        ip domain-lookup
        ip name-server 10.53.5.3
        ip name-server 10.53.5.4

##### Add the NTP address from ntp.br servers:

**SW03,SW04,SW05:**

    configure terminal
        clock timezone EST -4 0
        ntp server 200.160.0.8
        ntp server 200.160.7.186

##### Add the services Vlan's of the local network:         

**SW03,SW04,SW05:**

    configure terminal
        vlan 339
            name VLAN-339-EMPLOYEES
            exit
        vlan 605
            name VLAN-605-COSTUMERS
            exit

##### Add the management Vlan for ssh remote access: 

**SW03,SW04,SW05:**

    configure terminal
        interface vlan 1
            exit


##### Add the Vlan's to each port range for end device access: 

**SW03,SW04,SW05:**

    Configure terminal
        interface range ethernet 5/2 - 3
            switchport trunk encapsulation dot1q
            switchport mode trunk
            switchport trunk allowed vlan 99,339,605
            exit
        interface range ethernet 0/3, ethernet 1/0 - 3, ethernet 2/0 - 3, ethernet 3/0 - 3, ethernet 4/0 - 3
            switchport mode access
            switchport access vlan 339
            exit
        interface ethernet 5/0
            switchport mode access
            switchport access vlan 339
            exit
        interface range ethernet 0/0 - 2
            switchport mode access
            switchport access vlan 605
            exit

##### Add the PVSTP configuration to prevent loopings on the network: 

**SW03,SW04,SW05:**

    configure terminal
        spanning-tree mode rapid-pvst
        spanning-tree vlan 339,605