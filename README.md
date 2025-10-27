# H1 Sniff

## Karvinen 2025: Wireshark - Getting Started
    - Wireshark on ohjelma, jolla voi nähdä tietokoneen verkkoliinekenteen.
    - Ohjelmalla voi tutkia, mitä paketteja kulkee verkossa.
    - Sitä voi käyttää ongelmien etsimiseen, kuten miksi netti ei toimi.

## Karvinen 2025: Network Interface Names on Linux
    - Network interface tarkoittaa verkkolaitetta, joka yhdistää koneen verkkoon
    - Jokaisella laitteella on oma nimi Linuxissa.
    -Nimen alku keroo, millainen laite on
        - en=kaapeli (Ethernet)
        -  wl=langaton(Wi-Fi)
        -  lo=oma kone (loopback)
    - Numerot nimen lopussa kertovat laiteen sijainnin tai MAC-osoitteen.
    - Vanhoissa Linuxissa nimet kuten eth0 ja wlan0 saattoivat muuttua.

  ## b) Ei voi kalastaa
    - Katkaisin virtuaalikoneen Internet-yhteyden komennolla:
        -sudo ip Link set enp0s3 donw
     - Testasin pingillä:
        - ping 1.1.1.1
        - Tuloks tuli " Network is unreachable", eli yhteys oli poikki.  
     - Palautin yhdeyden komennolla:
        -  sudo ip link set enp0s3 up
        - Testasin uudelleen pingillä, ja nyt yhteys toimi (sain vastauksia "64 bytes from...").
        
  ## c) Wireshark
    - Asensin Wiresharkin komennolla:  
      -  sudo apt install wireshark -y
      
