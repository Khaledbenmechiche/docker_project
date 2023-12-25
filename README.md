

Projet Docker 1 - Stack Applicative WordPress et Maria DB
Objectif :
Mettre en place une stack applicative composée d'une application frontend WordPress et d'une base de données Maria DB, utilisant Docker et Docker Compose.
Les images de base de système d'exploitation optimisée que nous avons utilisé pour WordPress est : wordpress:latest & mariadb:latest
Justification du choix des images :
Nous avons choisi l'image officielle de WordPress optimisée et  qui est basée sur php et Apache.
Prêt à l'emploi : Ces images sont préconfigurées pour fonctionner immédiatement avec WordPress et Maria DB sans nécessiter une configuration complexe supplémentaire.
Maintenance simplifiée : Elles sont maintenues par la communauté Docker, assurant des mises à jour régulières et la correction des vulnérabilités de sécurité.
Compatibilité : Ces images sont bien testées pour fonctionner ensemble, garantissant une intégration fluide entre WordPress et la base de données Maria DB.

Le fichier Dockerfile : 


Variables d'environnement : WordPress utilise des variables d'environnement pour configurer la base de données. Les principales sont WORDPRESS_DB_HOST, WORDPRESS_DB_USER, WORDPRESS_DB_PASSWORD, et WORDPRESS_DB_NAME. Ces variables seront configurées dans le fichier wp-config.php.
Explication docker-compose.yml : 
Réseaux virtuels : Docker Compose crée un réseau virtuel par défaut, ce qui permet aux conteneurs de se référencer les uns les autres par leur nom de service (wordpress et db dans ce cas).
Variables d'environnement : Les informations de base de données nécessaires sont fournies en tant que variables d'environnement dans le service WordPress et le service MariaDB.
Volumes persistants : Des volumes Docker sont utilisés pour stocker les données persistantes de WordPress et de la base de données MariaDB.

image: wordpress:latest : Spécifie l'image Docker à utiliser pour ce service, dans ce cas, il s'agit de l'image "latest" de WordPress, ce qui signifie la version la plus récente disponible.
restart: always : Cette instruction indique à Docker de redémarrer automatiquement le conteneur si celui-ci s'arrête pour une raison quelconque, sauf si l'arrêt a été explicitement demandé.
ports : - "8080:80" : Cela mappe le port 8080 de votre machine hôte au port 80 à l'intérieur du conteneur WordPress. Cela permet d'accéder à WordPress via le navigateur en utilisant le port 8080.
environment: Cette section définit les variables d'environnement nécessaires pour que WordPress communique avec la base de données. Ces variables spécifient l'hôte de la base de données (WORDPRESS_DB_HOST), le nom d'utilisateur (WORDPRESS_DB_USER), le mot de passe (WORDPRESS_DB_PASSWORD) et le nom de la base de données (WORDPRESS_DB_NAME).
depends_on: - db : Cela spécifie la dépendance du service WordPress sur le service de base de données (db). Docker s'assurera que le service de base de données est opérationnel avant de démarrer le service WordPress.
volumes: - wordpress_data:/var/www/html : Cela monte un volume nommé wordpress_data dans le répertoire /var/www/html du conteneur WordPress. Cela permet de stocker les fichiers et données de WordPress de manière persistante, même si le conteneur est arrêté ou supprimé.



Partie MariaDB :
image: mariadb:latest : Spécifie l'image Docker à utiliser pour ce service, dans ce cas, l'image "latest" de MariaDB.
restart: always : Cette instruction indique à Docker de redémarrer automatiquement le conteneur si celui-ci s'arrête pour une raison quelconque, sauf si l'arrêt a été explicitement demandé.
environment: : Cette section définit les variables d'environnement nécessaires pour la configuration de la base de données MariaDB, telles que le nom de la base de données (MYSQL_DATABASE), le nom d'utilisateur (MYSQL_USER), les mots de passe pour cet utilisateur et le mot de passe root (MYSQL_ROOT_PASSWORD).
volumes : Montage de deux volumes Docker. Le premier (db_data:/var/lib/mysql) est utilisé pour stocker les données de la base de données MariaDB de manière persistante. Le second (./my.cnf:/etc/mysql/my.cnf) lie un fichier de configuration personnalisé pour MariaDB.

frontend_network: : Il s'agit d'une déclaration de réseau qui pourrait être utilisée pour connecter des services spécifiques, mais dans ce fichier, ce réseau n'est pas utilisé. Il est simplement défini mais n'a pas de services associés.
backend_network: : C'est la définition du réseau dans lequel les services wordpress-mariadb et db sont ajoutés. Ce réseau est destiné à être utilisé pour la communication entre ces services.
services: : Cela spécifie les services Docker qui seront connectés à ce réseau (backend_network). Dans ce cas, les services wordpress-mariadb et db sont ajoutés à ce réseau, leur permettant de communiquer entre eux via ce réseau défini.
Démonstration :
En lançant le “localhost:80:80” on obient ce résultat:


                     Project 02:  Cluster elasticsearch avec kabana 

Objectif :   Mettre en place un cluster Elasticsearch composé de trois nœuds et un conteneur Kibana qui a accès au cluster.
Demande : 

Reponse : 

Elasticearch.yml:

cluster.name: Nom du cluster. Il s'agit de "my-cluster" dans ce cas.
node.name: Nom du nœud Elasticsearch. Il est défini comme "es01".
network.host: Spécifie à quelle adresse IP le nœud Elasticsearch doit écouter les connexions. "0.0.0.0" signifie qu'il écoutera sur toutes les interfaces réseau.
discovery.seed_hosts: Liste des nœuds qui peuvent agir comme points de découverte pour les autres nœuds lors de la formation du cluster. Dans ce cas, ce sont "es02" et "es03".
cluster.initial_master_nodes: Liste des nœuds qui doivent être considérés comme les premiers maîtres du cluster. Dans ce cas, ce sont "es01", "es02", et "es03".
http.port: Le port sur lequel le nœud Elasticsearch écoutera les requêtes HTTP. Ici, c'est le port standard 9200.
Kibana.yml: 

server.name: Nom du serveur Kibana
server.host: Spécifie à quelle adresse IP Kibana doit écouter les connexions. "0.0.0.0" signifie qu'il écoutera sur toutes les interfaces réseau.
elasticsearch.hosts: Liste des nœuds Elasticsearch auxquels Kibana se connectera. Ici, il se connecte à "es01" sur le port 9200.
Note : 
Network.host à "0.0.0.0": Permet aux nœuds Elasticsearch d'accepter les connexions provenant de n'importe quelle interface réseau, ce qui est utile pour une configuration sur un réseau local.
discovery.seed_hosts et cluster.initial_master_nodes : Ils sont configurés pour permettre la découverte et l'initialisation correctes du cluster, assurant une formation de cluster réussie.
server.host à "0.0.0.0"dans kibana.yml`: Permet à Kibana d'accepter les connexions depuis n'importe quelle interface réseau, rendant l'accès à l'interface utilisateur de Kibana plus flexible.
elasticsearch.hosts dans kibana.yml: Indique à Kibana où se trouve le cluster Elasticsearch auquel il doit se connecter.
Le but est de créer une configuration robuste et flexible pour un cluster Elasticsearch avec Kibana, en s'assurant que les différents composants peuvent se découvrir et se connecter correctement.
docker-compose.yml:


Version Docker Compose :
La version 3 est une version stable largement utilisée et prend en charge les fonctionnalités nécessaires sans complications.
Services Elasticsearch (es01, es02, es03) :
Utilisation de l'image Elasticsearch avec la version spécifiée par la variable d'environnement ES_VERSION.
Configuration de trois nœuds Elasticsearch pour former un cluster.
Utilisation de volumes Docker pour stocker les données Elasticsearch de manière persistante.
Configuration des ports pour permettre l'accès à Elasticsearch depuis l'hôte.
Configuration du réseau pour permettre la communication entre les conteneurs.
Configuration des variables d'environnement pour les paramètres Elasticsearch.
Justification :
L'utilisation de la version spécifiée dans ES_VERSION permet de rendre le cluster flexible et conforme à la demande du projet.
Les volumes Docker garantissent la persistance des données même si les conteneurs sont détruits.
La configuration des ports et du réseau facilite la communication entre les services et l'accès à Elasticsearch depuis l'hôte.
Service Kibana :
Utilisation de l'image Kibana avec la même version spécifiée pour Elasticsearch.
Configuration des ports pour permettre l'accès à Kibana depuis l'hôte.
Configuration du réseau pour permettre la communication avec Elasticsearch.
Configuration de l'URL Elasticsearch pour Kibana.
Justification :
L'utilisation de la même version pour Kibana assure la compatibilité avec Elasticsearch.
La configuration des ports et du réseau facilite l'accès à Kibana depuis l'hôte et la communication avec Elasticsearch.
La configuration de l'URL permet à Kibana de se connecter correctement au cluster Elasticsearch.
Volumes :
Définition de volumes pour la persistance des données Elasticsearch.
Justification : 
Les volumes Docker garantissent la persistance des données entre les redémarrages des conteneurs, assurant ainsi la durabilité du cluster.
Réseau personnalisé (es-net) :
Configuration d'un réseau personnalisé pour les services.
Justification :
 La création d'un réseau personnalisé facilite la communication entre les services tout en les isolant du réseau par défaut, améliorant ainsi la sécurité et la gestion du trafic.
Résumé :
Les choix techniques visent à créer un environnement Elasticsearch stable, persistant et sécurisé, conforme aux exigences du projet. L'utilisation de variables d'environnement permet une flexibilité accrue, tandis que la configuration des ports, des volumes et du réseau garantit la communication efficace entre les services.
Résultat : 



          Approche 02: Cluster elasticsearch avec kabana 

Cette approche qui est fonctionnelle consiste à déployer un seul elasticsearch avec kabana.
Cette approche consiste à configurer un environnement Elasticsearch et Kibana, où Elasticsearch fonctionne en mode autonome et Kibana dépend d'Elasticsearch. L'utilisation de la version 7.13.2 assure la compatibilité entre les deux services.

docker-compose.yml:


Résultat d'exécution: 

localhost:9200


localhost:5601


