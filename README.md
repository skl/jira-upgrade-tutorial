# Upgrading JIRA

This tutorial walks you through an upgrade of JIRA 6.0.2 to 6.4.4. First you
start off with a clean installation of 6.0.2 and run through the setup. Then
you will upgrade this installation to 6.4.4.

The following configuration is assumed and are the JIRA installation defaults:
* Installation Directory: /usr/local/JIRA
* Home Directory: /var/atlassian/application-data
* Database: Builtin Evaluation Database (HSQL in-memory DB)

Caveats:
* Doesn't take an external database into account (e.g. MySQL)

## 6.0.2 to 6.4.4

Spin up the Virtualbox VM in this directory with Vagrant, when it's running
you'll be to access the setup screen for JIRA 6.0.2 on http://localhost:8080/

```
# May take a few minutes
vagrant up
```

When you first access JIRA, if it does not detect an existing installation,
then either one does not exist or the database configuration was not picked
up correctly.

### First-time Setup of 6.0.2

If you wish to monitor the JIRA log during setup, execute the following:

```
vagrant ssh

# Monitoring the server log
sudo tail -f /usr/local/JIRA/logs/catalina.out

# Monitoring the application log
sudo tail -f /var/atlassian/application-data/jira/log/atlassian-jira.log
```

1. Access http://localhost:8080/
1. Set "English (UK)" and "Built In" database
1. Click Next
1. Click Next
1. Follow the instructions to generate a key
1. Setup the admin account
1. Set "Later"
1. Click Finish

Assuming all is well at this point, continue with the upgrade
to 6.4.4.

### Upgrade to 6.4.4

The unattended mode of the JIRA Linux Installer allows you to automated some of the
upgrade process using an previous installation.

#### Download JIRA version 6.4.4

```
vagrant ssh
wget https://www.atlassian.com/software/jira/downloads/binary/atlassian-jira-6.4.4-x64.bin
chmod +x atlassian-jira-6.4.4-x64.bin
```

#### Archive the current version 6.0.2

```
# Shutdown JIRA
sudo /usr/local/JIRA/bin/stop-jira.sh

# Archive the JIRA Installation Directory
sudo mv /usr/local/JIRA /usr/local/JIRA.6.0.2

# Archive the JIRA Home Directory
sudo tar czf /var/atlassian/application-data.6.0.2.tar.gz /var/atlassian/application-data

# At this point in production you would backup the database
# mysqldump -u jirauser -p jiradb | gzip > jira.6.0.2.sql.gz

# Copy the installation configuration from the previous version
cp /usr/local/JIRA.6.0.2/.install4j/response.varfile .

# Run the installation script
sudo ./atlassian-jira-6.4.4-x64.bin -q -varfile response.varfile
```

At this point, JIRA should be upgraded to 6.4.4.

### Downgrade to 6.0.2

A downgrade simply restores the backups taken prior to the upgrade:

```
# Shutdown JIRA
sudo /usr/local/JIRA/bin/stop-jira.sh

# Swap back Installation Directory
sudo rm -rf /usr/local/JIRA
sudo mv /usr/local/JIRA.6.0.2 /usr/local/JIRA

# Restore Home Directory
sudo rm -rf /var/atlassian/application-data
sudo mkdir /var/atlassian/application-data
sudo tar xzf /var/atlassian/application-data.6.0.2.tar.gz -C /var/atlassian/application-data --strip-components=3

# At this point in production you would restore the database
# mysql -u root -p -e 'drop database jiradb;'
# mysql -u root -p -e 'create database jiradb;'
# gunzip < jira.6.0.2.sql.gz | mysql -u root -p jiradb

# Start JIRA
sudo /usr/local/JIRA/bin/start-jira.sh

# Watch the startup logs
sudo tail -f /usr/local/JIRA/logs/catalina.out
```

Once JIRA has started back up you should be back in pre-upgrade state.
