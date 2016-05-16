# ansible-aos-scripts
a few helper scripts for install openshift
 couple of scripts


## do use 
#1 Subscribe nodes 
ansible-playbook  -i inventory playbooks/subscribe.yml --user=vagrant   --private-key=/Users/imckinle/Projects/openshift/vagrant-openshift-multihost/ose.key   --key-file=/Users/imckinle/Projects/openshift/vagrant-openshift-multihost/ose.key  --become-method=sudo --become

### setup ose for install 
ansible-playbook  -i inventory playbooks/configall.72.yml --user=vagrant   --private-key=/Users/imckinle/Projects/openshift/vagrant-openshift-multihost/ose.key   --key-file=/Users/imckinle/Projects/openshift/vagrant-openshift-multihost/ose.key  --become-method=sudo --become
## This also copies a host file to the target machine 

#### After install ##### 
install registry 
mkdir /mnt/registry 
sudo chown 1001:root /mnt/registry
oadm registry --service-account=registry     --config=/etc/origin/master/admin.kubeconfig     --credentials=/etc/origin/master/openshift-registry.kubeconfig     --images='registry.access.redhat.com/openshift3/ose-${component}:${version}'     --mount-host=/mnt/registry --selector="region=infra"


###TODO####
# because the machines are based on the cdk. it may be required to remove the already installed ose. the playbook should do this but have aboserved it doesn't 
sudo yum remove  kubernetes-master -y; sudo yum remove  kubernetes-client -y ; sudo yum remove  kubernetes-node -y; 
# Configure the playbook to be a bit more dyncamice with 
# need to configure host files

## todo
# because i instal the LB o nthe same machine as the dns server.  the openshift install configures iptables rules only for the LB. There fore any dns ports opene 53() get rmeove 

## to resolve this the file /usr/share/ansible/openshift-ansible/roles/haproxy/defaults/main.yml is updated 
os_firewall_allow:
- service: proxy
  port: "8443/tcp"
- service: proxy
  port: "80/tcp"
- service: dns
  port: "53/udp"
- service: haproxy stats
  port: "9000/tcp"
- service: haproxy balance
  port: "{{ haproxy_frontend_port }}/tcp"

## TODO bug 
 theres a bug where  the ansible install won'T work corectly 
the following fixes it 

    2  git clone https://github.com/openshift/openshift-ansible.git
    6  export ANSIBLE_FILTER_PLUGINS=/home/vagrant/openshift-ansible/filter_plugins
    7  export ANSIBLE_LOOKUP_PLUGINS=/home/vagrant/openshift-ansible/lookup_plugins
 
