x) Lue ja tiivistä. (Tässä x-alakohdassa ei tarvitse tehdä testejä tietokoneella, vain lukeminen tai kuunteleminen ja tiivistelmä riittää. Tiivistämiseen riittää muutama ranskalainen viiva.)
Hubacek 2019: Universal Radio Hacker SDR Tutorial on 433 MHz radio plugs (Video, alkaen 3:19 ja päättyen 7:40. Yhteensä noin 4 min.)
Cornelius 2022: Decode 433.92 MHz weather station data

# Hubacek 2019: Universal Radio Hacker SDR Tutorial on 433 MHz radio plugs (Video, alkaen 3:19 ja päättyen 7:40. Yhteensä noin 4 min.) EI auennut


# Cornelius 2022: Decode 433.92 MHz weather station data – tiivistelmä
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

Toin näytetiedoston Linuxiin VirtualBoxin jaetun kansion avulla. Kun kansio näkyi polussa /media/sf_share, kopioin näytteen omaan kotihakemistoon:
- cp /media/sf_share/Converted_433.92M_2000k.cs8 ~/
Tämän jälkeen ajoin analyysin komennolla: Tämän jälkeen ajoin analyysin komennolla:
rtl_433 tunnisti näytteestä 433,92 MHz:n kaukosäätimen signaaleja. Viestit olivat KlikAanKlikUit/Nexa/Proove -tyyppisen pistorasialaitteen lähettämiä. Ohjelma löysi useita toistuvia OFF-komentoja samasta laitteesta.

Analyysin tärkeimmät tunnistetiedot:

ID / House Code: 8785315

Unit: 3

Command: OFF

Group: No

Dim: No

Näyte sisältää toistuvia OFF-käskyjä, jotka vastaavat tilannetta, jossa käyttäjä painaa kaukosäätimen OFF-nappia useamman kerran. 433 MHz -laitteet lähettävät komennon aina useasti, jotta vastaanotin varmasti huomaa sen — tämä näkyi analyysissä selvästi.
<img width="739" height="546" alt="image" src="https://github.com/user-attachments/assets/c70cd018-0f58-4a60-8344-16c549cbe9ca" />
