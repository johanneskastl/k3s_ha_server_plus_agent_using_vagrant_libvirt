# k3s_ha_server_plus_agent_using_vagrant_libvirt

Vagrant setup for a k3s cluster with three servers and one worker (aka agent) using the libvirt provider 

This setup creates three k3s server VMs and one VM as k3s agent (aka worker) and installs k3s using Ansible.

Default OS is openSUSE Leap 15.2, but that can be changed in the Vagrantfile. Same holds true for the sizing of the machines.

## Vagrant

1. You need vagrant obviously.
2. Fetch the box, per default this is `opensuse/Leap-15.2.x86_64`, using `vagrant box add opensuse/Leap-15.2.x86_64`.
3. Make sure the git submodules are fully working by issuing `git submodule init && git submodule update`
4. Run `vagrant up`
5. Run `kubectl --kubeconfig ansible/k3s-kubeconfig get nodes` and you should see your server and agent nodes.
6. Party!

## Disabling the Ansible provisioning

In case you do not want Ansible to install k3s (because you want to install it yourself), just comment out the following lines in the `Vagrantfile`:
```
    node.vm.provision "ansible" do |ansible|
      ansible.compatibility_mode = "2.0"
      ansible.limit = "all"
      ansible.groups = {
        "k3sservers"  => [ "k3sserver1", "k3sserver2", "k3sserver3" ],
        "k3sagents"   => [ "k3sagent1" ]
      }
      ansible.playbook = "ansible/playbook-vagrant.yml"
    end # node.vm.provision
```

## Cleaning up

When tearing down the machine, the kubeconfig and token files that was download does not get deleted unfortunately.
To not cause problems the next time you start, just run `rm ansible/k3s-kubeconfig ansible/k3s-token` and all is well.

## Creating additional agent nodes

You can modify the Vagrantfile to create additional agent nodes by tweaking two lines.

1. Setting the number of agents (in this example to `2`):

```
  ###################################################################################
  # define number of agents
  W = 2
```

2. Adding the additional agent nodes to the `ansible_groups` line:
```
      ansible.groups = {
        "k3sservers"  => [ "k3sserver1", "k3sserver2", "k3sserver3" ],
        "k3sagents"   => [ "k3sagent1", "k3sagent2" ]
      }
```

## Creating additional server nodes

You can modify the Vagrantfile to create additional server nodes by tweaking two lines similar to adjusting the agent number described above.
