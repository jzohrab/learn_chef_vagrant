# Chef DK Vagrant box

This provides an Ubuntu development environment for the tutorials at
[learnchef](https://learn.chef.io).

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
* Run `vagrant up` to set up the ubuntu VM with the Chef DK.  This
  also creates a `chef-repo` synced file in the local folder (ignored
  by git).

### Creating cookbooks

Generate a cookbook in the VM:

    vagrant ssh
    cd ~/chef-repo
    chef generate cookbook learn_chef_apache2

Through Vagrant syncing, this file is then available for editing
locally machine (under `./chef-repo/cookbooks/learn_chef_apache2`).
Changes are synced to the VM.

Note: the `chef-repo/` directory is ignored in Git.

### Applying recipes or cookbooks

Running recipes or cookbooks will change the hosted VM, and so the
commands will need to be run as root.  Either use `sudo` or `su` with
password vagrant, e.g.:

    $ vagrant ssh
    ...
    vagrant@server:~$ su
    Password:   # the password is vagrant
    root@server:/home/vagrant# cd chef-repo/
    root@server:/home/vagrant/chef-repo# chef-apply hello.rb
    Recipe: (chef-apply cookbook)::(chef-apply recipe)
    ...

## Shutdown

Use `vagrant [halt|suspend|destroy]` to stop, depending on how you
want to shut down.  See the [Vagrant
documentation](https://docs.vagrantup.com/v2/getting-started/teardown.html)
for details.

Using `chef destroy` will *not* delete any recipes or cookbooks you're
editing in the `chef-repo/`.
