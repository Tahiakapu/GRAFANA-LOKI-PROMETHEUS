**PREPARATION DU PROJET TEST TAHIA :**
-

**1. Installer un docker sur la machine physique (pour test Tahia) ou sur le serveur**
-

**2. Création des fichiers du docker compose:** sur GITHUB.COM et local
-
- docker-compose.yml (avec les services GRAFANA, PROMTAIL, LOKI, PROMETHEUS, PUSHGATEWAY, TEMPO, les volumes et les networks)

- prometheus.yml

- loki-config.yaml

- promtail-config.yaml

- tempo-config.yaml

Mettre tout dans un seul dossier au même niveau, enregistrer les fichiers sur "Tous les fichiers" et bien noter le chemin pour la commande

<img width="1861" height="743" alt="GITHUB PROJET GRAFANA" src="https://github.com/user-attachments/assets/c7c42b8c-c357-4db5-8aea-2a5efe572c0e" />

**3. Sur Docker, créer les conteneurs (containers) avec la commande :**
-
docker compose up -d 

docker ps -a

Et voir si tous les conteneurs sont bien monté (UP)

<img width="1919" height="972" alt="Docker Desktop Stack" src="https://github.com/user-attachments/assets/ea3b4ac0-b66c-4111-82c6-7beba0c92293" />

**4. Sur un navigateur, ouvrir un URL **
-

http://localhost:3000

L'interface GRAFANA doit apparaître

<img width="1919" height="893" alt="INTERFACE GRAFANA" src="https://github.com/user-attachments/assets/72639671-6403-4d44-8257-9393a0ee50be" />

**5. On crée des nouvelles sources de données et de nouveaux tableaux de bord**
-
*5.1 PROMETHEUS*

Avec l'url : http://prometheus:9090

On valide et on crée un nouveau tableau de bord avec une configuration de base, ici on mettra UP pour voir le contenu des métriques

<img width="1889" height="883" alt="SOURCE DE DONNEES PROMETHEUS URL" src="https://github.com/user-attachments/assets/05e48b8f-ce91-4a43-b7bd-b9c6186a2e3c" />

UP et on fait un Shift+Entrée pour appliquer la requête

On pourra alors crée un nouveau tableau de bord et enregistrer sous PROMETHEUS METRICS

<img width="1902" height="885" alt="TABLEAU DE BORD PROMETHEUS METRICS OK" src="https://github.com/user-attachments/assets/c2967808-a9d5-4d82-ab71-092b9fd3b2d3" />

*5.2 LOKI*

Avec l'url : http://loki:3100

On valide et on crée un nouveau tableau de bord avec une configuration de base dans une requête

<img width="1895" height="844" alt="SOURCE DE DONNEES LOKI URL" src="https://github.com/user-attachments/assets/6bb53bbd-8cf7-4881-b6af-debd4e74675e" />

{job=container} et on fait un Shift+Entrée pour applpiquer la requête

On pourra alors crée un nouveau tableau de bord et enregistrer sous LOKI LOGS

<img width="1919" height="883" alt="TABLEAU DE BORD LOKI LOGS OK" src="https://github.com/user-attachments/assets/acabf746-e2ff-4549-9cf5-a27724c3007d" />

**6. On pourra maintenant créer des alertes**
-
Pour être informer des incidents suite aux évènements anormales crées lors des tableaux de bord :

*6.1 PUSHOVER (Alertes par téléphone)*

- Création de compte sur PUSHOVER.NET avec une clé UTILISATEUR (très important à retenir)

- Création d'un API/TOKEN (très important à retenir)

- 



