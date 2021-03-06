#Simple Chef Cookbook for MySQL on Ubuntu 14.04

Usage :

create your git repository with a Berksfile, a environments directory, and a Vagrantfile.


In the Berksfile

    cookbook 'git'
    cookbook 'apt', git: 'git://github.com/opscode-cookbooks/apt.git'
    cookbook 'mysql-simple', git: 'git://github.com/christopher5106/mysql-simple.git'

In environments/development.rb :

```json
  {
    "name": "development",
    "description": "The master development branch",
    "cookbook_versions": {},
    "json_class": "Chef::Environment",
    "chef_type": "environment",
    "default_attributes": {},
    "override_attributes":
    {
      "environment":"development",
      "mysql":{
        "server_root_password":"XXXXXXXXXXXXXX",
        "datadir":"/var/lib/mysql",
        "logdir":"/var/log/mysql"
      }
    }
  }
```

In the Vagrantfile

    VAGRANTFILE_API_VERSION = "2"

    Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
      config.vm.box = "ubuntu/trusty64"
      config.vm.network "forwarded_port", guest: 3306, host: 3306
      config.berkshelf.enabled = true;
      config.vm.provision :chef_solo do |chef|
        chef.cookbooks_path = "./"
        chef.add_recipe "mysql-simple"
        chef.environments_path = "environments"
        chef.environment = "development"
      end
    end

In Opsworks,

- create a stack with your git repository, berkshelf enabled, and the json :

```json
{
      "mysql":{
        "server_root_password":"XXXXXXXXXXXXXX",
        "datadir":"/var/lib/mysql",
        "logdir":"/var/log/mysql"
      }
}
```

- create a layer and add the recipe "mysql-simple" to a layer

- create an app with name equals to the name of the app in the JSON.


Launch your instance in Opsworks.

