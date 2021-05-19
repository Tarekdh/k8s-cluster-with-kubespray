Vagrant.configure(2) do |config|
  common = <<-SHELL
  if ! grep -q deploykub /etc/hosts; then  sudo echo "192.168.6.120     deploykub" >> /etc/hosts ;fi
  if ! grep -q node01 /etc/hosts; then  sudo echo "192.168.6.121     node01" >> /etc/hosts ;fi
  if ! grep -q node05 /etc/hosts; then  sudo echo "192.168.6.125     node05" >> /etc/hosts ;fi
  #if ! id admin >  /dev/null 2>&1 ; then sudo useradd -G 10 -p $(openssl passwd -1 redhat) admin; fi
  #if [ ! -f /etc/sudoers.d/admin ] ; then sudo echo "admin        ALL=(ALL)       NOPASSWD: ALL" > /etc/sudoers.d/admin ; fi
  sudo apt update
  sudo apt install software-properties-common
  sudo add-apt-repository ppa:deadsnakes/ppa
  sudo apt update
  sudo apt -y install vim tree net-tools telnet git python3-pip
  #sudo apt update
  #sudo pip3 install ansible==2.7.6
  sudo echo "autocmd filetype yaml setlocal ai ts=2 sw=2 et" > /home/vagrant/.vimrc
  sed -i 's/ChallengeResponseAuthentication no/ChallengeResponseAuthentication yes/g' /etc/ssh/sshd_config
  sudo systemctl restart sshd
  SHELL
  

	config.vm.box = "ubuntu/bionic64"
	config.vm.box_url = "ubuntu/bionic64"

	config.vm.define "deploykub" do |control|
		control.vm.hostname = "deploykub"
		control.vm.network "private_network", ip: "192.168.6.120"
		control.vm.provider "virtualbox" do |v|
			v.customize [ "modifyvm", :id, "--cpus", "1" ]
			v.customize [ "modifyvm", :id, "--memory", "512" ]
			v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
      			v.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
			v.customize ["modifyvm", :id, "--name", "deploykub"]
		end
		config.vm.provision :shell, :inline => common
	end
	config.vm.define "node01" do |node1|
		node1.vm.hostname = "node01"
		node1.vm.network "private_network", ip: "192.168.6.121"
		node1.vm.provider "virtualbox" do |v|
			v.customize [ "modifyvm", :id, "--cpus", "2" ]
			v.customize [ "modifyvm", :id, "--memory", "2048" ]
			v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
      			v.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
			v.customize ["modifyvm", :id, "--name", "node01"]
		end
		config.vm.provision :shell, :inline => common
	end
	
	config.vm.define "node05" do |node5|
		node5.vm.hostname = "node05"
		node5.vm.network "private_network", ip: "192.168.6.125"
		node5.vm.provider "virtualbox" do |v|
			v.customize [ "modifyvm", :id, "--cpus", "1" ]
			v.customize [ "modifyvm", :id, "--memory", "2048" ]
			v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
      			v.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
			v.customize ["modifyvm", :id, "--name", "node05"]
		end
		config.vm.provision :shell, :inline => common
	end

end