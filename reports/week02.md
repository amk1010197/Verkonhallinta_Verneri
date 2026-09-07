
# 1. Johdanto 

SNMP on sovelluskerroksen protokolla joka lähettää hallinta dataa verkkolaitteiden välillä. 
SNMP kuuluu TCIP/IP perheeseen ja on yksi eniten käytetyistä protokollista verkkolaitteiden valvontaan ja halintaan.

SNMP:n avulla voidaan lukea verkkolaitteiden ja palvelimien tilatietoja, kuten: 

- CPU-kuormaa
- Muistin käyttöä 
- Rajapintojen liikennemääriä
- Virhelaskureita
- Laitteiden lämpötiloja

SNMP perustuu agenttiin ja manageriin.


# 2. Asennus

SNMP-agentti asennettiin web1-palvelimelle apt komenolla.
Tämän jälkeen muutettiin konfiguraatiotiedostosta yhteisön nimi ja agent address: eli sallitiin yhteys 161 portista.
Lopuksi Palvelu käynnistettiin uudelleen ja service status käskyllä tarkistetiin palvelimen käynnissä olo.

**Komennot:** 

docker exec -it clab-hamk-verkonhallinta-golden-web1 bash

apt update

apt install snmp snmpd -y

nano /etc/snmp/snmpd.conf

service snmpd restart

service snmpd status 


#3. Kerätyt tiedot 

## Yhteyden testaaminen

root@ansible:/etc/snmp# snmpwalk -v2c -c public web1 system
SNMPv2-MIB::sysDescr.0 = STRING: Linux web1 6.18.33.2-microsoft-standard-WSL2 #1 SMP PREEMPT_DYNAMIC Thu Jun 18 21:54:43 UTC 2026 x86_64
SNMPv2-MIB::sysObjectID.0 = OID: NET-SNMP-MIB::netSnmpAgentOIDs.10
DISMAN-EVENT-MIB::sysUpTimeInstance = Timeticks: (221935) 0:36:59.35
SNMPv2-MIB::sysContact.0 = STRING: Me <me@example.org>
SNMPv2-MIB::sysName.0 = STRING: web1
SNMPv2-MIB::sysLocation.0 = STRING: Sitting on the Dock of the Bay
SNMPv2-MIB::sysServices.0 = INTEGER: 72
SNMPv2-MIB::sysORLastChange.0 = Timeticks: (0) 0:00:00.00
SNMPv2-MIB::sysORID.1 = OID: SNMP-FRAMEWORK-MIB::snmpFrameworkMIBCompliance
SNMPv2-MIB::sysORID.2 = OID: SNMP-MPD-MIB::snmpMPDCompliance
SNMPv2-MIB::sysORID.3 = OID: SNMP-USER-BASED-SM-MIB::usmMIBCompliance
SNMPv2-MIB::sysORID.4 = OID: SNMPv2-MIB::snmpMIB
SNMPv2-MIB::sysORID.5 = OID: SNMP-VIEW-BASED-ACM-MIB::vacmBasicGroup
SNMPv2-MIB::sysORID.6 = OID: TCP-MIB::tcpMIB
SNMPv2-MIB::sysORID.7 = OID: UDP-MIB::udpMIB
SNMPv2-MIB::sysORID.8 = OID: IP-MIB::ip
SNMPv2-MIB::sysORID.9 = OID: SNMP-NOTIFICATION-MIB::snmpNotifyFullCompliance
SNMPv2-MIB::sysORID.10 = OID: NOTIFICATION-LOG-MIB::notificationLogMIB
SNMPv2-MIB::sysORDescr.1 = STRING: The SNMP Management Architecture MIB.
SNMPv2-MIB::sysORDescr.2 = STRING: The MIB for Message Processing and Dispatching.
SNMPv2-MIB::sysORDescr.3 = STRING: The management information definitions for the SNMP User-based Security Model.
SNMPv2-MIB::sysORDescr.4 = STRING: The MIB module for SNMPv2 entities
SNMPv2-MIB::sysORDescr.5 = STRING: View-based Access Control Model for SNMP.
SNMPv2-MIB::sysORDescr.6 = STRING: The MIB module for managing TCP implementations
SNMPv2-MIB::sysORDescr.7 = STRING: The MIB module for managing UDP implementations
SNMPv2-MIB::sysORDescr.8 = STRING: The MIB module for managing IP and ICMP implementations
SNMPv2-MIB::sysORDescr.9 = STRING: The MIB modules for managing SNMP Notification, plus filtering.
SNMPv2-MIB::sysORDescr.10 = STRING: The MIB module for logging SNMP Notifications.
SNMPv2-MIB::sysORUpTime.1 = Timeticks: (0) 0:00:00.00
SNMPv2-MIB::sysORUpTime.2 = Timeticks: (0) 0:00:00.00
SNMPv2-MIB::sysORUpTime.3 = Timeticks: (0) 0:00:00.00
SNMPv2-MIB::sysORUpTime.4 = Timeticks: (0) 0:00:00.00
SNMPv2-MIB::sysORUpTime.5 = Timeticks: (0) 0:00:00.00
SNMPv2-MIB::sysORUpTime.6 = Timeticks: (0) 0:00:00.00
SNMPv2-MIB::sysORUpTime.7 = Timeticks: (0) 0:00:00.00
SNMPv2-MIB::sysORUpTime.8 = Timeticks: (0) 0:00:00.00
SNMPv2-MIB::sysORUpTime.9 = Timeticks: (0) 0:00:00.00
SNMPv2-MIB::sysORUpTime.10 = Timeticks: (0) 0:00:00.00
root@ansible:/etc/snmp#

**Selitys:** 

Kaikkea en tästä ymmärrä, eikä tarvitsekkaan. Pääasia on, että ymmärrän web1:sen vastaavan ja yhteyden toimivan.
Objektit näyttävät toimivan, vastausta tulee stringinä, kuten pitääkin.


## Järjestelmän nimi 

root@ansible:/etc/snmp# snmpget -v2c -c public web1 sysName.0
SNMPv2-MIB::sysName.0 = STRING: web1

**selitys:**

Tässä kyselyssä kysytään järjestelmän nimeä ja vastauksena saadaan string muododssa web1.


## Järjestelmän kuvaus 

root@ansible:/etc/snmp# snmpget -v2c -c public web1 sysDescr.0
SNMPv2-MIB::sysDescr.0 = STRING: Linux web1 6.18.33.2-microsoft-standard-WSL2 #1 SMP PREEMPT_DYNAMIC Thu Jun 18 21:54:43 UTC 2026 x86_64
root@ansible:/etc/snmp#

**selitys:**

Komennon objekti hakee järjestelmän kuvauksen. 
Tietona saadaan, että kyseessä on WSL2:sessa toimiva Linux palvelin. Lopussa on myös timestamp, ja suoritin on 64 bittinen.   


## järjestelmän käyttöaika 

root@ansible:/etc/snmp# snmpget -v2c -c public web1 sysUpTime.0
DISMAN-EVENT-MIB::sysUpTimeInstance = Timeticks: (349897) 0:58:18.97
root@ansible:/etc/snmp#


**selitys:**

SysUpTime objektilla saadaan tieto kauanko järjestelmä on ollut käynnissä
tickkeinä sekä selkokielisenä. Minulla palvelin on ollut päällä nyt noin 58 minuuttia.
 


# 4. Verkkorajapinnat

root@ansible:/etc/snmp# snmpwalk -v2c -c public web1 ifDescr
IF-MIB::ifDescr.1 = STRING: lo
IF-MIB::ifDescr.2 = STRING: eth0
root@ansible:/etc/snmp#


**selitys:**

En ole varma muistanko väärin, mutta joskus minulla oli näitä kyllä kolme, niinkuin sinulla tallenteessa.
Se kolmas oli se virallinen reitti, jota käytetiin muulloin kun hallinnassa. Tässä tehtävässä käytetään kuitenkin tätä hallintaväylää, joten en ole vielä ihmetellyt miksi.

Verkkorajapintoja on tällä hetkellä siis vain 2 kappaletta, toinen on loopback ja toinen on eth0, joka yhdistää laitteen hallinta verkkoon. 
Tarkistin tämän vielä ip a komennolla web1:sessä.


# 5. OID-analyysi

sysName.0 = Kertoo järjestelmän nimen
sysDescr.0 = kertoo järjestelmän kuvauksen 
sysUpTime.0 = kertoo järjestelmän käynnissäolo ajan 
ifDescr= näyttää kaikki rajapinnat
ifOperStatus = Raportoi meneekö rajapinnan kautta liikennettä vai onko ongelmia: eli raportoi statuksen. 
 
 
## Usean laitteen valvonta 


|     Laite     |      Nimi     | Käyttöjärjestelmä | Uptime 
| ------------- | ------------- | ----------------- | ------
| web1          | web1          | Linux             | 1:23:05.18
| db1           | db1           | Linux             | 0:03:04.74
| branch-client | branch-client | Linux             | 0:00:13.35


# 6. Pohdinta 

SNMP on selkeä käyttää ja sillä saa tietoa järjestelmistä helposti ja luotettavasti. SNMP on kaikkialla rakennettu jo sisään, ihan yhtälailla kun käytät ip a käskyä monesti, käytän todennäköisesti SNMP käskyjä palvelimilla, se on kevyt ja mahdollistaa proaktiivisuuden.
SNMP mahdollistaa monitorointi alustojen tekemisen kuten grafana, jolloin komenotoja ei tarvitse itse suorittaa vaan kaikki löytyy alustalta. Voit kerätä tietoa kuten lämpötila, status, cpu:n käyttö, muistinkäyttö ja rajapintojen liikenteenmäärät. 

SNMPv2 suurin ongelma on sama kuin http:ssä. Data liikkuu selkokielisenä, joten kaappaja voi lukea sen suoraan. 
Myös community stringejen käyttö salasanoina heikentää tietoturvaa selkeästi. Operatiiviset ongelmat liittyvät timeoutteihin ja konfiguraation yhteensopivuuteen. 

SNMPv3:sta käyttäisin, kun tietoturva ja kryptaus on tärkeää. Onhan sitä v2c, joka korjaa paljon, mutta v3 on uudempi ja vastaa myös regulaatioon paremmin.
Voit myös asettaa käyttäjä tilejä yksittäin eri käyttöoikeuksilla yhden commmunity stringin sijaan. 





**Tekoälyn käyttö tehtävässä:**

Minulla oli ongelmia saada yhteys ansiblesta web1:seen. numeerisesti sain yhteyden, mutta en sanallisesti. Kysyin tästä sinulta sähköpostilla, ja ohjasit laittamaan agent addressiksi 161 portin, jotta yhteys olisi auki. Tein tämän, mutta se ei kuitenkaan riittänyt. 
Tämän johdosta kysyin tekoälyltä, ja se ohjasi lataamaan mibs downloaderin uudestaan sekä kommentoimaan rivin asetuksista. Olin tämän jo aiemmin tehnyt, mutta todennäköisesti vahingossa web1:selle.
Tämän jälkeen tehtävän jatkaminen onnistui ilman tekoälyn avustusta. 

Koin tekoälyn järkevämmäksi, kun sinun vaivaamisen uudestaan viikonloppuna, kyse oli sanojesi mukaan omasta lapsuksestasi, mutta mibsdownloader homma oli todennäköisesti minun oma virheeni.
