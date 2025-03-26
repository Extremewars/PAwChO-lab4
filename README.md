# PAwChO-lab4

https://hub.docker.com/repository/docker/extremical/pawcho-lab4/general

Obrazy stworzone za pomocą poleceń:
$ docker build -t web100 .
$ docker run -d -p 8080:80 --name web100_container web100

Obraz o tagu ":gorszy" jest zrobiony nie używając dobrych praktyk. Zawiera on 11 warstw i waży ok. 270 MB
Obraz o tagu ":lepszy" został zbudowany za pomocą dobrych praktyk. Zawiera on 12 warstw i waży ok. 220 MB\

Obrazy stworzone za pomocą poleceń:
$ docker build -t web100 .
$ docker run -d -p 8080:80 --name web100_container web100

Obraz o tagu ":gorszy" jest zrobiony nie używając dobrych praktyk. Zawiera on 11 warstw i waży ok. 270 MB
Obraz o tagu ":lepszy" został zbudowany za pomocą dobrych praktyk. Zawiera on 12 warstw i waży ok. 220 MB

pawel@cinnamon:~/lab4-web101$ curl http://localhost:8080/
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


pawel@cinnamon:~$ docker history web100
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
pawel@cinnamon:~$ docker history web101
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
