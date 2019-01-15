
Vagrant.configure("2") do |config|
  config.vm.box = "bento/ubuntu-18.10"

  config.vm.provider "vmware_desktop" do |v|
    v.vmx["memsize"] = "2048"
    v.vmx["numvcpus"] = "2"

    v.gui = false
  end

  # Dokku is available on port 80
  config.vm.network "forwarded_port", guest: 80, host: 8080
  config.vm.hostname = "dokku.me"
  config.vm.network :private_network, ip: "10.0.0.2"

  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  config.vm.provision "shell", inline: <<-SHELL
    # setup dokku apt repository
    wget -nv -O - https://packagecloud.io/dokku/dokku/gpgkey | apt-key add -
    export SOURCE="https://packagecloud.io/dokku/dokku/ubuntu/"
    export OS_ID="$(lsb_release -cs 2> /dev/null || echo "trusty")"
    echo "utopicvividwilyxenialyakketyzestyartfulbionic" | grep -q "$OS_ID" || OS_ID="trusty"
    echo "deb $SOURCE $OS_ID main" | tee /etc/apt/sources.list.d/dokku.list

    apt-get update

    apt-get install -y nginx
    ufw allow 'Nginx Full'

    # install docker
    wget -nv -O - https://get.docker.com/ | sh

    # install dokku
    apt-get install -y dokku

    dokku plugin:install-dependencies --core
  SHELL
end
