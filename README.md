# Rock64AnsibleELK
![Follow on Twitter](https://img.shields.io/twitter/follow/initroott?label=Follow%20&style=social)
[![Medium Article](https://img.shields.io/badge/Medium-View%20Medium%20Article-blue)](https://medium.com/@fbotes2/secure-dns-ids-and-wifi-ap-using-arm64-rock64-a0faa85bd833)
![GitHub last commit](https://img.shields.io/github/last-commit/initroot/Rock64AnsibleELK)
![GitHub stars](https://img.shields.io/github/stars/initroot/Rock64AnsibleELK)

The below outlines my current automated (or progress towards) build for running ELK stack on a ROCK64.
I've added OSSEC to the mix, but with modifications you can run anything. This is to be an update on my work published here:

https://medium.com/@fbotes2/secure-dns-ids-and-wifi-ap-using-arm64-rock64-a0faa85bd833

## ROCK64 SETUP


### PREP SETUP:
	sudo apt-get install npm ant texinfo default-jdk build-essential rbenv sshpass
	sudo apt-get install ruby-dev
	wget -qO - https://adoptopenjdk.jfrog.io/adoptopenjdk/api/gpg/key/public | sudo apt-key add -
	sudo add-apt-repository --yes https://adoptopenjdk.jfrog.io/adoptopenjdk/deb/

	sudo apt-get update && sudo apt-get install adoptopenjdk-8-hotspot
	sudo update-alternatives --config java
	Set correct imezone on Armbian
   
## ANSIBLE:
Run the ansible setups. Short descriptions below for each file.

* elk-rpi.yml - Base playbook. Setup variables under /vars and enable correct roles.
* hosts - Setup your IP and account used for Ansible. You'll see its currently defined as [RPI2]
* confbackups - Backup storage of my current config files.
* /vars/vars-rpi.yml - Playbook for ansible. Modify here for download locations and version numbers.
* /roles/... - Setup tasks to run for each role here.
	
It would be good for a quick introduction in Ansible, you can proceed to read here:

https://www.guru99.com/ansible-tutorial.html

## CONFIGS TO UPDATE STILL MANUAL:
Run the below configurations manually after running the Anisble script. These have not been automated yet.

### ELK STACK SETUP:
	If you rerun the Ansible script remove failed items as its already installed and rerun. Can always attempt to do manually, but defeats the purpose.

### LOGSTASH:
	.//usr/share/logstash/bin/logstash-plugin install logstash-filter-translate  
	gem install ci_reporter_rspec -v '1.0.0'
	gem install nokogiri -v '1.10.5'
	  
	systemctl daemon-reload
	systemctl enable logstash

### FILEBEAT:
	scp .\filebeat-oss-6.5.4-armhf.deb root@192.168.1.109:/home/root/
	dpkg -i filebeat-oss-6.5.4-armhf.deb

	logstash-plugin update logstash-input-beats

### OSSEC:
Install OSSEC from source

	wget https://github.com/ossec/ossec-hids/archive/3.3.0.tar.gz
	tar -zxvf 3.3.0.tar.gz ossec-hids-3.3.0/
	cd ossec-hids-3.3.0/
	sudo PCRE2_SYSTEM=yes ./install.sh
	wget https://ftp.pcre.org/pub/pcre/pcre2-10.32.tar.gz
	ls
	tar xzf pcre2-10.32.tar.gz -C src/external
	./install.sh
	 
Install wazuh plugin

## Debugging

### KIBANA:
	#Manual steps (only run if automation fail and debugging):
	wget http://node-arm.herokuapp.com/node_latest_armhf.deb
	dpkg -i node_latest_armhf.deb

	sudo tar -xzf kibana-6.5.4-linux-x86_64.tar.gz -C /opt/
	sudo mv -r /opt/kibana-6.5.4-linux-x86_64 /opt/kibana

	 
	#Replace Kibana node with our one
	sudo rm /opt/kibana/node/bin/node
	sudo rm /opt/kibana/node/bin/npm

	#Symlink it
	sudo ln -s /usr/local/bin/node /opt/kibana/node/bin/node
	sudo ln -s /usr/local/bin/npm /opt/kibana/node/bin/npm

	#Ensure older node is used
	sudo n install 8.14.0

	#Test run service for kibana
	/opt/kibana/bin/kibana  "-c /opt/kibana/config/kibana.yml" --allow-root

	systemctl status kibana
	
## TODO
- [x] Logstash Playbook
- [x] Elastic Playbook
- [x] Kibana Playbook
- [ ] Create playbook for Filebeat
- [ ] Update Logstash Playbook with new changes
- [ ] Create playbook for OSSEC
- [ ] Perform Cleanup and Debugging
 
## LINKS:
 * https://practicalassurance.com/blog/ossec-elk-stack-integration/  
 * https://ackcent.com/blog/elk-on-a-raspberry-pi/
 * https://github.com/MHHK89/Snort-RPi-ELK
 * http://bagl.io/sec/2016/06/18/Balancing-an-ELK-on-top-of-a-Raspberry-part-one 
 * https://www.tripwire.com/state-of-security/security-data-protection/sweet-security-part-2-creating-a-defensible-raspberry-pi/
 * https://www.ossec.net/docs/cookbooks/recipes/elasticstack.html



