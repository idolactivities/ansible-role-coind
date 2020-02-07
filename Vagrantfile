Vagrant.configure("2") do |config|
  config.vm.box = "debian/buster64"

  # This exposes the RPC API port of bitcoind at 127.0.0.1:18332, allowing you
  # to use `bitcoin-cli` from your dev machine to interact with the instance:
  #
  #     bitcoin-cli -testnet=1 -rpcuser=apiuser -rpcpassword=apipass getnetworkinfo
  config.vm.network "forwarded_port", guest: 18332, host: 18332, auto_correct: true

  config.vm.provider :libvirt do |libvirt|
    libvirt.memory = 1024
    libvirt.cpus = 1
    # This is not enough for bitcoind, so you'll probably want to change it for a non-test node
    libvirt.storage :file, :size => '2G'
  end

  config.vm.provision :ansible do |ansible|
    ansible.limit = "all,localhost"
    ansible.playbook = "tests/vagrant/package_role.yml"
    ansible.verbose = true
  end
  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "tests/vagrant/provision.yml"
    ansible.verbose = true
  end
end
