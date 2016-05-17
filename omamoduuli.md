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

#Tiedostorakenne

Aloitin rakentamalla oikeanlaisen tiedostorakenteen. Ensin loin puppet kansion.

`mkdir puppet`

Tämän jälkeen siirryin juuri luotuun puppet kansioon.

`cd puppet`

Loin puppet kansioon tarvittavat kansiot.

`mkdir -p modules/vagrant/manifests/`

Manifest kansioon loin init.pp tiedoston, johon suurin osa moduulista kirjoitettaisiin.

`nano modules/vagrant/manifests/init.pp`

#Pakettien asennus

Lisäsin ensin init.pp tiedostoon vain pakettien asennuksia varten tarvittavat rivit.

`class vagrant {`

         Package { ensure => "installed", allowcdrom => "true" }
         package { "virtualbox": }
         package { "vagrant": }
`}`

Tämän jälkeen koitin moduulin toimivuutta tässä vaiheessa ajamalla sen.

`sudo puppet apply --modulepath modules/ -e 'class {"vagrant":}'`

Pakettien asennuksessa kesti hetki, mutta virheilmoituksia ei tullut.
Varmistin vielä, että molemmat paketit olivat asentuneet.

`virtualbox`

Ohjelma avautuin, joten VirtualBox oli saatu asennettua onnistuneesti.

`vagrant`

Terminaali antoi ison läjän mahdollisia komentoja, joten Vagrant oli asentunut onnistuneesti.

#Projektin kansio

Tämän jälkeen Vagrant vaatii, että sille luodaan oma kansio. Tämä toteutettii lisäämällä uusi file-luokka init.pp:hen.

`file {'/home/xubuntu/vagrant_project':`
        `ensure => 'directory',`
        `owner => 'xubuntu',`
        `mode => '0770',`
        `}`
        
Testattiin jälleen moduulin toimivuutta ajamalla se.

Moduulin ajo onnistui jälleen ja tarkistin vielä, että kansio oli luotu.

`cd`
`ls`

Kyseinen vagrant_project-kansio löytyi xubuntu-käyttäjän home-kansiosta, joten kaikki toimi tässä vaiheessa, kuten pitikin.

#Vagrant init

Tämän jälkeen Vagrant vaatii, että sille luodaan Vagrantfile, joka toimii Vagrantin konfiguraatiotiedostona.

Koitin lisätä moduuliin tämän toiminnon exec-luokkaa hyväksi käyttäen. Lisäsin init.pp seuraavat rivit.

`exec { 'init':`
    `command => 'vagrant init',`
    `cwd => '/home/xubuntu/vagrant_project',`
    `path    => '/usr/local/bin/:/bin/',`
  `}`
  
Koitin jälleen ajaa moduulia, mutta tällä kertaa sain tulosteeksi virheilmoituksen:

`Notice: /Stage[main]/Vagrant/Exec[init]/returns: /usr/lib/ruby/vendor_ruby/vagrant/environment.rb:592:in ``expand_path':` `couldn't find HOME environment -- expanding ``~' (ArgumentError)`
`Notice: /Stage[main]/Vagrant/Exec[init]/returns: 	from /usr/lib/ruby/vendor_ruby/vagrant/environment.rb:592:in ``setup_home_path'`
`Notice: /Stage[main]/Vagrant/Exec[init]/returns: 	from /usr/lib/ruby/vendor_ruby/vagrant/environment.rb:110:in ``initialize'`
`Notice: /Stage[main]/Vagrant/Exec[init]/returns: 	from /usr/bin/vagrant:105:in ``new'`
`Notice: /Stage[main]/Vagrant/Exec[init]/returns: 	from /usr/bin/vagrant:105:in ``<main>'`
`Error: vagrant init returned 1 instead of one of [0]`
`Error: /Stage[main]/Vagrant/Exec[init]/returns: change from notrun to 0 failed: vagrant init returned 1 instead of one of [0]`

Koitin ratkaista ongelmaa, mutta en keksinyt tähän mitään ratkaisua, joten ajoin komennon käsin.
Siirryin siis tuohon luotuun vagrant_project kansioon ja ajoin komennon.

`vagrant init`

Nyt Vagrantfile tiedosto saatiin luotua onnistuneesti.

#Vagrantfile konfiguraatio

Halusin, että konfiguraatiotiedosto saa haluamani arvot, joten päätin luoda templaten, jonka pohjalta Vagrant konfiguroitaisiin. Loin siis uuden templates hakemiston vagrant-hakemiston alle.

`cd puppet/modules/vagrant/`
`mkdir templates`

Templates hakemistoon loin uuden tiedoston Vargantfile.

`cd puppet/modules/vagrant/templates/`
`nano Vagrantfile`

Lisäsin tiedstoon seuraavat rivit:

`VAGRANTFILE_API_VERSION = "2"`
`Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|`
`config.vm.box = "precise32"`
`config.vm.box_url = "http://files.vagrantup.com/precise32.box"`
`end`

Tämän jälkeen muokkasin init.pp tiedostoa ja lisäsin sinne file-luokan, johon tuli seuraava sisältö:

`file {'/home/xubuntu/vagrant_project/Vagrantfile':`
        `content => template("vagrant/Vagrantfile"),`
        `}`
        
Tämä aiheuttaa tietysti ristiriidan, koska en saanut exec-luokkaa toimimaan ja yllä luotu file-luokan olisi tarkoitus muokata tuolla exec-komennolla luotua Vagrantfile:ä. Luokan avulla saatiin kyllä luotua tuo Vagrantfile oikeaan paikkaan, mutta koska "vagrant init"- komentoa ei ole ajettu, ei sillä tee mitään ja terminaali antaa virheilmoituksen, jonka mukaan tuo komento tulisi ajaa. Ajoin siis komennon, mutta se ei suostunut menemään läpi, koska kyseinen tiedosto oli jo luotu moduulin avulla. Jouduin siis poistamaan sen ja ajamaan komennon. Nyt kun moduuli ajettiin uudelleen, oikea tiedosto muokkaantui ja sai haluamani arvot.

##Vagrantin testaus

Nyt voitiin testata, onnistuiko konfigurointi. Tämä toteutettiin komennolla:

`vagrant up`

Vagrant alkoi asentamaan konfiguraatiotiedostossa olevaa boxia (precise32). Kun se oli ladattu annetusta osoitteesta, alkoi se käynnistämään konetta. Koneen pystytys onnistui, kun terminaaliin saatiin tuloste.

`[default] Machine booted and ready!`

Kokeiltiin vielä ssh-yhteydellä komennolla:

`vagrant ssh`

SSH-yhteyden todettiin toimivan, kun saatiin seuraava tuloste:

`Welcome to your Vagrant-built virtual machine.`
`Last login: Tue May 17 18:22:31 2016 from 10.0.2.2`
`vagrant@precise32:~$`

---

http://www.olindata.com/blog/2014/07/installing-vagrant-and-virtual-box-ubuntu-1404-lts
