class vagrant {

         Package { ensure => "installed", allowcdrom => "true" }

        package { "virtualbox": }
        package { "vagrant": }

        file {'/home/xubuntu/vagrant_project':
        ensure => 'directory',
        owner => 'root',
        mode => '0770',
        }

        file {'/home/xubuntu/vagrant_project/Vagrantfile':
        content => template("vagrant/Vagrantfile"),
        }
}

VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

config.vm.box = "precise32"

config.vm.box_url = "http://files.vagrantup.com/precise32.box"

end



http://www.olindata.com/blog/2014/07/installing-vagrant-and-virtual-box-ubuntu-1404-lts
