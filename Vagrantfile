# A dummy plugin for Barge to set hostname and network correctly at the very first `vagrant up`
module VagrantPlugins
  module GuestLinux
    class Plugin < Vagrant.plugin("2")
      guest_capability("linux", "change_host_name") { Cap::ChangeHostName }
      guest_capability("linux", "configure_networks") { Cap::ConfigureNetworks }
    end
  end
end

RKT_VERSION="v1.11.0"

Vagrant.configure(2) do |config|
  config.vm.define "vagga-barge"

  config.vm.box = "ailispaw/barge"

  config.vm.synced_folder ".", "/vagrant"

  config.vm.network :forwarded_port, guest: 8080, host: 8080

  config.vm.provision :shell do |sh|
    sh.inline = <<-EOT
      # https://github.com/coreos/rkt/blob/master/Documentation/trying-out-rkt.md

      # Install rkt
      if [ ! -d /opt/rkt-#{RKT_VERSION} ]; then
        cd /opt
        wget -qO- https://github.com/coreos/rkt/releases/download/#{RKT_VERSION}/rkt-#{RKT_VERSION}.tar.gz | tar xz
        cp /opt/rkt-#{RKT_VERSION}/rkt /opt/bin/
        cp /opt/rkt-#{RKT_VERSION}/bash_completion/rkt.bash /etc/bash_completion.d/rkt

        # Configure rkt group
        addgroup rkt
        addgroup bargee rkt
      fi
    EOT
  end

  config.vm.provision :shell, run: "always" do |sh|
    sh.inline = <<-EOT
      # Install acl on every boot, because it's not persistent
      /etc/init.d/docker start
      pkg install acl
      /etc/init.d/docker stop

      # Make rkt data persistent
      if [ ! -L /var/lib/rkt ]; then
        rm -rf /var/lib/rkt
        mkdir -p /mnt/data/var/lib/rkt
        ln -s /mnt/data/var/lib/rkt /var/lib/rkt
      fi

      # Setup rkt
      /opt/rkt-#{RKT_VERSION}/scripts/setup-data-dir.sh
      mkdir -p /usr/lib/rkt/stage1-images
      cp /opt/rkt-#{RKT_VERSION}/stage1-*.aci /usr/lib/rkt/stage1-images/
    EOT
  end
end
