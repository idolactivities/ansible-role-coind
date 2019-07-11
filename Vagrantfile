Vagrant.configure("2") do |config|
  config.vm.box = "debian/stretch64"

  # This exposes the RPC API port of bitcoind at 127.0.0.1:18332, allowing you
  # to use `bitcoin-cli` from your dev machine to interact with the instance:
  #
  #     bitcoin-cli -testnet=1 -rpcuser=apiuser -rpcpassword=apipass getnetworkinfo
  config.vm.network "forwarded_port", guest: 18332, host: 18332, auto_correct: true

  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "examples/playbook_bitcoin.yml"
  end
end
