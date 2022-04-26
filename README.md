# H4: Aikajana

Vinkkejä:

Lähes kaiken voi konfiguroida viidellä funktiolla: pkg-file-service (watch), user, exec.
Asenna aina ensin käsin
Ohjelmat tallentavat asetukset tiedostoihin. Joten aikajana tiedostoista näyttää, mitkä tiedostot pitää tehdä.
Saltilla asennetaan ohjelma (pkg), laitetaan asetustiedostot paikalleen (file) ja lopuksi jokin ohjelma lukee tiedot (service-watch; työpöytäohjelma kun se avataan; pakettilista sudo apt-get update tai pkg-refresh).
Testaa. Tee pienin testattava kokonaisuus kerralla.

## a) Captain obvious. Linuxissa on paketinhallinta, joten ohjelmien asentaminen on yksinkertaista. Tee tila, joka asentaa 10 suosikkiohjelmaasi paketinhallinnasta. Tässä a-kohdassa voit jättää ohjelmat oletusasetuksille.

> Vinkkejä: Asenna tässä a-kohdassa valmiiksi luotetuista pakettivarastoista, niin ei tarvitse asentaa uusia varastoja, ja tiedät saavasi vapaita ohjelmia. Ohjelmien asennus voi viedä aikaa, jos haluat seurata etenemistä komento komennolta niin 'sudo salt-call --local -l debug state.apply terosapps'. Sisäänrakennettu ohje kertoo, kuinka "pgks" toimii: 'salt-call --local sys.state_doc pkg.installed|less'

Tein uuden kansion salt-tilalle hakemistoon /srv/salt/ nimeltä favorites ja sinne state-tiedoston nimeltä init.sls
En keksinyt kuin vain kahdeksan ohjelmaa, joita itse voisin käyttää ja asentaa tuoreen asennuksen jälkeen.

![Ohjelmat]()

Ja tämän jälkeen otin tilan käyttöön komennolla:

	$ sudo salt '*' state.apply favorites

![Favorites state.apply]()

## b) CSI Pasila. Tiedostoista saa aikajanan 'cd /etc/; sudo find -printf '%T+ %p\n'|sort|tail'. Anna esimerkki aikajanasta

![Esimerkki aikajana]()

> Selitä jokainen kohta komennosta, jolla aikajana tehdään. Vinkki: '%T+' löytyy 'man find' kohdasta printf.

* Find ohjelma nimensämukaisesti hakee.
* -printf tulostaa ulostulon tietyssä muodossa.
* %T+ näyttää milloin tiedostoa on viimeksi muokattu.
* %p näyttää tiedoston nimen, \n on rivinvaihto
* sort järjestää edellisen ulostulon järkevämpään järjestykseen.
* tail jatkaa edellistä ulostuloa ja näyttää oletuksena viimeisimmät 10 riviä.

## Aja jokin komento, joka muuttaa järjestelmän yhteisiä asetustiedostoja

Tätä varten suljin ufw ohjelman kautta portin 80, eli apachen käyttämän http portin, komennolla

	& sudo ufw deny 80/tcp

Joka olikin näköjään valmiiksi jo suljettu aiemmin, joten suljin portin 666, jota aikoinaan käytettyyn DOOM:ia varten.

![ufw skipping]()

![doom port]()


## Ota uusi aikajana ja etsi muutos sieltä

Muutoksen löysin komennolla:

	& sudo cat /etc/ufw/user.rules
	
![catkomento]()

##Onko samalla hetkellä muutettu yhtä vai useampaa tiedostoa?

Find ohjelma näyttää, että on muutettu kahta tiedostoa.

![findkomento]()

## c) Tiedän mitä teit viime kesän^H^H^H komennolla. Säädä jotain ohjelmaa ja etsi sen muuttamat tiedostot aikajanasta. Tee sitten tästä oma Saltin tila.
> Vinkki: tässä kohdassa pitää muuttaa jonkin ohjelman asetuksia, pelkkä ohjelman asennus pkg.installed on liian helppoa.

Tätä tehtävää varten tein ufw:lle uuden Salt-hakemiston ja init-state tiedoston. Tiedostossa varmistan että ufw-sovellus on asennettu, sekä kopioin /srv/salt/ufw/ hakemistoon Debian-koneeni palomuurisäännöt.

![ufwinit]()

![saantokopio]()

Poistin tämän jälkeen ufw:n komennolla
	
	$ sudo apt-get purge ufw
	
 Debian koneeltani ja varmistin että säännöt ovat poistuneet myös. Tämän jälkeen ajoin uuden salt-tilan.
 
	& sudo salt '*' state.apply ufw

Tässä vaiheessa vanhat muutokset näkyvät asetuksissa.

![firewallinstall]()

![firewallsettings]()

## d) Asenna jokin toinen ohjelma asetuksineen.

> Vinkki: tässäkin kohdassa edellytetään asetusten muuttamista. Jos haluat, voit valita erilaisen ohjelman kuin c-kohdassa. Esimerkiksi jos asensit c-kohdassa demonin, voit asentaa tässä komentoriviohjelman tai graafisen käyttöliittymän ohjelman.

Ja tätä tehtävää varten asensin chromiumin, sekä muutin asetuksia hakemistosta /etc/chromium käsin, muutin master_preferences tiedostossa kotisivuksi [https://terokarvinen.com](https://terokarvinen.com).

![asetuksetkasin]()

Sen jälkeen tein uuden salt-hakemiston ja tilan hakemistoon /srv/salt/chromium, sekä kopioin Debian koneeni chromium asetukset hakemistosta /etc/chromium salt-hakemistoon.

![chromiumtila]()

Jonka jälkeen poistin chromiumin Debianilta komennoilla:

	& sudo apt-get purge chromium
	& sudo apt-get autoremove
	
Ja ajoin äsken luodun salt-tilan nimeltä chromium komennolla:

	& sudo salt '*' state.apply chromium
	
![chromiumapply]()
	
Jonka jälkeen voidaan huomata, että muutokset ovat toimineet, sekä loppujen lopuksi find komennolla voidaan nähdä, että molemmat muutokset ufw:hen ja chromiumiin ovat toimineet.

![findkomento2]()