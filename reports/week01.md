

# 1. Johdanto 

Ympäristö on luotu Containerlabilla opetuskäyttöön, sen tarkoituksena on pystyä pyörimään useimmilla 16gb RAM-koneilla, mutta sisältää kaikki verkonhallinnan kurssin kannalta tärkeät osa-alueet. Järjestelmässä on useita monitorointi ja analytiikka järjestelmiä/laitteita. 
Kaikki osa-alueet on jaettu järkevästi aliverkkoihin ja reititetty reitittimien kautta järjestelmät käyttävät pääosin Ubuntun ajantasalla olevia versioita.



# 2. Verkkokaavio 

Annettu kuvana repossa nimellä topology.png.

# 3. Taulukko

| Laite | Tarkoitus |
| ------| -------- |
| r1           | Ohjelmistopohjainen reititin, open source internet protokolla Linux järjestelmille. |
| r2           | Toinen ohjelmistopohjainen reititin |
| r3           | Kolmas ohjelmistopohjainen reititin |
| client 1     | Nimensä mukaissti asiakas, eli käyttöjärjestelmä, joka käyttää palvelimen palveluita. Tässä yhteydessä se on Linux ubuntu 24.04| 
| attacker     | Nimensä mukaisesti hyökkääjä, Kali linux eli penetraatiotestaukseen käytetty järjestelmä, eli suorittaa todennäköisesti sen työkaluilla hyökkäyksiä ja tekee skannauksia vaikka Niktolla ja Nmapilla. Kali Rolling versio, eli jatkuvasti päivittyvä.|
| web1         | Nomraali linux ubuntu 24.04, todennäköisesti käytetään jonkinlaiseen verkkoselailuun.|
| db1          | db eli tietokanta.| 
| branch-client| GIT-client, Ubuntu image|
| Ansible      | Ansible avoimen lähdekoodin IT automaatio moottori. infrastructure as a code ratkaisu. Automatisoi palvelimien konfigurointia/luontia. 
| Prometheus   | Monitorointi järjestelmä ja aikasarjatietokanta. Avoimen lähdekoodin ratkaisu, joka kerää, varastoi ja kyselee mittareita. 
| grafana      | Datan visualisointi ja analytiikka ohjelma, joka muuntaa raa'an datan, logit ja jäljet interaktiiviseksi näkymäksi.
| zabbix       | Avoimen lähdekoodin monitorointi järjestelmä. Käytetään järjestelmän tilan, suorituskyvyn ja saatavuuden seurantaan.



# 4. IP-osoitteiden dokumentointi

|     Verkko     |        Tarkoitus         | Yhdyskäytävä     | Laitteet
| -------------- | ------------------------ | ------------     | -----------
| 10.10.10.0/24  |     Käyttäjien verkko    | R1 (10.10.10.1)  | client 1 ja attacker linux koneet|
| 10.10.20.0/24  |     Palvelinten verkko   | R2 (10.255.12.2) | Web 1 ja db1 palvelimet          |
| 10.10.30.0/24  |     konttorin verkko     | R3 (10.255.23.2) | branch client                    |
| 10.10.99.0/24  |     Hallintaverkko       | R2 (10.255.12.2) | analytiikka järjestelmät/ohjelmat|
| 10.255.12.0/30 |     Yhdysverkko          | R1 - R2          | ei laitteita                     |
| 10.55.23.0/30  |     Yhdysverkko          | R2 - R3          | ei laitteita                     |


 Tärkeimmät IP-osoitteet on tehtävänantona hieman subjektiviinen, mutta näitä en ole vielä ainakaan kertonut:
 
 Client 1 ip = 10.10.10.101
 Branch-client ip = 10.10.30.101
 
 Verkko on harjoitusympäristo, kaikki osat on laitettu erillisiksi aliverkoiksi hallinnan takia.


# 5. Reitityksen analyysi

ip addr tuloste: 

root@client1:/# ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: eth0@if282: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
    link/ether f2:74:c8:cf:79:e5 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 172.20.20.16/24 brd 172.20.20.255 scope global eth0
       valid_lft forever preferred_lft forever
271: eth1@if272: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9500 qdisc noqueue state UP group default
    link/ether aa:c1:ab:11:31:39 brd ff:ff:ff:ff:ff:ff link-netnsid 1
    altname clab-o-05031180f95d8850
    inet 10.10.10.101/24 scope global eth1
       valid_lft forever preferred_lft forever
    inet6 fe80::a8c1:abff:fe11:3139/64 scope link
       valid_lft forever preferred_lft forever


## ip route tuloste: 

root@client1:/# ip route
default via 10.10.10.1 dev eth1
10.10.10.0/24 dev eth1 proto kernel scope link src 10.10.10.101
172.20.20.0/24 dev eth0 proto kernel scope link src 172.20.20.16

## ping tulosteet: 

root@client1:/# ping -c 4 10.10.20.101
PING 10.10.20.101 (10.10.20.101) 56(84) bytes of data.
64 bytes from 10.10.20.101: icmp_seq=1 ttl=62 time=0.110 ms
64 bytes from 10.10.20.101: icmp_seq=2 ttl=62 time=0.062 ms
64 bytes from 10.10.20.101: icmp_seq=3 ttl=62 time=0.059 ms
64 bytes from 10.10.20.101: icmp_seq=4 ttl=62 time=0.068 ms

--- 10.10.20.101 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3051ms
rtt min/avg/max/mdev = 0.059/0.074/0.110/0.020 ms


root@client1:/# ping -c 4 10.10.30.101
PING 10.10.30.101 (10.10.30.101) 56(84) bytes of data.
64 bytes from 10.10.30.101: icmp_seq=1 ttl=61 time=0.244 ms
64 bytes from 10.10.30.101: icmp_seq=2 ttl=61 time=0.063 ms
64 bytes from 10.10.30.101: icmp_seq=3 ttl=61 time=0.048 ms
64 bytes from 10.10.30.101: icmp_seq=4 ttl=61 time=0.063 ms

--- 10.10.30.101 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3070ms
rtt min/avg/max/mdev = 0.048/0.104/0.244/0.080 ms



## traceroute tuloste: 

root@client1:/# traceroute 10.10.30.101
traceroute to 10.10.30.101 (10.10.30.101), 30 hops max, 60 byte packets
 1  10.10.10.1 (10.10.10.1)  0.187 ms  0.012 ms  0.008 ms
 2  10.255.12.2 (10.255.12.2)  0.122 ms  0.016 ms  0.012 ms
 3  10.255.23.2 (10.255.23.2)  0.110 ms  0.019 ms  0.015 ms
 4  10.10.30.101 (10.10.30.101)  0.023 ms  0.018 ms  0.017 ms



Kaikki yhteydet toimivat, paketit kulkevat lyhintä mahdollisinta reittiä.
Eli clientiltä eteenpäin menee näin: r1 -> r2 -> r3 -> branch-client




# Yhteenveto

dokumentaation muodostamisessa vei eniten aikaa se, että ymmärsi miten saan selville reitittimien ja laitteiden ip-osoitteet. 
Topologiasta näki kyllä hyvin laitteet, mutta ip-osoitteina oli hallinta-osoitteet. 
Tämä sai meikäläisen aivan solmuun ja lopulta kysyin tekoälyltä, onko järjestelmäni rikki,ja se kertoi, että ei ole.
Menin sitten tekoälyn sijasta viime tunnin tallenteeseen ja siellä selititkin sen. Tekoälyä käytin kaavioni tarkistamisessa, ja siinä tekoäly huomasi, että olin käyttänyt yksisuuntaisia nuolia ja laittanut osoitteet epäselvästi näkyviin, ja ne korjasin tekoälyn neuvon pohjalta. 
Muuten tekoälyä ei käytetty, eikä sillä helpotettu mitään tehtävää varsinaisesti. Haluan kuitenkin rehellisyyden nimissä nämä pienetkin asiat kertoa. Tosin Googlen AI-tiivistelmääkin käytin, mutta -ai joka haun jälkeen on aika radikaali lähestymistapa.
Jokatapauksessa käytin sen 8h tehtävään ja koin suurta tuskaa miettiessäni, miten pääsen eteenpäin, kun tehtävässä jätettiin hieman arvailujen varaan, miten osoitteet selvitetään. Vastustin ai-pikanäppäimen painamista viimeiseen saakka ja sain lopulta onnistumisen tunteen. 



Kun kattava dokumentaatio löyty, on it-asiantuntijan helppo aloittaa vianetsintä tai uusien laitteiden/ohjelmistojen asennus, kun ei ensiksi tarvitse 8 tuntia ihmetellä miten tää toimii. 
