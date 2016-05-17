###Oman moduulin toteutus

##Harjoitusympäristö

Suoritin harjoitusta kotonani 17.5.2016 kannettavalla tietokoneellani.

Harjoituksessa on käytetty Xubuntu 14.04 LTS 64bit live-usb tikkua.

Macbook Pro 13" mid-2010

Prosessori  2,4 GHz Intel Core 2 Duo
Muisti  4 Gt 1067 MHz:n DDR3
Näytönohjain  NVIDIA GeForce 320M 256 Mt
Käyttöjärjestelmä OS X 10.9.5

----

##Aloitusasetukset

Aloitin tehtävän käynnistämällä Xubuntu 14.04 live-usb:lta. Tämän jälkeen avasin terminaalin ja ajoin komennon, jonka avulla näppäimistön kieli saatii vaihdettua suomeksi.

`setxkbmap fi`

Tämän jälkeen päivitin ohjelmiston ajamalla update komennon.

`sudo apt-get update`

Tämän jälkeen asensin vielä puppetin, jotta päästiin aloittamaan itse moduulin teko.

`sudo apt-get install puppet -y`

----

##Oman moduulin rakennus

Tämän moduulin tarkoituksena oli asentaa koneelle kaksi ohjelmistoa: VirtualBox sekä Vagrant. Tämän jälkeen Vagrantille tehtäisiin tarvittavat konfiguroinnit virtuaalikoneiden luontia varten.

Aloitin rakentamalla oikeanlaisen tiedostorakenteen. Ensin loin puppet kansion.

`mkdir puppet`

Tämän jälkeen siirryin juuri luotuun puppet kansioon.

`cd puppet`

Loin puppet kansioon tarvittavat kansiot.

`mkdir -p modules/vagrant/manifests/`

Manifest kansioon loin init.pp tiedoston, johon suurin osa moduulista kirjoitettaisiin.

`nano modules/vagrant/manifests/init.pp`

Lisäsin ensin init.pp tiedostoon vain pakettien asennuksia varten tarvittavat rivit.

class vagrant {

         Package { ensure => "installed", allowcdrom => "true" }
         package { "virtualbox": }
         package { "vagrant": }
}

Tämän jälkeen koitin moduulin toimivuutta tässä vaiheessa ajamalla sen.

`sudo puppet apply --modulepath modules/ -e 'class {"vagrant":}'`

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
