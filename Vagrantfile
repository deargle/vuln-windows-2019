Vagrant.configure("2") do |config|

  config.vm.box = "deargle/vuln-windows-2019"


  config.vm.provider "libvirt" do |libvirt|
    libvirt.cpus = 4
    libvirt.memory = 4096
    libvirt.management_network_name = 'vagrant-libvirt'
    libvirt.management_network_autostart = true
  end

  config.vm.provision "shell", path: "build/scripts/create-labuser.ps1"
  config.vm.provision "shell", path: "build/scripts/disable-dep.bat"
  config.vm.provision "shell", path: "build/scripts/install-icecast.ps1"
  config.vm.provision "shell", path: "build/scripts/set-password-not-expired.bat"

  # to trigger, run `vagrant provision --provision-with install-word`
  config.vm.provision "install-word", type: "shell", run: "never", path: "build/scripts/chocolatey-installs/install-word.ps1"

  # to trigger, run `vagrant provision --provision-with reboot`
  config.vm.provision "reboot", type: "shell", run: "never" do |s|
    s.inline = "echo 'rebooting'"
    s.reboot = true
  end
end
