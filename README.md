# Important!

[`peru`](https://app.vagrantup.com/peru/) seems to regularly build new boxes off of the windows eval licenses, which
is great because their licenses only last 180 days from build time. So,
keep bumping the `box_version` in the vagrantfile and/or packerfile to point
to an unexpired peru box.

# Push a new version

    vagrant package
    vagrant cloud publish ...

# Vagrantfile to use the box built from this project

need to bundle this as a template in the box on vagrantcloud...
```
Vagrant.configure("2") do |config|
 #config.vm.box = "deargle/vuln-windows-2019"
 #config.vm.box_version = "0.0.1"
 config.vm.box = "vuln-win-test-3"

 config.vm.provider "libvirt" do |libvirt|
   libvirt.cpus           = "2"
   libvirt.disk_bus       = "virtio"
   libvirt.driver         = "kvm"
   libvirt.graphics_type  = "spice"
   libvirt.nic_model_type = "virtio"
   libvirt.sound_type     = "ich6"
   libvirt.video_type     = "qxl"

   libvirt.channel :type  => 'spicevmc', :target_name => 'com.redhat.spice.0',     :target_type => 'virtio'
   libvirt.channel :type  => 'unix',     :target_name => 'org.qemu.guest_agent.0', :target_type => 'virtio'
   libvirt.random  :model => 'random'

   # Enable Hyper-V enlightments: https://blog.wikichoon.com/2014/07/enabling-hyper-v-enlightenments-with-kvm.html
   libvirt.hyperv_feature :name => 'relaxed',  :state => 'on'
   libvirt.hyperv_feature :name => 'stimer',   :state => 'off'
   libvirt.hyperv_feature :name => 'synic',    :state => 'on'
   libvirt.hyperv_feature :name => 'vapic',    :state => 'on'
   libvirt.hyperv_feature :name => 'vpindex',  :state => 'on'
   libvirt.memory = 2048
 end

 config.vm.network "forwarded_port", guest: 22, host: 2222

 # Port forward for RDP
 config.vm.network :forwarded_port, guest: 3389, host: 3389, id: "rdp", auto_correct:true
 # Port forward for WinRM
 config.vm.network :forwarded_port, guest: 5986, host: 5986, id: "winrm-ssl", auto_correct:true
 config.vm.network :forwarded_port, guest: 5985, host: 5985, id: "winrm", auto_correct:true

 config.vm.boot_timeout      = 1000
 config.vm.communicator      = "winrm"
 config.vm.guest             = :windows
 config.windows.halt_timeout = 15
 config.winrm.password       = "vagrant"
 config.winrm.retry_limit    = 30
 config.winrm.username       = "vagrant"

 # Disable NFS sharing (==> default: Mounting NFS shared folders...)
 config.vm.synced_folder ".", "/vagrant", type: "nfs", disabled: true

 if Vagrant.has_plugin?("vagrant-winrm-syncedfolders")
   config.vm.synced_folder ".", "/vagrant", type: "winrm"
 else
   config.vm.synced_folder ".", "/vagrant", disabled: true
 end

 config.vm.network :private_network,
     :ip => "192.168.56.100",
     :libvirt__network_name => "infosec-net",
     :libvirt__dhcp_enabled => false,
     :libvirt__host_ip => "192.168.56.101",
     :autostart => true

end
```
