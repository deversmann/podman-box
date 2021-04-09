# -*- mode: ruby -*-
# vi: set ft=ruby :

PODMAN_BOX_IP = ENV['PODMAN_BOX_IP']
if PODMAN_BOX_IP.nil? || PODMAN_BOX_IP.empty?
  PODMAN_BOX_IP = "192.168.68.69"
end

PODMAN_BOX_HOSTNAME = ENV['PODMAN_BOX_HOSTNAME']
if PODMAN_BOX_HOSTNAME.nil? || PODMAN_BOX_HOSTNAME.empty?
  PODMAN_BOX_HOSTNAME = "podman-box.local"
end

PODMAN_BOX_MEMORY_MB = ENV['PODMAN_BOX_MEMORY_MB']
if PODMAN_BOX_MEMORY_MB.nil? || PODMAN_BOX_MEMORY_MB.empty?
  PODMAN_BOX_MEMORY_MB = "1024"
end



Vagrant.configure("2") do |config|

  #config.vm.box = "fedora/32-cloud-base"
  config.vm.box = "generic/fedora33"

  config.vm.hostname = PODMAN_BOX_HOSTNAME
  config.vm.network "private_network", ip: PODMAN_BOX_IP, hostname: true

  config.vm.provider "virtualbox" do |vb|
    vb.name = "podman-box"
    vb.memory = PODMAN_BOX_MEMORY_MB
  end

  # config.vm.provision "shell", privileged: true, inline: <<-SHELL
  #   dnf -y install --enablerepo updates-testing podman
  #   loginctl enable-linger vagrant
  # SHELL

  # config.vm.provision "shell", privileged: false, inline: <<-SHELL
  #   systemctl --user enable --now podman.socket
  # SHELL

  config.vm.provision "ansible" do |ansible|
    # ansible.verbose = "v"
    ansible.playbook = "podman-box.yml"
  end

  config.trigger.after :provision, :reload, :resume, :up do |trigger|
    trigger.info = "*** podman remote info: ***"
    #trigger.run = {inline: "bash -c 'echo \"podman ip = $PODMAN_BOX_IP\"'"}
    trigger.ruby do |env,machine|
      puts "\n\e[;32m** active config options (specify before `vagrant up` to change) **\e[0m"
      puts "PODMAN_BOX_IP        = #{PODMAN_BOX_IP}"
      puts "PODMAN_BOX_HOSTNAME  = #{PODMAN_BOX_HOSTNAME}"
      puts "PODMAN_BOX_MEMORY_MB = #{PODMAN_BOX_MEMORY_MB}"
      puts "\n\e[1;34m** local config commands **\e[0m"
      puts "export CONTAINER_SSHKEY=#{machine.ssh_info[:private_key_path][0]}"
      puts "export CONTAINER_HOST=ssh://#{machine.ssh_info[:remote_user]}@#{machine.ssh_info[:host]}:#{machine.ssh_info[:port]}/run/user/1000/podman/podman.sock"
      puts "podman system connection add #{machine.ssh_info[:remote_user]} --identity $CONTAINER_SSHKEY $CONTAINER_HOST"
    end
  end
end
