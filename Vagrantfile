# -*- mode: ruby -*-
# vi: set ft=ruby :

required_plugins = %w(vagrant-omnibus vagrant-vbguest vagrant-share vagrant-berkshelf)
required_plugins.each do |plugin|
  next if Vagrant.has_plugin? plugin
  msg =  "Missing Vagrant plugin: '#{plugin}'. Please run:\n"
  msg << "  $ vagrant plugin install #{plugin}"
  fail msg
end

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION ||= '2'

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = 'bento/centos-6.7'

  name = "oneview-sdk-ruby-#{ENV['HOSTNAME'] || ENV['USER'] || ENV['USERNAME']}"
  config.vm.hostname = name
  config.vm.provider 'virtualbox' do |v|
    v.name = name
    v.memory = 2048
  end

  # config.vm.network 'forwarded_port', guest: 80, host: 8080

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on your network.
  # config.vm.network 'public_network', :bridge => 'Intel(R) Ethernet Connection I218-LM'

  config.ssh.private_key_path = ["#{ENV['HOME']}/.ssh/id_rsa", "#{ENV['HOME']}/.vagrant.d/insecure_private_key"]

  # To use ssh forwarding, you'll need to have the ssh-agent running on your host and a key added to it.
  #  See https://help.github.com/articles/working-with-ssh-key-passphrases/#auto-launching-ssh-agent-on-msysgit for info on how to do this automatically.
  # Turn off if you want to set up your own ssh keys on these machine
  config.ssh.forward_agent = true

 
  config.vm.provision 'shell', path: 'scripts/password_reset.sh' # Force password changes for root and vagrant users
  config.vm.provision 'shell', path: 'scripts/z_git_prompt.sh' # Copy the git_prompt download script
  # config.vm.provision 'shell', path: 'scripts/chef_shell_init.sh' # Set up the chef_shell_init script
  config.vm.provision 'shell', path: 'scripts/custom_bash_setup.sh' # Set up the custom_bash_setup script

  # Enable provisioning with chef
  config.omnibus.chef_version = :latest

  config.vm.provision 'chef_zero' do |chef|
    chef.json = {
      'yum-epel' => {
        'repositories' => ['epel']
      },
      'rvm' => {
        'rubies' => ['2.2.0'],
        'default_ruby' => 'ruby-2.2.0'
      }
    }
    chef.add_recipe 'yum-epel'
    chef.add_recipe 'git'
    chef.add_recipe 'rvm::system'
    chef.add_recipe 'rvm::vagrant'
  end

  # Copy your git config over
  if %w(up status).include?(ARGV.first.downcase)
    if File.exist?("#{ENV['HOME']}/.gitconfig")
      puts "Note: Running '$ vagrant up' will copy your .gitconfig over from #{ENV['HOME']}/.gitconfig , so make sure it is configured properly."
      config.vm.provision 'file', source: "#{ENV['HOME']}/.gitconfig", destination: '.gitconfig'
    else
      puts "No .gitconfig found at #{ENV['HOME']}/.gitconfig ... skipping"
    end
  end

  # Set git to use linux line endings
  config.vm.provision 'shell', inline: 'if command -v git > /dev/null 2>&1 ; then git config --system core.autocrlf input ; fi'
end