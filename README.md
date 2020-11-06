# Ansible Playbooks : Jakarta

Ansible Playbooks for Jakarta project.

Please find the complete project's documentation [here](https://192.168.4.16/projet/projects/equipe-1/wiki/presentation-globale-du-projet)

## Getting Started

### Presentation

This repo contains 5 playbooks :
- *install.yml* : core infrastructure installation
- *moodle.yml*  : moodle service deployment
- *tomcat.yml*  : tomcat service deployment
- *gitlab.yml*  : gitlab service deployment
- *jenkins.yml* : jenkins service deployment

#### install.yml
This play deploys last Jakarta project **main infrastructure** release on different hosts:
- **MariaDB databases** (3 DB clusterized by Galera Cluster)
- **HaProxy load balancers** (2 Load balancers clusterized by heartbeat)
- **UFW firewall** 
- **Zabbix server**
- **Rudder server**
- **Backup servers** (3 backup servers)
- **EyesOfApplication**

It configures as well every virtual machine listed above and adds them as an host to Zabbix and Rudder servers in order to be monitored.

It is also meant to deploy the needed network configuration on both switch and router of the project if the Ansible controller node is physically linked to them or if it can start a SSH connexion (**which is possible if the node is connected to eduroam** by SSH *192.168.4.193:3434* for the switch and *192.168.4.193* for the router).

Otherwise, you may want to comment out network configuration related lines in *install.yml*.

#### moodle.yml
This play will **create Moodle's VM** and deploy Moodle on them. Modify first *moodle.yml* task to deploy more or less VMs.

#### tomcat.yml
This play will **create Tomcat's VM** and deploy Tomcat on them. Modify first *tomcat.yml* task to deploy more or less VMs.

#### gitlab.yml
This play will **create Gitlab's VM** and deploy Moodle on them. Modify first *gitlab.yml* task to deploy more or less VMs.

#### jenkins.yml
This play will **create Jenkins' VM** and deploy Tomcat on them. Modify first *jenkins.yml* task to deploy more or less VMs.


Please take a look at each role's *README* for further information.

## Prerequisites

#### Network
- Cisco Catalyst 2960 (switch) with minimal install and SSH enabled
- Cisco 2911 (router) with minimal install and SSH enabled

#### Linux machines : Jakarta core installation
##### Debian 9
*install.yml* playbook will deploy Jakarta solution on hosts defined in *inventory.ini*. The project is using Debian 11 virtual machines (3 DB + Rudder + Zabbix + 2 HAProxy + UFW + 3 backup servers) as its core infrastructure, therefore 11 Debian 9 VM are needed with :
- Debian 9 minimal install
- SSH installed and sshd.service enabled
- **sudo** package installed
- Apporpriate network configuration (see *README* of each role)
- DNS set to 192.168.4.2 in */etc/resolv.conf*
- [User 'Ansible' already configured](https://github.com/nickjj/ansible-user)

**Note** : A template Debian 9 VM **with all requirements satisfied** is available [here](https://192.168.4.16/Equipe_1_Jakarta/debian9-template).

##### CentOS

EyesOfApplication for Linux has to be deployed on a CentOS 7 host. Moreover, as it is meant to send reports to Nagios, we recommend to use the [EyesOfNetwork] (https://www.eyesofnetwork.com/?page_id=48&lang=fr) distribution. 

#### Linux machines : Jakarta's services deployment
Playbooks such as *moodle.yml* or *tomcat.yml* are meant to create the VMs they will live on, therefore only VirtualBox is required.

[Debian 9 template](https://192.168.4.16/Equipe_1_Jakarta/debian9-template) should **not** be git cloned by the user except for a specific purpose as the VMs creation is meant to be 100% automated.

## Installing Jakarta
#### SSH
```
apt-get install ansible git -y
git clone gogs@192.168.4.16:Equipe_1_Jakarta/ansible-jakarta.git
cd ansible-jakarta
ansible-galaxy install -r roles/requirements.yml -p roles
ansible-playbook install.yml
```

#### HTTPS
```
apt-get install ansible git -y
git clone https://192.168.4.16/Equipe_1_Jakarta/ansible-jakarta.git
cd ansible-jakarta
ansible-galaxy install -r roles/requirements.yml -p roles
ansible-playbook install.yml
```

### Deploying Jakarta's services
Same as above, but run *moodle.yml* / *tomcat.yml* / ... instead of *install.yml*.

The user can set in *moodle* / *tomcat.yml* / ... more or less VMs to be deployed :

```
- name: Create Jakarta's moodle VM 1
  hosts: jakarta_pc1
  remote_user: etudiant
  tasks:
  - include_role:
      name: vm_creation
      tasks_from: install
    vars:
      vm_ip: "{{ moodle_1_ip }}"
      vm_name: moodle1
      vm_memory: 1024

- name: Create Jakarta's moodle VM 2
  hosts: jakarta_pc2
  remote_user: etudiant
  tasks:
  - include_role:
      name: vm_creation
      tasks_from: install
    vars:
      vm_ip: "{{ moodle_2_ip }}"
      vm_name: moodle2
      vm_memory: 1024

- name: Create Jakarta's moodle VM 3
  hosts: jakarta_pc3
  remote_user: etudiant
  tasks:
  - include_role:
      name: vm_creation
      tasks_from: install
    vars:
      vm_ip: "{{ moodle_3_ip }}"
      vm_name: moodle3
      vm_memory: 1024

```

**Important** : Depending on the service you want to deploy, *inventory.ini* file **has** to be changed. Please make sure IPs variables are set.

## Notes

The playbook runs locally. 
Modify eventually inventory.ini to set your list.
Deploy ssh authorized keys for remote execution.

## Authors

* **Toréa TESSIER** - <torea.tessier@reseau.eseo.fr> - [Jakarta Project](https://192.168.4.16/Equipe_1_Jakarta/)
