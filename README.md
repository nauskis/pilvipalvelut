# OpenStack -alustan käyttöönotto
**Projektimme on osa Haaga-Helian Pilviteknologiat-kurssia keväällä 2018**

**Jussi Isosomppi, Samuli Kinnunen, Mikko Knutas, Eino Kupias, Saku Kähäri**

## Konsepti / virtuaalitoteutus

Projektimme tarkoituksena on luoda useasta nodesta koostuva OpenStack -asennus, jossa on huomiotu tuotantotason ratkaisun vaatima tietoturva ja vikasietoisuus. Loppuratkaisun tulisi olla käytännössä yrityksen käyttöön soveltuva, "valmis" ratkaisu.

### Virtuaalikoneen luominen CloudPlatformiin

Päätimme toteuttaa projektin ensimmäisenä vaiheena virtuaaliasennuksen, jotta pääsisimme tutustumaan tarkemmin OpenStackin toimintaan. Asensimme OpenStackin kehittäjäversion, DevStackin, Haaga-Helian CloudPlatform -alustalle. Virtuaalikoneen isäntäkäyttöjärjestelmänä toimii Ubuntu Server 16.04.3 LTS, johon on asennuksen jälkeen tehty tarvittavat ohjelmisto- ja turvallisuuspäivitykset sekä asennettu vaadittavat ohjelmat. Loin tarvittavat palomuurisäännöt CloudPlatformiin, jonka jälkeen sain otettua toisesta koneesta SSH-yhteyden suoraan virtuaalikoneeseen hitaan selainkonsolin käyttämisen sijaan.

### DevStackin asennus

DevStackin asennus onnistuu helposti seuraamalla [virallisia asennusohjeita](https://docs.openstack.org/devstack/latest/), kunhan muistaa vaihtaa asetustiedostoon turvallisemmat salasanat *secret*in tilalle.
Omalle kotipalvelimelle asentaminen sujui suoraan ohjeiden mukaan, mutta koulun CloudPlatform -alustassa asennus keskeytyi, kun yhteyttä GitHubiin ei voitu muodostaa. Syyksi selviytyi koulun verkon rakenne, joka esti yhdistämisen git:// -alkuisiin osoitteisiin. Kyseessä lienee palomuurin tai proxyn asetukset.
Kyseiset osoitteet voi onneksi muuttaa perinteisiksi https:// -osoitteeksi antamalla komento `git config --global url."https://".insteadOf git://`, joka korjasi ongelman heti seuraavalla asennusyrityksellä.

DevStackin asennusskriptin ajaminen CloudPlatformiin kesti n. 27min, jonka aikana asennusskripti latasi ajan tasalla olevat versiot DevStackin sisältämistä OpenStack -moduleista.

DevStackin asennuttua tein sen vaatimat port forwardit CloudPlatformiin, jonka jälkeen OpenStackin graafinen käyttöliittymä Horizon löytyi selaimella.

```
CloudPlatform IP:         10.207.3.232
CloudPlatform VM SSH:     10.207.3.232:1001
DevStack Horizon (GUI):   10.207.3.232:1002
```

### DevStackin käyttökokeilu CloudPlatformissa

Teimme tämän jälkeen jokaiselle ryhmän jäsenelle omat käyttäjätunnukset, ja varmistimme niiden toimivan kunkin omalla selaimella. Näin varmistuimme port forwardien ja muiden verkkoasetusten sekä DevStackin verkkohallintaliittymän toimivuudesta. Yritimme myös luoda jokaiselle omaa virtuaalikonetta (instanssia), mutta valitettavasti CloudPlatform-virtuaalikoneestamme loppui ilmeisesti muisti kesken. CloudPlatformissa olemassa olevan virtuaalikoneen laitteistokokoonpanoa voi kyllä muuttaa, mutta vain sammuttamalla virtuaalikoneen ja vain ennalta määräitettyihin kokoonpanoihin. Tässä yhteydessä edes CloudPlatformiin määritelty *Large Instance* ei riittänyt tarpeisiimme, joten totesimme CloudPlatform-kokeilun lähestyvän loppuaan.  

Totesimme lisäksi myös, että DevStack-instanssien koekäyttö olisi turhan vaikeaa, koska niille olisi pitänyt määritellä port forward -sääntöjä sekä CloudPlatformin että DevStackin virtuaalireitittimiin. Tämän lisäksi CloudPlatform sallii vain rajallisen määrän port forwardeja, eikä se olisi pidemmän päälle riittänyt tarpeisiimme.

## Asennus fyysiselle palvelimelle

Siirryimme projektin seuraavaan vaiheeseen toteuttamalla asennuksen fyysiselle palvelimelle koulun Servulassa. Kyseinen palvelin on HP:n ProLiant DL360p Gen8, jonka 2x Xeon E5-2603 -prosessoria ja 132Gb muistia riittänevät projektiin paremmin kuin CloudPlatformin tarjoamat. Palvelimeen on asennettu 8kpl 146Gb 15K RPM SAS-levyjä, jotka riittävät niin nopeudeltaan kuin kapasiteetiltaankin projektiimme hyvin. Tyhjensimme palvelimen kovalevyt, ja siirryimme käyttöjärjestelmän asennukseen.

### Käyttöjärjestelmän asennus

Asensimme palvelimelle DevStackin pohjaksi suositellun Ubuntu Server 16.04.4 LTS:n, jonka paketit päivitettiin heti asennuksen jälkeen uusimpiin. Otimme välittömästi käyttöön myös palomuurin (`sudo ufw allow 22/tcp && sudo ufw enable`) ja poistimme root-tunnuksen kokonaan käytöstä (estimme salasanalla kirjautumisen sekä käytön SSH-yhteyden yli). Kokeilimme SSH-yhteyden toimivuuden sekä labraluokan 5004 koneilta että omilta läppäreiltä (Tielab -WLAN on osa labraverkkoa).

### Verkkoasetukset

Jouduimme taistelemaan jonkin aikaa koulun labraverkon asetuksien kanssa, mutta tuloksena saimme omat osoitteet sekä itse palvelimelle, että sen iLO-etäkäyttöliittymälle. iLO:n kautta voimme hallita etänä koko palvelinta, emmekä vain sen käyttöjärjestelmää. Määritimme iLO:on projektia varten uuden käyttäjän, jonka avulla voimme esimerkiksi käynnistää palvelimen miltä tahansa labraverkon koneelta. Verkkoyhteyden toimivuus tarkistettiin yksinkertaisesti asentamalla Apache2, avaamalla sille palomuurista portti 80 ja toteamalla placeholder-sivun näkyvän muillakin koneilla.

```
Palvelimelle varatut IP:t: 172.28.230.6-10
iLO:n etäkäyttö-IP:        172.28.230.6
Palvelimen IP:             172.28.230.7
Palvelimen IP (DHCP):      172.28.171.20
```

### Palvelimen levyasetukset

Yritimme muuttaa palvelimen levyasetuksia niin, että näkisimme sen kovalevyt itsenäisinä laitteina käyttöjärjestelmässä. Ideana oli käyttää hardware RAIDin sijaan ZFS tiedostojärjestelmää, jolloin olisimme saaneet itse päättää levyjen pariteettitiedot. Valitettavasti HP:n Smart Array -RAID-kortti ei kuitenkaan tue enempää kuin kahta loogista levyä ilman lisämuistin asennusta. 

### DevStackin asennus

DevStackia asentaessa kohtasimme taas pari ongelmaa:
* Unohdimme ajaa tuon aiemmin mainitun komennon, joka muuttaa git:// -osoitteet https:// -osoitteiksi
* ZFS-asennusta yrittäessä lisätty apt-repository oli jollain tavalla puutteellinen, jonka takia DevStack antoi virheilmoituksen ja keskeytti asennuksen
* DevStackin local.conf:issa annettu salasana sisälsi ääkkösiä, joka johti asennuksen virheilmoitukseen *"Not UTF-8 compliant"*

Nämä ongelmat korjattuamme saimme taas DevStackin toimimaan samalla tavalla, kuin aiemminkin.
