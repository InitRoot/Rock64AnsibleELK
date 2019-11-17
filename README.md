# Rock64AnsibleELK

##ROCK64 SETUP:


##PREP SETUP:
	sudo apt-get install npm ant texinfo default-jdk build-essential rbenv sshpass
	sudo apt-get install ruby-dev
	echo "deb https://adoptopenjdk.jfrog.io/adoptopenjdk/deb buster main" | sudo tee /etc/apt/sources.list.d/adoptopenjdk.list
	apt install adoptopenjdk-8-hotspot -y
	sudo update-alternatives --config java
	Set correct imezone on Armbian
   
##ANSIBLE:
	- Run the ansible GIT I modified.

##ELASTIC CONFIG (TO-ADD):
	xpack.ml.enabled: false

##ELK STACK SETUP:
	Run the modified ansible file, remove failed items.

##LOGSTASH:
	.//usr/share/logstash/bin/logstash-plugin install logstash-filter-translate  
	gem install ci_reporter_rspec -v '1.0.0'
	gem install nokogiri -v '1.10.5'
	  
	systemctl daemon-reload
	systemctl enable logstash

##KIBANA:
	wget http://node-arm.herokuapp.com/node_latest_armhf.deb
	dpkg -i node_latest_armhf.deb

	sudo tar -xzf kibana-6.5.4-linux-x86_64.tar.gz -C /opt/
	sudo mv -r /opt/kibana-6.5.4-linux-x86_64 /opt/kibana

	 
	Replace Kibana node with our one
	sudo rm /opt/kibana/node/bin/node
	sudo rm /opt/kibana/node/bin/npm

	Symlink it
	sudo ln -s /usr/local/bin/node /opt/kibana/node/bin/node
	sudo ln -s /usr/local/bin/npm /opt/kibana/node/bin/npm

	Ensure older node is used
	sudo n install 8.14.0

	Test run service for kibana
	/opt/kibana/bin/kibana  "-c /opt/kibana/config/kibana.yml" --allow-root

	systemctl status kibana
 
##FILEBEAT:
	scp .\filebeat-oss-6.5.4-armhf.deb root@192.168.1.109:/home/root/
	dpkg -i filebeat-oss-6.5.4-armhf.deb

	logstash-plugin update logstash-input-beats

##OSSEC:
	- Install OSSEC from source

	wget https://github.com/ossec/ossec-hids/archive/3.3.0.tar.gz
	tar -zxvf 3.3.0.tar.gz ossec-hids-3.3.0/
	cd ossec-hids-3.3.0/
	sudo PCRE2_SYSTEM=yes ./install.sh
	wget https://ftp.pcre.org/pub/pcre/pcre2-10.32.tar.gz
	ls
	tar xzf pcre2-10.32.tar.gz -C src/external
	./install.sh
	 
	Install wazuh plugin
 
##LINKS:
  https://practicalassurance.com/blog/ossec-elk-stack-integration/  
  https://ackcent.com/blog/elk-on-a-raspberry-pi/
  https://github.com/MHHK89/Snort-RPi-ELK
  http://bagl.io/sec/2016/06/18/Balancing-an-ELK-on-top-of-a-Raspberry-part-one 
  https://www.tripwire.com/state-of-security/security-data-protection/sweet-security-part-2-creating-a-defensible-raspberry-pi/
  https://www.ossec.net/docs/cookbooks/recipes/elasticstack.html



