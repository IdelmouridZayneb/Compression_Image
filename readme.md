PixelOptimize — Système Multi‑Agents Intelligent pour l'Optimisation de la Compression d'Images
Mini-Projet · Licence IRM · Module : Traitement d'Images
Équipe 4 · Université Hassan II · FSTM

Introduction
PixelOptimize est un système intelligent conçu pour optimiser la compression d'images en utilisant une architecture multi‑agents et l'intégration de modèles de langage avancés (LLM) comme Groq (llama-3.3-70b).
Le système analyse les caractéristiques des images, prend des décisions intelligentes concernant le format et la qualité de compression optimaux, applique les compressions, puis génère des rapports détaillés de performance.

Description de l'Architecture
Le projet se compose de deux parties principales :
1. Interface Utilisateur — n8nstrm.py
Application interactive développée avec Streamlit permettant :

Téléchargement d'images
Visualisation du pipeline de traitement
Consultation des rapports de compression

2. Serveur Multi‑Agents — server_groq.py
Serveur Flask API qui héberge les agents intelligents, gère le traitement des images et intègre le modèle LLM Groq.
Pipeline des 5 Agents
AgentRôle🔬 Agent AnalysteExtrait les caractéristiques visuelles : entropie, variance, contours, OCR, type d'image🧠 Agent DécideurDeux agents IA (Groq llama-3.3-70b) analysent les caractéristiques et proposent chacun une stratégie de compression, puis sélectionnent ensemble la solution optimale⚖️ Agent ComparateurCompare les deux stratégies proposées et valide la décision finale⚙️ Agent ExécuteurApplique la compression physique au format choisi (JPEG/PNG/WebP/AVIF/HEIF)📋 Agent RapporteurCalcule SSIM · PSNR · MSE · τ% · Q/T et génère le rapport complet
Workflows Disponibles
Le projet propose deux workflows distincts :

Workflow 1 — Avec Streamlit (Streamlit_WORKFLOW_n8n_compressionX.json) : Interface graphique interactive dans le navigateur
Workflow 2 — Sans Streamlit (n8n_Workflow_sans Streamlit.json) : Pipeline direct via n8n, sans interface graphique


⚠️ Pour le Workflow sans Streamlit : vous devez modifier manuellement la valeur de image_path dans deux agents :

Agent Analyste : remplacez image_path par le chemin de votre image sur votre PC
Agent Exécuteur : remplacez également image_path par le même chemin

Exemple Windows : C:/Users/VotreNom/Images/photo.jpg
Exemple Linux/macOS : /home/utilisateur/images/photo.jpg

Structure du Projet
PixelOptimize/
├── n8nstrm.py                                   # Interface Streamlit
├── server_groq.py                               # Serveur Flask + 5 Agents
├── requirements.txt                             # Dépendances Python
├── PixelOptimize_Logo_2.png
├── users.json                                   # Comptes utilisateurs
├── Streamlit_WORKFLOW_n8n_compressionX.json     # Workflow avec Streamlit
├── n8n_Workflow_sans Streamlit.json             # Workflow sans Streamlit
├── database-projet/                             # Images de test (30 images)
└── rapports/
    ├── resume_global.json
    └── <nom_image>_rapport.json

Installation de l'Environnement
Prérequis

Python 3.x
n8n (instance locale)
Tesseract OCR
Clé API Groq


Étape 1 — Cloner le projet
bashgit clone https://github.com/IdelmouridZayneb/Compression_Image.git
cd Compression_Image

Étape 2 — Créer un environnement virtuel
PyCharm
PyCharm crée automatiquement un environnement virtuel. Si ce n'est pas le cas :

File → Settings → Project → Python Interpreter → Add Interpreter → Virtual Environment

VS Code (obligatoire)
bash# Créer le venv
python -m venv venv

# Activer sur Windows
venv\Scripts\activate

# Activer sur macOS/Linux
source venv/bin/activate

✅ Vérifiez que (venv) apparaît au début de votre terminal avant de continuer.

Si VS Code ne reconnaît pas le venv : Ctrl+Shift+P → Python: Select Interpreter → choisir venv/
Terminal classique
bashpython -m venv venv
source venv/bin/activate   # macOS/Linux
venv\Scripts\activate      # Windows

Étape 3 — Installer les dépendances Python
bashpip install -r requirements.txt
En cas de problème avec HEIF ou AVIF :
bashpip install pillow-heif pillow-avif-plugin

Étape 4 — Installer Tesseract OCR
PixelOptimize utilise Tesseract OCR pour détecter le texte dans les images.
Windows
Télécharger depuis : https://github.com/UB-Mannheim/tesseract/wiki
macOS
bashbrew install tesseract
Linux (Ubuntu/Debian)
bashsudo apt-get install tesseract-ocr
Configuration du chemin Tesseract
Dans server_groq.py, la ligne suivante gère automatiquement le chemin :
pythonpytesseract.pytesseract.tesseract_cmd = os.getenv("TESSERACT_PATH", "tesseract")
Ou définissez la variable d'environnement :
bash# Linux/macOS
export TESSERACT_PATH="/usr/local/bin/tesseract"

# Windows (PowerShell)
$env:TESSERACT_PATH = "C:\Program Files\Tesseract-OCR\tesseract.exe"

Étape 5 — Configuration des clés API
PixelOptimize utilise l'API Groq pour les agents LLM (Agent Décideur et Agent Comparateur).
Obtenir une clé Groq

Rendez-vous sur https://console.groq.com
Créez un compte gratuit
Générez une clé API dans API Keys

Configurer la clé API
Option A — Variable d'environnement (recommandée) :
bash# Linux/macOS
export GROQ_API_KEY="votre_clé_groq_ici"

# Windows (PowerShell)
$env:GROQ_API_KEY = "votre_clé_groq_ici"

# Windows (CMD)
set GROQ_API_KEY=votre_clé_groq_ici
Option B — Fichier .env :
Créez un fichier .env à la racine du projet :
GROQ_API_KEY=votre_clé_groq_ici
TESSERACT_PATH=/usr/local/bin/tesseract

⚠️ Ne partagez jamais votre clé API. Ajoutez .env dans votre .gitignore.


Étape 6 — Configurer n8n

Installer n8n :

bashnpm install -g n8n

Lancer n8n :

bashn8n start

Ouvrir http://localhost:5678
Importer le workflow JSON (Avec ou Sans Streamlit selon votre choix)
Activer le workflow

Le webhook utilisé est :
http://localhost:5678/webhook/compression-pipeline

Utilisation du Système
Lancer le serveur Flask
bashpython server_groq.py
Serveur disponible sur : http://0.0.0.0:5000
Vérifier que le serveur fonctionne :
bashcurl http://localhost:5000/health

Lancer l'application Streamlit (Workflow 1 uniquement)
Dans un deuxième terminal :
bashstreamlit run n8nstrm.py
Le navigateur s'ouvre automatiquement sur http://localhost:8501

Exemples de Commandes
Tester un agent directement via l'API Flask
bash# Agent Analyste — analyser une image
curl -X POST http://localhost:5000/agent/analyste \
  -H "Content-Type: application/json" \
  -d '{"image_path": "database-projet/photo1.jpg"}'

# Agent Exécuteur — compresser une image
curl -X POST http://localhost:5000/agent/executeur \
  -H "Content-Type: application/json" \
  -d '{"image_path": "database-projet/photo1.jpg", "format": "WEBP", "quality": 85}'

# Lancer le pipeline complet sur toutes les images
curl -X POST http://localhost:5000/run-all

# Vérifier le statut du pipeline
curl http://localhost:5000/run-all/status

# Lister toutes les images disponibles
curl http://localhost:5000/run-all/list
Utilisation via l'interface Streamlit

Ouvrir http://localhost:8501
Se connecter ou continuer en tant qu'invité
Aller dans 🗜️ Compresser
Uploader une image
Cliquer sur ▶ Lancer l'optimisation
Consulter les métriques SSIM · PSNR · MSE · τ%
Télécharger l'image compressée ou le rapport JSON


Résultats Expérimentaux
Pour chaque image testée, le système génère :

Rapport JSON (rapports/<nom_image>_rapport.json) contenant :

Caractéristiques extraites (entropie, variance, type, contours, OCR)
Recommandation du LLM avec justification
Image compressée dans le format optimal
Métriques de qualité calculées : SSIM · PSNR · MSE · τ% · Q/T
Comparaison visuelle avant/après


Résumé global (rapports/resume_global.json) : synthèse de toutes les images testées
Graphiques : comparaison des stratégies, taux de compression par catégorie

Métriques calculées
MétriqueDescriptionSSIMQualité visuelle structurelle (0 → 1, idéal ≥ 0.95)PSNRFidélité du signal en dB (idéal > 30 dB)MSEErreur quadratique moyenne pixel par pixelτ%Taux de compression — espace disque économiséQ/TScore composite Qualité / Taille

Dépannage
ProblèmeSolutionModuleNotFoundErrorpip install -r requirements.txtTesseractNotFoundErrorVérifier installation Tesseract + variable TESSERACT_PATHServeur Flask inaccessibleVérifier que python server_groq.py est lancé sur le port 5000n8n webhook timeoutVérifier que n8n est actif sur le port 5678HEIF / AVIF non supportépip install pillow-heif pillow-avif-pluginVS Code venv non reconnuCtrl+Shift+P → Python: Select Interpreter → venv/Réponse vide de n8nVérifier que le workflow est activé dans n8n

Technologies Utilisées

Python 3.x — langage principal
Streamlit — interface utilisateur web
Flask — serveur API REST
Groq LLM (llama-3.3-70b) — intelligence artificielle pour la décision
n8n — orchestration du pipeline d'agents
Tesseract OCR — analyse et détection de texte
Pillow — traitement et compression d'images
Scikit-image — calcul des métriques SSIM et PSNR
Plotly / Matplotlib — visualisation des résultats


Auteurs
Équipe 4 · Université Hassan II · FSTM
Module : Traitement d'Images · Licence IRM

---



# PixelOptimize

Système Intelligent Multi‑Agents pour l’Optimisation de Compression d’Images

---
