# oneview-sdk-ruby-Vagrant

This repository contains the Vagrant configurations to quickly spin up a development server for the oneview-sdk-ruby repository found at [oneview-sdk-ruby](https://github.com/HewlettPackard/oneview-sdk-ruby). It's really just a pretty bare-bones CentOS 6.7 box with Git installed and RVM with Ruby 2.2.0


## Dependencies
 - [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
 - [Vagrant](https://www.vagrantup.com/downloads.html) and Vagrant plugins:
    - vagrant-omnibus
    - vagrant-proxyconf
    - vagrant-share
    - vagrant-vbguest
    - NOTE: Run `$ vagrant plugin install PLUGIN_NAME` to install Vagrant plugins.
 - [ChefDK](https://downloads.chef.io/chef-dk/)
 - A ssh-agent running with any keys you want to use on this box added. See [this article](https://help.github.com/articles/working-with-ssh-key-passphrases/#auto-launching-ssh-agent-on-msysgit) for more on this.



## Usage
 1. Clone this repo and then cd into the directory it creates.
 2. Edit the Vagrantfile and change anything specific to your system that might not mesh, including the config.vm.network bridge adapter name.
   - Note: To list your available Intel Ethernet network adapters, run `$ ipconfig /all | findstr /c:"Intel(R)\ Ethernet"` in a Windows Command Prompt.
 3. Run `$ vagrant status` to show the VM status and install any necessary plugins.
 4. Run `$ vagrant up` to build the VM
 5. Run `$ vagrant ssh` to login to the system

### Configuring the VM
Although Vagrant and Chef are great for setting things up, there's still some user-specific configuration that you'll need to do to the VM after you've created it:

1. Change the passwords for users 'vagrant' and 'root'. You should be prompted upon login to do this for both users, so simply log in to each user. The default password for both users is `vagrant`.
3. Make sure your ssh forwarding agent is working: run `$ ssh-add -L` and verify that these are the same keys that your host is using.
  - Note: You'll also need to add this key to your GitHub account if you haven't already. See [this article](https://help.github.com/articles/generating-ssh-keys/#step-3-add-your-ssh-key-to-your-account) for a guide.
4. Configure git and clone the oneview-sdk-ruby repository:
    - `$ cd /home/vagrant`
    - `$ git config --global user.name "John Doe"` (replace with your name)
    - `$ git config --global user.email "john.doe@domain.com"` (replace with your email)
    - `$ git clone git@github.com:HewlettPackard/oneview-sdk-ruby.git`

### Coverage Report
This Vagrant box is configured to serve the coverage report (expected to be in `/home/vagrant/oneview-sdk-ruby/coverage`) to port 80 of the guest machine. To make this easier to access, it also uses port forwarding so you can access it from your guest by going to [localhost:8080](http://localhost:8080).

Note that this report gets generated by running `$ rake test` or `$ rake spec`; if you havent' done that yet, you won't see the report.


## Troubleshooting
 - **Vagrant::Errors::SSHInvalidShell: The configured shell (config.ssh.shell) is invalid and unable...**
   - The system is waiting on password resets for the vagrant and/or root users. Log in with `$ vagrant ssh` and then `$ su root`. Follow any password reset prompts.
 - **Cookbook <X> not found**
   - Update the vagrant-berkshelf plugin by running:
     
     ```bash
     $ vagrant plugin uninstall vagrant-berkshelf
     $ vagrant plugin install vagrant-berkshelf`
     ```
