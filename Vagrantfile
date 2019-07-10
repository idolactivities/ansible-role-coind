Vagrant.configure("2") do |config|
  config.vm.box = "generic/debian9"
  # This exposes the RPC API port of bitcoind at 127.0.0.1:18332, allowing you
  # to run `bitcoin-cli -rpcport 18332 getnetworkinfo` from your dev machine.
  config.vm.network "forwarded_port", guest: 8332, host: 18332, auto_correct: true

  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "examples/playbook_bitcoin.yml"
  end
end
