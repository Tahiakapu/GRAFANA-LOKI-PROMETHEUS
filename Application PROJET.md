**PREPARATION DU PROJET TEST TAHIA :**
-
**1. Installer un docker sur la machine physique (pour test Tahia) ou sur le serveur**
-
**2. Création des fichiers .yml et .yaml pour :**
-
- docker-compose.yml (avec les services GRAFANA, PROMTAIL, LOKI, PROMETHEUS, PUSHGATEWAY, TEMPO, les volumes et les networks)

- prometheus.yml

- loki-config.yaml

- promtail-config.yaml

- tempo-config.yaml

Mettre tout dans un seul dossier au même niveau, enregistrer les fichiers sur "Tous les fichiers" et bien noter le chemin pour la commande

**3. Sur Docker, créer les conteneurs (containers) avec la commande :**
-
docker compose up -d 

docker ps -a

Et voir si tous les conteneurs sont bien monté (UP)

**4. Sur un navigateur, ouvrir un URL **
-

http://localhost:3000

L'interface GRAFANA doit apparaître

**5. On crée des nouvelles sources de données et de nouveaux tableaux de bord**
-
*5.1 PROMETHEUS*

Avec l'url : http://prometheus:9090

On valide et on crée un nouveau tableau de bord avec une configuration de base, ici on mettra UP pour voir le contenu des métriques

UP et on fait un Shift+Entrée pour appliquer la requête

On pourra alors crée un nouveau tableau de bord et enregistrer sous PROMETHEUS METRICS

*5.2 LOKI*

Avec l'url : http://loki:3100

On valide et on crée un nouveau tableau de bord avec une configuration de base dans une requête

{job=container} et on fait un Shift+Entrée pour applpiquer la requête

On pourra alors crée un nouveau tableau de bord et enregistrer sous LOKI LOGS

**6. On pourra maintenant créer des alertes**
-
Pour être informer des incidents suite aux évènements anormales crées lors des tableaux de bord :

*6.1 PUSHOVER (Alertes par téléphone)*

- Création de compte sur PUSHOVER.NET





