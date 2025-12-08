


# x) Lue ja tiivistä.
Cornelius 2022: Decode 433.92 MHz weather station data – tiivistelmä
- Artikkelissa selitetään, miten 433.92 MHz sääasemat lähettävät tietoja lyhyinä radiosignaaleina.
- Nämä signaalit ovat yleenäs ASK/OOK-moduloituja, eli plussit ovat joko päällä tai poiss.
- Signaali voidaan kaapata SDR-laitteella ja avata ohjelmassa, kuten URH tai rtl_433
- rtl_433 pystyy tulkitsemaan tietoa automaattisesti ja näyttää esim. laitteen ID:n lämpötilan, kosteuden ja praistotiedot.
- Artikkeli näyttää myös, miten pulssien pituuksia ja bittijonoja voi tarkastella käsin, jos automaattinen purku ei toimi.

# a) WebSDR
<img width="595" height="445" alt="image" src="https://github.com/user-attachments/assets/a902c508-0f8a-4722-ab1b-bfd32c15aaae" />

Käytin WebSDR-vastaanotinta, joka sijaitsee Bedfordissa, Englannissa. Yhteys avattiin selaimella osoitteesta: http://remoteradio.changeip.org:8073/ Valitsin vastaanottimelta taajuuden 270.00 kHz ja laitoin modulaatioksi AM. Tällä taajuudella alkoi kuulua musiikkia, joka tuli radiolähetyksenä suoraan vastaanottimelta.
Ääni tuli suoraan selaimesta ilman lisäasetuksia, joten jätin muut asetukset oletusarvoihin.

# b) rtl_433

rtl_433 oli minulla jo valmiiksi asennettuna, koska latasin sen edellisellä tunnilla, kun teimme harjoituksia. 
Tarkistin nyt vain, että se toimii. Avasin terminaalin ja kirjoitin: rtl_433 -h

# c) Automaattinen analyysi

Toin näytetiedoston Linuxiin VirtualBoxin jaetun kansion avulla. Kun kansio näkyi polussa:
/media/sf_share

kopioin näytteen omaan kotihakemistoon: - cp /media/sf_share/Converted_433.92M_2000k.cs8 ~/

Tämän jälkeen ajoin analyysin komennolla: rtl_433 -r Converted_433.92M_2000k.cs8

rtl_433 tunnisti näytteestä 433,92 MHz:n kaukosäätimen signaaleja. Viestit olivat KlikAanKlikUit/Nexa/Proove -tyyppisen pistorasialaitteen lähettämiä. Ohjelma löysi useita toistuvia OFF-komentoja samasta laitteesta.

Analyysin tärkeimmät tunnistetiedot:

ID / House Code: 8785315

Unit: 3

Command: OFF

Group: No

Dim: No

Näyte sisältää toistuvia OFF-käskyjä, jotka vastaavat tilannetta, jossa käyttäjä painaa kaukosäätimen OFF-nappia useamman kerran. 433 MHz -laitteet lähettävät komennon aina useasti, jotta vastaanotin varmasti huomaa sen — tämä näkyi analyysissä selvästi.
<img width="739" height="546" alt="image" src="https://github.com/user-attachments/assets/c70cd018-0f58-4a60-8344-16c549cbe9ca" />


# d) Too compex 16? 

Muutin URH-ohjelmalla tallennetun .complex16s näytteen rtl_433-yhteensopivaan muotoon vaihtamalla sen tiedostonimen oikeaan rakenteeseen.
rtl_433 ei tarvitse varsinaista signaalimuunnosta – riittää, että tiedoston nimi sisältää taajuuden ja näytteenottotaajuuden.

Siirryin jaettuun kansioon: cd /media/sf_share


Nimesin URH:n tallentaman tiedoston uudelleen oikeaan muotoon:mv Recorded-HackRF-20250411_183354-433_92MHz-2MSps-2MHz.complex16s HackRF_433.92M_2000k.cs8

Analysoin näytteen rtl_433-ohjelmalla: rtl_433 -r HackRF_433.92M_2000k.cs8


<img width="681" height="528" alt="image" src="https://github.com/user-attachments/assets/d0a5c3a6-100d-4d0e-a8c1-9500e1b562a3" />

rtl_433-ohjelma katsoi tiedoston sisälle ja löysi sieltä monta eri 433 MHz radiolaitteen lähettämää viestiä.

Näitä laitteita olivat esimerkiksi:

- KlikAanKlikUit 
- Proove-Security 
- Nexa-Security

rtl_433 purki jokaisen viestin, ja näytti:

- mikä laitemalli lähetti viestin
- mikä kanava (Channel 3)
- mitä käskyä laite lähetti (OFF / ON)
- millä hetkellä signaali tuli (aika)

## e) Ultimate Asenna URH, the Ultimate Radio Hacker.

En pystynyt asentamaan URH ohjelmaa, koska laitteessa ei ollut tarpeeksi vapaata tallennustilaa. Asennus keskeytyi virheeseen “No space left on device”, enkä saanut ohjelmaa toimimaan. Yritin vapauttaa tilaa, mutta sekään ei onnistunut, joten en saanut ohjelmaa asennettua.



  



