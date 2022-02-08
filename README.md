# kubespary-installation
![Kubernetes Logo](https://raw.githubusercontent.com/kubernetes-sigs/kubespray/master/docs/img/kubernetes-logo.png)
#### To deploy the cluster you can use :
  #### Ansible
 ##### Usage
 ```
 # Copy the kubespary file (In master node)
  git clone https://github.com/kubernetes-sigs/kubespray.git
 # Update apt repo
  sudo apt update
 # Install python pip3
  sudo apt install -y python3-pip
 # Check pip3 list
  sudo pip3 list
 # Install dependencies from ``requirements.txt``
  sudo pip3 install -r requirements.txt
 # Copy ``inventory/sample`` as ``inventory/mycluster``
  cp -rfp inventory/sample inventory/mycluster
 # Edit /etc/hosts file ---> add nodes ip
  vim /etc/hosts 
    192.168.100.XX node1
    192.168.100.XX node2
    ...
 # Update Ansible inventory file
  vim inventory/mycluster/inventory.ini
      [all]
      node1 ansible_host=192.168.100.52  # ip=10.3.0.1 etcd_member_name=etcd1
      node2 ansible_host=192.168.100.115  # ip=10.3.0.2 etcd_member_name=etcd2
      node3 ansible_host=192.168.100.162  # ip=10.3.0.3 etcd_member_name=etcd3
      # node4 ansible_host=95.54.0.15  # ip=10.3.0.4 etcd_member_name=etcd4
      # node5 ansible_host=95.54.0.16  # ip=10.3.0.5 etcd_member_name=etcd5
      # node6 ansible_host=95.54.0.17  # ip=10.3.0.6 etcd_member_name=etcd6

      # ## configure a bastion host if your nodes are not directly reachable
      # [bastion]
      # bastion ansible_host=x.x.x.x ansible_user=some_user

      [kube_control_plane]
      node1
      # node2
      # node3

      [etcd]
      node1
      # node2
      # node3

      [kube_node]
      node2
      node3
      # node4
      # node5
      # node6

      [calico_rr]

      [k8s_cluster:children]
      kube_control_plane
      kube_node
      calico_rr
      
 # Ansible ping test (In master node)
  ssh-keygen
  ssh-copy-id node2
  ssh-copy-id node3
  ...
  ansible all -i inventory/mycluster/inventory.ini -m ping -k
  
 # Deploy Kubespray with Ansible Playbook - run the playbook as root
 # The option `--become` is required, as for example writing SSL keys in /etc/,
 # installing packages and interacting with various systemd daemons.
 # Without --become the playbook will fail to run!
  ansible-playbook -i inventory/mycluster/inventory.ini --become --become-user=root cluster.yml
  
