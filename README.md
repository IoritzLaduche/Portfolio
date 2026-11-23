# Portfolio — Ioritz Laduche

Site statique néobrutaliste : présentation, projets Mintza et MainsTendues, présentations interactives et CV imprimable. Aucun serveur PHP ni base de données n’est nécessaire pour ce portfolio : les projets sont présentés, pas exécutés.

## Démarrer

Installer et démarrer Docker Desktop sur Mac, ou Docker Engine avec le plugin Compose sur un serveur Linux. Depuis ce dossier :

```sh
cd ~/Desktop/Alternance/portfolio
docker compose up -d --build
```

Ouvrir http://localhost:8080. La première commande télécharge l’image Nginx et construit le conteneur. `docker compose up` seul fonctionne également ; il garde les journaux au premier plan. Après une modification du site, utiliser `--build` pour reconstruire l’image.

## Changer le port

Le port par défaut est 8080. Pour le modifier durablement :

```sh
cp .env.example .env
```

Modifier `PORT` dans `.env`, puis relancer `docker compose up -d --build`. Par exemple, `PORT=80` sert le site sur http://localhost si ce port est disponible. Un changement ponctuel est aussi possible :

```sh
PORT=8090 docker compose up -d --build
```

## Accès sur un réseau ou sur Internet

Compose publie le port HTTP sur la machine qui exécute Docker.

- Sur le même réseau : ouvrir `http://IP-DE-LA-MACHINE:8080`, si le pare-feu autorise ce port.
- Sur un VPS : copier ce dossier sur le serveur, lancer Compose, puis ouvrir `http://IP-PUBLIQUE:8080`, si le port est autorisé par les pare-feu du serveur et de l’hébergeur.
- Depuis un ordinateur à domicile : l’accès Internet demande une redirection de port sur la box et une adresse publique joignable. Certains accès sont derrière un CGNAT et nécessitent une autre solution.
- Pour un domaine et HTTPS : configurer le DNS vers le serveur et un reverse proxy HTTPS tel que Caddy ou Traefik. Ce dossier fournit HTTP ; Compose ne crée ni domaine ni certificat. Derrière un reverse proxy local, on peut limiter `ports` à `127.0.0.1:8080:8080`.

La machine et Docker doivent rester démarrés pour que le site reste accessible. Le conteneur redémarre automatiquement avec Docker sauf s’il a été explicitement arrêté.

## Modifier le contenu

- `site/index.html` : nom, formation, présentation, projets et lien de contact GitHub.
- `site/style.css` : couleurs, typographie et mise en page responsive.
- `site/app.js` : textes des présentations des projets et filtres.
- `site/cv.html` : CV, à compléter avec les coordonnées, les dates et les expériences. Le bouton d’impression permet d’enregistrer en PDF dans le navigateur.
- `site/assets/` : logos issus des deux dépôts locaux.

Les informations du parcours proviennent de la demande d’Ioritz ; les descriptions techniques des projets proviennent des fichiers Mintza et SAE-MainsTendues. Aucune date de diplôme ni expérience professionnelle n’a été inventée. Les polices sont chargées via Google Fonts ; des polices système prennent le relais sans connexion.

Après chaque modification :

```sh
docker compose up -d --build
```

## Commandes utiles

```sh
docker compose ps                          # État et contrôle de santé
docker compose logs -f                    # Journaux (Ctrl+C pour quitter)
docker compose exec portfolio nginx -t    # Vérifier Nginx
docker compose down                       # Arrêter et supprimer le conteneur
```

Le contrôle de santé utilise `/healthz`. Le serveur tourne avec l’utilisateur `nginx`, sans privilèges, avec un système de fichiers en lecture seule et un espace temporaire en mémoire. Aucun secret ni donnée des bases des projets n’est embarqué.

## Documentation

- https://docs.docker.com/compose/reference/up/
- https://docs.docker.com/get-started/docker-concepts/running-containers/publishing-ports/
- https://hub.docker.com/_/nginx
