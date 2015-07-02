# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
    config.vm.provider "virtualbox" do |vb, override|
        override.vm.box = "ubuntu/trusty64"
        vb.customize ["modifyvm", :id, "--cpus", "1", "--memory", "512"]
    end

    config.vm.define "worker" do |host|
        host.vm.hostname = "worker"
        host.vm.network "private_network", ip: "192.168.20.3"
    end

    config.vm.define "proxy" do |host|
        host.vm.network "private_network", ip: "192.168.20.2"
        host.vm.hostname = "proxy"

        # call provisioning only once after all boxes are up
        config.vm.provision "ansible" do |ansible|
            ansible.groups = {
                "proxy" => ["proxy"],
                "worker" => ["worker"]
            }
            ansible.playbook = "ansible-espeakbox/site.yml"
            ansible.limit = "all"

            # default provider is :virtualbox
            ansible.extra_vars = { ansible_ssh_user: "vagrant" }
            ansible.sudo = true

            ansible.raw_arguments = Shellwords.shellsplit(ENV['ANSIBLE_ARGS']) if ENV['ANSIBLE_ARGS']
        end
    end
end
