FGCI-ansible on openstack
=========================

playbooks:
 - single.yml: sets up a single FGCI style node with Nordugrid ARC client, all the certificates and CVMFS
 - site.yml: sets up a FGCI style cluster

Only tested with CentOS7, many things are hard coded with EL specific things

Instructions regarding heat:
 - https://github.com/CSC-IT-Center-for-Science/etherpad-deployment-demo

Instructions regarding how to get the openrc.sh file and how to use it:
 - https://research.csc.fi/pouta-command-line-tools

Instructions regarding FGCI, modules and certificates:
 - https://research.csc.fi/fgci-user-guide
 - https://research.csc.fi/fgci-grid-certificates#1.1.3

Usage
------

Step 0:
 - Launch an Ubuntu 16.04 virtual machine in your openstack

Setup of virtual machine + heat and heat deployments
---------

Because nova client was unhappy for me on my RHEL7 laptop using a Ubuntu 16.04 VM to run ansible.

Remember to allow the VM to SSH into the nodes in the stack and think about where your ssh key is. ssh -A can be used to forward the SSH agent

<pre>
sudo apt-get install python-pip python-setuptools gcc python-dev libssl-dev
sudo pip install ansible shade
git clone https://github.com/CSC-IT-Center-for-Science/fgci-ansible -b openstack
cd fgci-ansible/clouds
cp -v ../examples/group_vars/all/secrets.example group_vars/all/secrets.yml # copy secrets file
$EDITOR group_vars/all/secrets.yml # set a slurm_mysql_password + mail address
cd ..; ansible-galaxy install -r requirements.yml; cd clouds # install ansible roles
# fetch and source the openrc.sh file from your tenant in openstack
source tenant-openrc.sh
cp -v example-fgci-heat-params.yml fgci-heat-params.yml
$EDITOR fgci-heat-params.yml # see heat instructions URL above
</pre>

Then run either playbook:
<pre>
ansible-playbook site.yml
</pre>

or

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
