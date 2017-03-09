FGCI-ansible on openstack
=========================

playbooks:
 - single.yml: sets up a single FGCI style node with Nordugrid ARC client, all the certificates , CVMFS and modules with scientific software
 - site.yml: sets up a FGCI style cluster

Only tested with CentOS7, many things are hard coded with EL specific things

Instructions regarding heat:
 - https://github.com/CSC-IT-Center-for-Science/etherpad-deployment-demo

Instructions regarding how to get the openrc.sh file and setup an environment where you can run ansible.
 - https://research.csc.fi/pouta-command-line-tools

Instructions regarding FGCI, modules and certificates:
 - https://research.csc.fi/fgci-user-guide
 - https://research.csc.fi/fgci-grid-certificates#1.1.3

Usage - a place where one can run ansible
------

Also you need "shade" which provides the nova tool to interface with openstack.

 - Get an environment where you can run ansible and nova (talks with openstack). This can be done in several ways. See https://research.csc.fi/pouta-install-client for some examples. For example setup a python virtual environment:

<pre> 
virtualenv osclient
source osclient/bin/activate
pip install shade ansible
</pre>

Configuration
---------

Remember to allow the VM to SSH into the nodes in the stack and think about where your ssh key is. ssh -A can be used to forward the SSH agent

Step 1:
<pre>
git clone https://github.com/CSC-IT-Center-for-Science/fgci-ansible -b openstack
cd fgci-ansible/clouds
cp -v ../examples/group_vars/all/secrets.example group_vars/all/secrets.yml # copy secrets file
$EDITOR group_vars/all/secrets.yml # set a slurm_mysql_password + mail address
$EDITOR group_vars/all/heat.yml # set ssh user and the openstack network name
cd ..; ansible-galaxy install -r requirements.yml; cd clouds # install ansible roles
# fetch and source the openrc.sh file from your tenant in openstack
source tenant-openrc.sh
cp -v example-fgci-heat-params.yml fgci-heat-params.yml
$EDITOR fgci-heat-params.yml # see heat instructions URL above
</pre>

Running ansible
---------------

Then run either site playbook for an FGCI cluster:
<pre>
ansible-playbook site.yml
</pre>

or single playbook for a single FGCI style node:

<pre>
ansible-playbook single.yml
ssh IP
</pre>

Differences
-----------

 - using DHCP/PXE between VMs in cPouta is difficult/impossible
 - no need for any roles which only apply to hardware, like dell, smartd
 - a few variables are pointing to facts rather than hard coded addresses

Known Limitations:
------------------

 - The "reinstall" script doesn't work. How to tie this into openstack reprovision?
 - If you see this - just run ansible again:
<pre>
TASK [setup] *******************************************************************
Friday 16 December 2016  10:55:44 +0000 (0:00:08.419)       0:00:35.512 ******* 
fatal: [single_node]: UNREACHABLE! => {"changed": false, "msg": "Failed to connect to the host via ssh: Warning: Permanently added 'PUBLIC.IP.HERE' (ECDSA) to the list of known hosts.\r\nPermission denied (publickey,gssapi-keyex,gssapi-with-mic,password).\r\n", "unreachable": true}
</pre>

