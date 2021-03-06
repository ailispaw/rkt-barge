# A dummy plugin for Barge to set hostname and network correctly at the very first `vagrant up`
module VagrantPlugins
  module GuestLinux
    class Plugin < Vagrant.plugin("2")
      guest_capability("linux", "change_host_name") { Cap::ChangeHostName }
      guest_capability("linux", "configure_networks") { Cap::ConfigureNetworks }
    end
  end
end

RKT_VERSION     = "v1.30.0"
ACBUILD_VERSION = "v0.4.0"
GO_VERSION      = "1.7.6"

Vagrant.configure(2) do |config|
  config.vm.define "rkt-barge"

  config.vm.box = "ailispaw/barge"
  config.vm.box_version = ">= 2.4.0"

  config.vm.synced_folder ".", "/vagrant"

  config.vm.network :forwarded_port, guest: 5000, host: 5000
  config.vm.network :forwarded_port, guest: 8080, host: 8080

  config.vm.provision :shell do |sh|
    sh.inline = <<-EOT
      # https://coreos.com/rkt/docs/latest/trying-out-rkt.html

      # Install rkt
      if [ ! -d /opt/rkt-#{RKT_VERSION} ]; then
        echo "Installing rkt #{RKT_VERSION}"
        cd /opt
        wget -qO- https://github.com/coreos/rkt/releases/download/#{RKT_VERSION}/rkt-#{RKT_VERSION}.tar.gz | tar xz
        cp /opt/rkt-#{RKT_VERSION}/rkt /opt/bin/
        cp /opt/rkt-#{RKT_VERSION}/bash_completion/rkt.bash /etc/bash_completion.d/rkt
        sed -i 's/^getent/#getent/g' /opt/rkt-#{RKT_VERSION}/scripts/setup-data-dir.sh

        # Configure rkt group
        addgroup -S rkt
        addgroup bargee rkt
        addgroup -S rkt-admin
        addgroup bargee rkt-admin
      fi

      if [ ! -d /opt/acbuild-#{ACBUILD_VERSION} ]; then
        echo "Installing acbuild #{ACBUILD_VERSION}"
        cd /opt
        wget -qO- https://github.com/containers/build/releases/download/#{ACBUILD_VERSION}/acbuild-#{ACBUILD_VERSION}.tar.gz | tar xz
        cp /opt/acbuild-#{ACBUILD_VERSION}/acbuild /opt/bin/
      fi

      if [ ! -d /opt/go ]; then
        echo "Installing go v#{GO_VERSION}"
        cd /opt
        wget -qO- https://storage.googleapis.com/golang/go#{GO_VERSION}.linux-amd64.tar.gz | tar xz
        echo "export GOROOT=/opt/go" >> /home/bargee/.bash_profile
        echo "export GOPATH=\\$HOME/go" >> /home/bargee/.bash_profile
        echo "export PATH=$PATH:\\$GOROOT/bin:\\$GOPATH/bin" >> /home/bargee/.bash_profile
        chown bargee:bargees /home/bargee/.bash_profile
      fi
    EOT
  end

  config.vm.provision :shell, run: "always" do |sh|
    sh.inline = <<-EOT
      # Install extra packages on every boot, because it's not persistent
      /etc/init.d/docker start
      pkg install acl
      pkg install make
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
      mount -t cgroup2 none /sys/fs/cgroup
    EOT
  end
end
