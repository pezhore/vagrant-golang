# -*- mode: ruby -*-
# vi: set ft=ruby :

@vagrant_home = "/home/vagrant"
@ansible_home = "#{@vagrant_home}/ansible"

Vagrant.configure("2") do |config|
  #config.vm.box = "puppetlabs/ubuntu-16.04-64-puppet"
  config.vm.box = "bento/ubuntu-18.04"
  config.vm.synced_folder "golang/", "#{@vagrant_home}/go", type: 'rsync'
  config.vm.synced_folder "ansible/", "#{@ansible_home}", type: 'rsync'
  config.vm.provision "shell", inline: "chown vagrant:vagrant #{@vagrant_home}"

  # Run our ansible playbook
  config.vm.provision 'preemptively give others write access to /etc/ansible/roles', type: :shell, inline: <<~'EOM'
	sudo mkdir /etc/ansible/roles -p
	sudo chmod o+w /etc/ansible/roles
  EOM

  config.vm.provision 'Update vm', type: :shell, inline: <<~'EOM'
    apt update
    DEBIAN_FRONTEND=noninteractive apt dist-upgrade -y
    apt autoremove --purge -y
  EOM

  config.vm.provision 'ansible', run: 'always', type: :ansible_local do |ansible|
	ansible.galaxy_role_file = "#{@ansible_home}/requirements.yml"
	ansible.galaxy_roles_path = '/etc/ansible/roles'
	ansible.galaxy_command = 'ansible-galaxy install --role-file=%{role_file} --roles-path=%{roles_path}'
	ansible.playbook = "#{@ansible_home}/playbook.yml"
  end
end
