FGCI-ansible on openstack
=========================

Sets up an FGCI cluster in openstack

Only tested with CentOS7, many things are hard coded with EL specific things

Instructions regarding heat:

https://github.com/CSC-IT-Center-for-Science/etherpad-deployment-demo

Usage
------

Step 1:
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
# fetch and source the openrc.sh file from your tenant in openstack
# at this point "nova list" should work and you can continue with instructions from 
# https://github.com/CSC-IT-Center-for-Science/etherpad-deployment-demo
#
# copy secrets file and set a password + mail address
cp -v ../examples/group_vars/all/secrets.example group_vars/all/secrets.yml
$EDITOR group_vars/all/secrets.yml
# run ansible
ansible-playbook site.yml
</pre>

Differences
-----------

 - using DHCP/PXE between VMs in cPouta is difficult/impossible
 - no need for any roles which only apply to hardware, like dell, smartd
 - a few variables are pointing to facts rather than hard coded addresses

Known Limitations:
------------------

 - The "reinstall" script doesn't work. How to tie this into openstack reprovision?
