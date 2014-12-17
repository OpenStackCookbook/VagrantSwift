# -*- mode: ruby -*-
# vi: set ft=ruby :

nodes = {
    'proxy'	=> [1, 110],
    'swift1'  => [1, 221],
    'swift2'  => [1, 222],
}

Vagrant.configure("2") do |config|
    # Virtualbox
    config.vm.box = "bunchc/trusty-x64"
    config.vm.synced_folder ".", "/vagrant", type: "nfs"

    # VMware Fusion / Workstation
    config.vm.provider "vmware_fusion" or config.vm.provider "vmware_workstation" do |vmware, override|
      override.vm.box = "bunchc/trusty-x64"
      override.vm.synced_folder ".", "/vagrant", type: "nfs"

      # Fusion Performance Hacks
      vmware.vmx["logging"] = "FALSE"
      vmware.vmx["MemTrimRate"] = "0"
      vmware.vmx["MemAllowAutoScaleDown"] = "FALSE"
      vmware.vmx["mainMem.backing"] = "swap"
      vmware.vmx["sched.mem.pshare.enable"] = "FALSE"
      vmware.vmx["snapshot.disabled"] = "TRUE"
      vmware.vmx["isolation.tools.unity.disable"] = "TRUE"
      vmware.vmx["unity.allowCompostingInGuest"] = "FALSE"
      vmware.vmx["unity.enableLaunchMenu"] = "FALSE"
      vmware.vmx["unity.showBadges"] = "FALSE"
      vmware.vmx["unity.showBorders"] = "FALSE"
      vmware.vmx["unity.wasCapable"] = "FALSE"
      vmware.vmx["vhv.enable"] = "TRUE"
    end

    #Default is 2200..something, but port 2200 is used by forescout NAC agent.
    config.vm.usable_port_range= 2800..2900 

    # Sync folder for proxy cache
    # config.vm.synced_folder "apt-cacher-ng/", "/var/cache/apt-cacher-ng"


    nodes.each do |prefix, (count, ip_start)|
        count.times do |i|
            hostname = "%s" % [prefix, (i+1)]

            config.vm.define "#{hostname}" do |box|
                box.vm.hostname = "#{hostname}.rpc"
   		# eth0 Nat (auto)
		# eth1 host network
                box.vm.network :private_network, ip: "172.16.0.#{ip_start+i}", :netmask => "255.255.0.0"

                box.vm.provision :shell, :path => "keystone.sh"
                box.vm.provision :shell, :path => "#{prefix}.sh"

                # If using Fusion
                box.vm.provider :vmware_fusion do |v|
                    v.vmx["memsize"] = 1024
        	    if prefix == "compute"
	              	v.vmx["memsize"] = 2048
	            elsif prefix == "proxy"
    	                v.vmx["memsize"] = 512
	            end
                end

                # Otherwise using VirtualBox
                box.vm.provider :virtualbox do |vbox|
	            # Defaults
                    vbox.customize ["modifyvm", :id, "--memory", 2048]
                    vbox.customize ["modifyvm", :id, "--cpus", 2]
                end
            end
        end
    end
end
