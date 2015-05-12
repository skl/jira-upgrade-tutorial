# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|
  config.vm.box = "ubuntu/trusty64"
  config.vm.network "forwarded_port", guest: 8080, host: 8080

  config.vm.provider "virtualbox" do |v|
    v.customize ["modifyvm", :id, "--memory", 2048]
    v.customize ["modifyvm", :id, "--cpus", 1]
    v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    v.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
  end

  config.vm.provision "shell", inline: <<-SHELL
      echo 'Downloading JIRA 6.0.2'
      wget -nv https://www.atlassian.com/software/jira/downloads/binary/atlassian-jira-6.0.2-x64.bin
      chmod +x atlassian-jira-6.0.2-x64.bin
      sudo ./atlassian-jira-6.0.2-x64.bin -q
      echo 'JIRA 6.0.2 now available on http://localhost:8080/'
  SHELL
end
