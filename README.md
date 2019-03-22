# Simple Vagrant Master/Slave Initial Setup

#### This is meant to initialize (2) virgin jenkins servers in a master/slave setup running on the default CentOS7 vagrant boxes.  Useful for lab testing, learning or whatever.  It will install Jenkins latest, openjdk 1.8, python3/pip, awscli, 

### Setup Instructions

1. Modify the VagrantFile if needed
2. 'vagrant up' to start the boxes and complete initial provisioning
3. To complete the slave node addition to the master, manually exchange keys
* On the master ('jenkins') - copy generated public key:

```bash
vi /var/lib/jenkins/.ssh/id_rsa.pub
```
* On the slave ('jenkins-slave'), paste the key into authorized_keys

```bash
vi /var/lib/jenkins-slave/.ssh/authorized_keys
```

TODO - update this to use ssh copy key, this is hacky!!

NOTE: Both boxes use the default vagrant logins (vagrant/vagrant).  Both servers have a 'jenkins' user with no password set.  You can sudo/su to the 'jenkins' user as needed.

###  Further Jenkins master setup for a decent lab (if desired):

1. gain access via the default admin pwd; finish setup with default plugins
2. create your admin user
3. switch to matrix security
4. give admin user all rights in the matrix
5. add a restricted dev user
6. give this restricted user the following permissions:
    OVERALL: read
    CREDENTIALS: NONE
    AGENT: NONE
    JOB: build,cancel,configure,discover,read, workspace (maybe give create...)
    RUN: update
    VIEW: configure, create, read 
    SCM: NONE
7. Additional Plugins:
    * Amazon EC2 plugin
8. Attach the slave node (user is 'jenkins' and slave server = 'jenkins-slave') 



0

I've just fixed this up in a complex multi CentOS (BoxCutter 6.9) VM Vagrant setup. There are two levels to this problem, my problem was 2:

Get your Vagrant config right so that you have a "private_network" and the IP addresses of all your VM's are in the same subnet (static or DHCP): https://www.vagrantup.com/docs/networking/private_network.html

When ssh'ing between VM's if you get this error: 
[root@vm01 ~]# ssh root@vm02
Permission denied (publickey,gssapi-keyex,gssapi-with-mic).
The error message means that none of the authentication methods managed to authenticate your session, and there is no mention of a password option so password authentication has probably been disabled. To fix edit /etc/ssh/sshd_config and check that you have PasswordAuthentication yes and it's not commented out (#), then restart sshd if required: service sshd restart (it won't disconnect any sesssions coz it's clever).

sshpass -p "$REPLICA_SSH_PASS" ssh -o StrictHostKeyChecking=no $REPLICA_SSH_USER@$REPLICA_IP
