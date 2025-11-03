
## x)
# Tuskan pyramidi (Bianco 2013):
Tuskan pyramidi luokittelee hyökkäystä osoittavat merkit helpoista (esim. tiedostojen hashit, IP
    osoitteet) vaikeimpiin (TTP:t). Mitä korkeammalla merkki on pyramidissa, sitä enemmän sen estäminen
    pakottaa hyökkääjän muuttamaan toimintatapojaan — eli se aiheuttaa hänelle enemmän “kipua”. (Bianco,
    2013)

# Timanttimalli (Caltagirone et al. 2013):
 Timanttimallissa hyökkäys kuvataan neljänä solmukohtana: hyökkääjä, infrastruktuuri, kyvykkyys ja uhri,
     ja niiden väliset yhteydet auttavat hahmottamaan, miten hyökkäys on toteutettu. Malli on
     käytännöllinen, koska se ohjaa analyytikkoa etsimään todisteita näiden neljän osan välillä.
     (Caltagirone ym., 2013)


## a) Apache log:

Vaiheet:
 1-Asensin ensin Apache2-palvelimen omaan virtuaalikoneeseeni komennolla:
     ( sudo apt-get install apache2) Asennus vaati internetin, joten tein sen ennen kuin katkaisin yhteyden.
     Kun Apache oli asennettu ja käynnistetty, laitoin netin kokonaan pois päältä.
          
 2-Tarkistin, että yhteys oli varmasti poikki komennolla:(ping -c 2 google.com) Tuloksena tuli virheilmoitus Temporary failure in name resolution,
          eli internetyhteys ei ollut käytössä.
          
3-Testasin, toimiiko Apache ilman nettiä. Kirjoitin terminaaliin:(curl -I http://localhost) Toimii, koska tuloksena oli :
          (HTTP/1.1 200 OK Server: Apache/2.4.58 (Ubuntu)) Tämä osoittaa, että palvelin vastasi paikallisesti osoitteessa 127.0.0.1 (localhost), vaikka internet oli pois.


## b) Nmapped

Suoritin porttiskannauksen komennolla:
     sudo nmap -T4 -vv -A -p 80 -oN ~/nmap_localhost_80.txt localhost

<img width="593" height="182" alt="h2bb" src="https://github.com/user-attachments/assets/e976f5d6-9882-4182-bd5a-cb4b486e1b0e" />

Nmap-skannauksen tulos osoittaa, että portti 80 on auki ja Apache 2.4.58 (Ubuntu) toimii localhostissa.

 Kuvan perusteella Nmap löysi portin 80/tcp (HTTP) olevan avoinna, mikä tarkoittaa, 
     että Apache-palvelin toimii oikein. Skannaus paljasti myös palvelimen version: 
     Apache httpd 2.4.58 (Ubuntu). Lisäksi Nmapin -A -parametri käynnisti automaattisesti NSE-skriptit kuten http-title ja http-server-header,
     jotka tunnistivat palvelimen otsikon ja tekniset tiedot. Tuloksesta voi päätellä, 
     että palvelin vastaa paikallisesti osoitteessa 127.0.0.1 (localhost), eikä yhteyttä ulkoverkkoon tehty.
     Lähde:https://nmap.org/book/man.html

## c) Skriptit

<img width="688" height="263" alt="h2c" src="https://github.com/user-attachments/assets/69bba21d-b761-493c-9f3d-03d71cdd1536" />

 Kuvassa näkyvät automaattisesti ajetut skriptit, jotka tulivat käyttöön -A-parametrin ansiosta:
     
 1.http-title Tämä on NSE-skripti, joka hakee palvelimen etusivun HTML-sisällön ja
           näyttää <title>-tagin. Tässä tuloksena oli “Apache2 Ubuntu Default Page: It works”,
           mikä kertoo että Apache-palvelin toimii ja näyttää oletussivun. 
           
 2.http-server-header Tämä skripti lukee palvelimen HTTP-vastausotsikon Server:
           Tuloksena Apache/2.4.58 (Ubuntu) — ohjelman nimi ja versio. Nämä skriptit ajettiin automaattisesti,
           koska -A aktivoi default-kategorian NSE-skriptit Use Control + Shift + m to toggle the tab key moving focus.
           Alternatively, use esc then tab to move to the next interactive element on the page.
           No file chosen Attach files by dragging & dropping, selecting or pasting them.

## d) Jäljet lokissa

Käytin komentoa: sudo grep -i "nmap" /var/log/apache2/access.log
     
- Komento etsii sanan "nmap" lokitiedostosta riippumatta siitä, onko se kirjoitettu isolla vai
     pienellä kirjaimella (-i tekee hausta kirjainkoon riippumattoman).
     
- Löysin lokista rivejä, joissa sana "nmap" esiintyi pienillä kirjaimilla, User-Agent-kentässä:
     "Mozilla/5.0 (compatible; Nmap Scripting Engine; https://nmap.org/book/nse.html)"
     
- Tämä kertoo, että Nmapin skriptit (NSE) tekivät HTTP-pyyntöjä palvelimelle skannauksen aikana.
      
- Ne tulivat siis omalta koneelta (127.0.0.1), koska skannaus tehtiin paikallisesti ilman internetiä.
      
  - Jos lokissa ei näkyisi sanaa nmap, porttiskannauksen voisi tunnistaa esimerkiksi näillä tavoilla:
               -IP-osoite tekee monta pyyntöä hyvin nopeasti.
               -Lokissa näkyy paljon virhekoodeja (404 tai 403).
               -User-Agent-nimistä kuten curl, wget tai scanner

<img width="656" height="457" alt="h3c" src="https://github.com/user-attachments/assets/682e4da5-270f-4252-94c7-9e119583b157" />


## e) Wire sharking

Wiresharkilla tutkittiin, näkyykö Nmapin porttiskannauksen aiheuttama liikenne paikallisessa verkossa.  
     Aluksi ohjelma ei näyttänyt mitään paketteja, vaikka kaappaus oli käynnissä, ja ruudulle tuli virheilmoitus:
     “/usr/bin/dumpcap: No such file or directory”. Tämä osoitti, että dumpcap-komponentti puuttui eikä Wireshark pystynyt sieppaamaan liikennettä.
     
Wireshark asennettiin uudelleen komennolla:
     sudo apt install --reinstall wireshark-common wireshark
     ja sille annettiin tarvittavat käyttöoikeudet, minkä jälkeen kaappaus alkoi toimia.

Tämän jälkeen dumpcapille annettiin käyttöoikeudet komennolla: 
     sudo setcap cap_net_raw,cap_net_admin=eip /usr/bin/dumpcap
     Kun nämä oli tehty, Wireshark alkoi toimia normaalisti ja kaappaus käynnistyi oikein.

Toiminta testattiin ensin ping-komennolla loopback-osoitteeseen (127.0.0.1), ja ICMP-paketit näkyivät onnistuneesti.  
     Tämän jälkeen käynnistin uuden kaappauksen ja suoritin Nmap-skannauksen Apache-palvelimelle komennolla:

sudo nmap -T4 -vv -A -p 80 --script http-title --script-args "http.useragent=nmap-test" localhost

Skannauksen aikana Wireshark tallensi liikenteen.  
     Kun tulokset suodatettiin komennolla `http.user_agent contains "nmap"`, näkyi useita HTTP-paketteja, 
     joissa User-Agent-kentässä oli teksti **“nmap-test”**.  
     Tämä vahvisti, että Nmapin tekemä liikenne näkyi Wiresharkissa oikein, ja kaikki paketit kulkivat 
     paikallisesti osoitteiden **127.0.0.1 ↔ 127.0.0.1** välillä.


<img width="641" height="436" alt="h3e" src="https://github.com/user-attachments/assets/f64e4919-5641-4c32-bf2c-11e24051b633" />


## f) Net grep
<img width="517" height="386" alt="Screenshot 2025-11-02 171512" src="https://github.com/user-attachments/assets/fc36b436-656f-4a3c-b0f8-104da146c579" />



 Asensin ngrep-komennon terminaalissa:
               sudo apt update  
               sudo apt install ngrep

 Tarkistin verkkorajapinnan:
               ip a
          
Sen jälkeen käynistin sieppauksen komennolla:
                sudo ngrep -d enp0s3 -W byline '(?i)nmap' -O nmap_hits.pcap |& tee nmap_hints.txt


Avasin toisen terminaalin ja testasin:
     ping nmap.org

Palasin ensimmäiseen terminaaliin, jossa näkyi nmap-liikennettä.

Lopetin sieppauksen painamalla Ctrl + C.

       

<img width="515" height="172" alt="h3f" src="https://github.com/user-attachments/assets/099615b5-0222-476f-be9e-2f9473f86cfd" />


## g) Agentti

 Avasin kaksi terminaalia, A ja B, ja tein seuraavat vaiheet:
          
 Terminaali A:
          Aloitin sieppauksen ngrep-ohjelmalla, joka kuunteli verkon liikennettä ja etsi User-Agent-tietoja.
          sudo ngrep -d enp0s3 -W byline '(?i)User-Agent' -O ua_test.pcap |& tee ua_test.txt
          # Tämä komento jäi pyörimään ja näytti kaikki paketit, joissa oli User-Agent-otsikko.# 

Terminaali B:
          Suoritin kaksi nmap-testiä.
          Ensin ajoin normaalin nmap-komennon ilman muutoksia:
          sudo nmap -p80 --script http-title target.example.com

Sen jälkeen ajoin saman komennon, mutta muutin Nmapin User-Agentin näyttämään tavalliselta selaimelta:
          sudo nmap -p80 --script http-title --script-args 'http.useragent=Mozilla/5.0 (Windows NT 10.0; Win64; x64) Chrome/116' target.example.com

Terminaali A :
          Kun molemmat testit oli ajettu, pysäytin sieppauksen painamalla Ctrl + C.
          Tarkistin tulokset etsimällä lokitiedostosta User-Agent-rivejä:
          grep -i nmap ua_test.txt
          grep -i mozilla ua_test.txt

Lopuksi avasin tallennetun pcap-tiedoston Wiresharkissa ja tarkistin, että User-Agent näkyi pakettien tiedoissa:
    

Testissä tarkkailin, miten Nmapin HTTP-pyyntöjen User-Agent voidaan muuttaa.
Ngrep sieppasi liikenteen onnistuneesti, ja Wiresharkissa näkyi eri User-Agent-rivit – ensin Nmapin oma ja sen jälkeen muokattu selainversio.




## h) Pienemmät jäljet.

<img width="475" height="200" alt="Screenshot 2025-11-02 191728" src="https://github.com/user-attachments/assets/81f850fa-5fc3-4db4-9d94-ad8a5bc7041a" />

Kuvassa näkyy, että ajoin komennon:  
        sudo nmap -sS -p 80 --script http-title --script-args http.useragent="Mozilla/5.0" localhost
        Eli suoritin nmap-skannauksen omaan localhost-palvelimeeni (portti 80) ja vaihdoin User-Agentin selaimen tyyliseksi (Mozilla/5.0).

Apache-lokissa näkyy seuraavaa:
        "GET /nmaplowercheck..." HTTP/1.1 404 451 "-" "nmap-test"
        User-Agent: "nmap-test" → siinä ei enää näy "Nmap Scripting Engine", joten User-Agent vaihtui onnistuneesti.
        Silti lokissa näkyy edelleen nmapin jättämiä pyyntöjä, kuten /nmaplowercheck ja "nmap-test", jotka paljastavat, että kyseessä oli nmap-skannaus.

Lokin rivit, kuten:
          - GET /sdk HTTP/1.1 404 451 "-" "nmap-test"
          - GET /HNAP1 HTTP/1.1 404 451 "-" "nmap-test"
        ovat osa http-title-skriptin testipyyntöjä. Ne kertovat, että vaikka User-Agent näyttää selaimelta, nmapin oma skripti tekee edelleen tunnistettavia pyyntöjä.

Näin ollen User-Agentin vaihtaminen vähensi nmapin jälkiä, mutta ei poistanut niitä kokonaan, skannaus on edelleen tunnistettavissa lokista.




## Lähteet :
     https://nmap.org/nsedoc/scripts/http-title.html
     https://nmap.org/nsedoc/scripts/http-useragent-tester.html
     https://httpd.apache.org/docs/2.4/logs.html
     https://www.sumologic.com/blog/apache-access-log
     
     
     




               
