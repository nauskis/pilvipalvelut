# OpenStack -alustan käyttöönotto
**Projektimme on osa Haaga-Helian Pilviteknologiat-kurssia.**

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

## Asennus fyysiselle palvelimelle

Siirryimme projektin seuraavaan vaiheeseen toteuttamalla asennuksen fyysiselle palvelimelle koulun Servulassa. Kyseinen palvelin on HP:n ProLiant, jonka 132Gb muistia riittänee projektiin paremmin kuin CloudPlatformin tarjoamat.

### Käyttöjärjestelmän asennus

Asensimme palvelimelle pohjaksi Ubuntu Server 16.04.4 LTS:n, jonka paketit päivitettiin heti asennuksen jälkeen uusimpiin. Otimme välittömästi käyttöön myös palomuurin (`sudo ufw allow 22/tcp && sudo ufw enable`) ja poistimme root-tunnuksen kokonaan käytöstä (estimme salasanalla kirjautumisen sekä käytön SSH-yhteyden yli).

### Verkkoasetukset

Jouduimme taistelemaan jonkin aikaa koulun labraverkon asetuksien kanssa, mutta tuloksena saimme omat osoitteet sekä itse palvelimelle, että sen iLO-etäkäyttöliittymälle. iLO:n kautta voimme hallita etänä koko palvelinta, emmekä vain sen käyttöjärjestelmää.

```
iLO -etäkäyttö IP:         172.28.230.6
Palvelimen IP:             172.28.230.7
Palvelimen IP (DHCP):      172.28.171.20
```
### Palvelimen levyasetukset

Yritimme muuttaa palvelimen levyasetuksia niin, että näkisimme sen kovalevyt itsenäisinä laitteina käyttöjärjestelmässä. Ideana oli käyttää hardware RAIDin sijaan ZFS tiedostojärjestelmää, 

### DevStackin asennus

