# Install the ChefDK on a VM.

$WORKSTATION_SETUP = <<-END
# Install chef dk v0.10.0 (Ref https://github.com/chef/chef-dk)
# Hardcoding v. 0.10.0, as script was hanging when using "-c current"
apt-get install -y curl tree
curl https://omnitruck.chef.io/install.sh | sudo bash -s -- -v 0.10.0 -P chefdk
END

$SERVER_SETUP = <<-END
# Install chef server
# (Ref https://docs.chef.io/release/server_12-3/install_server.html)

# Hardcoding server version.
file=https://packagecloud.io/chef/stable/packages/ubuntu/trusty/chef-server-core_12.3.1-1_amd64.deb/download

mkdir -p /tmp
echo Downloading $file ...
if [ -f /vagrant/server.deb ]; then
  echo File already downloaded, copying ...
  cp /vagrant/server.deb /tmp/server.deb
else
  wget $file -O /tmp/server.deb 2>/dev/null
fi
dpkg -i /tmp/server.deb
chef-server-ctl reconfigure

echo stopping now
exit 0

# Create admin and org
# Password must have at least 6 characters
chef-server-ctl user-create localadmin Local Admin localadmin@local.com 'adminpassword' --filename /tmp/admin.pem
chef-server-ctl org-create short_name "test_org" --association_user localadmin --filename /tmp/testorg-validator.pem

# Chef Manage web UI
chef-server-ctl install opscode-manage
chef-server-ctl reconfigure
opscode-manage-ctl reconfigure

END

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/trusty64"

  config.vm.define :workstation do |w|
    w.vm.hostname = "workstation"
    w.vm.network "forwarded_port", guest: 80, host: 8080
    w.vm.synced_folder "chef-repo/", "/home/vagrant/chef-repo", create: true
    w.vm.provision "shell", inline: $WORKSTATION_SETUP
  end

  config.vm.provider "virtualbox" do |v|
    v.memory = 4096
    v.cpus = 2
  end
  
  config.vm.define :server do |srv|
    srv.vm.hostname = "server"
    srv.vm.network "forwarded_port", guest: 80, host: 8081
    srv.vm.network "forwarded_port", guest: 443, host: 8082
    srv.vm.provision "shell", inline: $SERVER_SETUP
  end

end
