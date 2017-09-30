# WebAnno
### WebAnno Server Installation Guide (Version 3.2.2)

The following installation is based on **Debian Operating System (Jessie 8.4.4)** and the installation commands are executed as a root user.

First of all, Login as the **root user** using the following command: <br>
  `sudo su -`

You're now logged in as the **root** user. Run the following commands:

1.  Add the repository source: <br>
    `echo "deb http://ppa.launchpad.net/webupd8team/java/ubuntu trusty main" | tee /etc/apt/sources.list.d/webupd8team-java.list` <br>
    `echo "deb-src http://ppa.launchpad.net/webupd8team/java/ubuntu trusty main" | tee -a /etc/apt/sources.list.d/webupd8team-java.list`

2.    verify it with the keys: <br>
    `apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys EEA14886`

3.  Update the apt-get package manager: <br>
    `apt-get update`

4.  Install the java8 package: <br>
    `apt-get install oracle-java8-installer` <br>
    `apt-get install oracle-java8-set-default`

5.  Check whether the installation is successful using the following command: <br>
    `java -version`

6.  Create a user for the tomcat Server (before we install the Tomcat server): <br>
    `sudo adduser --system --shell /bin/bash --gecos 'Tomcat Java Servlet and JSP engine' --group --disabled-password --home /home/tomcat tomcat`

7.  Create a directory called tmp: <br>
    `mkdir -p ~/tmp`

8.  Choose the directory: <br>
    `cd ~/tmp`

9. Download the Apache Tomcat Server from a mirror: <br>
    `wget http://muug.ca/mirror/apache-dist/tomcat/tomcat-8/v8.5.20/bin/apache-tomcat-8.5.20.tar.gz`

10. unzip the tar contents: <br>
    `tar xvzf ./apache-tomcat-8.5.20.tar.gz`

11. Once you unzip the contents, you can delete the gzip file: <br>
    `rm ./apache-tomcat-8.5.20.tar.gz`

12. Move the Apache Tomcat directory to /usr/share : <br>
    `sudo mv ./apache-tomcat-8.5.20/ /usr/share/`

13. remove the older tomcat directory(if you have any) to make sure you are using the newest version: <br>
    `sudo rm -f /usr/share/tomcat`

14. Provide the soft link to Apache tomcat directory: <br>
    `sudo ln -s /usr/share/apache-tomcat-8.5.5 /usr/share/tomcat`

15. Install the apache server: <br>
    `apt-get install apache2`

16. Load and source the environment configurations of apache2 server: <br>
    `source /etc/apache2/envvars`

17. Start the Apache server: <br>
    `/etc/init.d/apache2 start`

18. To print the process info of apache server: <br>
    `ps -ef | grep apache`

19. Install the MySql server: <br>
    `sudo apt-get install mysql-server`

20.  Now we will need to add the `character-set-server=utf8` line to the *my.cnf* file(read the next step)

21.  Open the my.cnf file in nano file editor using `nano /etc/mysql/my.cnf` and add this line `character-set-server=utf8` at the end of the file <br>
(You can exit the nano editor with `Ctrl`+`x` , then type `y` at the prompt and hit `Enter`)

22. Login to MySQL:<br>
    `mysql -u root -p` <br>
    Enter your password at the prompt

23. Execute following commands in Mysql interpreter: <br>

    Create a database user called **webanno** with your password **'enter_your_password'** which is later used by the application to access the database (instructions for settings.properties file below).

    *mysql>* `CREATE DATABASE webanno DEFAULT CHARACTER SET utf8 COLLATE utf8_bin ;`

    *mysql>* `CREATE USER 'webanno'@'localhost' IDENTIFIED BY 'enter_your_password';`

    *mysql>* `GRANT ALL PRIVILEGES ON webanno.* TO 'webanno'@'localhost';`

    *mysql>* `exit`

24. Install **aptitude** package manager with following command: <br>
    `aptitude install tomcat8-user authbind`

25. Create a directory called */opt*: <br>
    `cd /opt/`

26. Create a new Tomcat Instance: <br>
    `tomcat8-instance-create -p 18080 -c 18005 webanno`

27. Change the permissions of */opt/webanno*: <br>
    `chown -R www-data /opt/webanno`

28. Open the webanno configuration file in nano editor using: <br>
    `nano /etc/init.d/webanno`

29.  Paste the following script in the file: <br>
```bash
    #!/bin/sh

    # Licensed under the Apache License, Version 2.0: http://www.apache.org/licenses/LICENSE-2.0

    # kFreeBSD do not accept scripts as interpreters, using #!/bin/sh and sourcing.
    if [ true != "$INIT_D_SCRIPT_SOURCED" ] ; then
        set "$0" "$@"; INIT_D_SCRIPT_SOURCED=true . /lib/init/init-d-script
    fi
    ### BEGIN INIT INFO
    # Provides:          webanno
    # Required-Start:    $remote_fs $syslog
    # Required-Stop:     $remote_fs $syslog
    # Default-Start:     2 3 4 5
    # Default-Stop:      0 1 6
    # Short-Description: WebAnno init script
    # Description:       This file should be placed in /etc/init.d. It
    #                    allows starting/stopping WebAnno using the
    #                    "service" command and ensures that WebAnno starts
    #                    when the system is booted.
    ### END INIT INFO

    # Author: Richard Eckart de Castilho

    NAME="WebAnno"
    DAEMON=none
    WEBANNO_HOME="/srv/webanno"
    WEBANNO_PORT="18080"
    WEBANNO_USER="www-data"
    CATALINA_BASE="/opt/webanno"
    AUTHBIND=""
    JAVA_OPTS="-Djava.awt.headless=true -Xmx750m -XX:+UseConcMarkSweepGC -Dwebanno.home=$WEBANNO_HOME"

    setup_authbind() {
      # log_action_msg "Setting up authbind configuration for $DESC on port $WEBANNO_PORT"
      touch /etc/authbind/byport/$WEBANNO_PORT
      chmod 500 /etc/authbind/byport/$WEBANNO_PORT
      chown $WEBANNO_USER /etc/authbind/byport/$WEBANNO_PORT
      AUTHBIND="authbind --deep"
    }

    tomcat_pid() {
      echo `ps -fe | grep -- "-Dcatalina.base=$CATALINA_BASE" | grep -v grep | tr -s " "|cut -d" " -f2`
    }

    do_start_cmd_override() {
      if [ $WEBANNO_PORT -lt 1024 ]
      then
        setup_authbind
      fi

      su - www-data -s "/bin/bash" -c "JAVA_OPTS=\"$JAVA_OPTS\" $AUTHBIND $CATALINA_BASE/bin/startup.sh" > /dev/null 2>&1
    }

    do_stop_cmd_override() {
      su - www-data -s "/bin/bash" -c "$CATALINA_BASE/bin/shutdown.sh" > /dev/null 2>&1
    }

    do_status() {
      local pid
      pid=$(tomcat_pid)
      if [ -n "$pid" ]
      then
        log_action_msg "Status $DESC: running"
        return 0
      else
        log_action_msg "Status $DESC: stopped"
        return 1
      fi
    }
```
You can exit the nano editor with `Ctrl`+`x` , then type `y` at the prompt and hit `Enter`

30. Change the permissions of the above script file to an executable: <br>
    `chmod +x /etc/init.d/webanno`

31. Update the boot script to include webanno while starting system: <br>
    `update-rc.d webanno defaults`

32. Download the Webanno Web archive(.WAR) file: <br>
    `wget https://github.com/webanno/webanno/releases/download/webanno-3.2.2/webanno-webapp-3.2.2.war`

33. Moving the .WAR file to /opt/webanno/webapps: <br>
    `cp webanno-webapp-3.2.2.war /opt/webanno/webapps/webanno.war`

34. Create a directory `/srv/webanno` using following command: <br>
    `mkdir /srv/webanno`

35. Open the file *settings.properties* in the nano editor: <br>
    `nano /srv/webanno/settings.properties`

36. Paste the following properties definition: <br>
  ```sql
  database.dialect=org.hibernate.dialect.MySQL5InnoDBDialect
  database.driver=com.mysql.jdbc.Driver
  database.url=jdbc:mysql://localhost:3306/webanno?useSSL=false
  database.username=webanno
  database.password=t0t4llYSecreT

  # 60 * 60 * 24 * 30 = 30 days
  backup.keep.time=1000000

  # 60 * 5 = 5 minutes
  backup.interval=1000

  backup.keep.number=10

  ui.brat.sentences.number = 5
  ```

    You can exit the nano editor with `Ctrl`+`x` , then type `y` at the prompt and hit `Enter`

37. Change the permissions of webanno home directory: <br>
    `chown -R www-data /srv/webanno`

38. **Finally, Start WebAnno!** <br>

    `service webanno start` <br>

    WebAnno will be up and running at `http://localhost:18080/webanno`

39. Following commands will display all the active ports with the service name: <br>
    `netstat -lp`
<br>
  Following are the commands to run ngrok. <br>
40.  wget https://bin.equinox.io/c/4VmDzA7iaHb/ngrok-stable-linux-amd64.zip
41.  unzip ngrok-stable-linux-amd64.zip
42.  apt-get install unzip
43.  unzip ngrok-stable-linux-amd64.zip
45.  ./ngrok authtoken <Auth_token_from_ngrok>
46.  ./ngrok http 18080
47.  nano /root/.ngrok2/ngrok.yml
48.  nohup ./ngrok http 18080
49.  nano nohup.out
50.  ngrok -log=stdout 18080 > ngrok.log &
51.  ps -ef | grep ngrok
