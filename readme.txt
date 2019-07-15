# Austin Weng
### Udacity Nanodegree in Full Stack Development


### Server Info
- IP Address: 3.89.48.235
- SSH Port: 
- URL: http://3.89.48.235
- User Password for Grader: password

### Software and Configuration Changes

# Software used:
    Amazon Lightsail

    Apache2   
    - `sudo apt-get install apache2`

    Github
    - `sudo apt-get install git`

    PostgreSQL
    - `sudo apt-get install libpq-dev python-dev`
    - `sudo apt-get install postgresql postgresql-contrib`

    pip
    - Install pip with `sudo apt-get install python-pip`

    Flask
    - Install Flask `pip install Flask`

    mod_wsgi
    - 'sudo apt-get install libapache2-mod-wsgi python-dev`

    Other Item Catalog Project Libraries
    - `sudo pip install httplib2 oauth2client sqlalchemy psycopg2 sqlalchemy_utils`


# Configuration Changes:
    a. Added grader user
    - '$ sudo adduser grader`
    - Create new file: `sudo vim /etc/sudoers.d/grader`
    - Add the following: `grader ALL=(ALL:ALL) ALL`

    b. Update/upgrade packages
    - Download the latest package lists with `sudo apt-get update`
    - Install the new versions of the packages `sudo apt-get upgrade`

    c. Change SSH Port
    - Run `sudo view /etc/ssh/sshd_config`
    - Change the port from 22 to 2200 and uncomment this line
    - In the Networking tab of your instance in Lightsail, add a custom TCP protocol for port 2200 to your firewall

    d. Configure Firewall 
    - `sudo ufw allow 2200/tcp`
    - `sudo ufw allow 80/tcp`
    - `sudo ufw allow 123/udp`
    - `sudo ufw enable`

    e. Change Timezone to UTC
    - Run `sudo dpkg-reconfigure tzdata` and select UTC

    f. Disable login for root user
    - Run `sudo vim /etc/ssh/sshd_config`
    - Change `PermitRootLogin without-password` line to `PermitRootLogin no` and uncomment
    - Restart ssh with `sudo service ssh restart`

    g. Clone Restaurant Site from Github
    - `cd /var/www`
    - `sudo mkdir restaurant`
    - Allow grader access to folder `sudo chown -R grader:grader restaurant`
    - `cd /restaurant`
    - Clone your project from github `git clone https://github.com/aweng93/restaurant_site restaurant`
    - Create restaurant.wsgi and add the following to the file:

        import sys
        import logging
        logging.basicConfig(stream=sys.stderr)
        sys.path.insert(0, "/var/www/restaurant/")
  
        from restaurant import app as application
        application.secret_key = 'supersecretkey'

    h. Install Virtual Environment
    - Install `sudo pip install virtualenv`
    - Create `sudo virtualenv venv`
    - Activate `source venv/bin/activate`
    - Change permissions `sudo chmod -R 777 venv`
    
    i. Configure virtual host
      - Run this: `sudo nano /etc/apache2/sites-available/restaurant.conf`
    - Paste this code: 
  
        <VirtualHost *:80>
            ServerName 35.167.27.204
            ServerAdmin admin@35.167.27.204
            WSGIDaemonProcess restaurant python-path=/var/www/restaurant:/var/www/restaurant/venv/lib/python2.7/site-packages
            WSGIProcessGroup restaurant
            WSGIScriptAlias / /var/www/restaurant/restaurant.wsgi
            <Directory /var/www/restaurant/restaurant/>
                Order allow,deny
                Allow from all
            </Directory>
            Alias /static /var/www/restaurant/restaurant/static
            <Directory /var/www/restaurant/restaurant/static/>
                Order allow,deny
                Allow from all
            </Directory>
            ErrorLog ${APACHE_LOG_DIR}/error.log
            LogLevel warn
            CustomLog ${APACHE_LOG_DIR}/access.log combined
        </VirtualHost>
        
    - Enable virtual host `sudo a2ensite restaurant`

    j. Configure PostgreSQL

    - `sudo su - postgres`
    - `psql`
    - `CREATE USER restaurant WITH PASSWORD 'password';`
    - `ALTER USER restaurant CREATEDB;`
    - `CREATE DATABASE restaurant WITH OWNER restaurant;`
    - `\c restaurant`
    - `REVOKE ALL ON SCHEMA public FROM public;`
    - `GRANT ALL ON SCHEMA public TO restaurant;`
    - `\q`
    - `exit`
    - Change create engine lines to: `engine = create_engine('postgresql://restaurant:password@localhost/restaurant')`
    - `python /var/www/restaurant/restaurant/database_setup.py`

### Third Party Resources

https://www.linux.com/LEARN/VIM-101-BEGINNERS-GUIDE-VIM
https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys--2
https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-18-04
https://www.ostechnix.com/configure-apache-virtual-hosts-ubuntu-part-1/
https://help.ubuntu.com/community/UFW
https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps
