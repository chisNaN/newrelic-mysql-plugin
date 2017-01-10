#Install MySQL New Relic plugin agent

>Process for Ubuntu 16.04 AWS ec2 instance

###Install JDK

`sudo apt install default-jdk`

###Install the newrelic agent

```
echo "license_key: YOUR_LICENSE_KEY" | sudo tee -a /etc/newrelic-infra.yml

curl https://download.newrelic.com/infrastructure_agent/gpg/newrelic-infra.gpg | sudo apt-key add -

printf "deb [arch=amd64] https://download.newrelic.com/infrastructure_agent/linux/apt xenial main" | sudo tee -a /etc/apt/sources.list.d/newrelic-infra.list

sudo apt-get update

sudo apt-get install newrelic-infra -y
```

###Create the newrelic mysql user ("password" as password)

(Install a mysql client if not done)

Connect to the mysql docker container: `mysql -u root -h 127.0.0.1 -p` (password)

and execute the following commands

```
CREATE USER newrelic@% IDENTIFIED BY PASSWORD '*2470C0C06DEE42FD1618BB99005ADCA2EC9D1E19';

GRANT PROCESS,REPLICATION CLIENT ON *.* TO newrelic@%;
```
###Install the NPI tool

`LICENSE_KEY=YOUR_KEY_HERE bash -c "$(curl https://download.newrelic.com/npi/release/install-npi-linux-debian-x64.sh)"`

###Install the MySQL plugin

 `./npi install com.newrelic.plugins.mysql.instance`

Your `./npi/plugins/com.newrelic.plugins.mysql.instance/newrelic_mysql_plugin-2.0.0/config/plugin.json`

should be configured like this:

```js
{
  "agents": [
    {
      "name"    : "Your mysql Project",
      "host"    : "127.0.0.1",
      "metrics" : "status,newrelic",
      "user"    : "newrelic",
      "passwd"  : "password"
    }
  ]
}
``

###Enable and start the service

Enable the service: `sudo ./npi add-service com.newrelic.plugins.mysql.instance`

Start the service: `sudo ./npi start com.newrelic.plugins.mysql.instance`

Check if the plugin is running: `./npi list`

The plugin should be available in the "plugins" tab of your newrelic account
