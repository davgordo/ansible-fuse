# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

cluster = {
  "fuse-001.local" => { :group => "fabric", :ip => "100.0.44.101", :cpus => 2, :mem => 2048 },
  "fuse-002.local" => { :group => "fabric", :ip => "100.0.44.102", :cpus => 2, :mem => 2048 },
  "fuse-003.local" => { :group => "fabric", :ip => "100.0.44.103", :cpus => 2, :mem => 2048 },
}

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  groups = { "all" => [] }
    cluster.each do |(hostname, info)|
      if ! groups.has_key?(info[:group])
        groups[info[:group]] = [ hostname ]
      else
        groups[info[:group]].push(hostname)
      end
      groups["all"].push(hostname)
  end

  cluster.each_with_index do |(hostname, info), index|

    config.vm.define hostname do |cfg|

      cfg.vm.provider :virtualbox do |vb, override|
        override.vm.box = "centos/7"
        override.vm.network :private_network, ip: "#{info[:ip]}"
        override.vm.hostname = hostname
        vb.name = hostname
        vb.customize ["modifyvm", :id, "--memory", info[:mem], "--cpus", info[:cpus], "--hwvirtex", "on" ]
      end

      # provision nodes with ansible
      if index == cluster.size - 1

        cfg.vm.provision :ansible do |ansible|
          #ansible.verbose = "vvvv"
          ansible.groups = groups
          ansible.limit = "all"
          ansible.playbook = "playbook.yml"
        end # end provision

      end #end if

    end # end config

  end #end cluster

end #end vagrant
