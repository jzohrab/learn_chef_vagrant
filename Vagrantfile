# Install the ChefDK on a VM.

$SCRIPT = <<-END
# Install chef dk v0.10.0 (Ref https://github.com/chef/chef-dk)
# Hardcoding v. 0.10.0, as script was hanging when using "-c current"
apt-get install -y curl tree
curl https://omnitruck.chef.io/install.sh | sudo bash -s -- -v 0.10.0 -P chefdk
END

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/trusty64"

  config.vm.define :workstation do |w|
    w.vm.hostname = "workstation"
    w.vm.network "forwarded_port", guest: 80, host: 8080
    w.vm.synced_folder "chef-repo/", "/home/vagrant/chef-repo", create: true
    w.vm.provision "shell", inline: $SCRIPT
  end

  config.vm.define :node do |n|
    n.vm.hostname = "node"
    n.vm.network "forwarded_port", guest: 80, host: 8081
    n.vm.network "private_network", ip: "192.168.1.10"
  end

end
