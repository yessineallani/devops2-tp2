\# Réponses — TP2 DevOps



\## Q1. Variables d’environnement de Strapi



Les variables `DATABASE\_CLIENT`, `DATABASE\_HOST`, `DATABASE\_PORT`, `DATABASE\_NAME`, `DATABASE\_USERNAME` et `DATABASE\_PASSWORD` permettent à Strapi de se connecter à PostgreSQL. Le nom d’hôte est `postgres`, car il correspond au nom du service Docker Compose.



\## Q2. Fichiers `.env` et `.env.example`



Le fichier `.env` contient les valeurs réelles des variables d’environnement et les secrets du projet. Il ne doit pas être envoyé sur GitHub. Le fichier `.env.example` fournit un modèle sans secrets réels pour permettre aux autres utilisateurs de configurer leur environnement.



\## Q3. Différence entre `docker compose down` et `docker compose down -v`



`docker compose down` arrête et supprime les conteneurs et le réseau du projet, mais conserve les volumes nommés. `docker compose down -v` supprime également les volumes nommés et donc les données persistantes qui y sont stockées.



\## Q4. Healthcheck PostgreSQL



La commande `pg\_isready` vérifie si PostgreSQL est prêt à accepter les connexions. Dans un fichier Compose, `$$` empêche l’interpolation immédiate par Docker Compose et permet à la variable d’être interprétée dans le conteneur.



\## Q5. Configuration de pgAdmin



Le nom `postgres` permet à pgAdmin de joindre le service PostgreSQL sur le réseau Docker. L’option `:ro` monte le fichier `servers.json` en lecture seule. La variable `${PGADMIN\_PORT:-5050}` utilise le port défini dans `.env` ou le port 5050 par défaut.



\## Q6. Cache des couches Docker



Le Dockerfile copie généralement les fichiers de dépendances avant le code source et exécute `npm ci`. Ainsi, si le code change sans que les fichiers de dépendances changent, Docker peut réutiliser la couche des dépendances en cache. Si les fichiers de dépendances changent, cette couche doit être reconstruite.



\## Q7. Utilisateur non privilégié



L’instruction `USER node` permet d’exécuter l’application avec l’utilisateur non privilégié `node`, ce qui réduit les risques liés à l’exécution en tant que root. L’option `--chown=node:node` attribue les fichiers à cet utilisateur et à son groupe.



\## Q8. `depends\_on` et `service\_healthy`



`depends\_on` avec `condition: service\_healthy` attend que le service dépendant soit déclaré sain par son healthcheck avant de démarrer le service suivant. Sans cette condition, un conteneur peut démarrer avant que PostgreSQL soit prêt. Cette configuration ne garantit toutefois pas que PostgreSQL restera sain pendant toute l’exécution.



\## Q9. Nombre d’articles dans PostgreSQL



Le nombre de lignes de la table `articles` doit être vérifié dans pgAdmin. Le nombre attendu dépend des données réellement enregistrées dans la base. Dans notre application, trois articles sont affichés sur le frontend ; il faut vérifier que la table correspondante contient bien les enregistrements attendus.



\## Q10. URL de Strapi depuis le frontend



Dans un conteneur, `localhost` désigne le conteneur lui-même. Le frontend doit donc utiliser `http://strapi:1337` pour contacter Strapi via le réseau Docker partagé.



\## Q11. Build multi-stage de Next.js



Un build multi-stage sépare les étapes de dépendances, de compilation et d’exécution. Cela permet de limiter les fichiers présents dans l’image finale. L’option `output: "standalone"` de Next.js génère les fichiers nécessaires au déploiement autonome. Sans cette option, le répertoire `.next/standalone` attendu par le Dockerfile risque de ne pas être généré. La taille réelle de l’image peut être vérifiée avec `docker images tp2/frontend`.



\## Q12. Séparation des réseaux Docker



PostgreSQL et pgAdmin appartiennent au réseau `backend`. Le frontend appartient au réseau `frontend`. Strapi est connecté aux deux réseaux afin de communiquer avec PostgreSQL et de recevoir les requêtes du frontend. Cette organisation limite les communications directes entre les composants.



\## Q13. Améliorations de sécurité



On peut renforcer la sécurité en utilisant des secrets robustes, en évitant de publier inutilement les ports internes, en limitant les privilèges des utilisateurs, en mettant régulièrement à jour les images et les dépendances, en utilisant HTTPS en production et en réalisant des sauvegardes régulières.



\## Q14. Politique de redémarrage Docker



Avec `restart: unless-stopped`, Docker redémarre généralement le conteneur après une sortie inattendue ou le redémarrage du démon Docker, sauf s’il a été arrêté volontairement. `docker compose stop` arrête le conteneur sans le supprimer. `docker compose kill` envoie par défaut un signal d’arrêt forcé ; le comportement de redémarrage dépend notamment de la politique et de la manière dont le conteneur a été arrêté.



