# Docker in 60 minuti

- [Youtube: Docker in 60 minuti](https://youtu.be/kYdP0BIGQAM);

## Comandi mostrati

- docker version
- docker images
- docker run -d --name <nome-a-piacere> <immagine-nome>:<immagine-versione>
- docker ps (oppure docker ps -a)
- docker exec -ti <container-id> bash -> esegue la bash all'interno del container indicato utilizzando il terminale interattivo
- docker stop <container-id> -> ferma un container
- docker start <container-id oppure immagine-nome> -> avvia un container
- docker rm <container-name> -> distrugge il container se il container è stoppato (si può aggiungere l'opzione -f per forzare la rimozione)

## Binding porte
- docker run -d --name <nome-a-piacere> -p <porta-host>:<porta-container> <immagine-nome>:<immagine-versione> -> verifica del funzionamento (in caso di nginx e -p 8080:80) con curl localhost:8080

## Volumi

Montare una directory dell'host all'interno del container.
Per esempio preparano una directory sull'host:
code/html
e ci salvano un file index.html

- docker rm -f nginx-1-13-7 -> cancella il container per poi ricrearlo (è l'unico modo per modificarlo)
- docker run -d --name <nome-a-piacere> -p <porta-host>:<porta-container> -v "<dir-container>:<dir-host>" <immagine-nome>:<immagine-versione> -> monta la directory <dir-host> sulla directory <dir-container>
    - es. docker run -d --name nginx-1-13-7 -p 8080:80 -v "/home/stefano/code/html:/usr/share/nginx/html" nginx:1.13.7
    - il volume persiste sulla macchina host anche dopo la rimozione del container

## Comunicazione fra containers

- docker run --name php -d php:fpm -> avvia un nuovo container dall'immagine php:fpm
    - mostra che non comunicano fra loro
- rimuove entrambi i containers

### Network

- docker network create <network-nome> -> crea la network network-nome che utilizzeremo come ponte di comunicazione fra containers
- docker network ls -> mostra la lista di network attive
- docker run --name php -v `pwd`/code:/code --network=webinar -d php:fpm -> lancia il container con volume e rete
- docker run --name nginx-1-13-7 -v `pwd`/code:/code -v `pwd`/code/site.conf:/etc/nginx/conf.d/default.conf --network=webinar -p 8080:80 -d nginx:1.13.7 -> due volumi (uno serve a configurare nginx), rete e porta 8080
- curl localhost:8080 -> per verificare

## Installare applicativi sulle immagini

Utilizzando Dockerfile:
- cd docker
- mkdir build
- cd build
- vim Dockerfile:
    - FROM nginx:1.13.7 -> indica l'immagine di partenza
    - RUN apt-get update && apt-get install -y vim -> installa VIM all'interno dell'immagine
- docker build -t webinar_nginx:0.1 . -> costruisce l'immagine con il nome web... e posiziona il container nella directory corrente (indicata dal punto)
- docker run --name nginx_web -d webinar_nginx:0.1 -> istanzia l'immagine con il nome nginx_web
- docker exec -ti nginx_web bash -> esegue la bash all'interno del container e proviamo a lanciare vim
- Dockerfile:
    - COMMAND -> specifica l'istruzione che il container esegue all'avvio
    - ENTRYPOINT -> utilizza il COMMAND 
    - WORKDIR -> directory di lavoro dei comandi
    - USER -> utente da utilizzare per i comandi e per l'applicazione
    - se aggiungiamo un'altra istruzione RUN al Dockerfile docker utilizzerà la cache per recuperare i passaggi già soggetti a build
    - COPY -> permette di copiare dei file dall'esterno all'interno dell'immagine

## Docker Compose

E' un tool per velocizzare il setup e teardown delle infrastrutture.
Si appoggia ad un manifesto per lanciare tutti i container necessari per l'infrastruttura.
- stop ed rm di tutti i container in esecuzione
- docker network remove webinar
- cd docker
- vim docker-compose.yaml
    - contenuto:

```yaml
version: '2'

services:
  php:
    image: php:fpm
    volumes:
      - code:/code
  
  nginx:
    image: nginx:1.13.7
    volumes:
      - code:/code
      - site.conf:/etc/nginx/conf.d/default.conf
    ports:
      - "8080:80"
```
- docker-compose up -> esegue il file, manda in esecuzione i container e vi rimane "appeso"
- docker-compose down -> stop e rimuove i container
- docker-compose up -d -> crea ed avvia i container ma rimangono in esecuzione in background
- docker-compose ps -> mostra lo stato di tutti i container lanciati con compose
- curl localhost:8080 -> per provare
- docker-compose down

si può usare docker-compose con l'opzione -f che permette di specificare il nome del file -> se non specificato la convenzione prevede che il file si chiami docker-compose.yaml 


