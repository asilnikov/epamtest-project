# -*- mode: ruby -*-
# vi: set ft=ruby :


Vagrant.configure("2") do |config|
config.vm.box = "centos/7"
config.vm.hostname = "jenkins"
config.vm.network "public_network", ip: "10.40.1.251"
config.vm.define "jenkins"
config.vm.provider "virtualbox" do |vb|
     vb.gui = false
     vb.memory = "1024"
	 vb.name = "vagrant_test"
 end
config.vm.provision "shell", inline: <<-SHELL
	sudo su -
	yum -y install epel-release
	yum -y update
	yum -y install mc
	yum -y install nano
	yum -y install wget
	sed 's/enforcing/disabled/' /etc/sysconfig/selinux
	yum -y install java-1.8.0-openjdk
	yum -y install nginx 
	echo "127.0.0.1 jenkins" > /etc/host
	touch /etc/nginx/conf.d/jenkins.conf;
	echo "server {
        listen 80;
        server_name jenkins;
        access_log /var/log/nginx/jenkins-access.log;
        error_log /var/log/nginx/jenkins-error.log;
location / {
        proxy_pass http://127.0.0.1:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Real-IP $remote_addr;
 }
}
    " >>/etc/nginx/conf.d/jenkins.conf;
	systemctl enable nginx;
	systemctl start nginx;
	
	groupadd jenkins
	useradd -g jenkins -m jenkins
	echo jenkins | passwd jenkins --stdin
	mkdir /opt/jenkins
	mkdir /opt/jenkins/bin
	mkdir /opt/jenkins/master
	chown -R jenkins:jenkins /opt/jenkins
	
	wget -P /opt/jenkins/bin/ http://mirrors.jenkins.io/war-stable/latest/jenkins.war 
	
	touch /etc/systemd/system/jenkins.service
	echo "
	[Unit]
	Description=Jenkins Demon
	After=network.target
	Requires=network.target
	
	[Service]
	Type=simple
	Environment=JENKINS_HOME=/opt/jenkins/master
	ExecStart=/usr/bin/java -jar /opt/jenkins/bin/jenkins.war
	Restart=always
	User=jenkins
	RestartSec=10
	
	[Install]
	WantedBy=multi-user.target " >> /etc/systemd/system/jenkins.service
	systemctl enable jenkins.service;
	systemctl start jenkins
SHELL
end
 