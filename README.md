# Chef DK Vagrant box

This provides an Ubuntu development environment and separate node
for the tutorials at [learnchef](https://learn.chef.io).

The introductory [tutorial](https://learn.chef.io/)
recommends setting up a disposable VM and installing ChefDK in it, as
the tutorial modifies the host machine.  By using Vagrant, you can
work with your favorite editor on your own machine, and then ssh into
the guest machine and apply recipes.

## Prerequisites

* [Vagrant](http://vagrantup.com/downloads)
* [VirtualBox](https://www.virtualbox.org/)

## Usage

* clone this repository, or copy the Vagrantfile to your local machine.

* Run `vagrant up workstation` to set up the ubuntu VM with the Chef
  DK.  This also creates a `chef-repo` synced file in the local folder
  (ignored by git).

* Run `vagrant up node` to create a new node that Chef will manage.

### Creating cookbooks

Generate a cookbook in the VM:

    vagrant ssh workstation
    cd ~/chef-repo
    chef generate cookbook learn_chef_apache2

Through Vagrant syncing, this file is then available for editing
locally machine (under `./chef-repo/cookbooks/learn_chef_apache2`).
Changes are synced to the VM.

Note: the `chef-repo/` directory is ignored in Git.

### Applying recipes or cookbooks to the development environment

Pertains to tutorials:

* https://learn.chef.io/learn-the-basics/ubuntu/configure-a-package-and-service/
* https://learn.chef.io/learn-the-basics/ubuntu/make-your-recipe-more-manageable/

Running recipes or cookbooks will change the hosted VM, and so the
commands will need to be run as root.  Either use `sudo` or `su` with
password vagrant, e.g.:

    $ vagrant ssh workstation
    ...
    vagrant@server:~$ su
    Password:   # the password is vagrant
    root@server:/home/vagrant# cd chef-repo/
    root@server:/home/vagrant/chef-repo# chef-apply hello.rb
    Recipe: (chef-apply cookbook)::(chef-apply recipe)
    ...

The host machine's port 8080 is forwarded to the `workstation` guest
VM's port 80.  After running the `webserver.rb` recipe, you will be
able to see the site at http://localhost:8080.

### Using Chef to manage the `node` VM

Pertains to tutorial: https://learn.chef.io/manage-a-node/ubuntu/bootstrap-your-node/

The `node` VM has private IP address 192.168.1.10, with the default
vagrant user.  You can provision this node as follows:

    $ vagrant ssh workstation
    vagrant@workstation:~$ ping 192.168.1.10   # verify node is up
    vagrant@workstation:~$ cd chef-repo/
    vagrant@workstation:~/chef-repo$ knife bootstrap 192.168.1.10 --ssh-user vagrant --ssh-password 'vagrant' --sudo --use-sudo-password --node-name node1 --run-list 'recipe[learn_chef_apache2]'

The host machine's port 8081 is forwarded to the `node` guest VM's
port 80, so when the provisioning is complete you can view the results
in your browser at http://localhost:8081/.


## Shutdown

Use `vagrant [halt|suspend|destroy]` to stop your VM(s), depending on
how you want to shut down.  See the [Vagrant
documentation](https://docs.vagrantup.com/v2/getting-started/teardown.html)
for details.

Using `vagrant destroy workstation` will *not* delete any recipes or
cookbooks you're editing in the `chef-repo/`.
