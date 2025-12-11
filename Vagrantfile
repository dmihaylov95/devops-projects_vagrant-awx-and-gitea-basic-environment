# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|
    
  config.ssh.insert_key = false

  config.vm.define "ans" do |ans|
    ans.vm.box = "almalinux/9"
	ans.vm.box_version = "9.6.20250522"
    ans.vm.hostname = "ans.do1.lab"
    ans.vm.network "private_network", ip: "192.168.99.99"
    ans.vm.synced_folder "vagrant/", "/vagrant"
    ans.vm.provider "virtualbox" do |vb|
	  vb.name = "ans"
      vb.cpus = "4"
      vb.memory = "12288" # 12 GB RAM
    end
	
	ans.vm.provision "shell", inline: <<EOS
echo "* Install updates ..."
sudo dnf update -y
EOS

    ans.vm.provision "shell", inline: <<EOS
echo "* Install Ansible Core ..."
dnf install -y ansible-core
ansible-galaxy collection install -p /usr/share/ansible/collections ansible.posix
EOS

    ans.vm.provision "shell", inline: <<EOS
echo "* Add Docker repository ..."
dnf -y install dnf-plugins-core
dnf config-manager --add-repo https://download.docker.com/linux/rhel/docker-ce.repo

echo "* Install Docker ..."
dnf install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

echo "* Enable and start Docker ..."
systemctl enable docker
systemctl start docker

echo "* Add vagrant user to docker group ..."
usermod -aG docker vagrant
EOS

    ans.vm.provision "shell", inline: <<EOS
echo "* Enable NodeJS 18 module ..."
sudo dnf module enable nodejs:18 -y

echo "* Install NodeJS 18 ..."
sudo dnf install nodejs -y
EOS

	ans.vm.provision "shell", inline: <<'EOS'
echo "* Install n package ..."
sudo npm install -g n

echo "* Add the n package path to sudoers ..."
n_dir=$(dirname "$(whereis -b n | awk '{print $2}')")
if ! sudo grep -q "$n_dir" /etc/sudoers; then
  sudo sed -i "s|\(Defaults[[:space:]]*secure_path[[:space:]]*=[[:space:]]*\)\(.*\)|\1\2:$n_dir|" /etc/sudoers
fi
EOS

	ans.vm.provision "shell", inline: <<EOS
echo "* Clone AWX repository ..."
git clone https://github.com/ansible/awx.git --depth 1
EOS

	ans.vm.provision "shell", inline: <<'EOS'
echo "* Build AWX UI ..."
sudo dnf install make -y
cd awx
sudo make clean/ui ui

echo "* Start AWX docker containers ..."
make docker-compose COMPOSE_UP_OPTS=-d

echo "* Waiting for AWX UI to be ready ..."
sleep 300

echo "* Create AWX UI superuser ..."
docker exec -i tools_awx_1 bash -c '
export DJANGO_SUPERUSER_USERNAME=awx
export DJANGO_SUPERUSER_PASSWORD=awx
export DJANGO_SUPERUSER_EMAIL=awx@example.com
awx-manage createsuperuser --noinput || echo "Superuser already exists"
'
EOS

	ans.vm.provision "shell", inline: <<EOS
echo "* Build Gitea local version control ..."
cd ..
mkdir gitea
cd gitea
cat <<'YAML' > ./docker-compose.yaml
networks:
  gitea:
    external: false

services:
  server:
    image: docker.gitea.com/gitea:1.24.3
    container_name: gitea
    environment:
      - USER_UID=1000
      - USER_GID=1000
      - GITEA__security__INSTALL_LOCK=true
      - GITEA__security__SECRET_KEY=supersecretkey123
      - GITEA__database__DB_TYPE=sqlite3
      - GITEA__server__ROOT_URL=http://192.168.99.99:8888/
      - GITEA_ADMIN_USERNAME=gitea
      - GITEA_ADMIN_PASSWORD=giteagitea
      - GITEA_ADMIN_EMAIL=gitea@example.com
    restart: always
    networks:
      - gitea
    volumes:
      - ./gitea:/data
      - /etc/timezone:/etc/timezone:ro
      - /etc/localtime:/etc/localtime:ro
    ports:
      - "8888:3000"
      - "222:22"
YAML
docker compose up -d

echo "* Waiting for Gitea to start ..."
sleep 60
docker exec -u 1000 gitea gitea admin user create \
    --username gitea \
    --password giteagitea \
    --email gitea@example.com \
    --admin \
    --config /data/gitea/conf/app.ini
EOS

  end

  config.vm.define "alma1" do |alma1|
    alma1.vm.box = "almalinux/9"
	alma1.vm.box_version = "9.6.20250522"
    alma1.vm.hostname = "alma1.do1.lab"
    alma1.vm.network "private_network", ip: "192.168.99.101"
	alma1.vm.provider "virtualbox" do |vb|
	  vb.name = "alma1"
    end
	alma1.vm.provision "shell", inline: <<EOS
echo "* Install updates ..."
sudo dnf update -y
EOS

  end

  config.vm.define "deb1" do |deb1|
    deb1.vm.box = "debian/bookworm64"
    deb1.vm.box_version = "12.20250126.1"
    deb1.vm.hostname = "deb1.do1.lab"
    deb1.vm.network "private_network", ip: "192.168.99.201"
	deb1.vm.provider "virtualbox" do |vb|
	  vb.name = "deb1"
    end
	deb1.vm.provision "shell", inline: <<EOS
echo "* Install updates ..."
sudo apt-get update -y
sudo apt-get upgrade -y
EOS

  end

end
