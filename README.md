# Rapport TP1 - DEVOPS

## Objectifs

Les objectifs du travail pratique étaient de :

- Créer un repository Github avec un identifiant EFREI.
- Développer un wrapper qui récupère la météo d'un lieu spécifique via ses coordonnées géographiques en utilisant l'API openweathermap, dans le langage choisi.
- Mettre ce code dans une image Docker.
- Publier l'image sur DockerHub.
- Rendre le code accessible dans un repository Github.

## Livrables

Les livrables requis comprenaient :

- L'URL du repository Github public avec le Dockerfile et le wrapper
- L'URL du registry DockerHub public
- Un rapport

## Introduction

Le but de ce TP était de créer un wrapper en Node.js pour obtenir la météo d'un lieu via ses coordonnées, en utilisant l'API openweathermap, puis de packager ce wrapper dans une image Docker et de la publier sur DockerHub et Github.

## Réalisation

- Langage utilisé : Node.js, pour sa simplicité dans la gestion des requêtes HTTP via Axios
- Docker : Usage de l'image Node.js Alpine pour réduire la taille de l'image finale
- Gestion des variables d’environnement : Utilisation de dotenv pour charger les variables d'un fichier .env, évitant ainsi l'exposition de données sensibles

### Développement du Wrapper

```javascript
require('dotenv').config();

const axios = require('axios');

const API_KEY = process.env.API_KEY;
const LATITUDE = process.env.LAT;
const LONGITUDE = process.env.LONG;

async function fetchWeatherData() {
    try {
        const response = await axios.get('https://api.openweathermap.org/data/2.5/weather', {
            params: {
                lat: LATITUDE,
                lon: LONGITUDE,
                appid: API_KEY
            }
        });
        
        console.log(response.data);
    } catch (error) {
        console.error("Error fetching weather data:", error.message);
    }
}

fetchWeatherData();
```

Le code Node.js charge les variables d'environnement, utilise Axios pour interroger l'API OpenWeather et affiche les résultats.

### Fichier .env

Ce fichier, exclu du suivi version grâce à .gitignore, contient la clé API et les coordonnées géographiques ciblées.

### Dockerfile

```Dockerfile
FROM node:alpine3.19
WORKDIR /weather-docker
COPY . .
RUN npm install
RUN apk update && apk upgrade
RUN apk add openssl
EXPOSE 8080
CMD ["node", "server.js"]
```

Le Dockerfile construit l'image Docker de l'application, installe les dépendances nécessaires et configure le port et la commande par défaut pour exécuter l'application.

### Publication sur DockerHub

1. Construction de l'image Docker

````docker
docker build -t docker-image-weather .
````

2. Exécution de l'image avec les variables d'environnement spécifiées

````docker
docker run --env LAT="47.286918" --env LONG="-2.391378" --env API_KEY=******** docker-image-weather
````

3. Publication de l'image sur DockerHub

````docker
docker tag b2dd0b248aaf leez7one/weather-repository:docker-image-weather                           
docker push leez7one/weather-repository:docker-image-weather
docker login
````

L’API qui renvoie la météo en utilisant la commande suivante :

````docker
docker run --env LAT="31.2504" --env LONG="-99.2506" --env API_KEY=******** leez7one/weather-docker-repository:docker-image-weather
````


## Conclusion

La gestion des vulnérabilités a présenté ici certains défis, mais ce projet a permis de renforcer mes compétences en développement et déploiement d'applications Dockerisées. Cet exercice met en exergue l'importance de l'automatisation et de la standardisation dans les processus de développement.