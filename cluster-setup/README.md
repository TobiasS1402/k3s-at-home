## Build the cluster
Building the cluster uses the Ansible K3S Role by Xanmanning https://github.com/PyratLabs/ansible-role-k3s

```
git clone https://github.com/TobiasS1402/k3s-at-home.git

cd k3s-at-home/cluster-setup

ansible-galaxy install xanmanning.k3s
```

### Executing
```
ansible -i inventory.yml -m ping all --ssh-common-args="-o StrictHostKeyChecking=no" --private-key="/home/tobias/.ssh/id_rsa"

ansible-playbook -i inventory.yml cluster.yml --ssh-common-args="-o StrictHostKeyChecking=no" --private-key="/home/tobias/.ssh/id_rsa"
```
