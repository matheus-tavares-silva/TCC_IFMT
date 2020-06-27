# TCC - Matheus Tavares
## High Availability Networking


### Linux Bridge Interface Configuration:

##### Set a bridge interface on system for virtual manager using netplan:

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

#### Add a bridge to libvirt for this, create a temporary file named br0.xml:

    <network>
        <name>br0</name>
            <forward mode='bridge'/>
        <bridge name='br0'/>
    </network>


#### Add this network file via 'virsh' command:

    virsh net-define /tmp/br0.xml
    virsh net-start br0
    virsh net-autostart br0

#### Finally make a test to confirm if all working correctly:

    virsh net-list --all