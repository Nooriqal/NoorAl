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

          Lähde: https://nmap.org/book/nse.html
