# YouTube Video Transcriber & Analyzer - Product Requirements Document

## Document Control

- **Version:** 1.0.0
- **Date:** 2025-01-02
- **Status:** Initial Draft
- **Author:** AI Assistant
- **Project Owner:** Rolland Melet

---

## 1. Introduction

### Project Overview

Développement d'un workflow automatisé n8n pour traiter les vidéos YouTube partagées via Telegram, extraire automatiquement les transcriptions, métadonnées et liens externes, puis générer des notes structurées dans Obsidian via l'analyse IA.

### Business Objectives

- **Gain de temps** : Automatiser le traitement de 1-20 vidéos/jour
- **Capitalisation knowledge** : Transformer le contenu YouTube en notes référençables
- **ROI immédiat** : Solution opérationnelle sous 48h
- **Évolutivité** : Architecture extensible pour d'autres sources

### Target Audience

**Utilisateur principal** : CEO/Entrepreneur consommant beaucoup de contenu YouTube éducatif

- Niveau technique : Débutant IA, utilisateur avancé outils business
- Besoin : Synthétiser rapidement les enseignements des vidéos
- Contrainte : Budget optimisé, solution fiable

---

## 2. Core Features & Functionality (MVP)

### Feature 1: Déclenchement Telegram

**Description :** Réception d'URL YouTube via bot Telegram **User Story :** En tant qu'utilisateur, je partage une URL YouTube dans Telegram pour déclencher automatiquement l'analyse **Acceptance Criteria :**

- ✅ Bot Telegram reconnaît les URL YouTube (format court et long)
- ✅ Validation URL et extraction video_id
- ✅ Notification immédiate de début de traitement
- ✅ Gestion d'erreur pour URL invalides

### Feature 2: Extraction Métadonnées YouTube

**Description :** Récupération des informations video via YouTube Data API v3 **User Story :** En tant qu'utilisateur, je veux automatiquement récupérer titre, description, chaîne, durée et statistiques **Acceptance Criteria :**

- ✅ Utilisation YouTube Data API v3 (quota gratuit)
- ✅ Extraction : titre, description, chaîne, durée, vues, likes
- ✅ Gestion des vidéos privées/supprimées
- ✅ Formatage des durées (PT15M51S → 15:51)

### Feature 3: Transcription Vidéo

**Description :** Extraction du transcript via RapidAPI YouTube Transcript **User Story :** En tant qu'utilisateur, je veux obtenir la transcription complète avec timestamps **Acceptance Criteria :**

- ✅ Priorité RapidAPI YouTube Transcript (Phase 1)
- ✅ Fallback youtube-transcript-api local (Phase 3)
- ✅ Support multi-langues (auto-détection)
- ✅ Nettoyage automatique du texte (caractères spéciaux)
- ✅ Timestamps préservés pour références

### Feature 4: Analyse IA Multi-Provider

**Description :** Analyse intelligente du contenu avec comparaison de providers **User Story :** En tant qu'utilisateur, je veux une synthèse structurée optimisée coût/qualité **Acceptance Criteria :**

- ✅ Support OpenAI, Claude, Gemini, Mistral
- ✅ Template de prompt standardisé
- ✅ A/B testing automatique sur 10 premières vidéos
- ✅ Métriques : qualité, coût, vitesse
- ✅ Sélection automatique du meilleur provider

### Feature 5: Sauvegarde Obsidian Structurée

**Description :** Création automatique de notes formatées dans Obsidian **User Story :** En tant qu'utilisateur, je veux une note Obsidian prête à l'emploi avec tags et liens **Acceptance Criteria :**

- ✅ Template Markdown standardisé
- ✅ Synchronisation via Google Drive
- ✅ Tags automatiques intelligents
- ✅ Organisation dans dossiers appropriés
- ✅ Notification Telegram de fin de traitement

---

## 3. Recommended Development Solution

**Solution choisie :** Workflow Automation avec n8n + APIs spécialisées

**Justification :** Le projet est un workflow d'automation, pas une application traditionnelle. Les solutions de développement d'apps (Databutton, Firebase Studio, SaaS-Boilerplate) seraient surdimensionnées et inappropriées pour ce cas d'usage spécifique.

---

## 4. Technical Stack & Architecture

### Plateforme Principal

- **n8n Community 1.100.1** (self-hosted)
- **Architecture :** Event-driven workflow automation
- **Hébergement :** Instance privée utilisateur

### APIs et Services

1. **RapidAPI YouTube Transcript** (~$0.01-0.05/requête)
2. **YouTube Data API v3** (10k requêtes/jour gratuit)
3. **Provider IA :** OpenAI/Claude/Gemini/Mistral (tests comparatifs)
4. **Google Drive API** (synchronisation Obsidian)
5. **Telegram Bot API** (déclencheur et notifications)

### Données et Stockage

- **Cache local** n8n pour éviter retraitement
- **Google Drive** pour sync Obsidian
- **Métadonnées** stockées dans workflow n8n

---

## 5. MVP Definition & Phased Rollout

### Phase 1 - Core MVP (Semaine 1) 🎯

**Objectif :** Workflow fonctionnel end-to-end

1. **Telegram Bot Setup** (30 min)
2. **YouTube Metadata extraction** (45 min)
3. **RapidAPI Transcription** (1h)
4. **IA Analysis basique** (OpenAI uniquement) (1h)
5. **Obsidian via Google Drive** (45 min)

**Résultat :** URL Telegram → Note Obsidian en <5 min

### Phase 2 - Optimisations (Semaine 2) ⚡

**Objectif :** Fiabiliser et enrichir

6. **Extraction liens description/commentaires** (1h30)
7. **Comparatif multi-IA** (2h)
8. **Template Obsidian enrichi** (45 min)
9. **Gestion d'erreurs robuste** (1h)

### Phase 3 - Fonctionnalités Avancées (Semaine 3+) 🚀

**Si validation MVP et budget confirmé**

10. **youtube-transcript-api local + Synology proxy**
11. **Scraping liens externes** (Skool, Gumroad)
12. **Commentaires YouTube avec analyse sentiment**
13. **Cache intelligent et métriques**

---

## 6. Technical Implementation Guide

### 6.1 n8n Workflow Architecture

```
[Telegram Webhook] 
    ↓ 
[URL Validation & Video ID Extract]
    ↓
[PARALLEL BRANCHES]
├── [YouTube Data API] → [Metadata]
├── [RapidAPI Transcript] → [Transcript] 
└── [Comments API] → [Comments]
    ↓
[MERGE DATA]
    ↓
[IA Analysis] 
    ↓
[Format Obsidian Note]
    ↓
[Google Drive Upload]
    ↓
[Telegram Notification]
```

### 6.2 Telegram Bot Configuration

```javascript
// n8n Webhook Trigger Node
{
  "httpMethod": "POST", 
  "path": "youtube-transcriber",
  "responseMode": "responseNode",
  "authentication": "none"
}

// Telegram Bot Setup (via BotFather)
// 1. Create bot: /newbot
// 2. Set webhook: https://your-n8n.domain/webhook/youtube-transcriber
// 3. Commands: /start, /help
```

### 6.3 YouTube Data API v3 Implementation

```javascript
// n8n HTTP Request Node Configuration
{
  "method": "GET",
  "url": "https://www.googleapis.com/youtube/v3/videos",
  "sendQuery": true,
  "queryParameters": {
    "id": "{{ $json.video_id }}",
    "key": "{{ $credentials.youtube_api_key }}",
    "part": "snippet,contentDetails,statistics"
  },
  "sendHeaders": true,
  "headerParameters": {
    "Accept": "application/json"
  }
}

// Response Processing
const response = $input.first().json;
const video = response.items[0];

return {
  id: video.id,
  title: video.snippet.title,
  description: video.snippet.description,
  channelTitle: video.snippet.channelTitle,
  duration: video.contentDetails.duration, // PT15M51S format
  viewCount: video.statistics.viewCount,
  likeCount: video.statistics.likeCount,
  publishedAt: video.snippet.publishedAt
};
```

### 6.4 RapidAPI YouTube Transcript Integration

```javascript
// n8n HTTP Request Node pour RapidAPI
{
  "method": "POST",
  "url": "https://youtube-transcript1.p.rapidapi.com/transcript",
  "sendHeaders": true,
  "headerParameters": {
    "X-RapidAPI-Key": "{{ $credentials.rapidapi_key }}",
    "X-RapidAPI-Host": "youtube-transcript1.p.rapidapi.com",
    "Content-Type": "application/json"
  },
  "sendBody": true,
  "bodyContentType": "json",
  "jsonBody": {
    "url": "{{ $json.youtube_url }}"
  }
}

// Cleaning Function (n8n Code Node)
function cleanTranscript(transcript) {
  return transcript
    .map(item => ({
      text: item.text
        .replace(/&amp;/g, '&')
        .replace(/&lt;/g, '<')
        .replace(/&gt;/g, '>')
        .replace(/&quot;/g, '"')
        .replace(/&#39;/g, "'")
        .trim(),
      start: item.start,
      duration: item.duration
    }))
    .filter(item => item.text.length > 0);
}

return { transcript: cleanTranscript($input.first().json) };
```

### 6.5 Multi-IA Analysis Implementation

```javascript
// n8n Switch Node pour A/B Testing
const providers = ['openai', 'claude', 'gemini', 'mistral'];
const selectedProvider = providers[Math.floor(Math.random() * providers.length)];

// OpenAI Node Configuration
{
  "resource": "text",
  "operation": "message",
  "model": "gpt-4o-mini", // Economique pour tests
  "messages": [
    {
      "role": "system",
      "content": `Tu es un expert en synthèse de contenu. Analyse cette transcription YouTube et génère:
1. Résumé exécutif (max 100 mots)
2. 3-5 points clés principaux
3. Concepts/outils mentionnés
4. 3 tags pertinents
5. Enseignements actionnables

Format: JSON structuré selon template fourni.`
    },
    {
      "role": "user", 
      "content": "Titre: {{ $('YouTube Metadata').first().json.title }}\n\nTranscript: {{ $('Transcript').first().json.transcript_text }}"
    }
  ]
}

// Template de prompt standardisé
const ANALYSIS_PROMPT = `
Analyse cette vidéo YouTube et génère une synthèse structurée:

VIDÉO:
- Titre: {{title}}
- Chaîne: {{channel}}
- Durée: {{duration}}

TRANSCRIPT:
{{transcript}}

GÉNÉRER (FORMAT JSON):
{
  "resume_executif": "Synthèse en 100 mots max",
  "points_cles": ["Point 1", "Point 2", "Point 3"],
  "concepts_outils": ["Outil 1", "Concept 2"],
  "tags_sugeres": ["tag1", "tag2", "tag3"],
  "enseignements": ["Action 1", "Action 2"],
  "niveau_difficulte": "Débutant|Intermédiaire|Avancé",
  "domaines": ["Marketing", "Tech", "Business"]
}
`;
```

### 6.6 Google Drive & Obsidian Integration

```javascript
// Google Drive Upload Node
{
  "resource": "file",
  "operation": "upload",
  "name": "{{ $json.video_title }}_{{ $json.date }}.md",
  "folderId": "{{ $credentials.obsidian_folder_id }}",
  "content": "{{ $json.formatted_note }}",
  "mimeType": "text/markdown"
}

// Note Formatting Function
function formatObsidianNote(data) {
  const template = `# ${data.title}

## 📋 Métadonnées
- **URL** : ${data.url}
- **Chaîne** : ${data.channel}
- **Durée** : ${data.duration}
- **Date publication** : ${data.published_date}
- **Vues** : ${data.view_count}
- **Date d'analyse** : ${new Date().toISOString().split('T')[0]}

## 🎯 Synthèse IA (30 sec max)
${data.analysis.resume_executif}

## 🔑 Points clés
${data.analysis.points_cles.map(point => `- ${point}`).join('\n')}

## 💡 Enseignements principaux
### Concepts
${data.analysis.concepts_outils.join(', ')}

### Actions possibles
${data.analysis.enseignements.map(action => `- ${action}`).join('\n')}

## 🌐 Ressources externes
${data.external_links ? data.external_links.join('\n') : '*Aucun lien détecté*'}

## 🏷️ Tags suggérés
${data.analysis.tags_sugeres.map(tag => `#${tag}`).join(' ')}

## 📝 Notes personnelles
*[Section à remplir manuellement]*

---
*Généré automatiquement le ${new Date().toISOString().split('T')[0]} via workflow n8n*`;

  return template;
}
```

---

## 7. Conceptual Data Model

### Entités principales

**VideoMetadata**

```javascript
{
  video_id: "string",
  title: "string", 
  description: "text",
  channel_name: "string",
  duration_seconds: "number",
  view_count: "number",
  like_count: "number",
  published_at: "datetime",
  url: "string"
}
```

**Transcript**

```javascript
{
  video_id: "string",
  segments: [
    {
      text: "string",
      start_time: "number", 
      duration: "number"
    }
  ],
  language: "string",
  source: "rapidapi|local|youtube-api"
}
```

**AIAnalysis**

```javascript
{
  video_id: "string",
  provider: "openai|claude|gemini|mistral",
  resume_executif: "string",
  points_cles: ["string"],
  concepts_outils: ["string"],
  tags_sugeres: ["string"],
  enseignements: ["string"],
  processing_time: "number",
  cost_estimate: "number",
  quality_score: "number"
}
```

**ObsidianNote**

```javascript
{
  video_id: "string",
  file_path: "string",
  created_at: "datetime", 
  google_drive_id: "string",
  tags: ["string"],
  word_count: "number"
}
```

---

## 8. UI/UX Principles

### Interface Telegram

- **Simplicité** : Partage URL → Notification résultat
- **Feedback temps réel** : Messages de progression
- **Gestion d'erreurs** : Messages clairs et actions correctives

### Template Obsidian

- **Lisibilité** : Structure hiérarchique claire
- **Interactivité** : Liens internes et tags cliquables
- **Extensibilité** : Section notes personnelles

### Workflow n8n

- **Robustesse** : Gestion d'erreurs à chaque étape
- **Observabilité** : Logs détaillés pour debugging
- **Performance** : Traitement parallèle optimisé

---

## 9. Security & Compliance Considerations

### Authentication Strategy

- **API Keys** : Stockage sécurisé dans n8n credentials
- **Telegram Bot** : Token privé, webhook HTTPS uniquement
- **Google Drive** : OAuth2 avec scopes minimaux

### Data Privacy

- **Rétention** : Suppression cache transcript après 30 jours
- **Transit** : Chiffrement HTTPS toutes communications
- **Stockage** : Google Drive personnel, contrôle utilisateur

### Rate Limiting

- **YouTube API** : 10k requêtes/jour (monitoring quota)
- **RapidAPI** : Respect limites plan sélectionné
- **IA APIs** : Batching pour optimiser coûts

---

## 10. Key Integrations

### APIs Externes Requis

1. **YouTube Data API v3**
    
    - Endpoint: `https://www.googleapis.com/youtube/v3/videos`
    - Auth: API Key
    - Quota: 10,000 units/day gratuit
2. **RapidAPI YouTube Transcript**
    
    - Endpoint: `https://youtube-transcript1.p.rapidapi.com/transcript`
    - Auth: X-RapidAPI-Key header
    - Coût: ~$0.01-0.05 par requête
3. **OpenAI API** (provider principal)
    
    - Model: gpt-4o-mini (optimisé coût/performance)
    - Endpoint: `https://api.openai.com/v1/chat/completions`
    - Coût estimé: ~$0.01-0.03 par analyse
4. **Google Drive API v3**
    
    - Endpoint: `https://www.googleapis.com/drive/v3/files`
    - Auth: OAuth2
    - Scope: drive.file (accès fichiers créés)
5. **Telegram Bot API**
    
    - Endpoint: `https://api.telegram.org/bot{token}/`
    - Auth: Bot token
    - Webhook: Configuration via setWebhook

---

## 11. Test-Driven Development (TDD) Approach

### Stratégie de Test n8n

**Tests de Workflow**

- **Test unitaire par node** : Validation inputs/outputs
- **Tests d'intégration** : Workflow complet end-to-end
- **Tests de performance** : Temps traitement <5 min/vidéo

```javascript
// Exemple test YouTube API
{
  "test_name": "YouTube API Metadata Extraction",
  "input": {
    "video_id": "dQw4w9WgXcQ" // Rick Roll pour tests
  },
  "expected_output": {
    "title": "Rick Astley - Never Gonna Give You Up",
    "channelTitle": "RickAstleyVEVO",
    "status": "success"
  }
}

// Test RapidAPI Transcript
{
  "test_name": "RapidAPI Transcript Processing", 
  "input": {
    "url": "https://www.youtube.com/watch?v=dQw4w9WgXcQ"
  },
  "validation": {
    "transcript_length": ">100",
    "language_detected": "en",
    "segments_count": ">10"
  }
}
```

**Métriques Qualité**

- **Taux succès** : >95% sur 100 vidéos test
- **Temps moyen** : <5 minutes par vidéo
- **Coût moyen** : <$0.10 par analyse complète

---

## 12. Code Quality & Safety Guidelines

### Standards n8n Workflow

- **Nommage** : Conventions claires pour nodes
- **Documentation** : Annotations sur nodes complexes
- **Gestion d'erreurs** : Try/catch sur chaque API call
- **Timeouts** : Limites sur toutes requêtes HTTP

```javascript
// Template Error Handling
try {
  const response = await this.helpers.httpRequest(options);
  return { success: true, data: response };
} catch (error) {
  console.error(`API Error: ${error.message}`);
  return { 
    success: false, 
    error: error.message,
    retry_possible: error.status !== 404
  };
}
```

### Prévention Boucles Infinites

- **Pagination** : Limite max 1000 commentaires
- **Timeouts** : 30s max par requête API
- **Circuit breakers** : Arrêt après 3 échecs consécutifs

### Versioning Workflow

- **Export JSON** : Sauvegarde avant modifications
- **Git tracking** : Versions dans repository
- **Rollback plan** : Procédure retour version stable

---

## 13. Potential Challenges & Mitigation

### Défi 1: Rate Limiting APIs

**Risque** : Dépassement quotas YouTube/RapidAPI **Mitigation** :

- Monitoring quota en temps réel
- Queue requests avec délais
- Cache intelligent (pas de retraitement)

### Défi 2: Qualité Transcription Variable

**Risque** : Transcripts incomplets ou incorrects **Mitigation** :

- Validation longueur minimum transcript
- Fallback vers provider alternatif
- Indication qualité dans note finale

### Défi 3: Coûts IA Scaling

**Risque** : Explosion coûts si volume important **Mitigation** :

- Monitoring coût par provider
- Switch automatique vers provider moins cher
- Option résumé court vs détaillé

### Défi 4: Synchronisation Google Drive

**Risque** : Échec upload, conflits fichiers **Mitigation** :

- Nommage unique (timestamp + video_id)
- Retry logic avec backoff exponentiel
- Notification échec avec action manuelle

---

## 14. Future Considerations

### Phase 2 Enhancements

- **Commentaires YouTube** : Analyse sentiment
- **Liens externes** : Scraping automatique ressources
- **Multi-langues** : Support français, anglais, espagnol
- **Recherche sémantique** : Index vectoriel notes

### Phase 3 Advanced Features

- **youtube-transcript-api local** : Via proxy Synology
- **Batch processing** : Traitement playlists complètes
- **AI summarization** : Résumés multi-niveaux
- **Analytics dashboard** : Métriques consommation contenu

### Scaling Considerations

- **Performance** : Traitement parallèle multiple vidéos
- **Storage** : Archivage notes anciennes
- **Monitoring** : Alertes proactives pannes
- **Multi-utilisateurs** : Partage workflow famille/équipe

---

## 15. Initial Configuration & Setup Summary

### Prérequis Techniques

```bash
# n8n Self-hosted 1.100.1
docker run -it --rm --name n8n -p 5678:5678 -v ~/.n8n:/home/node/.n8n n8nio/n8n

# Variables d'environnement requises
YOUTUBE_API_KEY=AIza...              # Google Cloud Console
RAPIDAPI_KEY=abc123...              # RapidAPI Dashboard  
OPENAI_API_KEY=sk-...               # OpenAI Platform
TELEGRAM_BOT_TOKEN=123456:ABC...    # BotFather
GOOGLE_DRIVE_FOLDER_ID=1abc...      # Dossier Obsidian
```

### APIs Setup Checklist

- [ ] **Google Cloud Console** : Créer projet + activer YouTube Data API v3
- [ ] **RapidAPI Account** : Souscrire YouTube Transcript API
- [ ] **OpenAI Account** : Créer clé API + crédits
- [ ] **Telegram BotFather** : Créer bot + configurer webhook
- [ ] **Google Drive** : Autoriser accès dossier Obsidian

### Configuration n8n

1. **Import workflow** : `PRD-YouTube-Transcriber-v1.0.0.json`
2. **Credentials setup** : Configurer toutes les APIs
3. **Test workflow** : Vidéo de test YouTube courte
4. **Monitor logs** : Vérifier exécution sans erreurs
5. **Telegram setup** : Test bot avec URL exemple

### Obsidian Integration

```bash
# Sync Google Drive folder avec Obsidian Vault
# Chemin: /Users/rollandmelet/Développement/RoRworld/YouTube-Notes/
# Auto-sync: Activé
# Conflict resolution: Prefer Drive version
```

---

## Appendix A: Code Examples Repository

### A.1 Complete n8n Workflow JSON Structure

```json
{
  "name": "YouTube Transcriber & Analyzer v1.0.0",
  "nodes": [
    {
      "parameters": {
        "httpMethod": "POST",
        "path": "youtube-transcriber",
        "responseMode": "responseNode"
      },
      "id": "webhook-trigger",
      "name": "Telegram Webhook",
      "type": "n8n-nodes-base.webhook",
      "typeVersion": 1,
      "position": [100, 300]
    },
    {
      "parameters": {
        "jsCode": "// Extract video ID from URL\nconst url = $input.first().json.message.text;\nconst videoId = extractVideoId(url);\n\nfunction extractVideoId(url) {\n  const regex = /(?:youtube\\.com\\/(?:[^\\/]+\\/.+\\/|(?:v|e(?:mbed)?)\\/|.*[?&]v=)|youtu\\.be\\/)([^\"&?\\/\\s]{11})/;\n  const match = url.match(regex);\n  return match ? match[1] : null;\n}\n\nreturn { video_id: videoId, youtube_url: url };"
      },
      "id": "extract-video-id", 
      "name": "Extract Video ID",
      "type": "n8n-nodes-base.code",
      "typeVersion": 2,
      "position": [300, 300]
    }
  ],
  "connections": {
    "Telegram Webhook": {
      "main": [
        [
          {
            "node": "Extract Video ID",
            "type": "main",
            "index": 0
          }
        ]
      ]
    }
  }
}
```

### A.2 Error Handling Template

```javascript
// n8n Error Handling Node Template
try {
  const result = await processYouTubeVideo($input.first().json);
  
  return {
    success: true,
    data: result,
    timestamp: new Date().toISOString(),
    processing_time: Date.now() - startTime
  };
  
} catch (error) {
  // Log error for debugging
  console.error('YouTube Processing Error:', {
    error: error.message,
    stack: error.stack,
    input: $input.first().json,
    timestamp: new Date().toISOString()
  });
  
  // Send error notification to Telegram
  await sendTelegramMessage({
    chat_id: $input.first().json.message.chat.id,
    text: `❌ Erreur lors du traitement de la vidéo: ${error.message}\n\nVeuillez réessayer ou contacter le support.`
  });
  
  return {
    success: false,
    error: error.message,
    retry_recommended: !error.message.includes('not found'),
    timestamp: new Date().toISOString()
  };
}
```

### A.3 Monitoring & Analytics

```javascript
// Performance Monitoring Function
function trackAnalytics(operation, duration, success, cost = 0) {
  const metrics = {
    operation: operation,
    duration_ms: duration,
    success: success,
    cost_usd: cost,
    timestamp: new Date().toISOString(),
    date: new Date().toISOString().split('T')[0]
  };
  
  // Log to n8n for monitoring
  console.log('ANALYTICS:', JSON.stringify(metrics));
  
  // Optional: Send to analytics service
  // await sendToAnalytics(metrics);
  
  return metrics;
}

// Usage dans workflow
const startTime = Date.now();
const result = await processVideo();
const duration = Date.now() - startTime;

trackAnalytics('video_processing', duration, result.success, 0.05);
```

---

_Document généré automatiquement - Version 1.0.0 - 2025-01-02_