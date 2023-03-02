# h11 Task

## Kone 
- Lenovo Y510P läppäri
- Suoritin: Intel(R) Core(TM) i7-4700MQ CPU @ 2.40GHz
- 8 GB RAM
- Windows 10, versio: 22H2

Aloitin tehtävän tekemisen ~00:20

## a) Django tuotantoasennus

Aloitin käynnistämällä apache:n ``$ sudo systemctl start apache2``, jonka jälkeen tarkistin, että serveri vastaa komenentokehotteesta ``$ curl localhost``.

![Add file: h11 first](h11-1.PNG)

Serveri vastasi aikaisemmin luomallani vastauksella, eli apache toimii.

Seuraavaksi tein kansiot sekä tiedostopolun samalla django projektia varten.

![Add file: h11 first](h11-2.PNG)

Tämän jälkeen loin ``mscom.conf`` tiedoston apachen palvelinta varten. 

    $ sudoedit /etc/apache2/sites-available/mscom.conf

Lisäsin tiedostoon tiedostopolun ``mscom.conf`` tiedostoa varten. Kävin kopioimassa tiedostopolut komentokehotteesta, jotta ne tulevat varmasti oikein. Menin kotihakemistoon ``$ cd``, josta operoin ``cd``:n ja ``tab``:n avulla oikeaan tiedostopolkuun ja lopussa ``pwd``:n avulla otin tiedostopolun ja kopioin sen.

![Add file: h11 4](h11-4.PNG)

Liitin tiedostopolut ``mscom.conf`` tiedostoon``

![Add file: h11 3](h11-3.PNG)

Seuraavaksi otan ``mscom.conf`` tiedoston käyttöön apachen palvelimelle.
- Ensin katson mitä apachen etusivu nyt näyttää.

![Add file: h11 5](h11-5.PNG)

- Apachen palvelin näyttää ``000-default.conf`` sivua, joten poistan sen käytöstä. ``$ sudo a2dissite 000-default.conf``
- Otan ``mscom.conf`` sivun käyttöön ``$ sudo a2ensite mscom.conf``
- Katson toimiiko uusi konfiguraatio ``$ /sbin/apache2ctl configtest``.

![Add file: h11 6](h11-6.PNG)

- Testi vastasi ``AH00558`` ja Syntax OK eli tiedostossa pitäisi olla kaikki ok.
- Pävitin Apachen palvelimen ``$ sudo systemctl restart apache2``
- Testaan tuliko uusi sivu näkyviin apachen etusivulle ``$ curl localhost/static/``

![Add file: h11 7](h11-7.PNG)

static/ sivut vastaavat index.html tiedostoon luomallani tekstillä eli static/ sivut toimivat.

### Uusi Django projekti.

- Aktivoin uuden virtuualiympäristön(env) ``publicwsgi`` kansioon`. 
    
    $ cd
    $ cd publicswgi
    $ virtualenv -p python3 --system-site-packages env

- Aktivoin virtuaaliympäristön(env)

    $ source env/bin/activate

- Asensin djangon uuteen virtuaali ympäristöön:

    $ 
    $ which pip
    /home/miikkas/publicwsgi/env/bin/pip
    
- ``requirements.txt`` kansioon paketin nimi eli ``django`` varmuuden vuoksi, ettei tule kirjoitusvirheitä.
    $ micro requirements.txt
    $ cat requirements.txt
    django
    $ pip install -r requirements.txt
    
- Django version tarkastus

    $ django-admin --version
    4.1.7

- Uusi projekti 

    $ django-admin startproject mscom
    
- Sain errorin:

![Add file: h11 8](h11-8.PNG)

Ratkaisin poistamalla kansion mscom 
- Ensin pois virtuaaliympäristöstä

    $ deactivate

- Poistin kansion sekä sen sisällöt eli myös static kansion ja index.html tiedoston, joka oli static kansion sisällä. Lisäämällä komentoon ``-r`` poistuu kansio sisältöineen.

    $ rm -r mscom/
   
- Aktivoin virtuaaliympäristön uudestaan   
   
    $ source env/bin/activate

- Loin django projektin 

    $ django-admin startproject mscom

- Poistuin virtuaaliympäristöstä ja kävin tekemässä uudestaan ``static/`` kansion sekä kansion sisälle ``index.html`` tiedoston, johon kirjoitin simppelin "Uusi static sivu"

- Takaisin virtuaaliympäristöön

    $ source env/bin/activate
    
- Muokkaamaan mscom.conf tiedostoa

    $ sudoedit /etc/apache2/sites-available/mscom.conf
    
![Add file: h11 9](h11-9.PNG)

Tiedoston mallin kopioin suoraan Tero Karviselta, Deploy Django 4 - Production Install, Luettavissa: https://terokarvinen.com/2022/deploy-django/#connect-python-to-apache-using-mod_wsgi

- Vaihdoin tiedostopolut omiini. 

- Asensin Apachen WSGI moduulin.

    $ sudo apt-get -y install libapache2-mod-wsgi-py3
    
- Tarkastin syntaksin.
    
![Add file: h11 10](h11-10.PNG)

- Syntaksi näyttäisi olevan jälleen kunnossa, käynnistetään apache uudelleen.

    $ sudo systemctl restart apache2

- Testataan mitä sivu vastaa, jonka jälkeen katsotaan, mitä serveriä sivu käyttää.

    $ curl -s localhost|grep title
    $ curl -sI localhost|grep Server

Vastaukset:

![Add file: h11 11](h11-11.PNG)

Homma pelaa, oikea title tuli näkyviin ja serverikin näyttäisi olevan Apache.

- Tsekataan vielä selaimella.

![Add file: h11 12](h11-12.PNG)

Toimii! Django projekti näkyy nyt apachen pyörittämällä sivulla.

### Napataan DEGUB pois päältä.

    $ cd
    $ cd publicwsgi/mscom/
    $ micro mscom/settings.py 

![Add file: h11 13](h11-13.PNG)

    $ touch mscom/wsgi.py
    
![Add file: h11 14](h11-14.PNG)

DEBUG sivu on nyt pois päältä 

![Add file: h11 15](h11-15.PNG)

http://localhost/admin/ toimii edelleen, jotta pääsen hallitsemaan projektia.

### Lisätään tyyliä.

    $ cd
    $ cd publicwsgi/mscom/
    $ micro mscom/settings.py

- Lisäsin tiedostoon:
    
    import os
    STATIC_ROOT = os.path.join(BASE_DIR, 'static/')
    
Syötin ``$ ./manage.py collectstatic``, joka vastasi seuraavalla errorilla.

![Add file: h11 16](h11-16.PNG)

Rivien lopussa seuraava rivi kertoo syyn

    "FILE ".......", line 13, in <module>
      STATIC_ROOT = os.path.join(BASE_DIR, 'static/')
    NameError: name 'BASE_DIR' is not defined. 

Eli ``BASE_DIR`` ei ole määritelty. Kävin tarkistamassa kansion 
    
    $ cd publicwsgi/mscom/
    $ ls

![Add file: h11 17](h11-17.PNG)

Huomasin, että static kansiossa ei ole perässä ``/`` merkkiä. Kävin muokkaamassa uudestaan ``mscom/settings.py`` tiedostoa ja poistin ``/`` merkin eli

    STATIC_ROOT = os.path.join(BASE_DIR, 'static')

Tämän jälkeen uudestaan 

    $ ./manage.py collectstatic

![Add file: h11 18](h11-18.PNG)

Nyt näyttää oikealta, pävitellään django projekti komennolla ``$ touch mscom/wsgi.py`` ja katsotaan, miltä admin sivut nyt näyttää.

![Add file: h11 19](h11-19.PNG)

Nyt sivut näyttävät paremmalta.

a) kohta valmis ~2:50 eli hieman alta 3h vierähti. 
