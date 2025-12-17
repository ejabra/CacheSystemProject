<div align="center">

# ⚡ Système de Cache Distribué en Temps Réel

**Orchestration de données événementielle via Kafka, NiFi, Redis et MongoDB.**

_Une architecture découplée conçue pour résoudre les problèmes de latence des applications monolithiques grâce au traitement asynchrone et au cache haute performance._

<br>

<img src="docs/architecture.webp" alt="Architecture Globale du Projet" width="800" style="border-radius: 10px; box-shadow: 0 4px 8px 0 rgba(0,0,0,0.2);">

<br>

[⬇️ Voir la Présentation Complète (PDF)](docs/presentation.pdf) · [🐛 Signaler un Bug](../../issues)

</div>

---

## 🚀 Problématique & Solution

Les applications monolithiques traditionnelles souffrent de **lenteurs** lorsqu'elles interrogent directement des bases de données volumineuses, impactant l'expérience utilisateur.

**Notre solution :** Une architecture **EDA (Event-Driven Architecture)** qui sépare les opérations d'écriture et de lecture :
* 🚀 **Cache Redis** : Pour un accès aux données en $O(1)$ (quelques millisecondes).
* 🗄️ **MongoDB (Replica Set)** : Pour l'historisation et la réactivité via les *Change Streams*.
* 🔄 **Mise à jour Temps Réel** : L'interface utilisateur se met à jour instantanément sans rechargement.

---

## 🛠️ Architecture Technique (Workflow)

Le projet suit un pipeline de données en 5 phases distinctes :

### Phase 1 : Le Producteur (Laravel & Python)
* **Déclencheur :** Un `Observer` Laravel détecte la création d'une commande.
* **Action :** Exécution d'un script Python qui pousse les données (ID Client, ID Commande) dans un topic **Kafka**.

### Phase 2 : Orchestration (Apache NiFi)
* **Ingestion :** NiFi consomme les messages du topic Kafka.
* **Routing :** Le flux est divisé en deux branches : une vers le cache (Redis), l'autre vers l'historique (MongoDB).

### Phase 3 : Transformation (JOLT)
* Utilisation de processeurs **JoltTransformJSON** pour nettoyer les données.
* Stratégie **Shift & Remove** pour ne garder que les champs pertinents et optimiser l'empreinte mémoire.

### Phase 4 : Stockage Polyglotte
* **Redis :** Stockage Clé-Valeur pour les requêtes fréquentes (Clé : `customerNumber`).
* **MongoDB :** Stockage persistant configuré en **Replica Set (rs0)** pour activer les fonctionnalités temps réel.

### Phase 5 : Diffusion Temps Réel (The Watcher)
* Un "Watcher" Laravel écoute les **Change Streams** de MongoDB.
* Diffusion de l'événement `NewOrder` vers le frontend via **Pusher (WebSockets)** pour une mise à jour immédiate du Dashboard manager.

---

## 📸 Aperçu du Pipeline NiFi

<div align="center">
  <img src="docs/Kafka.png" alt="Flux Apache NiFi" width="700">
  <p><i>Transformation et routage des données dans Apache NiFi -1- Kafka</i></p>
</div>
<div align="center">
  <img src="docs/Mongodb.png" alt="Flux Apache NiFi" width="700">
  <p><i>Transformation et routage des données dans Apache NiFi -2- Mongodb</i></p>
</div>
<div align="center">
  <img src="docs/Redis.png" alt="Flux Apache NiFi" width="700">
  <p><i>Transformation et routage des données dans Apache NiFi -3- Redis</i></p>
</div>

---

## 💻 Stack Technologique

Le projet repose sur un cluster de conteneurs orchestré par **Docker Compose**.

| Catégorie | Technologies |
| :--- | :--- |
| **Backend App** | Laravel (PHP), Python Scripting |
| **Message Broker** | Apache Kafka, Zookeeper |
| **ETL / Orchestration** | Apache NiFi |
| **Bases de Données** | MySQL (Transactionnel), MongoDB (Historique), Redis (Cache) |
| **Temps Réel** | MongoDB Change Streams, Pusher, WebSockets |
| **Infrastructure** | Docker, Docker Compose |

---

## 👥 L'Équipe

Ce projet a été réalisé par :

* **Brahim DARGUI**
* **Mourad Benamre**
* **Mohamed Machlou**
* **Mounir Jaouhari**

---

## 📄 Documentation

Pour comprendre les choix architecturaux en détail, notamment la configuration des transformations JOLT et du Replica Set MongoDB :

> **👉 [Consultez le rapport de présentation (PDF)](docs/presentation.pdf)**

---
<div align="center">
  <small>Projet Académique - Data Engineering & Architecture Distribuée</small>
</div>
