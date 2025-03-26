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

