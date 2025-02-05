###################################################################
#                 STAGE 1 : BUILD DU FRONT (REACT + VITE)         #
###################################################################
# On choisit l'image officielle Node (version 18 ou 19 si besoin).
# "slim" est un compromis entre "full" (buster, bullseye) et "alpine".
FROM node:18-slim AS build-front

# On définit le dossier de travail pour ce stage.
WORKDIR /app/front

# On copie d'abord les fichiers package.json et package-lock.json
# (ou yarn.lock) pour permettre le caching de npm install.
COPY front/package*.json ./

# On installe les dépendances du front.
RUN npm install

# On copie le reste du code source du front.
COPY front/ .

# On lance la commande de build Vite pour React.
RUN npm run build
# Résultat : le build final se trouve dans /app/front/dist

###################################################################
#                 STAGE 2 : BUILD DU BACK + FRONT         #
###################################################################

FROM node:alpine

# Ajout d'un user non root
RUN adduser -D user1

# Définition du répertoire de travail dans le user créé à l'étape précédente
WORKDIR /home/user1

# Récupération des fichiers du backend puis installation de ceux-ci
COPY back/*.json ./
RUN npm install

# Création arborescence nécessaire au front
RUN mkdir assets

# Copie des fichiers du back + Récupération des fichiers du stage 1
COPY back/ .
COPY --from=build-front /app/front/dist/assets back/assets
COPY --from=build-front /app/front/dist/index.html back/

# Exposition sur le port 8080
EXPOSE 8080

# Définition d'un healthcheck toutes les 5minutes sur la page api/hello
HEALTHCHECK --interval=5m \
  CMD curl -f http://localhost:8080/api/hello || exit 1

# Définition du user par défaut.
USER user1

#Démarrage du script start du back.
CMD ["npm","run","start"]




