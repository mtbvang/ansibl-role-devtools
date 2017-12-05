# -*- mode: ruby -*-
# vi: set ft=ruby :

#
# Environment variables that can be overriden on vagrant up
#

PROJECT_NAME = ENV['PROJECT_NAME'] || File.basename(File.expand_path(File.dirname(__FILE__)))

ANSIBLE_PLAYBOOK = ENV['ANSIBLE_PLAYBOOK'] || 'test.yml'

VM_CPUS = ENV['VM_CPUS'] || 2
VM_CPU_CAP = ENV['VM_CPU_CAP'] || 80
VM_MEMORY = ENV['VM_MEMORY'] || 1024
VM_GUI = ENV['VM_GUI'] || false
VB_GUEST = ENV['VB_GUEST'] || false
# Set env var ANSIBLE_GALAXY_FORCE='' to NOT force install of roles
ANSIBLE_GALAXY_FORCE = ENV['ANSIBLE_GALAXY_FORCE'] || "--force"
HTTP_PROXY = ENV['HTTP_PROXY']
HTTPS_PROXY = ENV['HTTPS_PROXY']
NO_PROXY = ENV['NO_PROXY']
boxes = [
	{
	:name => "centos",
	:box => "mtbvang/centos7-desktop",
	:cpu => VM_CPU_CAP,
	:ram => VM_MEMORY
	},
	{
	:name => "ubuntu",
	:box => "boxcutter/ubuntu1604-desktop",
	:cpu => VM_CPU_CAP,
	:ram => VM_MEMORY
	},
]

Vagrant.configure("2") do |config|

	if Vagrant.has_plugin?("vagrant-vbguest")
		config.vbguest.auto_update = VB_GUEST
	end
	
	if Vagrant.has_plugin?("vagrant-cachier")
		config.cache.scope = :box
	end

	if Vagrant.has_plugin?("vagrant-proxyconf")
		config.proxy.http		 = HTTP_PROXY
		config.proxy.https		= HTTPS_PROXY
		config.proxy.no_proxy = NO_PROXY
	end
 
 	# Register redhat boxes
	if Vagrant.has_plugin?('vagrant-registration')
 		config.registration.skip = false
 		config.registration.unregister_on_halt = true
		config.registration.username = ENV['SUB_USERNAME']
		config.registration.password = ENV['SUB_PASSWORD']
		config.registration.proxy = ENV['SUB_PROXY']
	end

	boxes.each do |box|
		config.vm.define box[:name] do |vms|
			vms.vm.box = box[:box]

			vms.vm.provider "virtualbox" do |v|
				v.gui = VM_GUI.to_s == 'true' ? true : false
				v.cpus = VM_CPUS
				v.customize ["modifyvm", :id, "--cpuexecutioncap", box[:cpu]]
				v.customize ["modifyvm", :id, "--memory", box[:ram]]
				v.customize ["modifyvm", :id, "--ioapic", "on"]
				v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
				vms.vm.synced_folder '../', '/vagrant', type: "virtualbox", disabled: false
			end
		
			vms.vm.provider "vmware_workstation" do |v|
				v.gui = VM_GUI
				v.vmx["memsize"] = box[:ram]
				v.vmx["numvcpus"] = VM_CPUS
			end

			if box[:name].eql? "centos"
				vms.vm.provision "shell",
				inline: "echo 'Work around for epel bug https://bugs.centos.org/view.php?id=13669&nbn=1' && rpm -ivh --replacepkgs https://kojipkgs.fedoraproject.org/packages/http-parser/2.7.1/3.el7/x86_64/http-parser-2.7.1-3.el7.x86_64.rpm"
			end
		 
			vms.vm.provision :ansible_local do |ansible|
				ansible.verbose = "v"
				ansible.install_mode = "pip"
				ansible.version = "2.4.2.0"
				#ansible.version = "2.2.1.0"
				ansible.playbook = "#{PROJECT_NAME}/tests/#{ANSIBLE_PLAYBOOK}"
				ansible.galaxy_role_file = "#{PROJECT_NAME}/requirements.yml"
				ansible.galaxy_roles_path = "#{PROJECT_NAME}/tests/roles"
				ansible.galaxy_command = "ansible-galaxy install --ignore-certs --role-file=%{role_file} --roles-path=%{roles_path} #{ANSIBLE_GALAXY_FORCE}"
				ansible.become = true
			end
		end
	end

end

