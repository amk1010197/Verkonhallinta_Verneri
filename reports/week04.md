

# 1. Johdanto 

Infrastructure as code tarkoittaa infrastruktuutin hallintaa ohjelmakoodin avulla. laC-mallin periaattena on, että ylläpitäjä hallitsee palvelimien tavoitetilaa eikä yksittäisiä palvelimia.
Toisin sanoen määritellään asiat kertaalleen ja käytetään samaa tiedostoa kaikkien konfiguroimiseen. Skaalautuvuus on siis suuri etu, sillä komentojen sijasta lisäät ryhmään vain uuden laitteen. 


# 2. Inventory 

Inventorysä on Ubuntu laitteina: 
- client1
- web1
- db1
- branch-client

Muuttuja ryhmässä on ansible. Node exporterin kohteina on routerit, clientit ja palvelimet. 
Ryhmien hyötynä on se, että voit organisoida ja valita ryhmän mukaan suoritettavia playbookkeja. 


# 2. SNMP Playbook 

Koodi toimi muuten täysin, mutta snmp service ei lähtenyt päälle kuin web1:sellä, koska service moduuli ei toiminut. 
Vaihdoin suoraan ilman moduulia, eli shell: service snmpd start, koska manuaalisesti toimi kyllä.
Sen jälkeen kaikki oli vihreetä ja toimi eli: päivittipakettilistan, asensi snmp ja snmpd sekä käynnisti palvelun. 
koska koodi jo valmiiksi targettasi ubuntu_hosts niin jätin sieltä poistamatta client 1. Eli asensi myös sille.
Koodi on images kansiossa nimellä snmp_code ja tuloste on nimellä snmp-tuloste.


#3. Node Exporter Playbook 

Node Exportereiden asennus toimi virhettömästi asensin ne web1,client1,db1 ja branch-clientille. 
Playbookkki loi hakemiston, latasi Exporterin, purki paketin ja käynnisti palvelun.
Koodi on images kansiossa nimellä exporter_code ja tuloste on nimellä exporter-tuloste


#4. Vertailu 

4.5 kohdassa järjestelmätiedot piti kerätä taulukkoon, mutta raportoinnin ohjeessa ei siitä mitään puhuta. 
Tämän vuoksi en rupea väsäämään taulukkoa, mutta kerron ne tässä lyhyesti. 16 corea, Ubuntu 24.04, mem = 8gb, ip = 172.20.20.50.


Automaatiolla voit täysin identtisesti samanlaiset konfiguraatiot tehdä kuin käsin. 
Kyse on siis vain siitä, ettei komentoja toisteta vaan määritellään tavoitetila kerran ja sen jälkeen lisätään laitteita vain tiedostoon. 
Kun laitteita on satoja olisi se lähes mahdotonta jokaisen laitteen terminaalin kirjautua ja suorittaa komennot. 
Automaation hyöty on toistettavuus, nopeus ja monitorointi siinä mielessä, että näät jokaisen laitteen kohdalta toimiiko se vai ei yhtenäisessä näkymässä. 
Automaatio on välttämätöntä kun laitteita on satoja eikä aikaa ole ihmettelyyn.


#5. Yhteenveto 

Opin miten automaatio toimii, sen että kyse on vain siitä, ettei manuaalista työtä tehdä sen enempää mitä tarvitsee. Play recapista näki hienosti onnistuuko asennukset yksityiskohtaisesti.
Ymmärsin myös nimenomaan mitä LaC:llä tarkoitetaan ja milloin se on hyödyllistä. 


# Tekoälyn käyttöni työssä 

Käytin tekoälyä ainoastaan SNMP Playbook kohdassa, jossa service moduuli ei toiminut. Kysyin tekoälyltä miksi palvelut ei käynnisty. Tekoäly pyysi tarkistamaan manuaalisesti toimiiko shellistä, se toimi, joten ohjeisti miten muutan koodin moduulista shell komentoon.












|
|
|
|
| 
