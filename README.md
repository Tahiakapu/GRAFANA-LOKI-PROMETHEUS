**Migration de l'Observabilité de Graylog/Observium vers Grafana Loki/Prometheus**
-
**1. Introduction**
-
Cette documentation propose une migration et une modernisation de l'infrastructure d'observabilité actuelle, basée sur Graylog pour la gestion des logs et Observium pour la surveillance réseau et système, vers une solution intégrée et plus performante utilisant Grafana Loki pour les logs et Prometheus pour les métriques. L'objectif principal est d'améliorer la visibilité sur les systèmes, de centraliser les données d'observabilité et de faciliter l'analyse pour une résolution plus rapide des incidents.

**2. Contexte Actuel**
-
Actuellement, l'observabilité est assurée par deux outils distincts :

*Graylog :* Utilisé pour la collecte, l'indexation et l'analyse des logs provenant de diverses applications et systèmes. Graylog offre une interface de recherche puissante et des tableaux de bord pour la visualisation des événements.

*Observium :* Dédié à la surveillance réseau, il collecte des données SNMP à partir des équipements réseau et serveurs, fournissant des graphiques et des alertes sur l'état des infrastructures.

Bien que ces outils remplissent leurs fonctions de base, leur découplage peut entraîner une fragmentation des informations et une complexité accrue lors de l'investigation de problèmes qui impliquent à la fois les logs et les métriques.

**3. Objectifs de la Migration**
-
La migration vers Grafana Loki et Prometheus vise à atteindre les objectifs suivants :

- Centralisation de l'Observabilité : Unifier la gestion des logs et des métriques au sein d'une plateforme unique (Grafana) pour une vue consolidée de l'état du système.

- Amélioration des Performances : Loki et Prometheus sont conçus pour être hautement performants et scalables, permettant de gérer de plus grands volumes de données.

- Facilitation de l'Analyse : Profiter des capacités avancées de requêtage (LogQL pour Loki, PromQL pour Prometheus) et de visualisation de Grafana pour des analyses plus approfondies et croisées.

- Réduction de la Complexité : Simplifier l'architecture d'observabilité en utilisant des outils plus intégrés et potentiellement moins gourmands en ressources que les solutions actuelles.

- Standardisation des Alertes : Utiliser le système d'alerte de Prometheus et Grafana pour une gestion cohérente des notifications.

- Préparation à l'Évolutivité : Mettre en place une architecture qui peut facilement s'adapter à la croissance future de l'infrastructure et des applications.

**4. Présentation des Nouvelles Solutions**
-
**4.1. Prometheus**

Prometheus est un système open-source de surveillance et d'alerte basé sur une architecture "pull" (tirer), où Prometheus va interroger les "exporters" exposant des métriques sur les cibles à surveiller.

- Modèle de données multidimensionnel : Les métriques sont identifiées par des noms et des paires clé/valeur (labels), permettant une grande flexibilité de requêtage.

- Langage de requêtage puissant (PromQL) : Permet de sélectionner et d'agréger des données en temps réel.

- Découverte de services : Intégration avec des systèmes comme Kubernetes, EC2, Consul, etc., pour découvrir automatiquement les cibles à surveiller.

- Stockage local efficace : Utilise un stockage sur disque local avec une compression efficace.

- Alerting Manager : Composant distinct pour la gestion des alertes, le déduplicage, le regroupement et l'envoi de notifications.

**4.2. Grafana Loki**

Grafana Loki est un système d'agrégation de logs inspiré de Prometheus. Contrairement à d'autres systèmes de gestion de logs qui indexent le contenu complet des logs, Loki se concentre sur l'indexation des métadonnées (labels) des logs.

- LogQL : Langage de requêtage pour les logs, inspiré de PromQL, permettant de filtrer les logs en fonction de leurs labels et de leur contenu.

- Faible coût de stockage : Grâce à son approche d'indexation des métadonnées plutôt que du contenu complet.

- Intégration native avec Grafana : Permet de visualiser les logs et les métriques côte à côte dans les mêmes tableaux de bord.

- Agent Promtail : Agent de collecte de logs qui envoie les logs à Loki, ajoutant des labels aux flux de logs.

**4.3. Grafana**

Grafana est une plateforme open-source pour l'analyse et la visualisation de données. Elle permet de créer des tableaux de bord interactifs à partir de diverses sources de données.

- Tableaux de bord personnalisables : Créez des visualisations riches avec différents types de graphiques, jauges, etc.

- Support de multiples sources de données : Connectez-vous à Prometheus, Loki, Elasticsearch, InfluxDB, PostgreSQL, MySQL, etc.

- Fonctionnalités d'alerte : Configurez des alertes basées sur des seuils pour les métriques.

- Exploration de données : Explorez les données en temps réel ou historiques.

**4.4. Prometheus Pushgateway**

Prometheus Pushgateway est un composant intermédiaire qui permet aux services éphémères ou aux jobs en mode "batch" (qui n'ont pas une durée de vie suffisamment longue pour être scrapés par Prometheus) d'exposer leurs métriques. Au lieu que Prometheus vienne les "puller", ces services "poussent" leurs métriques vers le Pushgateway, qui les rend ensuite disponibles pour le scraping par le serveur Prometheus.

- Jobs "batch" ou scripts de courte durée.

- Services qui ne peuvent pas être atteints directement par Prometheus (derrière un pare-feu, par exemple).

- Applications qui sont lancées à la demande et s'arrêtent rapidement.

**5. Choix Technologiques pour le Déploiement : Docker et Docker Compose**
-
Pour faciliter le déploiement, la gestion et l'évolutivité des composants d'observabilité, l'utilisation de Docker et Docker Compose est préconisée.

**5.1. Docker**

Docker est une plateforme open-source qui permet d'automatiser le déploiement d'applications dans des conteneurs légers et portables.

*Avantages :*

- Isolation : Chaque composant (Prometheus, Loki, Grafana, Pushgateway, etc.) s'exécute dans son propre conteneur isolé, évitant les conflits de dépendances.

- Portabilité : Les conteneurs peuvent être exécutés de manière cohérente sur n'importe quel environnement supportant Docker (développement, test, production).

- Gestion des Dépendances : Les images Docker incluent toutes les dépendances nécessaires, simplifiant l'installation.

- Scalabilité : Facilite la mise à l'échelle des services en lançant de multiples instances de conteneurs.

**5.2. Docker Compose**

Docker Compose est un outil pour définir et exécuter des applications Docker multi-conteneurs. Il utilise un fichier YAML pour configurer les services d'une application, ce qui permet de démarrer, arrêter et gérer l'ensemble de la stack avec une seule commande.

*Avantages :*

- Orchestration Simplifiée : Définir tous les services (Prometheus, Loki, Grafana, Pushgateway, Alertmanager) et leurs interconnexions dans un seul fichier.

- Déploiement Facile : Lancer l'ensemble de la stack d'observabilité avec docker-compose up.

- Gestion des Volumes : Configurer facilement le stockage persistant pour les données de Prometheus et Loki.

- Réseau Automatique : Docker Compose crée un réseau virtuel entre les conteneurs, facilitant leur communication.

**6. Architecture Cible Proposée (avec Docker et Docker Compose)**
-
L'architecture cible envisagée pour l'observabilité sera la suivante, avec les composants déployés sous forme de conteneurs Docker et orchestrés par Docker Compose pour la stack principale :

<img width="3840" height="3033" alt="Untitled diagram _ Mermaid Chart-2025-07-24-182758" src="https://github.com/user-attachments/assets/5d2a499e-92e7-4b1f-9aa1-12fb805807f2" />

*Explications des ajouts :*

Les composants clés de la stack d'observabilité (Loki, Prometheus, Alertmanager, Grafana, Pushgateway) seront déployés comme des services définis dans un fichier docker-compose.yml.

Promtail et les Exporters (comme node_exporter, snmp_exporter) pourront être déployés soit directement sur les machines hôtes (natif) pour une meilleure intégration avec le système, soit également en tant que conteneurs Docker sur ces machines si l'environnement le permet et si la gestion des volumes de logs ou l'accès aux interfaces est géré correctement. La flexibilité est clé ici.

Le Pushgateway est représenté comme un service au sein de la stack Docker Compose, collectant les métriques des jobs éphémères avant que Prometheus ne vienne les scraper.

**7. Étapes Préliminaires du Projet**
-
1. Inventaire détaillé des sources de logs (applications, OS, services) et des métriques (serveurs, équipements réseau, applications).

2. Identification des tableaux de bord et alertes critiques existants dans Graylog et Observium à reproduire.

3. Évaluation des volumes de données de logs et de métriques actuels pour dimensionner la nouvelle infrastructure.

4. Choix de l'Infrastructure Matérielle/Virtuelle : Machine virtuelle et Docker

5. Planifier les ressources matérielles (CPU, RAM, stockage) pour la machine hôte qui exécutera la stack Docker Compose (Loki, Prometheus, Grafana, etc.).

6. Assurer la capacité de stockage suffisante pour les données persistantes de Loki et Prometheus (volumes Docker).

7. Phase de Test et Preuve de Concept (PoC) : Petit test miniature sur PC portable Tahia

8. Déployer la stack minimale de Loki, Prometheus, Grafana et Pushgateway via Docker Compose sur un environnement de test.

9. Collecter des logs et des métriques à partir d'une source limitée (e.g., un serveur de test, une application simple) en utilisant Promtail et un exporter.

10. Simuler un envoi de métriques vers Pushgateway depuis un script test.

11. Créer des tableaux de bord et des alertes de base dans Grafana pour valider la chaîne d'observabilité.

**8. Plan de Migration (Provisoire)**
-
*Prérequis du Serveur Hôte :*

- Installation de Docker et Docker Compose sur le serveur dédié à la stack d'observabilité.

- Configuration des pare-feux pour les ports nécessaires (Grafana, Prometheus, Loki, Alertmanager, Pushgateway).

*Déploiement de la Stack via Docker Compose :*

- Création du fichier docker-compose.yml définissant les services Loki, Prometheus, Grafana, Alertmanager et Pushgateway, avec leurs configurations, volumes persistants et réseaux.

- Lancement de la stack avec docker-compose up -d.

*Collecte des Métriques :*

- Déployer les exporters Prometheus (node_exporter, snmp_exporter, etc.) sur les serveurs et équipements réseau. Ils pourront être déployés en conteneurs Docker ou nativement selon le contexte.

- Configurer Prometheus (via son fichier prometheus.yml monté en volume Docker) pour scraper les métriques des exporters.

- Implémenter l'envoi de métriques vers Pushgateway pour les jobs éphémères ou les services non-scrapables.

- Créer les premiers tableaux de bord Prometheus dans Grafana pour reproduire les vues d'Observium.

*Collecte des Logs :*

- Déployer Promtail sur les serveurs émettant des logs. Promtail peut aussi être conteneurisé.

- Configurer Promtail (via son fichier promtail-config.yml monté en volume Docker) pour lire les fichiers de logs et les envoyer à Loki avec les labels appropriés.

- Créer les premiers tableaux de bord Loki dans Grafana pour reproduire les vues de Graylog.

*Configuration des Alertes :*

- Définir les règles d'alerte dans Prometheus (alert.rules monté en volume Docker).

- Configurer Alertmanager (via son fichier alertmanager.yml monté en volume Docker) pour acheminer les notifications vers les canaux appropriés (e.g., e-mail, Slack, Teams).

- Reconfigurer les alertes existantes de Graylog et Observium dans le nouveau système.

*Tests et Validation :*

- Effectuer des tests de charge pour s'assurer que la nouvelle infrastructure peut gérer le volume de données.

- Valider la complétude et l'exactitude des données collectées.

- Tester les alertes de bout en bout.

- Vérifier le bon fonctionnement de Pushgateway pour les métriques concernées.

*Basculement Progressif :*

- Commencer par faire coexister les deux systèmes (Graylog/Observium et Loki/Prometheus) pendant une période de transition.

- Migrer progressivement les équipes et les processus vers la nouvelle plateforme.

- Former les utilisateurs à la nouvelle interface et aux langages de requêtage (PromQL, LogQL).

*Désactivation des Anciens Systèmes :*

- Une fois que la nouvelle solution est stable et que toutes les dépendances ont été migrées, désactiver et décommissionner Graylog et Observium.

**9. Sécurité**
-
- Authentification et Autorisation : Mettre en place l'authentification des utilisateurs pour Grafana (LDAP, OAuth, etc.) et gérer les rôles et permissions.

- Sécurisation des Communications : Utiliser HTTPS/TLS pour toutes les communications entre les composants (Promtail-Loki, Exporters-Prometheus, Grafana-Prometheus/Loki, clients-Pushgateway, Pushgateway-Prometheus).

- Accès aux Données : Restreindre l'accès aux serveurs Prometheus et Loki aux personnes autorisées uniquement.

- Journalisation de Sécurité : Configurer la journalisation des accès et des actions sur les composants de l'observabilité.

- Mises à Jour des Images Docker : Maintenir les images Docker à jour pour bénéficier des correctifs de sécurité.


