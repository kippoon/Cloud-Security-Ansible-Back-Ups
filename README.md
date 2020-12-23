# Cloud-Security-Ansible-Back-Ups
Backups of all lessons and scripts written for Cloud Security and Networking
## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

https://github.com/kippoon/Cloud-Security-Ansible-Back-Ups/blob/main/Diagrams/KI_Cloud_Infrastructure_Network_Diagram.drawio

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above or to install only certain pieces of it, such as Filebeat.

This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will have high availability, in addition to restricting unauthorized access to the network.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the log data and system metrics and statistics.

The configuration details of each machine may be found below.

| Name    | Function      | IP Address   | OS    |
|---------|---------------|--------------|-------|
| Jumpbox | Gateway       | 10.0.0.4     | Linux |
| Web-1   | Server        | 10.0.0.5     | Linux |
| Web-2   | Server        | 10.0.0.6     | Linux |
| ELKy    | ELK Server    | 10.1.0.4     | Linux |
| Loadie  | Load Balancer | 13.88.12.106 |       |
| Web-3   | Server        | 10.0.0.8     | Linux |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the Jumpbox/10.0.0.4 machine can accept connections from the Internet. Access to this machine is only allowed from the Host IP address and other machines on the virtual network (10.0.0.4 - 10.0.0.8).

Machines within the network can only be accessed by first connecting to the Jumpbox and then attaching to the corresponding container; unless the DVWA is exposed. The ELK VM is accessible via whitelisted Host IP and virtual network (10.0.0.4 - 10.0.0.8) IP addresses.

A summary of the access policies in place can be found in the table below.

| Name    | Publicly Accessible | Allowed IP Addresses      |
|---------|---------------------|---------------------------|
| Jumpbox | Yes                 | Host,                     |
| Web-1   | No                  | 10.0.0.4 - 10.0.0.8       |
| Web-2   | No                  | 10.0.0.4 - 10.0.0.8       |
| ELKy    | No                  | 10.0.0.4 - 10.0.0.8, Host |
| Web-3   | No                  | 10.0.0.4 - 10.0.0.8       |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because it eliminates any human error and saves time when setting up multiple environments.

The playbook implements the following tasks:
- Increases container memory to allow it to run ELK 
- Installs Docker onto the ELK server
- Installs Python
- Installs the Python Docker module
- Downloads and Launches the ELK Container
- Enables docker service

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

https://github.com/kippoon/Cloud-Security-Ansible-Back-Ups/blob/main/HW13_dockerps_elksnip.JPG

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- Jumpbox-Provisioner/10.0.0.4
- Web-1/10.0.0.5
- Web-2/10.0.0.6
- Web-3/10.0.0.8

We have installed the following Beats on these machines:
- Filebeat
- Metricbeat

These Beats allow us to collect the following information from each machine:
- Filebeat allows us to collect and centralize log data like system.log, wifi.log, error.log, etc. via harvesters that can then be forwarded to Logstash or Elasticsearch for analysis.
- Metricbeat allows us to collect metrics and statistics such as timestamps, dates, geolocation, etc. and insert them directly into Logstash or Elasticsearch for analysis also.

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the filebeat-config.yml file to the ELK container
- Update the filebeat-config.yml file to include the ELK server IP address with corresponding ports
- Run the playbook, and navigate to the Filebeat installation page on the ELK server GUI to check that the installation worked as expected.

_As a **Bonus**, provide the specific commands the user will need to run to download the playbook, update the files, etc._
- docker start elk
- curl https://gist.githubusercontent.com/slape/5cc350109583af6cbe577bbcc0710c93/raw/eca603b72586fbe148c11f9c87bf96a63cb25760/Filebeat to /etc/ansible/files/filebeat-config.yml
- nano filebeat-config.yml
  - edit output.elasticsearch host to ["10.1.0.4:9200"]
  - edit setup.kibana host to ["10.1.0.4:5601"] 
- save changes
- curl https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.4.0-amd64.deb to  /etc/ansible/files/filebeat-7.4.0-amd64.deb
- dpkg -i filebeat-7.4.0-amd64.deb
- copy filebeat-config.yml from container to Web-VMs
- filebeat modules enable system
- filebeat setup
- service metricbeat start
- ansible-playbook filebeat-playbook.yml
- curl https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-7.4.0-amd64.deb to /etc/ansible/files/metricbeat-7.4.0-amd64.deb
- dpkg -i metricbeat-7.4.0-amd64.deb
- update metric-config.yml from container to Web-VMs
- metricbeat modules enable docker
- metricbeat setup
- metricbeat -e
