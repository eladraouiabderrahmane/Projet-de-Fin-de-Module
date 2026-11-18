# Fiche de suivi des projets agents IA

Master • Présentation de l'avancement et plan de correction

| | |
|---|---|
| **Groupe** | [3] |
| **Projet n°** | [n°43] |
| **Date** | [18/11/2026] |
| **Intitulé du projet** | Système multi-agents de support client e-commerce |
| **Membres** | El Adraoui Abderrahmane, Koukham Anas, Azridou Mosàb |
| **Jalon examiné** | Cadrage et choix techniques (semaine 1) |
| **Prochain point** | [date] – Socle technique et premier agent |

---

## Bilan et périmètre

### 1. Quel problème métier traitez-vous, pour quel utilisateur et avec quel résultat attendu ?

**Problème :** les services client e-commerce reçoivent un grand volume de demandes répétitives (suivi de commande, retards de livraison, retours, remboursements, réclamations). Cela allonge les délais de réponse et surcharge les agents humains.

**Utilisateurs :**
- le **client final**, qui pose ses questions dans un chat ;
- l'**agent de support humain**, qui traite les escalades et valide les remboursements depuis un tableau de bord.

**Cas d'usage précis :** un client demande « Où est ma commande et pourquoi est-elle en retard ? ». L'agent Triage identifie l'intention. L'agent Commande interroge la base, vérifie le statut et compare la date réelle à la date de livraison estimée. Il répond avec des informations vérifiables. Si le client demande un remboursement, la demande est préparée puis validée par un humain.

**Critères de réussite mesurables** (objectifs, mesurés sur un jeu de test construit à partir des datasets) :

| Critère | Objectif |
|---|---|
| Précision de classification des intentions | ≥ 85 % |
| Taux de résolution automatique des demandes simples | ≥ 60 % |
| Fidélité des réponses (Ragas *faithfulness*) | ≥ 0,85 |
| Temps de réponse p95 | < 8 s |
| Remboursements validés par un humain | 100 % |

### 2. Quel périmètre MVP avez-vous retenu et quelles fonctions avez-vous reportées ?

**Inclus dans le MVP :**
- chat client (Next.js) ;
- agent Triage/Superviseur (intention, urgence, sentiment) ;
- agent Commande (statut, retard) ;
- agent Retours/Remboursement avec validation humaine ;
- agent FAQ (RAG sur les règles de retour et de livraison) ;
- agent Escalade avec création de ticket ;
- tableau de bord de l'agent humain ;
- traçage avec Langfuse, évaluation avec Ragas et pytest ;
- déploiement Docker.

**Reportés :**
- connecteur Odoo (prévu grâce à une couche d'adaptateurs, réalisé après le MVP) ;
- canaux email et WhatsApp ;
- voix ;
- darija et arabe ;
- paiement réel ;
- authentification avancée (SSO).

### 3. Qu'aviez-vous prévu depuis le dernier point et qu'avez-vous réellement terminé ?

Premier point, donc pas de point précédent.

| État | Éléments | Preuve |
|---|---|---|
| **Terminé** | Choix de l'option « plateforme from scratch avec dataset réel » ; choix de la stack ; architecture multi-agents cible ; organisation du travail (découpage par fonctionnalité, rôles transverses en rotation) ; création du dépôt GitHub | Cette fiche, [`docs/architecture.md`](../architecture.md), le dépôt |
| **En cours** | Analyse exploratoire des datasets ; `docker-compose` du socle technique | — |
| **Prévu** | Stratégie de branches et pull requests (mise en place au démarrage du développement) | — |
| **Bloqué** | Aucun blocage | — |

### 4. Que peut-on exécuter aujourd'hui sans intervention manuelle cachée ?

Rien n'est encore exécutable de bout en bout, ce qui est normal au jalon de cadrage.

**Objectif pour le prochain point :** `docker compose up` lance PostgreSQL, Redis, Langfuse et l'API. Les données Olist sont importées par script. On montrera un scénario reproductible : « suivi de la commande n° X », traité par Triage puis par l'agent Commande, avec la trace visible dans Langfuse.

**État général :** conforme au jalon

**Décision ou aide attendue de l'enseignant :**
- validation de l'option « from scratch » et des datasets retenus (voir la question 5) ;
- validation du périmètre MVP ;
- accès éventuel à une API de LLM ou à un budget de crédits.

---

## Données et architecture

### 5. Quel dataset ou corpus utilisez-vous exactement et comment y accède-t-on ?

| Dataset | URL | Licence | Fichiers retenus | Usage |
|---|---|---|---|---|
| **Brazilian E-Commerce Public Dataset by Olist** (environ 100 000 commandes réelles, 2016–2018) | https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce | CC BY-NC-SA 4.0 | `olist_orders`, `olist_order_items`, `olist_customers`, `olist_order_payments`, `olist_order_reviews`, `olist_products`, `olist_sellers`, `product_category_name_translation` (CSV) | Base transactionnelle réelle : commandes, livraisons, avis clients |
| **ABCD – Action-Based Conversations Dataset** (ASAPP, NAACL 2021, environ 10 000 dialogues de support humains) | https://github.com/asappresearch/abcd | MIT | `abcd_v1.1.json`, `kb.json` (règles), `ontology.json` | Intentions, règles métier, jeu d'évaluation |
| **Customer Support on Twitter** (environ 3 millions de tweets réels clients/marques) | https://www.kaggle.com/datasets/thoughtvector/customer-support-on-twitter | CC BY-NC-SA 4.0 | `twcs.csv` (échantillon filtré sur les marques e-commerce) | Ton réel des réclamations, tests urgence et sentiment |


**Accès :** téléchargement depuis Kaggle et GitHub, puis import dans PostgreSQL par un script (prévu en semaine 2). Les données brutes ne sont pas versionnées dans le dépôt.

**Limites identifiées :**
- les avis Olist sont en portugais : ils seront traduits pendant le prétraitement ;
- les produits Olist n'ont que leur catégorie, sans nom ;
- les licences NC interdisent un usage commercial : l'usage académique est conforme, et la version commerciale passerait par le connecteur Odoo branché sur les données d'un client.

L'architecture détaillée est décrite dans [`docs/architecture.md`](../architecture.md).
