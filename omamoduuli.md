lass vagrant {
        Package { ensure => "installed", allowcdrom => "true" }

        package { "virtualbox": }
        package { "vagrant": }

        service {'virtualbox':
                enable => "true",
                require => Package["virtualbox"],
        }

        service {'vagrant':
                enable => "true",
                require => Package["vagrant"],
        }

        exec { 'add_box':
        command => 'vagrant box add precise32 http://files/vagrantup.com/precise32.box',
        path    => ['/bin','/sbin/','/usr/bin/','/usr/sbin/']
  }
}
