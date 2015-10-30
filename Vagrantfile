$run_ansible = <<SCRIPT
set -e
apt-get update
apt-get install -y python-dev python-setuptools
easy_install pip
pip install ansible
PYTHONUNBUFFERED=1 ansible-playbook -i LOCAL_FOLDER/deploy/vagrant.sh LOCAL_FOLDER/deploy/vagrant-deploy.yml
SCRIPT

$rebuild = <<SCRIPT
cd LOCAL_FOLDER
export DOCKER_HOST=tcp://localhost:2375
deploy/rebuild
SCRIPT

$configure_iptables = <<SCRIPT
HOST_IP=`netstat -rn | grep "^0.0.0.0 " | cut -d " " -f10`
DOCKER_HOST_IP=172.17.42.1
sysctl -w net.ipv4.conf.eth0.route_localnet=1
iptables -t nat -I PREROUTING -p tcp -i eth0 --dport 2375 -j DNAT --to-destination 127.0.0.1:2375
DEBIAN_FRONTEND=noninteractive apt-get install -y iptables-persistent
service netfilter-persistent save
SCRIPT
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "debian/jessie64"

  config.vm.provider "virtualbox" do |v|
    v.memory = 4096
    v.cpus = 2
  end

  config.vm.network "private_network", type: "dhcp"

  config.vm.provider "virtualbox"

  config.vm.network "forwarded_port", guest: 2375, host_ip: "127.0.0.1", host: 2375
  config.vm.network "forwarded_port", guest: 80, host_ip: "127.0.0.1", host: 8080
  config.vm.network "forwarded_port", guest: 443, host_ip: "127.0.0.1", host: 4433

  if(Vagrant::Util::Platform.windows?)
    local_folder = "/vagrant"

    config.winnfsd.uid = Process.uid
    config.winnfsd.gid = Process.gid
  else
    local_folder = File.dirname(__FILE__)
    config.nfs.map_uid = Process.uid
    config.nfs.map_gid = Process.gid
  end

  config.vm.synced_folder ".", local_folder, type: "nfs"

  config.vm.provision "shell", inline: $run_ansible.gsub('LOCAL_FOLDER', local_folder)
  config.vm.provision "shell", inline: $rebuild.gsub('LOCAL_FOLDER', local_folder)
  config.vm.provision "shell", inline: $configure_iptables
end