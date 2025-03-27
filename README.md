# PAwChO-lab4

Link do obrazów w repozytorium Dockerhub: https://hub.docker.com/repository/docker/extremical/pawcho-lab4/general

Obraz o tagu ":gorszy" został zbudowany bez użycia dobrych praktyk, jego pliki znajdują się w tym repozytorium w folderze web100.\
Dla obrazu o tagu ":lepszy" zastosowano dobre praktyki, jego pliki są w folderze web101. \
Utworzono również obraz z tagiem ":apache_init" za pomocą gotowego szablonu w docker init.

# Tworzenie obrazów i kontenerów

Obraz szablonowy "PHP with Apache" utworzono za pomocą poleceń:
```bash
docker init
```
```bash
docker compose up
``` 

Pozostałe obrazy oraz kontenery stworzone zostały za pomocą poleceń:
```bash
docker build -t web100 .
```
```bash
docker run -d -p 8080:80 --name web100_container web100
```

# Dockerfile obrazu "gorszego" (w repozytorium web100/Dockerfile)

```dockerfile
FROM ubuntu:latest

LABEL maintainer="Paweł Olech <s99645@pollub.edu.pl>"
RUN apt-get update && \
    apt-get upgrade -y && \
    apt-get install -y apache2 && \
    apt-get clean

COPY index.html /var/www/html/index.html

EXPOSE 80

CMD ["apachectl", "-D", "FOREGROUND"]
```

# Dockerfile obrazu "lepszego" (w repozytorium web101/Dockerfile)

```dockerfile
FROM ubuntu:latest

LABEL maintainer="Paweł Olech <s99645@pollub.edu.pl>"

# Użycie łączonego polecenia run dla lepszej czytelności i pozbycia się ewentualnych błędów
# Poprzez --no-install-recommends zmniejszymy liczbę pobranych pakietów
RUN apt-get update && \
    apt-get upgrade -y && \
    apt-get install -y --no-install-recommends apache2 && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Ustawienie ścieżki roboczej dla przejrzystości i niezawodności
WORKDIR /var/www/html

# Bezpośrednie skopiowanie bez ścieżki
COPY index.html ./index.html

EXPOSE 80

ENTRYPOINT ["apachectl", "-D", "FOREGROUND"]
```

# Rezultaty

Można użyć przeglądarki wpisując link http://localhost:8080/ albo polecenia curl:
```bash
curl http://localhost:8080/
```
Powyższe polecenie przy poprawnej konfiguracji zwróci:
```html
<!DOCTYPE html>
<html lang="pl">
<head>
    <meta charset="UTF-8">
    <title>Paweł Olech</title>
</head>
<body>
    <h1>Paweł Olech</h1>
    <p>I1S 6.10</p>
</body>
</html>
```

# Ilość warstw

Ilość warstw można zobaczyć między innymi za pomocą polecenia
```bash
docker history <nazwa_obrazu>
```
W przypadku naszych obrazów terminal zwróci następujące rezultaty (web100 - obraz z brakiem dobrych praktyk, web101 - obraz z dobrymi praktykami, apache_init - szablonowy obraz z docker init):\
web100 - waga: ok. 270 MB, ilość warstw: 11
```bash
$ docker history web100
IMAGE          CREATED          CREATED BY                                      SIZE      COMMENT
6dda69ac2042   36 minutes ago   CMD ["apachectl" "-D" "FOREGROUND"]             0B        buildkit.dockerfile.v0
<missing>      36 minutes ago   EXPOSE map[80/tcp:{}]                           0B        buildkit.dockerfile.v0
<missing>      36 minutes ago   COPY index.html /var/www/html/index.html # b…   177B      buildkit.dockerfile.v0
<missing>      40 minutes ago   RUN /bin/sh -c apt-get update &&     apt-get…   191MB     buildkit.dockerfile.v0
<missing>      40 minutes ago   LABEL maintainer=Paweł Olech <s99645@pollub.…   0B        buildkit.dockerfile.v0
<missing>      8 weeks ago      /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B        
<missing>      8 weeks ago      /bin/sh -c #(nop) ADD file:6df775300d76441aa…   78.1MB    
<missing>      8 weeks ago      /bin/sh -c #(nop)  LABEL org.opencontainers.…   0B        
<missing>      8 weeks ago      /bin/sh -c #(nop)  LABEL org.opencontainers.…   0B        
<missing>      8 weeks ago      /bin/sh -c #(nop)  ARG LAUNCHPAD_BUILD_ARCH     0B        
<missing>      8 weeks ago      /bin/sh -c #(nop)  ARG RELEASE                  0B        
```
web101 - waga: ok. 220 MB, ilość warstw: 12
```bash
$ docker history web101
IMAGE          CREATED          CREATED BY                                      SIZE      COMMENT
f432ad07989b   19 minutes ago   ENTRYPOINT ["apachectl" "-D" "FOREGROUND"]      0B        buildkit.dockerfile.v0
<missing>      19 minutes ago   EXPOSE map[80/tcp:{}]                           0B        buildkit.dockerfile.v0
<missing>      19 minutes ago   COPY index.html ./index.html # buildkit         177B      buildkit.dockerfile.v0
<missing>      19 minutes ago   WORKDIR /var/www/html                           0B        buildkit.dockerfile.v0
<missing>      19 minutes ago   RUN /bin/sh -c apt-get update &&     apt-get…   142MB     buildkit.dockerfile.v0
<missing>      19 minutes ago   LABEL maintainer=Paweł Olech <s99645@pollub.…   0B        buildkit.dockerfile.v0
<missing>      8 weeks ago      /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B        
<missing>      8 weeks ago      /bin/sh -c #(nop) ADD file:6df775300d76441aa…   78.1MB    
<missing>      8 weeks ago      /bin/sh -c #(nop)  LABEL org.opencontainers.…   0B        
<missing>      8 weeks ago      /bin/sh -c #(nop)  LABEL org.opencontainers.…   0B        
<missing>      8 weeks ago      /bin/sh -c #(nop)  ARG LAUNCHPAD_BUILD_ARCH     0B        
<missing>      8 weeks ago      /bin/sh -c #(nop)  ARG RELEASE                  0B 
```
apache_init - waga: ok. 514 MB, ilość warstw: 16
```bash
$ docker history apache-server:latest 
IMAGE          CREATED          CREATED BY                                      SIZE      COMMENT
b9d6271cebd7   12 minutes ago   USER www-data                                   0B        buildkit.dockerfile.v0
<missing>      12 minutes ago   COPY ./ /var/www/html # buildkit                1.11kB    buildkit.dockerfile.v0
<missing>      12 minutes ago   RUN /bin/sh -c mv "$PHP_INI_DIR/php.ini-prod…   69kB      buildkit.dockerfile.v0
<missing>      2 weeks ago      CMD ["apache2-foreground"]                      0B        buildkit.dockerfile.v0
<missing>      2 weeks ago      EXPOSE map[80/tcp:{}]                           0B        buildkit.dockerfile.v0
<missing>      2 weeks ago      WORKDIR /var/www/html                           0B        buildkit.dockerfile.v0
<missing>      2 weeks ago      COPY apache2-foreground /usr/local/bin/ # bu…   1.35kB    buildkit.dockerfile.v0
<missing>      2 weeks ago      STOPSIGNAL SIGWINCH                             0B        buildkit.dockerfile.v0
<missing>      2 weeks ago      ENTRYPOINT ["docker-php-entrypoint"]            0B        buildkit.dockerfile.v0
<missing>      2 weeks ago      RUN /bin/sh -c docker-php-ext-enable sodium …   17B       buildkit.dockerfile.v0
<missing>      2 weeks ago      COPY docker-php-ext-* docker-php-entrypoint …   7.21kB    buildkit.dockerfile.v0
<missing>      2 weeks ago      RUN /bin/sh -c set -eux;   savedAptMark="$(a…   59.4MB    buildkit.dockerfile.v0
<missing>      2 weeks ago      COPY docker-php-source /usr/local/bin/ # bui…   587B      buildkit.dockerfile.v0
<missing>      2 weeks ago      RUN /bin/sh -c set -eux;   savedAptMark="$(a…   14.1MB    buildkit.dockerfile.v0
<missing>      2 weeks ago      ENV PHP_SHA256=0d3270bbce4d9ec617befce52458b…   0B        buildkit.dockerfile.v0
<missing>      2 weeks ago      ENV PHP_URL=https://www.php.net/distribution…   0B        buildkit.dockerfile.v0
<missing>      2 weeks ago      ENV PHP_VERSION=8.4.5                           0B        buildkit.dockerfile.v0
<missing>      2 weeks ago      ENV GPG_KEYS=AFD8691FDAEDF03BDF6E460563F15A9…   0B        buildkit.dockerfile.v0
<missing>      2 weeks ago      ENV PHP_LDFLAGS=-Wl,-O1 -pie                    0B        buildkit.dockerfile.v0
<missing>      2 weeks ago      ENV PHP_CPPFLAGS=-fstack-protector-strong -f…   0B        buildkit.dockerfile.v0
<missing>      2 weeks ago      ENV PHP_CFLAGS=-fstack-protector-strong -fpi…   0B        buildkit.dockerfile.v0
<missing>      2 weeks ago      RUN /bin/sh -c {   echo '<FilesMatch \.php$>…   237B      buildkit.dockerfile.v0
<missing>      2 weeks ago      RUN /bin/sh -c a2dismod mpm_event && a2enmod…   68B       buildkit.dockerfile.v0
<missing>      2 weeks ago      RUN /bin/sh -c set -eux;  apt-get update;  a…   49.8MB    buildkit.dockerfile.v0
<missing>      2 weeks ago      ENV APACHE_ENVVARS=/etc/apache2/envvars         0B        buildkit.dockerfile.v0
<missing>      2 weeks ago      ENV APACHE_CONFDIR=/etc/apache2                 0B        buildkit.dockerfile.v0
<missing>      2 weeks ago      RUN /bin/sh -c set -eux;  mkdir -p "$PHP_INI…   0B        buildkit.dockerfile.v0
<missing>      2 weeks ago      ENV PHP_INI_DIR=/usr/local/etc/php              0B        buildkit.dockerfile.v0
<missing>      2 weeks ago      RUN /bin/sh -c set -eux;  apt-get update;  a…   316MB     buildkit.dockerfile.v0
<missing>      2 weeks ago      ENV PHPIZE_DEPS=autoconf   dpkg-dev   file  …   0B        buildkit.dockerfile.v0
<missing>      2 weeks ago      RUN /bin/sh -c set -eux;  {   echo 'Package:…   46B       buildkit.dockerfile.v0
<missing>      2 weeks ago      # debian.sh --arch 'amd64' out/ 'bookworm' '…   74.8MB    debuerreotype 0.15
```

# Wnioski

- Gotowe szablony (z polecenia docker init) zabierają o wiele więcej pamięci niż własnoręcznie zbudowane obrazy.
- Dla uruchomienia prostej strony html nie potrzebowaliśmy tak rozbudowanego obrazu jaki oferuje nam szablon "PHP with Apache".
- W przypadku własnoręcznie zbudowanych obrazów dobre praktyki pozwoliły nam zaoszczędzić 50 MB poprzez nie instalację dodatkowych pakietów.
