# WebAnno
WebAnno server installation

The following installation is based on Debian Operating Sysyem(Jessie)
sudo su -
Login as the root user:

You're now logged in as the root user. Run the following commands:

1.  Add the repository source
`echo "deb-src http://ppa.launchpad.net/webupd8team/java/ubuntu trusty main" | tee -a /etc/apt/sources.list.d/webupd8team-java.list`

2.  verify it with the keys
    `apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys EEA14886`

3.  Update the apt-get package manager
    `apt-get update`

4.  Install the java8 package
    `apt-get install oracle-java8-installer`
5.  Check whether the installation is successful using the following command:
    `java -version`
6.  Create a user for the tomcat Server (before we install the Tomcat server):
    `sudo adduser --system --shell /bin/bash --gecos 'Tomcat Java Servlet and JSP engine' --group --disabled-password --home /home/tomcat tomcat`
7.  Create a directory called tmp:
    `mkdir -p ~/tmp`
8.  Choose the directory: `cd ~/tmp`
9. Download the Apache Tomcat Server from a mirror:
    `wget http://muug.ca/mirror/apache-dist/tomcat/tomcat-8/v8.5.20/bin/apache-tomcat-8.5.20.tar.gz`
10. unzip the tar contents:
    `tar xvzf ./apache-tomcat-8.5.20.tar.gz`
11. Once you unzip the contents, you can delete the gzip file:
    `rm ./apache-tomcat-8.5.20.tar.gz`
12. Move the Apache Tomcat directory to /usr/share
    `sudo mv ./apache-tomcat-8.5.20/ /usr/share/`
13. remove the older tomcat directory(if you have any) to make sure you are using the newest version:
    `sudo rm -f /usr/share/tomcat`
14. Provide the soft link to Apache tomcat directory
    `sudo ln -s /usr/share/apache-tomcat-8.5.5 /usr/share/tomcat`
15. Install the apache server.
    `apt-get install apache2`
16. Just to verify whether apache2 installed correctly
    `apache2`
17. Following are the commands to check what users are available on the system:
    groups
18.  ls /opt/
19.  ls -a /opt/
20.  ls /var/
21.  ls /var/opt/
22.  ls /var/www/
23.  ls /var/www/html/
24.  ls /var/www/html/index.html
25.  ls /etc/apache2/
26. Load and source the environment configurations of apache2 server
    `source /etc/apache2/envvars`
27. Start the Apache server:
    `/etc/init.d/apache2 start`
28. To print the process info of apache server:
    `ps -ef | grep apache`
29. Install the MySql server:
    `sudo apt-get install mysql-server`
30.  Now we will need to add the `character-set-server=utf8` line to the *my.cnf* file
31.  Open the my.cnf file in nano file editor and add this line `nano /etc/mysql/my.cnf`
32.  mysql -u root -p <mysql_root_password_here>
33.  aptitude install tomcat8-user authbind
34.  cd /opt/
35.  tomcat8-instance-create -p 18080 -c 18005 webanno
36.  chown -R www-data /opt/webanno
37.  nano /etc/init.d/webanno
38.  ###### webanno vars to be added in init.d/webanno file
39.  nano /etc/init.d/webanno
40.  chmod +x /etc/init.d/webanno
41.  update-rc.d webanno defaults
42.  wget https://github.com/webanno/webanno/releases/download/webanno-3.2.2/webanno-webapp-3.2.2.war
43.  cp webanno-webapp-3.2.2.war /opt/webanno/webapps/webanno.war
44.  mkdir /srv/webanno
45.  nano /srv/webanno/settings.properties
46.  mysql --version
47.  nano /srv/webanno/settings.properties
48.  chown -R www-data /srv/webanno
49.  service webanno start
50.  ps -ef | grep python
51.  ps -ef | grep web
52.  ps -ef | grep webanno
53.  netstat -lp | grep webanno
54.  netstat -lp
55.  wget https://bin.equinox.io/c/4VmDzA7iaHb/ngrok-stable-linux-amd64.zip
56.  unzip ngrok-stable-linux-amd64.zip
57.  apt-cache search unzip
58.  apt-get install unzip
59.  unzip ngrok-stable-linux-amd64.zip
60.  ./ngrok authtoken <Auth_token_from_ngrok>
61.  ./ngrok http 18080
62.  nano /root/.ngrok2/ngrok.yml
63.  nohup ./ngrok http 18080
64.  nano nohup.out
65.  ps -ef | grep ngrok
66.  kill -9 17134
67.  ngrok -log=stdout 18080 > ngrok.log &
68.  ps -ef | grep ngrok
