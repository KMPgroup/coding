### What is Vagrant?

[Vagrant][1] is a tool for creating and managing virtual development environments. Basicaly it allows you to create encapsulated environment (box) with dedicated tools (mysql/pg, redis, elasticsearch, mongo...) for each project. Those boxes are separated from your operating system. 

### How to run in?

#### installing prerequisites

- vagrant in default configuration uses [virtual box][2] for virtualization - download and instal it
- download and install [Vagrant][1] (there is dmg file)
- install [berkshelf][3] gem `gem install berkshelf`
- install vagrant berkshelf plugin: `vagrant plugin install vagrant-berkshelf`

and you are ready to go with....

#### Basic configuration

Each box uses Vagrantfile to configuration. Inside root of your rails app type:

    $ vagrant init

it will create basic Vagrantfile. Next inside it change line:

    config.vm.box = "base"

with:

    config.vm.box      = 'precise64'
    config.vm.box_url  = 'http://files.vagrantup.com/precise64.box'

It sets Ubuntu 12.04 amd64 as default OS. Go to console and type: 

    $ vagrant up

to download and boot raw machine. After couple of minutes you can connect with it via ssh:

    $ vagrant ssh

This logs you as a vagrant user. All of your project files are mirrored to `/vagrant` folder inside the box.

#### Setup environment

If you type ` ruby -v ` you will see that OS is very raw. So lets update ruby and install everything else. 

Vagrant can use [chef][4] cookbooks. Those scripts could be downloaded form github but there is a great tool for managing cookbooks: [berkshelf][3].

Think of a berkshelf for cookbooks as of a bundler for gems. 
As a basic development box configuration lets install: rvm, elasticsearch, nodejs and mysql.

Create a new file: `Berksfile` inside root of your rails app and fill it with: 

```ruby
site :opscode

cookbook 'apt' #install apt-get
cookbook 'build-essential' #compilers and headers
cookbook 'ruby_build' #installs and comlies rubies
cookbook 'rvm', git: 'https://github.com/fnichol/chef-rvm.git'
cookbook 'java' #for elastiscearch
cookbook 'elasticsearch', git: "https://github.com/elasticsearch/cookbook-elasticsearch.git"
cookbook 'nodejs' #for assets comilation
cookbook 'mysql'
```

next update Vagrantfile:

```ruby
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box      = 'precise64'
  config.vm.box_url  = 'http://files.vagrantup.com/precise64.box'

  config.berkshelf.enabled = true #setup berkshelf as cookbooks provider
  config.vm.provision :chef_solo do |chef|
    chef.add_recipe "apt"
    chef.add_recipe "build-essential"
    chef.add_recipe "ruby_build"
    chef.add_recipe "rvm::system"
    chef.add_recipe "rvm::user"
    chef.add_recipe "rvm::vagrant"
    chef.add_recipe "java"
    chef.add_recipe "elasticsearch"
    chef.add_recipe "nodejs"
    chef.add_recipe "mysql::server"
    chef.add_recipe "mysql::client"
    chef.json = {
      'rvm' => {
        'user_installs' => [
            { 'user'          => 'vagrant',
              'default_ruby'  => '1.9.3-p327',
              'rubies'        => ['1.9.3-p327'],
              'global'        => '1.9.3-p327'
            }
          ]
        },
      'elasticsearch' => {
          "cluster_name" => "elasticsearch_test_chef",
          "bootstrap.mlockall" => false
        },
      'mysql' => {
        'server_root_password' => "pDe86k",
        'server_repl_password' => "pDe86k",
        'server_debian_password' => "pDe86k"
        }
      }
  end
end
```

Now type in console: `vagrant reload` for machine rebooting. It is needed because during system boot Berkshelf downloads cookbooks and then links them inside virtual box.

After successful reboot type: `vagrant provision` to install all recipts. 

> In the future after changing Vagrantfile just run provision to update your box.

After a couple of minutes your machine will be almost ready.

#### Tuning

Now you have to mirror ports you will use to your host. You can do it in Vagrantfile:

```ruby
config.vm.network :forwarded_port, host: 4567, guest: 3000
```

and tune everything a little bit (because standard configuration is very slow). 

To increase speed of virtual box change standard folder sharing method to nfs. In Vagrantfile:

```ruby
config.vm.synced_folder ".", "/vagrant", nfs: true
config.vm.network "private_network", ip: "192.168.50.4" #has to be added to nfs to work (with example ip)
```

reboot machine `vagrant reload` (now vagrant could ask you for host root password) and after `vagrant ssh` test ruby version:

    $ ruby -v
    ruby 1.9.3p327 (2012-11-10 revision 37606) [x86_64-linux]

go to /vagrant `bundle install` and run webrick:

    $ cd /vagrant
    $ bundle install
    $ rails s

Dont forget to check `localhost:4567` in your local browser


  [1]: http://www.vagrantup.com/
  [2]: https://www.virtualbox.org/
  [3]: http://berkshelf.com/
  [4]: http://www.opscode.com/chef/