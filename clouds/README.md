FGCI-ansible on openstack
=========================

Sets up an FGCI cluster in openstack

Only tested with CentOS7, many things are hard coded with EL specific things

Instructions regarding heat:

https://github.com/CSC-IT-Center-for-Science/etherpad-deployment-demo

Usage
------

 - provision at least three virtual machines
 - set selinux to disabled and reboot - can be done with post-install??

Setup of virtual machine + heat and heat deployments
---------

Because nova client was unhappy for me on my RHEL7 laptop doing this in an Ubuntu 16.04 VM:

Remember to allow the VM to SSH into the nodes in the stack and ssh -A (forward SSH agent)

<pre>
sudo apt-get install python-pip python-setuptools gcc python-dev libssl-dev
sudo pip install ansible shade
git clone fgci-ansible
checkout openstack
# fetch and source the openrc.sh file from openstack
# at this point "nova list" should work and you can continue with instructions from 
# https://github.com/CSC-IT-Center-for-Science/etherpad-deployment-demo
</pre>

Differences
-----------

 - using DHCP/PXE is difficult
 - no need for any roles which only apply to hardware, like dell, smartd

Known Limitations:
------------------

 - The "reinstall" script doesn't work. How to tie this into openstack reprovision?
