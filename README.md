# OpenStack -alustan käyttöönotto
**Projektimme on osa Haaga-Helian Pilviteknologiat-kurssia.**



## Konsepti / virtuaalitoteutus

Päätimme toteuttaa projektin ensimmäisenä vaiheena virtuaaliasennuksen, jotta pääsisimme tutustumaan tarkemmin OpenStackin toimintaan. Asensimme OpenStackin kehittäjäversion, DevStackin, Haaga-Helian CloudPlatform -alustalle. Virtuaalikoneen isäntäkäyttöjärjestelmänä toimii Ubuntu Server 16.04.3 LTS, johon on asennuksen jälkeen tehty tarvittavat ohjelmisto- ja turvallisuuspäivitykset sekä asennettu vaadittavat ohjelmat.

### DevStackin asennus

DevStackin asennus onnistuu helposti seuraamalla virallisia asennusohjeita: https://docs.openstack.org/devstack/latest/
Omalle kotipalvelimelle asentaminen sujui suoraan ohjeiden mukaan, mutta koulun CloudPlatform -alustassa asennus keskeytyi, kun yhteyttä GitHubiin ei voitu muodostaa. Syyksi selviytyi koulun verkon rakenne, joka esti yhdistämisen git:// -alkuisiin osoitteisiin. Kyseessä lienee palomuurin tai proxyn asetukset.
Kyseiset osoitteet voi onneksi muuttaa perinteisiksi https:// -osoitteeksi antamalla komento `git config --global url."https://".insteadOf git://`, joka korjasi ongelman heti seuraavalla asennusyrityksellä.
