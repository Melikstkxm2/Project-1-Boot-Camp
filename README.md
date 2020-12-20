# Project-1-Boot-Camp
This is my first project in Boot Camp
## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

https://drive.google.com/file/d/11EL2fp7P0dHEih4HKUghtVkQv1GsqyHO/view?usp=sharing

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the ymal file may be used to install only certain pieces of it, such as Filebeat.

  ---
- name: my first playbook
  hosts: webservers
  become: true
  tasks:

    - name: install docker
      apt:
        update_cache: yes
        name: docker.io
        state: present

    - name: install pip3
      apt:
        name: python3-pip
        state: present

    - name: install docker pip
      pip:
        name: docker
        state: present


    - name: install dvwa container
      docker_container:
        name: dvwa
        image: cyberxsecurity/dvwa
        state: started
        restart_policy: always
        published_ports: 80:80

    - name: Enable docker service
      systemd:
        name: docker
        enabled: yes


---
- name: Configure Elk VM with Docker
  hosts: elk
#  remote_user: azadmin
  become: true
  tasks:
    # Use apt module
    - name: Install docker.io
      apt:
        update_cache: yes
        force_apt_get: yes
        name: docker.io
        state: present
      # Use apt module
    - name: Install python3-pip
      apt:
        force_apt_get: yes
        name: python3-pip
        state: present
      # Use pip module (It will default to pip3)
    - name: Install Docker module
      pip:
        name: docker
        state: present
      # Use command module
    - name: Increase virtual memory
      command: sysctl -w vm.max_map_count=262144
      # Use sysctl module
    - name: Use more memory
      sysctl:
        name: vm.max_map_count
        value: "262144"
        state: present
        reload: yes
      # Use docker_container module
    - name: download and launch a docker elk container
      docker_container:
        name: elk
        image: sebp/elk:761
        state: started
        restart_policy: always
        # Please list the ports that ELK runs on
        published_ports:
          -  5601:5601
          -  9200:9200
          -  5044:5044


---
- name: installing and launching filebeat
  hosts: webservers
  become: yes
  tasks:

  - name: download filebeat deb
    command: curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.4.0-amd64.deb

  - name: install filebeat deb
    command: dpkg -i filebeat-7.4.0-amd64.deb

  - name: drop in filebeat.yml
    copy:
      src: /etc/ansible/files/filebeat-config.yml
      dest: /etc/filebeat/filebeat.yml

  - name: enable and configure system module
    command: filebeat modules enable system

  - name: setup filebeat
    command: filebeat setup

  - name: start filebeat service
    command: service filebeat start

This document contains the following details:
- Description of the Topologu
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly available , in addition to restricting access  to the network.
-: What aspect of security do load balancers protect? What is the advantage of a jump box?_

Availability                                                 Allows you to give yourself controll and acess to VM's you created on your network
Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the logs and system traffic.
-What does Filebeat watch for? Log files & Locations_
-What does Metricbeat record? metrics and stats of machine, then sends you the data.

The configuration details of each machine may be found below.
_Note: Use the [Markdown Table Generator](http://) to add/remove values from the table_.


| Jump Box | Gateway     | 10.0.0.1  | Linux |   |
|----------|-------------|-----------|-------|---|
| Web 1    | Web Server  | 10.0.0.10 | Linux |   |
| Web 2    | Web Server  | 10.0.0.9  | Linux |   |
| Web 3    | Web Server  | 10.0.0.11 | Linux |   |
| Elk      | Monitoring  | 10.1.0.4  | Linux |   |




### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the jumpbox machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
76.244.13.188
Machines within the network can only be accessed by SSH.
-Which machine did you allow to access your ELK VM? Jump Box Provisioner. What was its IP address? 10.0.0.7

A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|---------------------|----------------------|
| Jump Box | Yes                 | 76.244.13.188        |
| Web-1    |No                   | 10.0.0.7             | 
| Web-2    |No                   | 10.0.0.7             |
| Web-3    |No                   | 10.0.0.7             |
| Elk Box  |No                   | 10.0.0.7             |

Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...
-What is the main advantage of automating configuration with Ansible? It let's you put multiple commands from a single playbook

The playbook implements the following tasks:
- : In 3-5 bullets, explain the steps of the ELK installation play. E.g., install Docker; download image; etc._
- ... install docker.io
- ...install python3-pip
- ...installdocker module
- ...Incresae Virtual Momory
- ...Install Docker Container

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

!: Update the path with the name of your screenshot of docker ps output](Images/docker_ps_output.png)
It's with my image files

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
-: List the IP addresses of the machines you are monitoring_
10.0.0.9
10.0.0.10
10.0.0.11

We have installed the following Beats on these machines:
-: Specify which Beats you successfully installed_

File beat

These Beats allow us to collect the following information from each machine:
-: In 1-2 sentences, explain what kind of data each beat collects, and provide 1 example of what you expect to see. E.g., `Winlogbeat` collects Windows logs, which we use to track user logon events, etc._

Filebeat monitors log files on give location, collects them and sends theem to Elasticsearch or Logstash
### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the file beat config file to your web vm's where you installed file beat. 
- Update the config file private ip to include the elk server_____ file to include...
- Run the playbook, and navigate to ____ to check that the installation worked as expected.

_TODO: Answer the following questions to fill in the blanks:_
- _Which file is the playbook? Where do you copy it? Filebeat - 
- _Which file do you update to make Ansible run the playbook on a specific machine? How do I specify which machine to install the ELK server on versus which to install Filebeat on?_
- _Which URL do you navigate to in order to check that the ELK server is running?

_As a **Bonus**, provide the specific commands the user will need to run to download the playbook, update the files, etc._
