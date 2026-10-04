
# 1. Johdanto 

Zabbix on yritystason ja avoimen lähdekoodin monitorointi ja seuranta alusta. Sitä käytetään esimerkiksi suorituskyvyn, verrkolaitteiden ja palveluiden seuraamiseen. 
Tässä tehtävässä tutustumme Zabbixiin, sen ominaisuuksiin ja sen mahdollisuuksiin. Lopuksi pohdin omaa oppimismatkaani hieman.

## 1.1 Zabbixin käyttöliittymä 

- **Hosts**: Tältä välilehdeltä voi lisätä hosteja sekä muuttaa niiden asetuksia. Hosteja voivat olla esimerkiksi palvelimet, laitteet ja sovellukset
- **Templates**: Tältä välilehdeltä voidaan lisätä omia malleja tai laittaa template jollekkin laitteelle. Templatet ovat valmiiksi tehtyjä paketteja, jotka tekevät ennalta määritetyt asiat jokaiselle valitulle kohteelle. Esimerkiksi hakee Linuxin tilatietoja. 
- **Dashboards**: Yleisnäkymä, jolta näet yleistilan ja laitteet. Voit luoda omia dashboardeja sekä laittaa haluamaasi dataa näkyviin. 
- **Alerts: Ilmoitukset**, täältä voidaan asettaa, kun jokin thresholdi täyttyy tulee ilmoitus. Esimerkiksi levytila on vähissä. 
- **Reports**: Voidaan luoda ajastettuja raportteja, jotka antavat hyödyllistä tietoa järjestlelmien tilasta. 

# 2. Hostien lisääminen 

Sain onnistuneesti lisättyä web1,db1 ja client1:sen hosteiksi. 
Cpu Usage, verkkoliikenne, muisti ja uptime on monitoroitavissa. Tästä kuva images kansiossa nimellä host_todiste.jpg 

# 3. Dashboard 

Kuva dashboardista images kansiossa nimellä dashboard.jpg 

# 4. Mittarit 

Tehtävässä ei määritelty miltä laitteelta mittaukset otetaan, valitsin web1:sen. 

| Mittari | Arvo | Merkitys |
|-------- |------|----------|
| CPU Usage | 2 % | Kertoo CPU:n kuorman. Tärkeä pullonkaulojen ja vikatilanteiden havaitsemisessa. |
| Uptime | 1 h 16 min | Kertoo laitteen päälläoloajan. Näkee, onko laite päällä ja kuinka kauan se on ollut päällä, jolloin tietää myös milloin kannattaa käynnistää uudelleen. |
| Memory Usage | 33 % | Kertoo käytetyn muistin. Tärkeä sujuvan toiminnan kannalta. |
| Disk Usage | 1.27 % | Kertoo levyn käyttötilan. Kertoo tekeekö levy mitään, hyvä seurata esimerkiksi tiedonsiirrossa. |
| Load average (5m avg) | 0.24 | Mittaus aktiivisista/odottavista prosesseista. Hyvä seurata pullonkaulojen, trendien ja kapasiteetin takia. | 



# 5. Triggerit 

## 1. Prosessorin täysi kuormitus 

Jos Cpu:n käyttö on yli 90% 5 minuuttia, niin silloin Zabbix lähettää ilmoituksen, että prosesori on ylikuormittunut. Vakavuusluokka on Warning 

## 2. Levyn vähäinen tila 

Jos levyllä on alle 20% tilaa käytössä, lähettää trigger ilmoituksen, että levyn tila on vähissä. Vakavuusluokka on Average 
 
# 6. 

Aiheutin hälytyksen cpu:n kuormituksesta. Hälytys lopulta tuli noin 6 minuutissa, kun 5 minuuttia oli threshold. Kuva on images kansiossa nimellä hälytys.jpg 

Pysäytin web1:seltä Zabbix agentin ja tilailmoitus tuli noin 30 minuutissa. Status on problem ja Zabbix kertoo selvästi, ettei web1:seen saatu pingiä aikaiseksi. Kuva on images kansiossa nimellä häiriö.jpg 

# 7. Vertailu 

| Ominaisuus | SNMP | Prometheus | Zabbix |
|------------|------|--------|------------|
| Tiedonkeruu | Verkkolaitteet | Ideaali pilvi-, kontti- ja Kubernetes-ympäristöihin | All-in-one |
| Dashboardit | Ei ole | Alkeellinen | Edistynyt / Grafanan kaltainen |
| Hälytykset | Menevät SNMP-managerille | YAML / PromQL | Web UI |
| Käyttöönotto | Helpoin | Keskivaikea | Keskivaikea |
| Skaalautuvuus | Riippuu muista järjestelmistä | Lineaarinen | Tietokanta rajoittaa |
| Yrityskäyttö | Legacy-protokolla, käytössä verkkolaitteissa | Erikoistunut dynaamisiin ympäristöihin | Kokonaisvaltainen ratkaisu | 

# 8. Yhteenveto  

Kurssilla pääsi näkemään erilaisten työkalujen toimivuutta ja niiden soveltuvuutta. Mikään ei ole sinänsä toistaan parempi, vaan eri käyttötarkoitukseen soveltuva. 
Tietoturva oli myös asia, jota sivuttiin, joka jäi mieleeni. En ollut aiemmin paljoa githubiin pushannut tehtäviä tähän tapaan, enkä tehnyt markdown tiedostoja. 
Tämä on ihan hyvä tapa varsinkin tarkistukseen, mutta opiskelijan kannalta PDF-tiedosto on ehkä hieman helpompi. 
Ymmärsin kurssilla myös monitoroinnin merkityksen, sillä mitä ei mitata, ei voida todistaa. Wiresharkkia olin käyttänyt joskus, mutta virkistys sen käytöstä oli kyllä tarpeellinen.
Aika paljon oli sinänsä tehtävää, mutta ymmärrän miten kokonaisuus on rakennettu. Kurssi antoi loppujen lopuksi tiiviisti olennaisen valvonnasta ja siihen liityvistä näkökulmista.
Kun katson oppimistavoitteita ja peilaan oppimistani, ollaan hyvää mallilla. 

## Tekoälyn käyttö

Tekoälyä käytettiin lähinnä muutamissa ongelmatilanteissa:

- Zabbixin active check -yhteysongelman selvittämisessä, jossa syyksi löytyi muuttunut Zabbix-serverin IP-osoite.
- CPU- ja levytila-triggerien ehtojen tulkinnassa.
- Tehtävänannon epäselvien kohtien, kuten dashboardin vaatimusten ja raportin rakenteen, tulkinnassa.
- Omien vastausten Markdown-taulukoiksi jäsentämisessä.

Hostien lisääminen, dashboardin rakentaminen, mittausten tekeminen, triggerien testaaminen ja häiriötestit tehtiin itse Zabbix-ympäristössä.








