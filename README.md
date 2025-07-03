# Analyseur de Vidéos YouTube avec n8n et IA

![Status](https://img.shields.io/badge/status-en%20d%C3%A9veloppement-yellow)
![Version](https://img.shields.io/badge/version-1.0.0-blue)
![License](https://img.shields.io/badge/license-MIT-green)

Ce projet est un workflow d'automatisation n8n conçu pour transformer des URLs de vidéos YouTube en notes structurées et intelligentes dans [Obsidian](https://obsidian.md/). L'objectif est de capitaliser sur le contenu éducatif consommé en automatisant l'extraction, la transcription, l'analyse par IA et la sauvegarde dans une base de connaissances personnelle.

## ✨ Fonctionnalités Clés

-   **🤖 Déclenchement via Telegram :** Partagez simplement une URL YouTube à un bot Telegram pour démarrer le processus.
-   **📊 Extraction Complète :** Récupère automatiquement les métadonnées de la vidéo (titre, chaîne, durée, stats) et sa transcription complète.
-   **🧠 Analyse par IA :** Utilise des modèles de langage (GPT-4o, Gemini, Claude, etc.) pour générer une synthèse, des points clés, des concepts, des tags et des enseignements actionnables.
-   **📄 Formatage pour Obsidian :** Crée une note Markdown parfaitement structurée, prête à être intégrée dans votre "Second Cerveau".
-   **☁️ Synchronisation Cloud :** Sauvegarde automatiquement la note finale dans un dossier Google Drive synchronisé avec votre coffre Obsidian.
-   **🔧 Robustesse et Monitoring :** Inclut une gestion des erreurs avec notifications et un plan de monitoring des coûts et des performances des APIs.

## 🚀 Schéma du Workflow (MVP)

Le flux de travail est simple et puissant, entièrement orchestré par n8n :

`[Partage URL sur Telegram] → [🤖 Bot Telegram] → [⚙️ Workflow n8n] → [📡 APIs (YouTube, Transcript, IA)] → [💾 Google Drive] → [📄 Note dans Obsidian]`

## 🛠️ Stack Technique

Ce projet s'appuie sur une stack moderne et spécialisée dans l'automatisation :

| Catégorie | Outil/Service | Rôle |
| :--- | :--- | :--- |
| **Automation** | [n8n](https://n8n.io/) (auto-hébergé) | Cœur de l'orchestration du workflow. |
| **Interface** | [Telegram Bot](https://core.telegram.org/bots) | Point d'entrée pour l'utilisateur. |
| **Transcription** | [RapidAPI - YouTube Transcript](https://rapidapi.com/thisisgazzar/api/youtube-transcript1) | Extraction du texte de la vidéo. |
| **Métadonnées** | [YouTube Data API v3](https://developers.google.com/youtube/v3/docs) | Récupération des informations de la vidéo. |
| **Analyse IA** | [OpenAI](https://openai.com/) / [Google Gemini](https://ai.google.dev/) / [Anthropic Claude](https://www.anthropic.com/) | Synthèse et structuration du contenu. |
| **Stockage** | [Google Drive](https://www.google.com/drive/) | Dépôt des notes pour la synchronisation. |
| **Base de Connaissances** | [Obsidian.md](https://obsidian.md/) | Application de prise de notes finale. |
| **Développement** | [VS Code](https://code.visualstudio.com/) + RooCode | Environnement de développement assisté par IA. |

## 📁 Structure du Projet

Ce dépôt contient principalement la documentation et la planification du projet.

-   `PRD.md`: **Product Requirements Document** - Définit les objectifs, les fonctionnalités et les spécifications du produit.
-   `PLAN.md`: **Plan d'Implémentation** - Planification technique détaillée des différentes phases du projet (MVP, Optimisations, Avancé).
-   `TASKS.md`: **Liste des Tâches** - Checklist opérationnelle et détaillée pour la mise en place du MVP, avec commandes et instructions.
-   `Note template.md`: **Modèle de Note** - La structure finale du fichier Markdown qui sera généré pour chaque vidéo analysée.
-   `RULES n8n Cline RooCode VS-CODE.md`: **Règles pour l'IA** - Définit le contexte et les règles pour l'assistant IA utilisé lors du développement.

## ⚙️ Guide d'Installation et de Configuration (MVP)

Pour mettre en place ce projet, suivez les étapes détaillées dans le fichier `TASKS.md`. Voici un résumé des grandes lignes :

1.  **Prérequis :** Assurez-vous d'avoir une instance n8n fonctionnelle (v1.100.1+), l'application Google Drive installée et un compte Obsidian.

2.  **Création des Comptes et Clés API :**
    -   Obtenez une clé API pour **YouTube Data API v3** sur la Google Cloud Console.
    -   Abonnez-vous à l'API **YouTube Transcript** sur RapidAPI pour obtenir une clé.
    -   Créez une clé API sur la plateforme **OpenAI** (ou le fournisseur IA de votre choix).
    -   Créez un **Bot Telegram** avec `@BotFather` pour obtenir un token.

3.  **Configuration du Webhook :**
    -   Exposez votre instance n8n sur internet (par exemple avec `ngrok` pour les tests locaux).
    -   Configurez le webhook de votre bot Telegram pour qu'il pointe vers votre URL de webhook n8n.

4.  **Configuration des Credentials n8n :**
    -   Dans votre interface n8n, ajoutez les *Credentials* pour chaque service (YouTube, RapidAPI, OpenAI, Telegram, Google Drive) en utilisant les clés et tokens obtenus.

5.  **Déploiement du Workflow :**
    -   Importez le fichier JSON du workflow n8n (une fois développé) dans votre instance.
    -   Activez le workflow.

## Usage

Une fois le workflow configuré et actif, l'utilisation est extrêmement simple :

1.  Ouvrez une conversation avec votre bot sur Telegram.
2.  Envoyez-lui l'URL complète d'une vidéo YouTube.
3.  Le bot confirmera la prise en charge.
4.  Après quelques minutes, vous recevrez une notification de succès et la note apparaîtra automatiquement dans votre dossier Obsidian.

## 🗺️ Roadmap

Le projet est structuré en plusieurs phases, comme détaillé dans le `PLAN.md` :

-   [x] **Phase 1 : Core MVP** - Workflow fonctionnel de bout en bout.
-   [ ] **Phase 2 : Optimisations** - Enrichissement avec extraction de liens, A/B testing des IA et amélioration du template.
-   [ ] **Phase 3 : Fonctionnalités Avancées** - Transcription locale, analyse des commentaires et scraping de contenu externe.

## 🤝 Contribution

Ce projet est personnel, mais les suggestions et idées d'amélioration sont les bienvenues. N'hésitez pas à ouvrir une *Issue* pour discuter d'une fonctionnalité ou signaler un problème.

## 📄 Licence

Ce projet est sous licence MIT. Voir le fichier `LICENSE` pour plus de détails.