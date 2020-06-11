# Rock64AnsibleELK
![Follow on Twitter](https://img.shields.io/twitter/follow/initroott?label=Follow%20&style=social)
[![Medium Article](https://img.shields.io/badge/Medium-View%20Medium%20Article-blue)](https://medium.com/@fbotes2/secure-dns-ids-and-wifi-ap-using-arm64-rock64-a0faa85bd833)
![GitHub last commit](https://img.shields.io/github/last-commit/initroot/Rock64AnsibleELK)
![GitHub stars](https://img.shields.io/github/stars/initroot/Rock64AnsibleELK)

The below outlines my current automated (or progress towards) build for running ELK stack on a ROCK64.
I've added OSSEC to the mix, but with modifications you can run anything. This is to be an update on my work published here:

https://medium.com/@fbotes2/secure-dns-ids-and-wifi-ap-using-arm64-rock64-a0faa85bd833

## NOTES TODO Apr 2020
Dietpi --> Lower requirements

### PREP SETUP:
	sudo apt-get install npm ant texinfo build-essential rbenv sshpass software-properties-common
	sudo apt-get install ruby-dev
	wget -qO - https://adoptopenjdk.jfrog.io/adoptopenjdk/api/gpg/key/public | sudo apt-key add -
	sudo add-apt-repository --yes https://adoptopenjdk.jfrog.io/adoptopenjdk/deb/

	sudo apt-get update && sudo apt-get install adoptopenjdk-8-hotspot
	sudo update-alternatives --config java
	
## Elastic 
https://discuss.elastic.co/t/elasticsearch-7-x-support-for-arm64-raspberry-pi-4-b/187976/2

	dpkg -i --force-all --ignore-depends=libc6 elasticsearch-7-x-x.deb
	relink java jdk with symlink.
	sudo rm -r /usr/share/elasticsearch/jdk
	sudo mkdir /usr/share/elasticsearch/jdk/bin
	which java 
	sudo ln -s /usr/lib/jvm/adoptopenjdk-8-hotspot-arm64/bin/java /usr/share/elasticsearch/jdk/bin/java
	#Update packages to ignore libc6
	sudo nano /var/lib/dpkg/status
	#Remove libc from elastic
	
	
## Logstash
https://gist.github.com/alexalouit/a857a6de10dfdaf7485f7c0cccadb98c

	1. Default install with dpkg -i --force-all logstash-7-x-x.deb
	
	- name: download maxmind geoip database
	  get_url:
	    url: http://geolite.maxmind.com/download/geoip/database/GeoLite2-City.mmdb.gz
	    dest: /tmp/GeoLite2-City.mmdb.gz
	  register: geoip_db_dl

	- name: extract geoip database
	  shell: 'gunzip < /tmp/GeoLite2-City.mmdb.gz > /etc/logstash/GeoLite2-City.mmdb'
	  when: geoip_db_dl|changed

	Run logstashfix.sh modifed to this git version

	/usr/share/logstash/bin/logstash-plugin install logstash-filter-translate
	/usr/share/logstash/bin/logstash-plugin install logstash-codec-netflow
	/usr/share/logstash/bin/logstash-plugin list 
	gem install ci_reporter_rspec -v '1.0.0'
	gem install nokogiri -v '1.10.5'
	  
	systemctl daemon-reload
	systemctl enable logstash


## Kibana Configs
	https://github.com/cblakely/openwrt-elk-dashboard
	#WGET new version 
	sudo dpkg -i --force-all kibana-xxxxx
	#install the required nodejs for arm
	#Replace Kibana node with our one
	sudo rm /opt/kibana/node/bin/node
	sudo rm /opt/kibana/node/bin/npm

	#Symlink it
	sudo ln -s /usr/local/bin/node /opt/kibana/node/bin/node
	sudo ln -s /usr/local/bin/npm /opt/kibana/node/bin/npm

	#Get ruby ready	
	npm install -g n
	n lts
	sudo n install 10.19.0

	#Test run service for kibana
	/opt/kibana/bin/kibana  "-c /opt/kibana/config/kibana.yml" --allow-root

	systemctl status kibana


# OLD BELOW
		
## ANSIBLE:
Run the ansible setups. Short descriptions below for each file.

* elk-rpi.yml - Base playbook. Setup variables under /vars and enable correct roles.
* hosts - Setup your IP and account used for Ansible. You'll see its currently defined as [RPI2]
* confbackups - Backup storage of my current config files.
* /vars/vars-rpi.yml - Playbook for ansible. Modify here for download locations and version numbers.
* /roles/... - Setup tasks to run for each role here.
	
It would be good for a quick introduction in Ansible, you can proceed to read here:

https://www.guru99.com/ansible-tutorial.html


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



