# -*- mode: ruby -*-
# vi: set ft=ruby :
unless Vagrant.has_plugin?("vagrant-vbguest")
    puts 'Installing vagrant-vbguest Plugin...'
    system('vagrant plugin install vagrant-vbguest')
end
   
unless Vagrant.has_plugin?("vagrant-reload")
    puts 'Installing vagrant-reload Plugin...'
    system('vagrant plugin install vagrant-reload')
end

Vagrant.configure(2) do |config|

#### CUANTAS MAQUINAS VIRTUALES QUIERES CREAR?
VM_NUMBER=1
#### CUANTOS DISCOS QUIERES QUE TENGA CADA VM?
DISK_NUMBER=4
 (1..VM_NUMBER).each do |i|
 config.vm.define "vm#{i}" do |node|
 node.vm.box = "centos/7"
 node.vm.hostname = "vm#{i}"
 node.vm.network :private_network, ip: "10.0.0.1#{i}"
 node.vm.synced_folder ".", "/vdata"
 
 if i==3
 node.vm.network "forwarded_port", guest: 5601, host: 5601
 end
 if i==4
 node.vm.network "forwarded_port", guest: 80, host: "80"
 end
 node.vm.provider "virtualbox" do |vb|
 if i==2
 vb.memory = "2536"
 else
 vb.memory = "1024"
 end
 vb.cpus = 2

 #vb.customize ["storagectl", :id, "--name", "SATA Controller", "--add", "sata", "--controller", "IntelAHCI"]
 
 ######
 (0..DISK_NUMBER).each do |p|
 d=p
 unless File.exist? ("extra_data_#{i}_#{p}_#{d}.vdi")
 vb.customize ['createhd', '--filename', "extra_data_#{i}_#{p}_#{d}.vdi", '--size', 5 * 1024,'--format', "VDI"]
 end
 # unless File.exist? ("docker_data#{i}_#{p}_#{d}.vdi")
 # vb.customize ['createhd', '--filename', "docker_data_#{i}_#{p}_#{d}.vdi", '--size', 6 * 1024]
 # end
 vb.customize ['storageattach', :id, '--storagectl', 'SATA Controller', '--port', "#{p}", '--device', 0, '--type', 'hdd', '--medium', "extra_data_#{i}_#{p}_#{d}.vdi"]
 # vb.customize ['storageattach', :id, '--storagectl', 'IDE', '--port', "#{p}", '--device', 0, '--type', 'hdd', '--medium', "docker_data_#{i}_#{p}_#{d}.vdi"]
 end
 end
 end
end
end