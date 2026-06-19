# Rendu Séance 1
**ADJASSEM Yao-fiawomo Justin**
## Résumé de la séance

# Cloud & Big Data — Séance 1 : Fondamentaux du Cloud Computing

**Contexte fil rouge :** projet *Anfa*, plateforme data d'une société de transport public à Lomé.

## Définition (NIST)
Accès réseau à la demande à des ressources informatiques partagées, provisionnées rapidement, avec effort de gestion minimal → modèle *utility computing* (comme l'électricité).

## 5 caractéristiques essentielles
1. **Libre-service à la demande** — provisionner sans intervention humaine
2. **Accès réseau large** — accessible partout via une URL
3. **Mutualisation des ressources** — infrastructure multi-locataire
4. **Élasticité rapide** — scale up/down (vertical), scale out/in (horizontal)
5. **Service mesuré** — facturation à l'usage (pay-as-you-go)

## Modèles de service (analogie pizza)
| Modèle | Vous gérez | Fournisseur gère | Exemple |
|---|---|---|---|
| **IaaS** | OS, runtime, app, données | Matériel, réseau | AWS EC2 |
| **PaaS** | App, données | OS, runtime, scaling | Heroku, EMR |
| **SaaS** | Données/préférences | Tout le reste | Gmail, Snowflake |
| **FaaS** | Code de la fonction | Tout, à la seconde | AWS Lambda |

## Modèles de déploiement
- **Public** : mutualisé chez un fournisseur (AWS, GCP, Azure)
- **Privé** : dédié à une organisation
- **Hybride** : mix (ex. Anfa : données sensibles en privé, analytique en public)
- **Multi-cloud** : plusieurs fournisseurs publics pour éviter la dépendance

## Open source = socle invisible du cloud
Les services managés (S3, EMR, Kinesis, EKS...) sont construits sur des briques open source (MinIO, Spark, Kafka, Kubernetes...). Maîtriser l'open source = maîtriser ce que le cloud vend ensuite empaqueté.

## Vendor lock-in
Dépendance qui empêche de changer de fournisseur sans coût/risque majeur (API propriétaires, formats fermés, frais de sortie).

**Stratégies de portabilité :**
1. Conteneurisation (Docker)
2. Infrastructure as Code (Terraform)
3. Standards ouverts (Kafka, Spark...)
4. Architecture découplée (interfaces standard)

## Stack du cours (100% open source, locale)
Docker/Compose · Kubernetes (Minikube) · Terraform · GitHub Actions · MinIO · Airflow · Kafka · MLflow · Spark · Prometheus/Grafana
## Difficultés rencontrées
Certaines des commandes sont en bash pour ceux qui sont sous powershell il doivent utiliser ` au lieu de \
## Exercices d'application

1.1 -> D , car un système est dit cloud quand il présente ces 5 caractéristiques : Libre service à la demande, accès réseau large, mutualisation des ressources, élasticité rapide, service mesuré 

1.2 -> C, Gmail est une application accessible directement via un navigateur web sans installation ni gestion de l'infrastructure par l'utilisateur ; le service est entièrement fourni et maintenu par Google.

1.3 -> D, Avec le modèle FaaS, une fonction est exécutée automatiquement à chaque événement (ici, l'arrivée d'une nouvelle position GPS) sans nécessiter de serveur dédié en fonctionnement continu.

1.4 -> C, Un cloud hybride, il permet de conserver les données sensibles et réglementées dans un environnement privé tout en utilisant les ressources élastiques du cloud public

1.5 -> B, Le vendor lock-in désigne une dépendance forte à un fournisseur qui rend une migration vers une autre solution difficile.

1.6 -> C


| Service | Modèle | Justification |
|---|---|---|
| Google Compute Engine (machine virtuelle) | IaaS | Fournit une infrastructure brute (VM, stockage, réseau) ; l'utilisateur gère l'OS et les applications. |
| AWS Lambda | FaaS | Exécute du code à la demande, déclenché par événements, sans gestion de serveur. |
| Snowflake (entrepôt de données) | SaaS | Service de base de données/analytics prêt à l'emploi, accessible via le web sans gestion d'infrastructure. |
| Heroku | PaaS | Plateforme de déploiement d'applications gérant l'infrastructure et le runtime, l'utilisateur ne fournit que le code. |
| Microsoft 365 (Word, Excel en ligne) | SaaS | Applications bureautiques complètes utilisables directement en ligne, sans installation ni gestion. |
| Databricks (Spark managé) | PaaS | Plateforme managée pour développer et exécuter des traitements Big Data (Spark), sans gérer les clusters sous-jacents. |
| Microsoft Azure Functions | FaaS | Service serverless exécutant des fonctions à la demande, facturé à l'exécution. |
| Tableau Online | SaaS | Outil de visualisation de données accessible directement via navigateur, prêt à l'emploi. |


## Exercice 3 : Lecture et interprétation

### 3.1 Commande `docker run`

```
docker run -d --name analyse-anfa -p 8888:8888 -v /home/koffi/notebooks:/notebooks \
  -e JUPYTER_TOKEN=anfa-token \
  jupyter/pyspark-notebook
```

| Option | Signification |
|---|---|
| `-d` | Mode détaché (*detached*) : le conteneur tourne en arrière-plan, le terminal reste libre. |
| `--name analyse-anfa` | Donne un nom explicite au conteneur (`analyse-anfa`) au lieu d'un nom aléatoire généré par Docker. |
| `-p 8888:8888` | Mappe le port 8888 de la machine hôte vers le port 8888 du conteneur (format `hôte:conteneur`). C'est ce qui permet d'accéder à Jupyter depuis le navigateur de l'hôte. |
| `-v /home/koffi/notebooks:/notebooks` | Monte (bind mount) le dossier `/home/koffi/notebooks` de l'hôte dans `/notebooks` à l'intérieur du conteneur. Les fichiers sont donc persistés sur l'hôte même si le conteneur est supprimé. |
| `-e JUPYTER_TOKEN=anfa-token` | Définit une variable d'environnement `JUPYTER_TOKEN` à la valeur `anfa-token`, utilisée par Jupyter comme jeton d'authentification pour se connecter à l'interface web. |
| `jupyter/pyspark-notebook` | Nom de l'image Docker utilisée pour créer le conteneur : une image préconfigurée avec Jupyter et PySpark. |

**Résumé :** Cette commande lance en arrière-plan un conteneur nommé `analyse-anfa` basé sur l'image `jupyter/pyspark-notebook`, accessible via le port 8888 de l'hôte et protégé par le token `anfa-token`. Le dossier local `/home/koffi/notebooks` est monté dans le conteneur afin que les notebooks créés ou modifiés soient conservés sur la machine hôte de façon persistante.

---

### 3.2 Lecture d'un `docker-compose.yml`

```yaml
services:
  minio:
    image: minio/minio:latest
    container_name: anfa-minio
    restart: always
    ports:
      - "9000:9000"
      - "9001:9001"
    environment:
      MINIO_ROOT_USER: anfa-admin
      MINIO_ROOT_PASSWORD: secret
    volumes:
      - minio-data:/data
    command: server /data --console-address ":9001"
volumes:
  minio-data:
```

**a. Adresses accessibles depuis le navigateur de l'hôte :**
- `http://localhost:9000` → API S3 de MinIO (accès programmatique, ex. `boto3`)
- `http://localhost:9001` → Console web d'administration MinIO

**b. Suppression du conteneur puis `docker compose up -d` :**

Non, les données ne sont **pas perdues**. Le volume `minio-data` est un **volume nommé Docker** (déclaré dans la section `volumes:` en bas du fichier), distinct du cycle de vie du conteneur. Quand on fait `docker rm anfa-minio`, seul le conteneur (la couche d'exécution) est supprimé ; le volume `minio-data`, lui, persiste sur le disque de l'hôte (géré par Docker, typiquement sous `/var/lib/docker/volumes/`). Au redémarrage avec `docker compose up -d`, un nouveau conteneur est créé et le même volume `minio-data` est réattaché à `/data`, donc les objets précédemment stockés dans MinIO sont toujours là.

si on avait fait `docker compose down -v` (avec `-v`), le volume aurait été supprimé et les données perdues. C'est `docker rm` seul (sans toucher au volume) qui préserve les données ici.

**c. Problème de sécurité à corriger pour la production :**

Le mot de passe root est écrit **en clair directement dans le fichier** (`MINIO_ROOT_PASSWORD: secret`), qui plus est trivial (`secret`). En production, il faudrait :
- Ne jamais committer un mot de passe en clair dans le fichier `docker-compose.yml` (versionné dans Git).
- Utiliser un fichier `.env` (ajouté au `.gitignore`) ou un gestionnaire de secrets (Docker Secrets, Vault, variables d'environnement injectées au déploiement).
- Choisir un mot de passe fort et unique, pas une valeur devinable comme `secret`.

*(Autres problèmes acceptables : ports 9000/9001 exposés publiquement sans restriction réseau/firewall, absence de TLS/HTTPS, pas de gestion fine des accès via des clés applicatives dédiées au lieu du compte root.)*

---

## Exercice 4 : Diagnostic

```python
import boto3
s3 = boto3.client(
    "s3",
    endpoint_url="http://localhost:9000",
    aws_access_key_id="anfa-admin",
    aws_secret_access_key="anfa-password-2026",
    region_name="us-east-1",
)
s3.upload_file("trajets.csv", "anfa-raw", "trajets.csv")
```

Erreur obtenue :
```
botocore.exceptions.ClientError: An error occurred (InvalidAccessKeyId) ...
```

**a. Cause précise de l'erreur :**

Le script utilise les identifiants **root/admin de la console MinIO** (`anfa-admin` / un mot de passe) pour s'authentifier via l'**API S3**. Or, l'énoncé précise que l'étudiant a créé une **clé applicative dédiée** via `mc` (le client MinIO en ligne de commande) — probablement quelque chose comme `anfa-app-key` / `anfa-app-secret-2026`. Le script utilise donc le mauvais couple d'identifiants : `anfa-admin` n'est pas une *Access Key* valide reconnue par l'API S3 (ce n'est pas le même espace d'identifiants que le compte root utilisé pour la console web).

**b. Correction du code :**

```python
import boto3
s3 = boto3.client(
    "s3",
    endpoint_url="http://localhost:9000",
    aws_access_key_id="anfa-app-key",        # clé applicative créée via mc
    aws_secret_access_key="anfa-app-secret-2026",  # secret applicatif correspondant
    region_name="us-east-1",
)
s3.upload_file("trajets.csv", "anfa-raw", "trajets.csv")
```

Il faut remplacer `aws_access_key_id` et `aws_secret_access_key` par les identifiants de la **clé applicative** créée précédemment dans le TP (et non par les identifiants root).

**c. Pourquoi le compte root fonctionne pour la console web mais pas ici :**

`MINIO_ROOT_USER` / `MINIO_ROOT_PASSWORD` sont les **identifiants super-administrateur** de l'instance MinIO : ils donnent un accès total et sont conçus pour l'administration globale (console web, création d'utilisateurs, de buckets, de politiques). Par bonne pratique de sécurité, MinIO encourage à créer des **clés d'accès applicatives** (via `mc admin user add` ou `mc admin accesskey create`), limitées en portée (un bucket, des droits précis), à utiliser dans le code des applications. Le compte root peut se connecter à la console web (qui accepte les identifiants root), mais il est recommandé — voire dans certaines configurations requis — d'utiliser des clés applicatives dédiées pour les appels API S3 programmatiques, afin de limiter les risques en cas de fuite d'identifiants dans le code applicatif.

---

## Exercice 5 : Mini-cas d'architecture (PME e-commerce alimentaire, Lomé)

### a. Deux limites de l'architecture actuelle (PC du data scientist + CSV mensuel)

1. **Absence de temps réel / fraîcheur des données** : un export CSV mensuel ne permet pas de produire des prédictions « quasi temps réel, chaque heure » — il y a un décalage structurel d'un mois entre la réalité et les données disponibles.
2. **Single Point of Failure (SPOF) et absence de partage** : tout repose sur l'ordinateur portable de Toyi. S'il est éteint, en panne ou indisponible, plus personne ne peut accéder aux modèles ni aux résultats ; et aucun autre analyste ne peut consulter un tableau de bord, puisque rien n'est centralisé ni accessible à distance.

*(Autre limite acceptable : pas de scalabilité — un PC ne peut pas absorber un pic de charge le vendredi soir.)*

### b. Besoins de la direction ↔ caractéristiques du cloud (NIST)

| Besoin | Caractéristique NIST | Justification |
|---|---|---|
| Prédictions quasi temps réel chaque heure | **Libre-service à la demande** | Permet de déclencher/relancer des traitements automatiquement et fréquemment sans intervention manuelle du fournisseur. |
| Tableau de bord partagé, sans installation locale | **Accès réseau large** | Le dashboard est accessible via une URL HTTPS depuis n'importe quel terminal (PC, tablette, smartphone), sans rien installer. |
| Augmenter la capacité lors des pics (vendredi soir, fêtes) | **Élasticité rapide** | Le système peut automatiquement ajouter des ressources de calcul (scale out) pendant les pics, puis revenir à la normale ensuite. |
| Maîtriser les coûts | **Service mesuré (pay-as-you-go)** | La facturation à l'usage réel évite de payer pour une capacité surdimensionnée en permanence. |
| Conserver les données clients en environnement contrôlé | **Mutualisation des ressources** *(à nuancer)* | La mutualisation impose une isolation logique réfléchie ; c'est précisément ce qui motive ici de privilégier un déploiement privé pour les données sensibles plutôt qu'un public pur. |

### c. Modèle de service par composant

| Composant | Modèle proposé | Justification |
|---|---|---|
| (i) Tableau de bord partagé | **SaaS** | Une application complète de visualisation (type Metabase, Tableau Online) accessible par URL, sans gestion de serveur ni d'installation par les analystes. |
| (ii) Calcul des prédictions à l'heure | **FaaS** (ou PaaS) | Traitement déclenché par un événement périodique (toutes les heures), de courte durée : facturé seulement à l'exécution, pas de serveur à maintenir en permanence. PaaS est aussi défendable si le traitement est plus long qu'une simple fonction. |
| (iii) Stockage des données clients | **IaaS** ou **PaaS** (stockage objet auto-hébergé/privé) | Contrôle maximal nécessaire pour la conformité ; un stockage objet de type MinIO en environnement privé (proche de l'IaaS) permet de garder la maîtrise complète des données sensibles. |

### d. Modèle de déploiement recommandé

**Cloud hybride.** Les données clients (sensibles, contrainte de conformité) sont conservées dans un **cloud privé** afin de garantir un contrôle maximal et la conformité réglementaire, tandis que les traitements analytiques et le calcul des prédictions — qui demandent de l'élasticité lors des pics (vendredi soir, fêtes) — sont exécutés dans un **cloud public**, qui offre une élasticité excellente et un coût initial faible. Ce modèle combine ainsi confidentialité maximale et scalabilité à la demande.

### e. Trois stratégies pour limiter le vendor lock-in

1. **Conteneuriser les applications** (Docker) afin qu'elles puissent tourner à l'identique chez n'importe quel fournisseur cloud, sans réécriture.
2. **Privilégier les standards et l'open source** (ex. Apache Kafka plutôt qu'un service de messagerie propriétaire, Spark plutôt qu'un moteur fermé) pour rester indépendant d'une API propriétaire.
3. **Décrire l'infrastructure en code** (Terraform) afin de pouvoir changer de fournisseur en modifiant la configuration plutôt qu'en réécrivant tout le déploiement manuellement.
