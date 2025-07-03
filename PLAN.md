# PLAN D'IMPLÉMENTATION - YouTube Video Transcriber & Analyzer

## Document Control

- **Version:** 1.0.0
- **Date:** 2025-01-02
- **Projet:** YouTube Transcriber Automation
- **Référence PRD:** PRD-YouTube-Transcriber-v1.0.0

---

## 🎯 **Vue Générale du Projet**

### Objectif

Implémenter un workflow n8n automatisé permettant de transformer les URLs YouTube partagées via Telegram en notes Obsidian structurées avec analyse IA.

### Timeline Global

- **Phase 1 (MVP)** : 3-5 jours - Workflow fonctionnel de bout en bout
- **Phase 2 (Optimisations)** : 5-7 jours - Enrichissement et fiabilisation
- **Phase 3 (Avancé)** : 2-3 semaines - Fonctionnalités avancées

### Budget Estimé Phase 1

- **APIs** : ~$25/mois (RapidAPI + IA)
- **Temps développement** : 8-12 heures
- **ROI** : Opérationnel sous 48h

---

## 📋 **PHASE 1 - MVP CORE (3-5 jours)**

### **ÉTAPE 1.1 : Setup Environnement (Jour 1 - 2h)**

#### 1.1.1 Vérification Infrastructure

```bash
# Vérifier n8n version
docker exec n8n n8n --version
# Doit être >= 1.100.1

# Vérifier accès n8n interface
curl -I http://localhost:5678
```

#### 1.1.2 Création Comptes APIs

- [ ] **Google Cloud Console**
    
    - Créer nouveau projet "YouTube Transcriber"
    - Activer YouTube Data API v3
    - Générer clé API
    - Noter quota : 10,000 units/day
- [ ] **RapidAPI Account**
    
    - S'inscrire sur rapidapi.com
    - Souscrire "YouTube Transcript API" (thisisgazzar)
    - Plan recommandé : Basic (~$10/mois)
    - Copier X-RapidAPI-Key
- [ ] **OpenAI Platform**
    
    - Compte OpenAI existant
    - Vérifier crédits disponibles ($5 minimum)
    - Créer nouvelle clé API dédiée

#### 1.1.3 Configuration Google Drive

```bash
# Créer dossier dédié
mkdir -p "/Users/rollandmelet/Développement/RoRworld/YouTube-Notes"

# Sync avec Google Drive (via Drive app)
# Dossier cible : /RoRworld/YouTube-Notes/
```

**✅ Validation Étape 1.1 :** Toutes les APIs accessibles via test cURL

---

### **ÉTAPE 1.2 : Telegram Bot Setup (Jour 1 - 1h)**

#### 1.2.1 Création Bot Telegram

```bash
# Via BotFather (@BotFather)
/newbot
# Nom : YouTube Transcriber Bot
# Username : @RoRworldTranscriberBot (ou similaire)
# Sauvegarder TOKEN
```

#### 1.2.2 Configuration Webhook

```bash
# URL webhook vers n8n
https://your-n8n-domain/webhook/youtube-transcriber

# Test webhook
curl -X POST "https://api.telegram.org/bot<YOUR_BOT_TOKEN>/setWebhook" \
  -H "Content-Type: application/json" \
  -d '{"url": "https://your-n8n-domain/webhook/youtube-transcriber"}'
```

#### 1.2.3 Test Bot

- [ ] Envoyer /start au bot
- [ ] Tester réception webhook dans n8n
- [ ] Valider parsing message

**✅ Validation Étape 1.2 :** Bot répond et webhook fonctionne

---

### **ÉTAPE 1.3 : n8n Credentials Configuration (Jour 1 - 30min)**

#### 1.3.1 Ajouter Credentials n8n

```javascript
// Dans n8n > Credentials > Add credential

1. YouTube API Key
   Type: "HTTP Request"
   Name: "YouTube Data API"
   Key: [YOUR_YOUTUBE_API_KEY]

2. RapidAPI Key  
   Type: "HTTP Request" 
   Name: "RapidAPI YouTube Transcript"
   Header: "X-RapidAPI-Key"
   Value: [YOUR_RAPIDAPI_KEY]

3. OpenAI API
   Type: "OpenAI"
   API Key: [YOUR_OPENAI_KEY]

4. Google Drive
   Type: "Google Drive"
   OAuth2: [Configurer OAuth]
```

**✅ Validation Étape 1.3 :** Test de connexion réussi pour chaque credential

---

### **ÉTAPE 1.4 : Core Workflow Development (Jour 2-3 - 4h)**

#### 1.4.1 Structure Workflow de Base

```
[Telegram Webhook] → [Extract Video ID] → [YouTube Metadata] 
     ↓
[RapidAPI Transcript] → [OpenAI Analysis] → [Format Note] → [Save to Drive] → [Telegram Reply]
```

#### 1.4.2 Nodes à Créer (ordre d'implémentation)

**Node 1: Telegram Webhook Trigger**

```javascript
{
  "httpMethod": "POST",
  "path": "youtube-transcriber",
  "responseMode": "responseNode"
}
```

**Node 2: Extract Video ID (Code Node)**

```javascript
const message = $input.first().json.message.text;
const videoId = extractVideoId(message);

function extractVideoId(url) {
  const regex = /(?:youtube\.com\/(?:[^\/]+\/.+\/|(?:v|e(?:mbed)?)\\/|.*[?&]v=)|youtu\.be\/)([^"&?\/\s]{11})/;
  const match = url.match(regex);
  return match ? match[1] : null;
}

if (!videoId) {
  throw new Error("URL YouTube invalide");
}

return {
  video_id: videoId,
  youtube_url: message,
  chat_id: $input.first().json.message.chat.id
};
```

**Node 3: YouTube Metadata (HTTP Request)**

```javascript
{
  "method": "GET",
  "url": "https://www.googleapis.com/youtube/v3/videos",
  "sendQuery": true,
  "queryParameters": {
    "id": "={{ $json.video_id }}",
    "key": "={{ $credentials.youtube_api.key }}",
    "part": "snippet,contentDetails,statistics"
  }
}
```

**Node 4: RapidAPI Transcript (HTTP Request)**

```javascript
{
  "method": "POST", 
  "url": "https://youtube-transcript1.p.rapidapi.com/transcript",
  "sendHeaders": true,
  "headerParameters": {
    "X-RapidAPI-Key": "={{ $credentials.rapidapi.key }}",
    "X-RapidAPI-Host": "youtube-transcript1.p.rapidapi.com"
  },
  "sendBody": true,
  "jsonBody": {
    "url": "={{ $('Extract Video ID').first().json.youtube_url }}"
  }
}
```

**Node 5: OpenAI Analysis**

```javascript
{
  "model": "gpt-4o-mini",
  "messages": [
    {
      "role": "system",
      "content": "Tu es un expert en synthèse de contenu YouTube. Analyse et structure selon le format demandé."
    },
    {
      "role": "user",
      "content": `Titre: {{ $('YouTube Metadata').first().json.items[0].snippet.title }}
Chaîne: {{ $('YouTube Metadata').first().json.items[0].snippet.channelTitle }}
Transcript: {{ $('RapidAPI Transcript').first().json.transcript }}

Génère un JSON avec: resume_executif, points_cles (array), concepts_outils (array), tags_sugeres (array), enseignements (array)`
    }
  ]
}
```

#### 1.4.3 Tests Progressifs

- [ ] **Test 1** : Webhook Telegram → Extract Video ID
- [ ] **Test 2** : + YouTube Metadata
- [ ] **Test 3** : + RapidAPI Transcript
- [ ] **Test 4** : + OpenAI Analysis
- [ ] **Test 5** : Workflow complet

**✅ Validation Étape 1.4 :** Workflow exécute sans erreur sur vidéo test

---

### **ÉTAPE 1.5 : Obsidian Integration (Jour 3 - 2h)**

#### 1.5.1 Template Obsidian (Code Node)

```javascript
function formatObsidianNote(metadata, transcript, analysis) {
  const video = metadata.items[0];
  const snippet = video.snippet;
  
  return `# ${snippet.title}

## 📋 Métadonnées
- **URL** : https://youtube.com/watch?v=${video.id}
- **Chaîne** : ${snippet.channelTitle}
- **Durée** : ${formatDuration(video.contentDetails.duration)}
- **Vues** : ${video.statistics.viewCount}
- **Date** : ${snippet.publishedAt.split('T')[0]}

## 🎯 Synthèse IA
${analysis.resume_executif}

## 🔑 Points clés
${analysis.points_cles.map(p => `- ${p}`).join('\n')}

## 💡 Concepts/Outils
${analysis.concepts_outils.join(', ')}

## 🏷️ Tags
${analysis.tags_sugeres.map(t => `#${t}`).join(' ')}

## 📝 Notes personnelles
*[À compléter]*

---
*Généré le ${new Date().toISOString().split('T')[0]}*`;
}
```

#### 1.5.2 Google Drive Upload (HTTP Request)

```javascript
{
  "method": "POST",
  "url": "https://www.googleapis.com/upload/drive/v3/files?uploadType=multipart",
  "sendHeaders": true,
  "headerParameters": {
    "Authorization": "Bearer ={{ $credentials.google_drive.access_token }}",
    "Content-Type": "multipart/related; boundary=boundary123"
  },
  "sendBody": true,
  "bodyContentType": "raw",
  "body": `--boundary123\nContent-Type: application/json\n\n{"name": "{{ $json.filename }}", "parents": ["{{ $credentials.google_drive.folder_id }}"]}\n--boundary123\nContent-Type: text/markdown\n\n{{ $json.content }}\n--boundary123--`
}
```

**✅ Validation Étape 1.5 :** Note créée dans Google Drive et visible dans Obsidian

---

### **ÉTAPE 1.6 : Finalisation MVP (Jour 3 - 1h)**

#### 1.6.1 Telegram Response (HTTP Request)

```javascript
{
  "method": "POST",
  "url": "https://api.telegram.org/bot{{ $credentials.telegram.token }}/sendMessage",
  "sendBody": true,
  "jsonBody": {
    "chat_id": "={{ $('Extract Video ID').first().json.chat_id }}",
    "text": "✅ Vidéo analysée avec succès !\n\n📄 Note créée : {{ $json.filename }}\n⏱️ Temps de traitement : {{ $json.processing_time }}s\n💰 Coût estimé : ${{ $json.estimated_cost }}"
  }
}
```

#### 1.6.2 Error Handling Global

```javascript
// Node Error Handler (à connecter à chaque node critique)
try {
  // Processing logic
} catch (error) {
  await $this.helpers.httpRequest({
    method: 'POST',
    url: `https://api.telegram.org/bot${credentials.telegram.token}/sendMessage`,
    body: {
      chat_id: $('Extract Video ID').first().json.chat_id,
      text: `❌ Erreur: ${error.message}\n\nVeuillez réessayer.`
    }
  });
  throw error;
}
```

**✅ Validation Phase 1 Complète :**

- URL Telegram → Note Obsidian < 5 minutes
- Notifications succès/erreur fonctionnelles
- Coût par vidéo < $0.10

---

## 🔧 **PHASE 2 - OPTIMISATIONS (5-7 jours)**

### **ÉTAPE 2.1 : Multi-IA Testing (Jour 4-5 - 3h)**

#### 2.1.1 A/B Testing Implementation

```javascript
// Node: IA Provider Selection
const providers = ['openai', 'claude', 'gemini', 'mistral'];
const videoId = $input.first().json.video_id;

// Hash video ID pour consistance tests
const hash = simpleHash(videoId);
const selectedProvider = providers[hash % providers.length];

return { 
  provider: selectedProvider,
  test_group: `group_${hash % 4}`
};
```

#### 2.1.2 Performance Metrics Collection

```javascript
// Node: Metrics Collector  
const startTime = Date.now();
const provider = $input.first().json.provider;
const result = $input.first().json.analysis;

const metrics = {
  provider: provider,
  processing_time: Date.now() - startTime,
  token_count: estimateTokens(result),
  cost_estimate: calculateCost(provider, result),
  quality_score: assessQuality(result),
  timestamp: new Date().toISOString()
};

// Log pour analyse
console.log('AI_METRICS:', JSON.stringify(metrics));
return metrics;
```

**✅ Validation Étape 2.1 :** Métriques collectées sur 10 vidéos test

---

### **ÉTAPE 2.2 : Link Extraction (Jour 5-6 - 2h)**

#### 2.2.1 Extract Links from Description

```javascript
// Node: Link Extractor
const description = $('YouTube Metadata').first().json.items[0].snippet.description;

function extractLinks(text) {
  const urlRegex = /https?:\/\/[^\s]+/g;
  const links = text.match(urlRegex) || [];
  
  return links.map(link => ({
    url: link,
    type: detectLinkType(link),
    platform: detectPlatform(link)
  }));
}

function detectPlatform(url) {
  if (url.includes('skool.com')) return 'skool';
  if (url.includes('gumroad.com')) return 'gumroad'; 
  if (url.includes('github.com')) return 'github';
  return 'other';
}

return { external_links: extractLinks(description) };
```

**✅ Validation Étape 2.2 :** Liens extraits et catégorisés correctement

---

### **ÉTAPE 2.3 : Enhanced Template (Jour 6 - 1h)**

#### 2.3.1 Template Obsidian Enrichi

```javascript
// Ajout sections liens externes et métriques
const enhancedTemplate = `
## 🌐 Ressources externes
${external_links.map(link => `- [${link.platform.toUpperCase()}](${link.url})`).join('\n')}

## 📊 Métriques
- **Provider IA** : ${metrics.provider}
- **Temps traitement** : ${metrics.processing_time}ms
- **Coût estimé** : $${metrics.cost_estimate}
- **Score qualité** : ${metrics.quality_score}/10
`;
```

**✅ Validation Phase 2 :** Template enrichi, métriques IA comparatives

---

## 🚀 **PHASE 3 - FONCTIONNALITÉS AVANCÉES (2-3 semaines)**

### **ÉTAPE 3.1 : Local Transcription Setup (Semaine 3)**

#### 3.1.1 Synology Proxy Configuration

```bash
# Sur Synology DS
# 1. Installer Docker
# 2. Container youtube-transcript-api
docker run -d -p 8080:8080 \
  -v /volume1/docker/youtube-transcriber:/app \
  --name youtube-transcriber \
  your-youtube-transcriber-image

# 3. Configuration reverse proxy
# Control Panel > Application Portal > Reverse Proxy
# Source: transcriber.your-domain.com:443
# Destination: localhost:8080
```

#### 3.1.2 Fallback Logic Implementation

```javascript
// Node: Transcription with Fallback
const rapidApiResult = $('RapidAPI Transcript').first();

if (rapidApiResult.json.error || !rapidApiResult.json.transcript) {
  // Fallback to local Synology API
  const localResult = await $this.helpers.httpRequest({
    method: 'POST',
    url: 'https://transcriber.your-domain.com/api/transcript',
    body: {
      video_id: $('Extract Video ID').first().json.video_id
    }
  });
  
  return { 
    transcript: localResult.transcript,
    source: 'local_synology' 
  };
}

return { 
  transcript: rapidApiResult.json.transcript,
  source: 'rapidapi' 
};
```

---

### **ÉTAPE 3.2 : Comments Analysis (Semaine 4)**

#### 3.2.1 YouTube Comments Extraction

```javascript
// Node: YouTube Comments API
{
  "method": "GET",
  "url": "https://www.googleapis.com/youtube/v3/commentThreads",
  "sendQuery": true,
  "queryParameters": {
    "part": "snippet",
    "videoId": "={{ $('Extract Video ID').first().json.video_id }}",
    "key": "={{ $credentials.youtube_api.key }}",
    "maxResults": "50",
    "order": "relevance"
  }
}
```

#### 3.2.2 Sentiment Analysis

```javascript
// Node: Comment Sentiment Analysis
const comments = $input.first().json.items;
const topComments = comments.slice(0, 10).map(item => ({
  text: item.snippet.topLevelComment.snippet.textDisplay,
  author: item.snippet.topLevelComment.snippet.authorDisplayName,
  likes: item.snippet.topLevelComment.snippet.likeCount
}));

// Analyse sentiment avec IA
const sentimentAnalysis = await analyzeCommentsSentiment(topComments);
return { 
  top_comments: topComments,
  sentiment: sentimentAnalysis 
};
```

---

### **ÉTAPE 3.3 : Advanced Link Scraping (Semaine 4-5)**

#### 3.3.1 External Link Content Extraction

```javascript
// Node: Scrape External Links
const externalLinks = $('Link Extractor').first().json.external_links;
const scrapedContent = [];

for (const link of externalLinks) {
  if (link.platform === 'skool' || link.platform === 'gumroad') {
    try {
      const content = await scrapePageContent(link.url);
      scrapedContent.push({
        url: link.url,
        title: content.title,
        description: content.description,
        type: content.type // course, ebook, community
      });
    } catch (error) {
      console.log(`Failed to scrape ${link.url}: ${error.message}`);
    }
  }
}

return { scraped_resources: scrapedContent };
```

---

## 📊 **MONITORING & MAINTENANCE**

### **Métriques à Surveiller**

```javascript
// Dashboard Metrics
const metrics = {
  daily_videos_processed: 0,
  success_rate: 0.95, // Target > 95%
  avg_processing_time: 0, // Target < 300s
  total_cost_daily: 0, // Budget < $5/day
  api_quota_usage: {
    youtube: 0, // Max 10k/day
    rapidapi: 0,
    openai_tokens: 0
  },
  error_rates: {
    youtube_api: 0,
    rapidapi: 0,
    openai: 0,
    google_drive: 0
  }
};
```

### **Alertes Automatiques**

- **Quota YouTube** > 80% → Email warning
- **Coût quotidien** > $3 → Slack notification
- **Taux d'erreur** > 10% → Investigation requise
- **Temps traitement** > 10min → Optimisation nécessaire

### **Maintenance Hebdomadaire**

- [ ] **Lundi** : Review métriques semaine précédente
- [ ] **Mercredi** : Clean cache transcripts > 30 jours
- [ ] **Vendredi** : Backup configuration n8n
- [ ] **Dimanche** : Update APIs si nouvelles versions

---

## 🎯 **CHECKLIST DE VALIDATION FINALE**

### **MVP Ready (Phase 1)**

- [ ] Telegram bot répond instantanément
- [ ] YouTube URL → Note Obsidian < 5 minutes
- [ ] Taux de succès > 90% sur 20 vidéos test
- [ ] Coût moyen < $0.10 par vidéo
- [ ] Error handling fonctionnel
- [ ] Notifications Telegram opérationnelles

### **Production Ready (Phase 2)**

- [ ] Multi-IA testing opérationnel
- [ ] Métriques qualité/coût collectées
- [ ] Liens externes extraits et validés
- [ ] Template Obsidian enrichi
- [ ] Performance optimisée < 3 minutes

### **Enterprise Ready (Phase 3)**

- [ ] Fallback local transcription
- [ ] Analyse commentaires intégrée
- [ ] Scraping liens externes automatisé
- [ ] Monitoring et alertes configurés
- [ ] Documentation complète

---

## 📋 **RESSOURCES & RÉFÉRENCES**

### **Documentation APIs**

- [YouTube Data API v3](https://developers.google.com/youtube/v3/docs)
- [RapidAPI YouTube Transcript](https://rapidapi.com/thisisgazzar/api/youtube-transcript1)
- [OpenAI API Reference](https://platform.openai.com/docs/api-reference)
- [Google Drive API v3](https://developers.google.com/drive/api/v3/reference)
- [Telegram Bot API](https://core.telegram.org/bots/api)

### **n8n Resources**

- [n8n Documentation](https://docs.n8n.io/)
- [n8n Community Templates](https://n8n.io/workflows/)
- [HTTP Request Node](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.httprequest/)

### **Répertoires de Travail**

```bash
/Users/rollandmelet/Développement/RoRworld/YouTube-Transcriber/
├── workflows/           # Exports n8n
├── templates/          # Templates Obsidian  
├── docs/              # Documentation
├── scripts/           # Scripts utilitaires
└── tests/             # Vidéos de test
```

### **Support & Escalation**

- **Issues techniques** : n8n Community Forum
- **APIs problèmes** : Support direct providers
- **Budget monitoring** : Alertes automatiques configurées

---

_Plan d'implémentation v1.0.0 - Prêt pour exécution - 2025-01-02_