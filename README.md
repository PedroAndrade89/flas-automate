Deployment of a python flask web application behind ngix working as a load balancer
-----------------------------------------------------------------------------

- Test made with Ansible 2.7.5
- Expects CentOS/RHEL 7 hosts

This playbook will deploy a Python flask web app from a GitHub repository that will be served by Gunicorn WSGI HTTP server.
Additionally nginx will be deployed to work as a load balancer in from of the servers. This set of playbooks also have the capability to dynamically add and remove web server nodes from the deployment.
There is tags in place, so if we want to run only a specific role(ex. load-balancer) and don't do the full deployment just add --tags "load-balancer" to ansible-playbook command

### Prerequesites

You need to install ansible and have provided ssh access to the sudo "ssh_user" in all nodes

```
pip install ansible
https://github.com/PedroAndrade89/flask-automate
cd flask-automate
```

### Initial Site Setup

First we configure the entire cluster by listing our hosts in the 'hosts'
inventory file, grouped by their purpose:

```
[webapps]
pandrade-1
pandrade-2

[lbalancer]
pandrade-3

[monitoring]
pandrade-3
```

After this we execute following command to deploy:

```
ansible-playbook -u "ssh_user" -i hosts deploy.yml
```

The deployment can be verified by accessing endpoint /hello using load balancer ip 

### Removing and Adding a Node

Removal and addition of nodes to the cluster is as simple as editing the hosts inventory and re-running:

    ansible-playbook -u "ssh_user" -i hosts deploy.yml



### Rolling Update

Rolling updates are the preferred way to update the web server software or deployed application, since the load balancer can be dynamically configured to take the hosts to be updated out of the pool. This will keep the service running on other servers so that the users are not interrupted.

In this example the hosts are updated in serial fashion, which means that only one server will be updated at one time. If you have a lot of web server hosts, this behaviour can be changed by setting the 'serial' keyword in rolling_update.yml file.

Once the code has been updated in the source repository for your application which can be defined in the group_vars/all file, execute the following command:
```
 ansible-playbook -i hosts rolling_update.yml
```













