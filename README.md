# homeserver

An Ansible playbook that sets up an Ubuntu-based home media server.

Assumes a fresh install of Ubuntu Server 20.04 on the host, and an Ubuntu-based distro on the client machine (for playbook configuration and running).

## Setup

Update and upgrade packages (reboot if necessary), and install Ansible:

```
sudo apt update
sudo apt upgrade
sudo apt install ansible
```

Generate an ssh key to the host (accepting defaults). Replace the email, port number, host IP address, and username with your stuff:

```
ssh-keygen -o -a 100 -t ed25519 -f ~/.ssh/homeserver -C youremail@example.com
ssh-copy-id -p 1111 -i ~/.ssh/homeserver host_username@1.1.1.1
```

Clone this repo on the client.

```
git clone https://github.com/dukeofjukes/homeserver
```

<!-- Create a host variable file. -->
<!---->
<!-- ``` -->
<!-- cd homeserver/ -->
<!-- mkdir -p host_vars/YOUR_HOSTNAME -->
<!-- vi host_vars/YOUR_HOSTNAME -->
<!-- ``` -->

Create a `vars.yml` file for your host to override variables in `./group_vars/all/vars.yml`.

```
mkdir -p group_vars/YOUR_HOSTNAME/
cp ./group_vars/all/vars.yml ./group_vars/YOUR_HOSTNAME/
vi ./group_vars/YOUR_HOSTNAME/vars.yml
```

Create a `host` file with these contents:

```
[YOUR_HOSTNAME]
YOUR_HOSTNAME ansible_host=1.1.1.1 ansible_port=1111 ansible_user=host_username ansible_connection=ssh ansible_ssh_private_key_file=/home/client_username/.ssh/homeserver
```

Run the playbook:

```
ansible-playbook run.yml -K
```

Note: The `-K` parameter is only necessary for the first run, since the playbook configures passwordless sudo.

For later runs, you may only want to update certain ansible roles. To do so, you can run the playbook like this, using tags from `run.yml`:

```
ansible-playbook run.yml --tags"containers"
```

## Extra Configuration

Certain containers require extra configuration outside of running the playbook in order to work properly. Those configurations are listed here.

### [binhex/arch-delugevpn](https://github.com/binhex/arch-delugevpn)

Before starting the container, place a wireguard configuration file (e.g. `wg0.conf`) from your VPN in `~/docker/delugevpn/config/wireguard/`. The container will fail to start without this file.
