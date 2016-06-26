# Ensure required directories exist
require 'fileutils'
FileUtils.mkdir_p(File.join(File.dirname(__FILE__), 'share'))

Vagrant.configure('2') do |config|
  vm_type = 'maptiles'

  # Base box name, https://atlas.hashicorp.com/bento/boxes/centos-6.7
  config.vm.box = 'bento/centos-6.7'

  # The url from where the 'config.vm.box' box will be fetched if it doesn't already exist on the user's system
  config.vm.box_url = 'https://atlas.hashicorp.com/bento/boxes/centos-6.7/versions/2.2.5/providers/virtualbox.box'


  # Assign this VM a unique hostname
  config.vm.host_name = "#{ENV['USER']}.#{vm_type}.centos6.vagrantup.com"

  # Use the vagrant-cachier plugin if it exists to save ourselves some wait time when building repeatedly
  if Vagrant.has_plugin?('vagrant-cachier')
    config.cache.scope = :box
  end

  # Create a forwarded port mapping which allows access to a specific port within the machine from a port on the host machine
  config.vm.network 'forwarded_port', guest: 22,   host: 2192, id: 'ssh', auto_correct: true
  config.vm.network 'forwarded_port', guest: 80,   host: 8192, auto_correct: true # Apache httpd running mod_tile
  config.vm.network 'forwarded_port', guest: 5432, host: 8193, auto_correct: true # PostgreSQL server

  # Share an additional folder to the guest VM.
  config.vm.synced_folder 'share', '/vagrant-share'

  # Enable provisioning with Ansible
  config.vm.provision 'ansible' do |ansible|
    ansible.playbook = 'playbook.yml'
    ansible.verbose = 'vv' # v, vv, vvv, vvvv
    ansible.sudo = true
    ansible.sudo_user = 'root'
  end

  config.vm.provider 'virtualbox' do |vb|
    # Set label in VirtualBox user interface
    vb.name = "#{vm_type}-centos6"

    # Set memory allocated to the VM in MB
    vb.customize ['modifyvm', :id, '--memory', '2048']
  end
end

