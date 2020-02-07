idolactivities.coind
==============

Deploys and configures the Bitcoin Core daemon, bitcoind.

This role has been written in a way so that Bitcoin forks and altcoins can be
supported, but does not currently support any others yet.

Quickstart
----------

If you already have Ansible installed and are pretty familiar with it, then:

    ansible-galaxy install idolactivities.coind --force
    ansible-playbook -i your.server.fqdn, ~/.ansible/roles/idolactivities.coind/examples/playbook_bitcoin.yml

Refer to that playbook for more information. You'll likely want to create your
own playbook (and store secrets using Ansible Vault or something).

If you don't have a server, you can also use Vagrant:

    # after following the installation step from earlier...
    cd ~/.ansible/roles/idolactivities.coind/
    vagrant up

The `bitcoind` running in that VM will be accessible from your machine, so you
can use `bitcoin-cli` if you have it installed:

    bitcoin-cli -testnet=1 -rpcuser=apiuser -rpcpassword=apipass getnetworkinfo

Development
-----------

If you are interested in contributing to this role, you may want to use the
following setup for testing changes with Vagrant locally.

    # Your clone method may differ (e.g. if you forked this repository)
    git clone git@github.com:idolactivities/ansible-role-coind.git
    cd ansible-role-coind/
    mkdir -p ~/.ansible/roles/
    ln -s $PWD ~/.ansible/roles/idolactivities.coind

We're effectively installing the Ansible role here, but it'll stay up to date
with any local modifications.

To bring up the development VM, use Vagrant with your favourite provider:

    vagrant up # brings up the VM and runs the playbook specified in the Vagrantfile
    vagrant provision # runs the playbook only, requires VM to be up
    vagrant destroy # stops and deletes the VM

Depending on what you're changing, you may want to write another playbook under
`examples/` and update the `Vagrantfile` to use that playbook.

Role Variables
--------------

If you're lost, you may want to review the content within the `examples/`
directory to get an idea of how to write your own playbook.

All of these role variables are completely optional (at least, at this point in
time) for a working installation, but you'll likely want to set a few.

    coind_name: bitcoin

Specifies which coin's software to deploy. Currently, only `bitcoin` is
supported, but support for other coins will be implemented in due time.

    coind_install_cli: no

Specifies whether or not to install the CLI utility on the destination host.
This should only be necessary if you plan to SSH into the host and interact
directly with the coin's RPC API using it.

    coind_user: "{{ coind_name }}"
    coind_group: "{{ coind_user }}"
    coind_home: "/var/lib/{{ coind_name }}"

Specifies the user to run the coin software as (by default, the name of the
coin itself) and the location of its block database, etc.

    coind_url: "{{ _coind_urls[coind_name] }}"
    coind_version: "{{ _coind_latest_version[coind_name] }}"
    coind_sha256sum: "{{ _coind_sha256sums[coind_name][coind_version] }}"

Specifies where to download the release tarball from, which version to use, and
the SHA256 sum of that tarball. These are by default defined in variables in
`vars/main.yml` so you can validate these yourself, but you can easily just
override these with your own.

    coind_offline_install: no
    coind_offline_artifact_path: "~/.cache/ansible/{{ coind_name }}"
    coind_offline_artifact_tarball: "{{ coind_offline_artifact_path }}/{{ coind_name }}-{{ coind_version }}.tar.gz"

For hosts with limited Internet access, you may want to download release
tarballs locally first and then push those files to the host. Set
`coind_offline_install` to yes to enable this. The `artifact_path` here is
where Ansible will download the tarballs locally, so make sure you have
permission to create/read it, and `artifact_tarball` is the path to the tarball
itself. The role does verify hashes in either situation.

    coind_binary: "{{ _coind_binary[coind_name] }}"
    coind_binary_cli: "{{ _coind_binary_cli[coind_name] }}"

These are the binary names of the coin daemon and CLI utility and are by
default defined in `vars/main.yml`. They're typically in the form of
`{{ coind_name }}d` and `{{ coind_name }}-cli`.

    coind_config_global: {}
    coind_config_main: {}
    coind_config_test: {}
    coind_config_regtest: {}

These get templated into the coin's configuration file and are typically
key-stringvalue dictionaries. The global config is where most configuration
options will typically fall into, but there are a few options that by default
only apply to mainnet and must be defined in their own section to be applied to
testnet/regtest (e.g. `port`, `rpcbind`).

For a list of configuration options, refer to the official documentation of the
coin that you are deploying. Note that the configuration file also accepts most
command line flags as well, so place any of those here. There is also an
example in the `defaults/main.yml` file that may prove useful.
