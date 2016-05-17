class vagrant {

         Package { ensure => "installed", allowcdrom => "true" }

        package { "virtualbox": }
        package { "vagrant": }

        file {'/home/xubuntu/vagrant_project':
        ensure => 'directory',
        owner => 'xubuntu',
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

Notice: /Stage[main]/Vagrant/Exec[init]/returns: /usr/lib/ruby/vendor_ruby/vagrant/environment.rb:592:in `expand_path': couldn't find HOME environment -- expanding `~' (ArgumentError)
Notice: /Stage[main]/Vagrant/Exec[init]/returns: 	from /usr/lib/ruby/vendor_ruby/vagrant/environment.rb:592:in `setup_home_path'
Notice: /Stage[main]/Vagrant/Exec[init]/returns: 	from /usr/lib/ruby/vendor_ruby/vagrant/environment.rb:110:in `initialize'
Notice: /Stage[main]/Vagrant/Exec[init]/returns: 	from /usr/bin/vagrant:105:in `new'
Notice: /Stage[main]/Vagrant/Exec[init]/returns: 	from /usr/bin/vagrant:105:in `<main>'
Error: vagrant init returned 1 instead of one of [0]
Error: /Stage[main]/Vagrant/Exec[init]/returns: change from notrun to 0 failed: vagrant init returned 1 instead of one of [0]



http://www.olindata.com/blog/2014/07/installing-vagrant-and-virtual-box-ubuntu-1404-lts
