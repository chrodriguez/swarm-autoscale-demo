hosts = %w(master worker)

Vagrant.configure("2") do |config|
  hosts.each do |host|
    config.vm.define host do |instance|
      instance.vm.box = "ubuntu/xenial64"
      instance.vm.hostname = host
    end
  end
  config.vm.provider "virtualbox" do |v|
    v.memory = 512
    v.cpus = 1
  end
  config.vm.provision "ansible" do |ansible|
    ansible.playbook = 'ansible/playbook.yml'
    ansible.galaxy_roles_path = 'ansible/roles'
    ansible.groups = {
      "docker_engine" => hosts,
      "docker_swarm_manager" => 'master',
      "docker_swarm_worker" => 'worker'
    }
  end
end
