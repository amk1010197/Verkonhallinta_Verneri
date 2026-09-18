
# 1. Johdanto 

Prometheus on avoimen lähdekoodin monitorointijärjestelmä, joka on suunniteltu eryitysesti palvelin-, pilvi ja konttiymäristöjen valvontaan. 
Se kerää jatkuvasti metriikkaa eri järjestelmistä ja tallentaa sen tietokantaan.
Prometheus kerää tiedot Exporter ohjelman avulla, joka muuttaa tilatiedot Prometheuksen ymmärtämään muotoon.
Prometheuksella voidaan esimerkiksi seurata cpu, muisti, levy, verkko ja käyttöjärjestelmä tilatietoja.

# 2. Node Exporterin käyttöönotto 

Node Exporter otettiin käyttöön web1:selle ja asennettiin tarvittavat työkalut komennoilla: 

- apt update
- apt install wget tar -y


Selvitettiin uusin saatavilla oleva versio Githubista joka oli 1.21.1

komento:

- wget https://github.com/prometheus/node_exporter/releases/latest/download/node_exporter-1.21.1.linux-amd64.tar.gz 

paketti purettiin ja siirryttiin Node Exporterin kansioon

- tar xvf node_exporter-*.linux-amd64.tar.gz

- cd node_exporter-1.21.1.linux-amd64

Lopuksi Node Exporter käynnistettiin komennolla:

./node_exporter

Olit ladannut tallenteessa Node Exporterin muillekkin palvelimille, mutta tässä tehtävässä pyydettiin vain web1:selle,
joten jätin muut toistaiseksi asentamatta. Lopuksi avasin uuden terminaalin ja kokeilin vastaako Exporter. Kuvankaappaus on images kansiossa nimellä Node-Exporter_proof



# 3. Prometheus

Prometheus näyttää, että web1 on päällä portissa 9100, johon Node Exporter laitettiinkin.
Prometheus hakee tietoa tasaisesti, jonka huomaa siitä, että Last scrape päivittyy. Status on UP, joka tarkoittaa että saadaan yhteys.
Todiste on images kansiossa nimellä prometheus-todiste. 

# 4. Dashboard

Dashboardista otin vain yhden kuvan, jossa on kuormitustestin tulokset samassa, joten todiste löytyy kuormitus-dashboard-todiste nimellä images kansiosta. 
Testattu yhteys löytyy taas nimellä testattu-yhteys samasta kansiosta.

# 5. Kuormitustesti 

Sain mittarit lukemaan tietoja sekä vaihdoin 5 minuutin tarkkuuteen, jotta huomaan eron selvästi.
Kokeilin ensiksi levy kuormituksen nostamista. Tiedosto oli suhteellisen pieni, joten levynprosentti nousi vain 0,02%. 
Tämän jälkeen kokeilin 4 coren kuormitusta 100% 60 sekunnin ajan. Se näkyi selvästi Cpu usagen nousuna 25 prosenttiin. Total Cpu Usage ei noussut 100 prosenttiin, koska säikeitä oli enemmän kuin 4. 
Verkkoliikennettäkin oli, sillä Node Exporterit ja Prometheus käyttää kaistaa, vaikka varsinaista nettitestiä ei tässä ollutkaan.


# 6. SNMP vs Prometheus


|           Ominaisuus           |           SNMP           |        Prometheus        |
|--------------------------------|--------------------------|--------------------------|
| Tiedonkeruu                    | UDP/TCP, OID             | Exporterit, HTTP/S       |
| Käyttöönotto                   | helpompi verkkolaitteissa| helpompi pilvi, palvelu  |
| Mittarien määrä                | OID, ei labels           | Enemmän metriikoita      |
| Visualisointi                  | Datan keruu protokolla   | Modernit graafit         |
| Hälytysmahdollisuudet          | SNMP traps, tarvii NMS   | Yksityiskohtainen        |
| Soveltuvuus pilviympäristöihin | Parempi verkkolaitteissa | Soveltuu hyvin           |

1. Prometheus voi yhdistää useita metriikoita, toimii paremmin pilvipalveluympäristöissä, queryt ovat joustavia ja Alertmanager toimii tehokkaasti. SNMP toimii paremmin vähemmän joustavuutta tarvittavissa asioissa. 
2. Ylläpitäjän kannattaa seurata vähintään Cpu usagea, muistia, verkkoliikennettä ja levyn käyttöä sekä sen tilaa. 
3. Oma dashbordini tarjoaa tietoa prosessorin, verkkoliikenteen, levyn ja muistin tilasta prosentteina. 
4. Voisin lisätä logi tietoja, latenssia, virheitä ja päälläoloaikaa. 
5. Jos tietoa on tallennettu tietokantaan ja palvelin on jossain vaiheessa kaatunut, voidaan mennä taaksepäin katsomaan mistä se mahdollisesti johtui.
 Voidaan katsoa, että oliko kyse cpusta, muistista vaiko levystä. Fyysisesti voidaan ihmetellä miksi cpu temp on vaikka 90c, ja huomataankin että prosesori coolerin tuuletin näytää nollaa, jolloin voidaan olettaa että tuuletin ei toimi. 


# Tekoälyn käyttöni tehtävässä: 

Käytin tekoälyä node exporterin vastaavusongelman korjaamiseen, lopulta se oli vain siitä, että en ollut web1:sesä sisällä ja yritin sen ulkopuolelta hakea curlilla. Käytin sitä myös kysymään miksi levyn käyttö nousi niin vähän sekä varmistin että olinko oikeassa siitä cpun käytöstä, eli miksi ei mennyt 100%. 
Olisin voinut Node Exporteriin tuota vastausta etsiä ja googletella tunteja, uskon että oppimisen kannalta oli parempi, että en ihmetellyt kovin kauaa ja käytin ajan sitten laadukkaseen tehtävän tekemiseen. Käytin tekoälyä myös tuohon taulukon troubleshoottaamiseen kun ihemettelin miksi se ei näkynyt kunnolla githubissa, aiemmassa tehtävässä olin saanut ihan ilman tekoälyäkin oikein, mutta nyt tuntejen jälkeen olin aivan solmussa. 



