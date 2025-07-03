# <!-- START OF FILE: TASKS.md -->
# FILENAME: TASKS.md
# Version: 1.1.0
# Date: 2025-06-21 18:00
# Author: Rolland MELET & AI Senior Coder
# Description: Version complète et détaillée des tâches pour la Phase 1 (MVP) du projet YouTube Transcriber, incluant les corrections de robustesse et les instructions pas-à-pas.

## Document Control
- **Version:** 1.1.0
- **Date:** 2025-06-21
- **Référence PRD:** PRD-YouTube-Transcriber-v1.0.0
- **Référence PLAN:** PLAN-YouTube-Transcriber-v1.0.0

---

## 📋 **VARIABLES & CREDENTIALS TRACKING**

### Tableau de Suivi des Credentials
| Service | Variable | Valeur | Status | Notes |
|---|---|---|---|---|
| YouTube Data API | `YOUTUBE_API_KEY` | *À remplir* | [ ] | Google Cloud Console |
| RapidAPI | `RAPIDAPI_KEY` | *À remplir* | [ ] | X-RapidAPI-Key header |
| OpenAI | `OPENAI_API_KEY` | *À remplir* | [ ] | Platform OpenAI |
| Telegram Bot | `TELEGRAM_BOT_TOKEN` | *À remplir* | [ ] | @BotFather |
| Google Drive | `GOOGLE_DRIVE_FOLDER_ID` | *À remplir* | [ ] | ID du dossier dans l'URL |
| n8n Instance | `N8N_WEBHOOK_URL` | *À remplir* | [ ] | Webhook endpoint |

### Répertoires de Travail

# /Users/rollandmelet/Développement/RoRworld/YouTube-Transcriber/
├── credentials/        # 🔒 Fichiers credentials (PRIVÉ, .gitignored)
├── workflows/          # 📄 Exports n8n JSON
├── templates/          # 📝 Templates Obsidian (si besoin)
├── tests/              # 🧪 Vidéos et scripts de test
├── docs/               # 📚 Documentation
└── scripts/            # ⚙️ Scripts utilitaires
Use code with caution.
Markdown
🚀 PRÉPARATION (P) - 45 minutes
P.1 Vérifications Prérequis ⏱️ 10 min
P.1.1 Vérifier n8n Installation et Version
P.1.1.a Ouvrir un terminal et vérifier que n8n est en cours d'exécution.
Generated bash
# Vérifier si n8n répond
curl -I http://localhost:5678
# Attendu: HTTP/1.1 200 OK

# Vérifier la version de n8n (le nom du container peut être 'n8n' ou autre)
docker exec n8n n8n --version
# Attendu: Une version >= 1.100.1
Use code with caution.
Bash
✅ Validation P.1.1 : n8n est accessible et sa version est correcte.
P.1.2 Vérifier Accès Synology/VPS (Optionnel pour Phase 1)
P.1.2.a Tester la connexion SSH si un serveur distant est déjà utilisé.
Generated bash
ssh rolland@your-synology-ip
# OU
ssh user@your-vps-ip
Use code with caution.
Bash
✅ Validation P.1.2 : Connexion SSH réussie.
P.1.3 Vérifier Obsidian et Google Drive
P.1.3.a Confirmer que l'application de bureau Google Drive est installée, connectée et que la synchronisation du dossier cible est active.
P.1.3.b Tester la création d'un fichier dans le dossier local pour valider la synchronisation.
Generated bash
# Créer un fichier de test
echo "Test de synchronisation Google Drive - $(date)" > "/Users/rollandmelet/Développement/RoRworld/YouTube-Notes/test-sync.md"
# Vérifier manuellement que le fichier `test-sync.md` apparaît dans l'interface web de Google Drive après quelques instants.
Use code with caution.
Bash
✅ Validation P.1.3 : Le fichier de test est bien visible dans Google Drive.
P.2 Setup Environnement de Travail ⏱️ 15 min
P.2.1 Créer la Structure des Répertoires
P.2.1.a Créer les répertoires du projet.
Generated bash
mkdir -p "/Users/rollandmelet/Développement/RoRworld/YouTube-Transcriber"
cd "/Users/rollandmelet/Développement/RoRworld/YouTube-Transcriber"
mkdir -p credentials workflows templates tests docs scripts
Use code with caution.
Bash
P.2.1.b Créer un fichier .gitignore pour exclure les informations sensibles.
Generated bash
cat > .gitignore << 'EOF'
# Fichiers de credentials
credentials/
*.env

# Dépendances Node
node_modules/

# Fichiers macOS
.DS_Store
EOF```
- [ ] **P.2.1.c** Créer un fichier de template pour les credentials.
bash
cat > credentials/template.env << 'EOF'
# Fichier à copier en credentials/actual.env et à remplir.
# NE PAS COMMIT CE FICHIER.

# YouTube Data API
YOUTUBE_API_KEY=

# RapidAPI
RAPIDAPI_KEY=

# OpenAI
OPENAI_API_KEY=

# Telegram Bot
TELEGRAM_BOT_TOKEN=

# Google Drive
GOOGLE_DRIVE_FOLDER_ID=

# n8n
N8N_WEBHOOK_URL=
EOF
Use code with caution.
Bash
✅ Validation P.2.1 : Structure des répertoires et .gitignore créés.
P.3 URLs de Référence Essentielles ⏱️ 20 min
P.3.1 Sauvegarder les Liens Importants
P.3.1.a Créer un fichier de documentation pour centraliser les URLs.
Generated bash
cat > docs/urls-reference.md << 'EOF'
# URLs de Référence

## APIs Documentation
- YouTube Data API: https://developers.google.com/youtube/v3/docs
- RapidAPI YouTube Transcript: https://rapidapi.com/thisisgazzar/api/youtube-transcript1
- OpenAI API: https://platform.openai.com/docs/api-reference
- Telegram Bot API: https://core.telegram.org/bots/api

## Consoles & Dashboards
- Google Cloud Console: https://console.cloud.google.com
- RapidAPI Hub: https://rapidapi.com/hub
- OpenAI Platform: https://platform.openai.com
- n8n Instance: http://localhost:5678

## Ressources de Test
- Vidéo de test standard (avec transcript): https://www.youtube.com/watch?v=dQw4w9WgXcQ
- Vidéo éducative courte (avec transcript): https://www.youtube.com/watch?v=_OBlgSz8sSM
EOF
Use code with caution.
Bash
✅ Validation P.3 : Les URLs sont sauvegardées et accessibles.
📡 PHASE 1 - MVP CORE (1) - 8-12 heures
1.1 Setup Environnement APIs ⏱️ 2h
1.1.1 Google Cloud Console - YouTube Data API
1.1.1.a Créer un projet sur Google Cloud Console (Nom: YouTube Transcriber).
1.1.1.b Activer l'API "YouTube Data API v3" pour ce projet.
1.1.1.c Dans "APIs et services > Identifiants", créer une "Clé d'API".
1.1.1.d Copier la clé générée et la sauvegarder dans votre gestionnaire de mots de passe et dans credentials/actual.env (créé à partir du template).
1.1.1.e Tester la clé API. Remplacer VOTRE_CLE_API par la clé réelle.
Generated bash
curl "https://www.googleapis.com/youtube/v3/videos?id=dQw4w9WgXcQ&key=VOTRE_CLE_API&part=snippet"
# Attendu: Un JSON contenant le titre "Rick Astley - Never Gonna Give You Up".
Use code with caution.
Bash
✅ Validation 1.1.1 : La clé YouTube API est fonctionnelle et sauvegardée.
1.1.2 RapidAPI Account - YouTube Transcript
1.1.2.a Créer un compte sur RapidAPI.
1.1.2.b S'abonner à l'API YouTube Transcript API by thisisgazzar. Le plan "Basic" est suffisant pour commencer.
1.1.2.c Sur la page de l'API, copier la X-RapidAPI-Key depuis les exemples de code.
1.1.2.d Sauvegarder cette clé et la tester. Remplacer VOTRE_CLE_RAPIDAPI.
Generated bash
curl --request POST \
	--url https://youtube-transcript1.p.rapidapi.com/transcript \
	--header 'content-type: application/json' \
	--header 'X-RapidAPI-Host: youtube-transcript1.p.rapidapi.com' \
	--header 'X-RapidAPI-Key: VOTRE_CLE_RAPIDAPI' \
	--data '{"url": "https://www.youtube.com/watch?v=dQw4w9WgXcQ"}'
# Attendu: Un JSON contenant un tableau de segments de transcription.
Use code with caution.
Bash
✅ Validation 1.1.2 : L'API de transcription est opérationnelle et la clé sauvegardée.
1.1.3 OpenAI Platform Setup
1.1.3.a Se connecter à OpenAI Platform et vérifier les crédits.
1.1.3.b Créer une nouvelle clé API dédiée au projet "YouTube Transcriber".
1.1.3.c Sauvegarder la clé (elle commence par sk-) et la tester. Remplacer VOTRE_CLE_OPENAI.
Generated bash
curl https://api.openai.com/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer VOTRE_CLE_OPENAI" \
  -d '{"model": "gpt-4o-mini", "messages": [{"role": "user", "content": "Ceci est un test."}], "max_tokens": 10}'
# Attendu: Un JSON avec une réponse dans `choices[0].message.content`.
Use code with caution.
Bash
✅ Validation 1.1.3 : L'API OpenAI est fonctionnelle et la clé sauvegardée.
1.1.4 Google Drive Configuration
1.1.4.a Dans Google Drive, créer le dossier de destination (ex: RoRworld-YouTube-Notes).
1.1.4.b Ouvrir le dossier et copier l'ID depuis l'URL. (ex: .../folders/1abc...xyz, l'ID est 1abc...xyz).
1.1.4.c Sauvegarder cet ID dans credentials/actual.env.
✅ Validation 1.1.4 : L'ID du dossier Google Drive est noté.
1.2 Telegram Bot Setup ⏱️ 1h
1.2.1 Création du Bot via BotFather
1.2.1.a Dans Telegram, démarrer une conversation avec @BotFather.
1.2.1.b Envoyer /newbot, choisir un nom (ex: "YouTube Transcriber") et un username (ex: @RoRworldTranscriberBot).
1.2.1.c BotFather va fournir un token. C'est la clé la plus importante. La copier et la sauvegarder précieusement dans credentials/actual.env.
✅ Validation 1.2.1 : Le bot est créé et son token est sauvegardé.
1.2.2 Configuration du Webhook n8n
1.2.2.a Déterminer l'URL publique de votre webhook n8n. Le path sera youtube-transcriber.
Important: Si votre n8n est local (localhost), il faut l'exposer sur internet. L'outil le plus simple pour cela est ngrok.
ngrok http 5678 -> Copier l'URL HTTPS fournie (ex: https://abcd-1234.ngrok-free.app).
L'URL complète sera https://abcd-1234.ngrok-free.app/webhook/youtube-transcriber.
1.2.2.b Définir le webhook auprès de Telegram. Remplacer BOT_TOKEN et WEBHOOK_URL.
Generated bash
curl -X POST "https://api.telegram.org/bot<BOT_TOKEN>/setWebhook" \
  -H "Content-Type: application/json" \
  -d '{"url": "<WEBHOOK_URL>"}'
# Attendu: {"ok":true,"result":true,"description":"Webhook was set"}
Use code with caution.
Bash
1.2.2.c Sauvegarder l'URL du webhook dans credentials/actual.env.
✅ Validation 1.2.2 : Le webhook est configuré pour pointer vers n8n.
1.3 Configuration des Credentials dans n8n ⏱️ 30 min
1.3.1 Dans l'interface n8n, aller dans "Credentials" > "Add credential".
1.3.2 YouTube API:
Type: HTTP Request
Name: YouTube Data API
Authentication: Query Auth -> Name: key, Value: [coller la clé YouTube]
1.3.3 RapidAPI:
Type: HTTP Request
Name: RapidAPI YouTube Transcript
Authentication: Header Auth -> Name: X-RapidAPI-Key, Value: [coller la clé RapidAPI]
1.3.4 OpenAI:
Type: OpenAI API
Name: OpenAI YouTube Analyzer
API Key: [coller la clé OpenAI]
1.3.5 Google Drive:
Type: Google Drive API
Name: Google Drive Obsidian
Authentication: OAuth2 -> Suivre le processus de connexion et d'autorisation avec votre compte Google.
1.3.6 Telegram:
Type: Telegram API
Name: Telegram Bot
Access Token: [coller le token du bot Telegram]
✅ Validation 1.3 : Tous les credentials sont créés et configurés dans n8n.
1.4 Développement du Workflow Principal ⏱️ 4h
Créez un nouveau workflow dans n8n et nommez-le "YouTube Transcriber v1.0".
1.4.1 Node 1: Telegram Trigger
Type: On Telegram call
Credential: Telegram Bot
Updates: message
1.4.2 Node 2: Extract Video ID (Code Node)
Nom: Extract Video ID
Code JavaScript:
Generated javascript
// <!-- START OF FILE: n8n_extract_video_id.js -->
// FILENAME: n8n_extract_video_id.js
// Version: 1.0.0
// Date: 2025-06-21
// Author: Rolland MELET & AI Senior Coder
// Description: Extracts a YouTube video ID from a given URL.
const messageText = $input.first().json.text;

function extractVideoId(url) {
  const patterns = [
    /(?:https?:\/\/)?(?:www\.)?(?:youtube\.com|youtu\.be)\/(?:watch\?v=|embed\/|v\/|)([\w-]{11})(?:\S+)?/
  ];
  for (const pattern of patterns) {
    const match = url.match(pattern);
    if (match && match) {
      return match;
    }
  }
  return null;
}

const videoId = extractVideoId(messageText);

if (!videoId) {
  // Ceci arrêtera le workflow si aucune URL valide n'est trouvée.
  // L'erreur sera gérée par le Error Trigger.
  throw new Error("URL YouTube invalide.");
}

return {
  video_id: videoId,
  youtube_url: messageText
};
// <!-- END OF FILE: n8n_extract_video_id.js -->
Use code with caution.
JavaScript
1.4.3 Node 3: YouTube Metadata (HTTP Request Node)
Nom: YouTube Metadata
Credential: YouTube Data API
Request Method: GET
URL: https://www.googleapis.com/youtube/v3/videos
Query Parameters:
id: {{ $json.video_id }}
part: snippet,contentDetails,statistics
1.4.4 Node 4: RapidAPI Transcript (HTTP Request Node)
Nom: RapidAPI Transcript
Credential: RapidAPI YouTube Transcript
Request Method: POST
URL: https://youtube-transcript1.p.rapidapi.com/transcript
Add Header:
Name: X-RapidAPI-Host, Value: youtube-transcript1.p.rapidapi.com
Body Content Type: JSON
JSON: {"url": "{{ $('Extract Video ID').first().json.youtube_url }}"}
1.4.5 Node 5: Clean & Join Transcript (Code Node)
Nom: Clean & Join Transcript
Code JavaScript:
Generated javascript
// <!-- START OF FILE: n8n_clean_transcript.js -->
// FILENAME: n8n_clean_transcript.js
// Version: 1.0.0
// Date: 2025-06-21
// Author: Rolland MELET & AI Senior Coder
// Description: Cleans and joins transcript segments into a single text block.
const transcriptData = $input.first().json;

if (!Array.isArray(transcriptData) || transcriptData.length === 0) {
    throw new Error("Transcript non disponible ou vide pour cette vidéo.");
}

const fullTranscript = transcriptData
    .map(item => item.text)
    .join(' ')
    .replace(/\s+/g, ' ') // Remplace les espaces multiples par un seul
    .trim();

if (fullTranscript.length < 100) {
    throw new Error("Transcript trop court pour être analysé.");
}

return {
    transcript_text: fullTranscript,
    word_count: fullTranscript.split(' ').length
};
// <!-- END OF FILE: n8n_clean_transcript.js -->
Use code with caution.
JavaScript
1.4.6 Node 6: OpenAI Analysis (OpenAI Node)
Nom: OpenAI Analysis
Credential: OpenAI YouTube Analyzer
Resource: Chat
Operation: Message
Model: gpt-4o-mini
Options > Response Format: JSON Object
System Message:
Generated code
Tu es un expert en synthèse de contenu YouTube. Analyse les informations de la vidéo et son transcript. Génère une synthèse structurée en JSON. RÉPONDS UNIQUEMENT avec un objet JSON valide, sans texte avant ou après.
Le JSON doit contenir ces champs EXACTS:
{
  "resume_executif": "Résumé en 2-3 phrases (100 mots max).",
  "points_cles": ["3 à 5 points clés principaux sous forme de liste."],
  "concepts_outils": ["Liste des concepts et outils mentionnés."],
  "tags_sugeres": ["Liste de 3 à 5 tags pertinents pour Obsidian."],
  "enseignements": ["Liste de 2 à 3 enseignements directement actionnables."]
}
Use code with caution.
User Message:
Generated code
Analyse la vidéo suivante :

Titre: {{ $('YouTube Metadata').first().json.items.snippet.title }}
Chaîne: {{ $('YouTube Metadata').first().json.items.snippet.channelTitle }}
TRANSCRIPT COMPLET:
{{ $('Clean & Join Transcript').first().json.transcript_text }}

Génère l'analyse JSON.
Use code with caution.
1.4.7 Node 7: Format Obsidian Note (Code Node)
Nom: Format Obsidian Note
Code JavaScript:
Generated javascript
// <!-- START OF FILE: n8n_format_note.js -->
// FILENAME: n8n_format_note.js
// Version: 1.0.0
// Date: 2025-06-21
// Author: Rolland MELET & AI Senior Coder
// Description: Formats the final Obsidian note in Markdown.
const videoData = $('Extract Video ID').first().json;
const metadata = $('YouTube Metadata').first().json.items;
const analysis = $('OpenAI Analysis').first().json.json; // Note: .json.json car OpenAI node renvoie le JSON dans un champ 'json'

function formatDuration(duration) {
  const match = duration.match(/PT(?:(\d+)H)?(?:(\d+)M)?(?:(\d+)S)?/);
  if (!match) return "N/A";
  const hours = parseInt(match || 0);
  const minutes = parseInt(match || 0);
  const seconds = parseInt(match || 0);
  if (hours > 0) return `${hours}:${minutes.toString().padStart(2, '0')}:${seconds.toString().padStart(2, '0')}`;
  return `${minutes}:${seconds.toString().padStart(2, '0')}`;
}

const noteContent = `
# ${metadata.snippet.title}

## 📋 Métadonnées
- **URL**: ${videoData.youtube_url}
- **Chaîne**: [${metadata.snippet.channelTitle}](https://www.youtube.com/channel/${metadata.snippet.channelId})
- **Durée**: ${formatDuration(metadata.contentDetails.duration)}
- **Date publication**: ${metadata.snippet.publishedAt.split('T')[0]}
- **Vues**: ${parseInt(metadata.statistics.viewCount).toLocaleString('fr-FR')}
- **Date d'analyse**: ${new Date().toISOString().split('T')[0]}

## 🎯 Synthèse IA (30 sec)
${analysis.resume_executif}

## 🔑 Points Clés
${analysis.points_cles.map(point => `- ${point}`).join('\n')}

## 💡 Concepts & Outils
${analysis.concepts_outils.map(item => `- ${item}`).join('\n')}

## 🚀 Enseignements Actionnables
${analysis.enseignements.map(action => `- ${action}`).join('\n')}

## 🏷️ Tags
${analysis.tags_sugeres.map(tag => `#${tag.replace(/\s+/g, '_')}`).join(' ')}

## 📝 Notes Personnelles
- 

---
*Généré automatiquement via RoR-Transcriber le ${new Date().toISOString()}*
`;

const cleanTitle = metadata.snippet.title.replace(/[^\w\s-]/g, '').replace(/\s+/g, '-').substring(0, 80);
const fileName = `${new Date().toISOString().split('T')[0]}_${cleanTitle}.md`;

return {
  note_content: noteContent,
  file_name: fileName
};
// <!-- END OF FILE: n8n_format_note.js -->
Use code with caution.
JavaScript
1.4.8 Node 8: Save to Google Drive (Google Drive Node)
Nom: Save to Google Drive
Credential: Google Drive Obsidian
Resource: File
Operation: Upload
Destination Folder ID: [coller l'ID du dossier Google Drive depuis credentials/actual.env]
File Name: {{ $json.file_name }}
Input Data: File Content
Property Name: note_content
MIME Type: text/markdown
1.4.9 Node 9: Send Success to Telegram (Telegram Node)
Nom: Send Success to Telegram
Credential: Telegram Bot
Operation: Send Message
Chat ID: {{ $('Telegram Trigger').first().json.chat.id }}
Text:
Generated code
✅ **Analyse terminée !**

📄 La note pour la vidéo "*{{ $('YouTube Metadata').first().json.items.snippet.title }}*" a été créée dans votre dossier Obsidian.

Synchronisation via Google Drive en cours...
Use code with caution.
Options > Parse Mode: MarkdownV2
1.5 Gestion d'Erreurs ⏱️ 1h
1.5.1 Créer un Error Trigger Node (le node de départ rouge). Il s'activera pour toute erreur dans le workflow.
1.5.2 Connecter à ce trigger un Telegram Node nommé "Send Error to Telegram".
Credential: Telegram Bot
Operation: Send Message
Chat ID: {{ $json.execution.data.trigger.json.chat.id }} (Chemin à vérifier, peut varier. Testez pour obtenir le bon Chat ID en cas d'erreur).
Text:
Generated code
❌ **Erreur lors du traitement de la vidéo.**

Raison: *{{ $json.error.message }}*

Veuillez vérifier l'URL ou réessayer plus tard. Si le problème persiste, la vidéo n'est peut-être pas compatible (pas de transcript, privée, etc.).
Use code with caution.
Options > Parse Mode: MarkdownV2
1.6 Tests et Validation du Workflow ⏱️ 1h
1.6.1 Activer le workflow dans n8n (bouton "Active" en haut à droite).
1.6.2 Envoyer l'URL d'une vidéo de test au bot Telegram.
Test 1 (standard): https://www.youtube.com/watch?v=_OBlgSz8sSM
1.6.3 Vérifier que le workflow s'exécute sans erreur dans n8n.
1.6.4 Vérifier que la note apparaît dans le dossier local synchronisé avec Google Drive et dans Obsidian.
1.6.5 Vérifier que la notification de succès est reçue sur Telegram.
1.6.6 Tester la gestion d'erreur en envoyant une URL invalide.
Test 2 (erreur): ceci n'est pas une url
1.6.7 Vérifier que la notification d'erreur est bien reçue.
1.7 Documentation et Sauvegarde ⏱️ 30 min
1.7.1 Dans n8n, exporter le workflow final (Download > Workflow JSON) et le sauvegarder dans workflows/youtube-transcriber-v1.0-final.json.
1.7.2 Mettre à jour docs/guide-utilisateur.md avec les instructions finales.
1.7.3 Faire un commit Git avec un message descriptif.
Generated bash
git add .
git commit -m "feat(n8n): implement initial MVP for youtube transcriber
> - Creates a full end-to-end workflow from Telegram to Obsidian.
> - Fetches metadata and transcript from YouTube.
> - Uses OpenAI for structured analysis.
> - Saves the final note directly to Google Drive.
> - Includes success and error notifications on Telegram."
git push origin main
Use code with caution.
Bash
✅ VALIDATION FINALE PHASE 1 : Le MVP est fonctionnel, robuste, documenté et versionné.
<!-- END OF FILE: TASKS.md -->


