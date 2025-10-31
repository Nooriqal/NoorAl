## a) Apache log:
     Vaiheet:
         1- Asensin ensin Apache2-palvelimen omaan virtuaalikoneeseeni komennolla:( sudo apt-get install apache2)
         Asennus vaati internetin, joten tein sen ennen kuin katkaisin yhteyden.
         Kun Apache oli asennettu ja  käynnistetty, laitoin netin kokonaan pois päältä.

         2- Tarkistin, että yhteys oli varmasti poikki komennolla:(ping -c 2 google.com)
         Tuloksena tuli virheilmoitus Temporary failure in name resolution, eli internetyhteys ei ollut käytössä.

         3- Testasin, toimiiko Apache ilman nettiä. Kirjoitin terminaaliin:(curl -I http://localhost) 
         Toimii, koska tuloksena oli : (HTTP/1.1 200 OK Server: Apache/2.4.58 (Ubuntu))
         Tämä osoittaa, että palvelin vastasi paikallisesti osoitteessa 127.0.0.1 (localhost), vaikka internet oli pois.

         4- katsoin Apachen lokitiedoston viimeisiä rivejä komennolla: (sudo nmap -T4 -vv -A -p 80 -oN ~/nmap_localhost_80.txt localhost)

         5- Kun skannaus on valmis, katso tulos: cat ~/nmap_localhost_80.txt
<img width="593" height="182" alt="h2bb" src="https://github.com/user-attachments/assets/73db21b5-ed4c-4b2e-baaa-9d88c46f8f8d" />



