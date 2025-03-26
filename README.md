# PAwChO-lab4

Link do obrazów w repozytorium Dockerhub: https://hub.docker.com/repository/docker/extremical/pawcho-lab4/general

Obraz o tagu ":gorszy" został zbudowany bez użycia dobrych praktyk, jego pliki znajdują się w tym repozytorium w folderze web100.\
Dla obrazu o tagu ":lepszy" zastosowano dobre praktyki, jego pliki są w folderze web101.

# Tworzenie obrazów i kontenerów

Obrazy oraz kontenery stworzone zostały za pomocą poleceń:
```bash
$ docker build -t web100 .
```
```bash
$ docker run -d -p 8080:80 --name web100_container web100
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
$ curl http://localhost:8080/
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
$ docker history <nazwa_obrazu>
```
W przypadku naszych obrazów terminal zwróci następujące rezultaty (web100 - obraz z brakiem dobrych praktyk, web101 - obraz z dobrymi praktykami):\
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

# Wnioski

W naszym przypadku dobre praktyki pozwoliły zaoszczędzić 50 MB poprzez nie instalację dodatkowych pakietów.
