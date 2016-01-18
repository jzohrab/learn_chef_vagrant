# Install the ChefDK on a VM.

$SCRIPT = <<-END
# Install chef dk v0.10.0 (Ref https://github.com/chef/chef-dk)
# Hardcoding v. 0.10.0, as script was hanging when using "-c current"
apt-get install -y curl tree
curl https://omnitruck.chef.io/install.sh | sudo bash -s -- -v 0.10.0 -P chefdk
END

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/trusty64"

  config.vm.define :server do |srv|
    srv.vm.hostname = "server"
    srv.vm.network "forwarded_port", guest: 80, host: 8080
    srv.vm.synced_folder "chef-repo/", "/home/vagrant/chef-repo", create: true
    srv.vm.provision "shell", inline: $SCRIPT
  end

end
