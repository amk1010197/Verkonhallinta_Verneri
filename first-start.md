# Käyttöönotto-ohje opiskelijalle

Tämä ohje käy läpi Verkonhallinta-kurssin ympäristön käyttöönoton Windows 11 -työasemalla. Ohjeessa asennetaan WSL2, Docker Engine (**ei** Docker Desktopia) sekä otetaan käyttöön repositoryssä oleva valmis virtuaaliympäristö.

## Sisällysluettelo

1. [Esivaatimukset](#1-esivaatimukset)
2. [WSL2:n asennus](#2-wsl2n-asennus)
3. [Linux-jakelun asennus ja peruskonfigurointi](#3-linux-jakelun-asennus-ja-peruskonfigurointi)
4. [Docker Enginen asennus WSL-ympäristöön](#4-docker-enginen-asennus-wsl-ympäristöön)
5. [Repositoryn kloonaus ja virtuaaliympäristön käyttöönotto](#5-repositoryn-kloonaus-ja-virtuaaliympäristön-käyttöönotto)
6. [Opiskelijalle jäävät tehtävät](#6-opiskelijalle-jäävät-tehtävät)
7. [Vianmääritys](#7-vianmääritys)

---

## 1. Esivaatimukset

- Windows 11 (versio 22H2 tai uudempi suositeltu)
- Käyttäjätunnus, jolla on **järjestelmänvalvojan oikeudet** työasemalle
- Toimiva internet-yhteys
- Vähintään 8 Gt RAM-muistia (16 Gt suositeltu) ja vapaata levytilaa n. 20 Gt
- Virtualisointi (Intel VT-x / AMD-V) käytössä tietokoneen BIOS/UEFI-asetuksissa

> **Huom:** Tarkista virtualisoinnin tila Tehtävienhallinnasta: `Suorituskyky` → `Suoritin` → `Virtualisointi: Käytössä`.

---

## 2. WSL2:n asennus

1. Avaa **PowerShell järjestelmänvalvojana** (hiiren oikea painike Käynnistä-valikossa → *Windows PowerShell (Järjestelmänvalvoja)* tai *Terminal (Järjestelmänvalvoja)*).
2. Asenna WSL oletusjakelulla komennolla:

   ```powershell
   wsl --install
   ```

3. Käynnistä tietokone uudelleen, kun asennus pyytää sitä.
4. Varmista uudelleenkäynnistyksen jälkeen, että WSL käyttää versiota 2:

   ```powershell
   wsl --status
   wsl --set-default-version 2
   ```

5. Päivitä WSL-ydin tarvittaessa:

   ```powershell
   wsl --update
   ```

---

## 3. Linux-jakelun asennus ja peruskonfigurointi

1. Asenna Ubuntu (suositeltu jakelu tälle kurssille):

   ```powershell
   wsl --install -d Ubuntu-24.04
   ```

2. Käynnistä Ubuntu Käynnistä-valikosta. Ensimmäisellä käynnistyskerralla sovellus pyytää luomaan:
   - **Käyttäjätunnuksen** (UNIX-käyttäjä, ei sama kuin Windows-tunnus)
   - **Salasanan**

3. Päivitä pakettitietokanta ja järjestelmä:

   ```bash
   sudo apt update && sudo apt upgrade -y
   ```

4. Asenna perustyökalut:

   ```bash
   sudo apt install -y curl git ca-certificates gnupg lsb-release
   ```

---

## 4. Docker Enginen asennus WSL-ympäristöön

> Kurssilla käytetään **Docker Enginea suoraan WSL2-ympäristössä**, ei Docker Desktopia.

1. Asenna Dockerin virallinen convenience script -skriptiä käyttäen:

   ```bash
   curl -fsSL https://get.docker.com -o get-docker.sh
   sudo sh get-docker.sh
   rm get-docker.sh
   ```

2. Lisää oma käyttäjätunnus `docker`-ryhmään, jotta `sudo` ei ole tarpeen joka komennossa:

   ```bash
   sudo usermod -aG docker $USER
   ```

   > Kirjaudu ulos ja takaisin sisään (tai suorita `newgrp docker`) jotta muutos astuu voimaan.

4. WSL:n uusimmissa versioissa systemd ei ole yleisesti automaattisesti käytössä. Ubuntu 24.04:n uudemmissa WSL-asennuksissa se voi kuitenkin olla käytössä oletuksena. Tarkista ensin:

   ```bash
   ps -p 1 -o comm=
   ```

   Jos tuloste ei ole `systemd`, ota systemd käyttöön tiedostossa `/etc/wsl.conf`:

   ```ini
   [boot]
   systemd=true
   ```

   Luo/muokkaa tiedosto:

   ```bash
   sudo nano /etc/wsl.conf
   ```

   Lisää yllä oleva sisältö, tallenna, ja käynnistä WSL uudelleen Windowsin puolelta. Jos `[boot]`-osio ja `systemd=true` ovat jo tiedostossa, niitä ei tarvitse lisätä:

   ```powershell
   wsl --shutdown
   ```

   Käynnistä Ubuntu uudelleen ja tarkista Dockerin tila:

   ```bash
   sudo systemctl status docker
   ```

   Jos palvelu ei ole käynnissä:

   ```bash
   sudo systemctl enable --now docker
   ```

5. Testaa Docker-asennus:

   ```bash
   docker run hello-world
   ```

---

## 5. Repositoryn kloonaus ja virtuaaliympäristön käyttöönotto

1. Kloonaa kurssin repository WSL-ympäristöön (**ei** Windows-tiedostojärjestelmään, suorituskykysyistä):

   ```bash
   cd ~
   git clone https://github.com/tjarvenpaa/Verkonhallinta.git
   cd Verkonhallinta
   ```

2. Tutustu repositoryn rakenteeseen, erityisesti `scripts`-hakemistoon, jossa sijaitsee ympäristön käyttöönotosta huolehtiva `deploy.sh`-skripti:

   ```bash
   ls -la scripts/
   cat scripts/deploy.sh
   ```

3. Tee skriptistä tarvittaessa suoritettava ja aja se:

   ```bash
   chmod +x scripts/deploy.sh
   ./scripts/deploy.sh
   ```

   > Skripti hoitaa ympäristön käyttöönoton (mm. tarvittavien konttien rakentamisen/käynnistämisen). Tarkista skriptin tuloste mahdollisten virheiden varalta.

4. Varmista, että kaikki kontit ovat käynnissä:

   ```bash
   docker ps
   ```

5. Tarkista myös containerlab käynnistyminen ajamalla inspect käsky:

   ```bash
   containerlab inspect -t topology/golden.clab.yml
   ```

---

## 6. Ominaisuudet joiden käyttöönotto on tehtävä käsin

Ympäristön automaattisen käyttöönoton jälkeen opiskelijan tulee itse suorittaa seuraavat vaiheet:

- [ ] **NetBoxin alkukonfigurointi (seed)**: aja repositoryssä oleva seed-skripti/komento NetBoxin perusdatan (esim. siteet, laitetyypit, IP-avaruudet) tuomiseksi tietokantaan.

  ```bash
  sudo configs/netbox/seed/netbox_seed.py
  ```

  > Tarkista repositoryn oma README/skripti seed-komennon tarkasta syntaksista, sillä komento voi vaihdella toteutuksen mukaan.

- [ ] **Käyttäjätunnusten luonti**: luo NetBoxiin oma käyttäjätunnus ja aseta salasana ensimmäisellä kirjautumisella (oletustunnus `admin` on tarkoitettu vain alkukonfigurointiin).
- [ ] **Ympäristömuuttujien tarkistus**: kopioi tarvittaessa `example.environment` tiedosto `.env`-tiedostoksi ja täytä puuttuvat arvot (salasanat, API-avaimet, portit).
- [ ] **Verkkoyhteyksien testaus**: varmista selaimella (Windowsin puolelta), että NetBoxin ja muiden palveluiden web-käyttöliittymät ovat saavutettavissa osoitteesta `http://localhost:<portti>`.
- [ ] **Varmuuskopiointikäytännön tarkistus**: tutustu repositoryn ohjeisiin tietokannan/volumien varmuuskopioinnista ennen harjoitustöiden aloittamista.
- [ ] **Git-konfigurointi**: aseta oma nimi ja sähköposti Git-työkaluun mahdollisia harjoitustyön palautuksia varten:

  ```bash
  git config --global user.name "Oma Nimi"
  git config --global user.email "oma.sahkoposti@example.com"
  ```

---

## 7. Vianmääritys

| Ongelma | Mahdollinen syy | Ratkaisu |
|---|---|---|
| `wsl --install` ei toimi | Windows-versio liian vanha tai virtualisointi pois päältä | Päivitä Windows, tarkista BIOS/UEFI-asetukset |
| Docker-komennot vaativat `sudo` joka kerta | Käyttäjä ei ole `docker`-ryhmässä tai istunto ei ole päivittynyt | Suorita `newgrp docker` tai kirjaudu ulos/sisään |
| `systemctl` ei toimi WSL:ssä | systemd ei ole käytössä | Muokkaa `/etc/wsl.conf`, aja `wsl --shutdown` ja käynnistä uudelleen |
| Kontit eivät käynnisty | Portti varattu tai virheellinen `.env`-konfiguraatio | Tarkista `docker logs <kontin nimi>`, vapauta portti tai korjaa `.env` |
| NetBox ei näytä dataa | Seed-skriptiä ei ole ajettu | Suorita seed-komento kohdan 6 mukaisesti |

---

Kun kaikki yllä olevat vaiheet on suoritettu onnistuneesti, ympäristö on valmis kurssin harjoitustöiden tekemiseen.
