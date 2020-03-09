packethost-role
=================
Nick's dev. Not fit for, well, anything much. 

Reserve a bare-metal machine from https://www.packet.com/.
Little more than an ansible role wrapper around packet_sshkey and packet_device modules.
Doesn't work for VMware host. 

Some people call Packet 'packet.net'.
I used the label 'packethost'. I saw that here. 
https://github.com/packethost



Requirements
------------

packet CLI 

A task in here shells out to the packet CLI.
roles/packethost-machine/tasks/get-project-id.yml
Cheap and nasty.

Role Variables
--------------

Variables are explained here.
```roles/packethost-machine/defaults/main.yml```
Variables are defined here.
```roles/packethost-machine/defaults/main.yml```

Role creates a machine and sets a couple host vars.
- IP address
- device ID
These are set in memory and written to this inventory file.
```roles/packethost-machine/vars/main.yml``` 
Bit unorthodox, but I want to avoid a dependency on a dynamic inventory plug-in
because nobody's written one for Packet.
Not here, see?
https://docs.ansible.com/ansible/latest/plugins/inventory.html
Some kind soul wrote a dynamic inventory script, but are scripts less cool than flared trousers?
https://github.com/ansible/ansible/blob/devel/contrib/inventory/packet_net.py


Dependencies
------------

packet-python
https://www.packet.com/developers/libraries/python/


Example Playbook and other files
---------------------------------

inventory

```
all:
  children:
    packethost:
      hosts: 
        my-packet-machine:
```

my-packet-key.pub

A public key file to upload to Packet. See variable packethost_pub_key_file

ansible.cfg 

```
# This file contains many parameters and descriptions. 
# https://github.com/ansible/ansible/blob/devel/examples/ansible.cfg

[defaults]
inventory=./inventory

# SSH connection key
# If this key is encrypted, use ssh-agent to avoid typing in the password every time you run the playbook.
# https://docs.ansible.com/ansible/latest/user_guide/connection_details.html
private_key_file = ./my-packet-key

[privilege_escalation]
become=no
become_user=root
become_method=sudo
become_ask_pass=false
```
my-packet-host.yml 

playbook

```
- name: rent a bare metal machine from https://www.packet.com/
  hosts: all
  gather_facts: no

  - packethost-role
```

Examples 
----------

add -v to see packet replies 
```
cd my-playbook-dir

```
find the project ID and add to vars file
```
ansible-playbook my-packet-host.yml  --tags=get-project-id
```

new server 
```
ansible-playbook my-packet-host.yml  
```

delete server 
```
ansible-playbook my-packet-host.yml  --tags=destroy
```

override variables
- Broken! Something is setting a tiny tiny subnet (/31), too small for VMware. 
```
ansible-playbook my-packet-host.yml  \
  --extra-vars='packethost_os=vmware_esxi_6_5' \
  --extra-vars='packethost_type=c2.medium.x86'
```



License
-------

BSD

