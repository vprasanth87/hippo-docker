# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

$bootstrap_script = <<SCRIPT
  wget -q -O - http://pkg.jenkins-ci.org/debian/jenkins-ci.org.key | apt-key add -
  sh -c 'echo deb http://pkg.jenkins-ci.org/debian binary/ > /etc/apt/sources.list.d/jenkins.list'
  # Adding an apt gpg key is idempotent.
  wget -q -O - https://get.docker.io/gpg | apt-key add -
  # Creating the docker.list file is idempotent, but it may overwrite desired
  # settings if it already exists.  This could be solved with md5sum but it
  # doesn't seem worth it.
  echo 'deb http://get.docker.io/ubuntu docker main' > /etc/apt/sources.list.d/docker.list

  apt-get update -q

  apt-get -q -y --force-yes install \
    openjdk-7-jdk \
    maven \
    git \
    jenkins \
    lxc-docker

  sudo -u jenkins cp -rv /vagrant/jenkins/* /var/lib/jenkins/

  usermod -a -G docker "vagrant"
  usermod -a -G docker "jenkins"

  # Need to restart jenkins to make sure his group permissions and the plugin come through.
  service jenkins restart
SCRIPT

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  # All Vagrant configuration is done here. The most common configuration
  # options are documented and commented below. For a complete reference,
  # please see the online documentation at vagrantup.com.

  # Every Vagrant virtual environment requires a box to build off of.
  config.vm.box = "raring64"

  # The url from where the 'config.vm.box' box will be fetched if it
  # doesn't already exist on the user's system.
  config.vm.box_url = "http://cloud-images.ubuntu.com/vagrant/raring/current/raring-server-cloudimg-amd64-vagrant-disk1.box"

  config.vm.provision :shell, :inline => $bootstrap_script

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  config.vm.network :forwarded_port, guest: 8080, host: 8080, auto_correct:true
  config.vm.network :forwarded_port, guest: 8081, host: 8081, auto_correct:true

  config.vm.provider "virtualbox" do |v|
    v.customize ["modifyvm", :id, "--memory", "4096", "--cpus", "4"]
  end
end
