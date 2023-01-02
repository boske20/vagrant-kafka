IMAGE_NAME = "generic/rocky8"

N = 2

Vagrant.configure("2") do |config|
    config.ssh.insert_key = false

    config.vm.provider "virtualbox" do |v|
        v.memory = 2048
        v.cpus = 2
    end
      
    config.vm.define "kafka-0" do |master|
        master.vm.box = IMAGE_NAME
        master.vm.network "private_network", ip: "172.16.1.30"
        master.vm.hostname = "kafka-0"
        master.vm.provision "ansible" do |ansible|
            ansible.playbook = "kafka-complete-setup/master-playbook.yml"
            ansible.extra_vars = {
                node_ip: "172.16.1.30",
            }
        end
    end
    config.vm.define "docker-0" do |docker|
        docker.vm.box = IMAGE_NAME
        docker.vm.network "private_network", ip: "172.16.1.33"
        docker.vm.hostname = "docker-0"
        docker.vm.provision "ansible" do |ansible|
            ansible.playbook = "kafka-zookeeper-setup/docker-playbook.yml"
            ansible.extra_vars = {
                node_ip: "172.16.1.33",
            }
        end
    end   
    (1..N).each do |i|
        config.vm.define "kafka-#{i}" do |node|
            node.vm.box = IMAGE_NAME
            node.vm.network "private_network", ip: "172.16.1.#{i + 30}"
            node.vm.hostname = "kafka-#{i}"
            node.vm.provision "ansible" do |ansible|
                ansible.playbook = "kafka-complete-setup/master-playbook.yml"
                ansible.extra_vars = {
                    node_ip: "172.16.1.#{i + 30}",
                }
                ansible.groups = {
                    "group1" => ["kafka-0","kafka-1","kafka-2"],
                }
            end
        end
    end
end

