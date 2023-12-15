Vagrant.configure("2") do |config|

  # name the VMs
  config.vm.define "k3s1" do |node|

    # which image to use
    node.vm.box = "opensuse/Leap-15.5.x86_64"

    # sizing of the VMs
    node.vm.provider "libvirt" do |lv|
      lv.random_hostname = true
      lv.memory = 8192
      lv.cpus = 4
    end

    # set the hostname
    node.vm.hostname = "k3s1"

    # disable the shared folder
    node.vm.synced_folder ".", "/vagrant", disabled: true

    node.vm.provision "ansible" do |ansible|
      ansible.compatibility_mode = "2.0"
      ansible.playbook = "ansible/playbook-vagrant.yml"
    end # node.vm.provision

    node.trigger.after :destroy do |trigger|
      trigger.warn = "Removing ansible/k3s-kubeconfig"
      trigger.run = {inline: "rm -vf ansible/k3s-kubeconfig"}
    end

  end # config.vm.define servers

end # Vagrant.configure
