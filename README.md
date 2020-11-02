# Welcome to the Ansible Hackathon Pilot

## Scenario:
Three teams with different operational goals but with the same
business goal, have to build and make available a loadbalanced web
application.

- Security Operations
- System Administration Operations
-  Web operations

## Playbook Tasks:
### All teams:
    Fork this repo and work on a feature branch.
    Create 3 branches
        - SecOps
        - Ops
        - WebOps

> Since each team will be fairly independent, feel free to build out your inventory to best suit your use cases

### Security Ops:
Build distinct playbooks to accomplish each of the following tasks:
Note: None of these tasks should modify the configurations. It should
simply report whether the security criteria is met or not:

1. Ensure that SELinux is enabled (enforcing) with a targeted policy
2. Ensure that the "root" user cannot log in over SSH.
3. Ensure that the motd file is configured and contains the following:
"Unauthorized access is not allowed"
4. Ensure that users that belong to the group "webadmin" are allowed
   to execute privileged commands without a password prompt.
5. Ensure that the firewall is configured to permit:
On Node1: TCP Port 8080
On Nodes2/3: TCP Port 9080

Pull them all together into a final playbook called security.yml
### System Administration Operations:
Build distinct playbooks to accomplish each of the following tasks:
1. Task 1:
- Use the following to create the users and groups on all servers.

``` yaml
#users.yml
    users:
      - username: user1
        groups: webadmin
      - username: user2
        groups: webadmin
      - username: user3
        groups: proxyadmin
      - username: user4
        groups: dbadmin
      - username: user5
        groups: appadmin

```
- Ensure that a file named "Welcome.text" exists in each user's home
  directory. This file should contain "Hello $username$".

2. Task 2:
- Ensure that the webadmin group has the ability to run privileged
  commands without needing to enter a password.

3. Task 3:
- Ensure that the packages `firewalld` and `httpd` are installed and
  are started. Also ensure that these services will automatically be
  started when the machine is rebooted.

4. Task 4:
- Ensure that root account cannot login using SSH

5. Task 5:
- Ensure that SELinux is enabled to be targeted.

6. Task 6:
- Ensure that an motd file is created that looks as follows:

``` bash
Welcome to NODE1
Unauthorized access is not allowed
Distribution: CentOS
Version: version 7.8
Processors: 2
Memory Installed: 1.8GB
Memory Swapfile: 2.0GB
```

7. Task 7
- On node 1, ensure that the firewall permits access to TCP port 8080
- On node 2 &  3, ensure that the firewall permits access to TCP port 9080

### Web operations

Task 1:
- Create a role that configures apache vhost. This
  role should accept a configurable variable for the listener port.
  The following vhost template can be used to generate the vhost file:

``` python
  # {{ ansible_managed }}

<VirtualHost *:{{ listen_port }}>
  ServerAdmin webmaster@{{ ansible_fqdn }}
  ServerName {{ ansible_fqdn }}
  ErrorLog logs/{{ ansible_hostname }}-error.log
  CustomLog logs/{{ ansible_hostname }}-common.log common
  DocumentRoot /var/www/vhosts/{{ ansible_hostname }}/

  <Directory /var/www/vhosts/{{ ansible_hostname }}/>
  Options +Indexes +FollowSymlinks +Includes
  Order allow,deny
  Allow from all
</Directory>
</VirtualHost>

```

  The resulting configuration should be placed on `/etc/httpd/vhost.conf`


- The role should also generate the index.html file that contains:

``` bash
You are on NODE2 at 172.32.2.2
```

The index file should be placed at:`/var/www/vhosts/{{
ansible_hostname }}/index.html`

- The role should restart the web server any time the config files are updated

- Add a playbook that invokes this role and use it to configure
  webservers listening on port 9080 on node 2 and node 3.

Task 2:
- Install and use the `geerlingguy.haproxy` role from galaxy.ansible.com

- Use this role in a playbook that installs and configures haproxy on
  node1. The frontend should listen on port 8080 and the backends
  should use node2 and node3, listening on port 9080



## Tower tasks



Login to your Tower instance using the following login/passwords

1. webadmin/webadmin123 (If you worked on the webadmin tasks)
2. opsadmin/opsadmin123 (If you worked on the Ops tasks)
3. secops/secops123 (If you worked on the security tasks)

> This should land you into the correct working tenancy.

### Create the inventory, credentials and projects
- Configure your project to use the appropriate branch for your playbooks
- Create the server login credentials
- Configure your project to source the inventory from your git branch

### SecOps

- Create a job template that runs the `security.yml` playbook


### AdminOps/WebOps

Create separate Job Templates that correspond to each of the playbooks
you created

### Create a workflow

As the webops admin, create a workflow that accomplishes the following:

1. Ensure that all devices are in compliance before configuring the
   load-balanced web servers.
2. If they are not in compliance send out a notification email and
   start a workflow that brings the systems into compliance (Note this
   workflow will have to be created by the Ops team and appropriate
   permissions must be provided to the WebsOps team to execute it)
3. Once the systems are in compliance (or if they are already in
   compliance), execute the job templates to install the webservers
   followed by the job templates to install and configure the HA
   Proxy.

Finally, if everything works, you should be able to access the public
IP address of NODE1 and display the contents served by NODE2 and NODE3
in a round robin method.
