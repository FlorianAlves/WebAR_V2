# WebAR_V2

# Modèle WebAR (MindAR + A-Frame)

Ce projet est un template de **Réalité Augmentée sur le Web (WebAR)**. Il permet de détecter des images physiques (posters, cartes, photos) et d'afficher par-dessus du contenu multimédia (Vidéos sur fond vert, Audio, Modèles 3D) sans installer d'application.

Le projet est conçu pour être **"Low Code"** : vous n'avez (presque) pas besoin de toucher au HTML/JS. Tout fonctionne par une nomenclature de fichiers stricte.

---

## 🚀 Fonctionnalités

* **Multi-Cibles :** Peut gérer jusqu'à 10 images différentes simultanément (peux être modifié, voir plus loin).
* **Auto-Détection :** Le code scanne le dossier `assets` et charge automatiquement ce qu'il trouve.
* **Vidéos Fond Vert (Chromakey) :** Suppression automatique du fond vert pour ajouter de la transparence à vos vidéos mp4.
* **Auto-Format :** Les vidéos s'adaptent automatiquement (Verticales/Horizontales) sans déformation.
* **Audio en boucle :** Les sons d'ambiance redémarrent automatiquement.
* **Optimisation Mobile :** Code optimisé pour iOS et Android (gestion mémoire, GPU).

---

## 🛠️ Installation & Utilisation

### 1. Préparer les Images Cibles (Tracking)

1. Allez sur le compilateur officiel : [MindAR Image Compiler](https://hiukim.github.io/mind-ar-js-doc/tools/compile).
2. Uploadez vos images cibles (ex: `imageA.jpg`, `imageB.jpg`).
3. **L'ordre est crucial !**
* La 1ère image uploadée aura l'index `0`.
* La 2ème image uploadée aura l'index `1`.
* Etc.


4. Cliquez sur "Compile" et téléchargez le fichier `targets.mind`.
5. Placez ce fichier dans le dossier : `assets/targets.mind` (remplacez l'ancien s'il y en a déjà un).

### 2. Ajouter vos Assets (Contenu)

La logique repose sur le nom des fichiers. Pour associer un contenu à l'image **Index 0**, vos fichiers doivent s'appeler `0`.

Allez dans le dossier `assets/` et déposez vos fichiers en respectant cette nomenclature :

| Type de média | Nom du fichier (Exemple pour Index 0) | Description |
| --- | --- | --- |
| **Vidéo** | `0.mp4` | Vidéo sur fond vert (ou normal). |
| **Audio** | `0.mp3` | Son d'ambiance ou voix off (joué en boucle). |
| **Objet 3D** | `0.glb` | Modèle 3D animé ou fixe. |

*Exemple pour la 2ème image cible (Index 1) :* `1.mp4`, `1.mp3`, `1.glb`.

> **Note :** Vous n'êtes pas obligé de mettre les trois. Vous pouvez mettre juste un MP3, ou juste une vidéo.

### 3. Tester le projet

Le WebAR nécessite obligatoirement un serveur sécurisé (**HTTPS**).

** Hébergez le projet sur **GitHub Pages** (Settings > Pages > Branch: main).

Parfois la mise en ligne depuis Github peux prendre un peu de temps. Attendez que l'encoche verte apparaisse et rechargé votre page web sur le téléphone.

---

## 🎨 Optimisation des Assets (CRUCIAL)

Pour que l'expérience soit fluide sur smartphone (4G/5G), vous **DEVEZ** optimiser vos fichiers.

### 🎥 Vidéos (.mp4)

* **Format :** `.mp4` encodé en **H.264** (Obligatoire pour iPhone). N'utilisez pas H.265/HEVC.
* **Dimensions :** Utilisez des puissances de 2 pour aider la carte graphique.
* Format Carré : `512x512` ou `1024x1024`. (exemple)
* Format 16:9 : restez en `1280x720` MAX !!!. Ensuite fais des testes, si ça lag baisse la résolution. 


* **Piste Audio :** Supprimez la piste audio de la vidéo (Mute) lors de l'export. Le son doit être géré par le fichier `.mp3` séparé. Si tu veux absolument que le son sorte de la vidéo mp4 (pour avoir un calage parfait par exemple) contacte moi.
* **Fond Vert :** Utilisez un vert pur (`R:0 G:255 B:0`) ou ajustez le code (voir plus bas).

### 🔊 Audio (.mp3)

* **Canaux :** Exportez en **Mono** si possible(divise le poids par 2).
* **Fréquence :** **22050 Hz** est suffisant pour des bruitages/voix.
* **Bitrate :** 96 kbps ou 128 kbps max.

### 🧊 3D (.glb)

* **Format :** Utilisez `.glb` (binaire) et non `.gltf`.
* **Poids :** Visez moins de **5 Mo** par modèle.
* **Textures :** "Bakez" les ombres et lumières sur la texture car le moteur de lumière dynamique est désactivé pour la performance.

---

## ⚙️ Réglages Avancés (Dans le code)

Si vous devez modifier le fichier `index.html` :

### Changer le nombre de cibles

Par défaut, le système cherche jusqu'à 10 cibles (0 à 9). Si vous en avez plus :

```javascript
const MAX_TARGETS = 10; // Changez ce chiffre

```

### Ajuster la couleur du fond vert (Chromakey)

Si votre vidéo a un fond vert différent (ex: vert foncé ou une autre couleur), modifiez ces valeurs dans le composant `videoPlane` (vers la ligne 185) :

```javascript
// color: R G B (Valeurs entre 0 et 1)
// Ex: Pour un vert pur (0, 255, 0) -> color: 0 1 0
// Ex: Pour un vert sombre (0, 100, 0) -> color: 0 0.4 0
videoPlane.setAttribute('material', `... color: 0.1 0.9 0.2 ...`);

```

---

## ❓ Dépannage (FAQ)

**La vidéo est noire (écran noir à la place de la vidéo) :**

* C'est un problème d'encodage. Votre vidéo n'est pas en **H.264**. Ré-encodez-la avec un logiciel comme *Handbrake* ou *Shutter Encoder*.

**La vidéo ne s'affiche pas (je vois à travers) :**

* Le shader de transparence est trop agressif ou la couleur verte est mal réglée. Changez la couleur dans le code ou essayez une vidéo avec un vert plus "standard".

**Le son fonctionne mais pas l'image sur Android :**

* Assurez-vous que votre fichier vidéo n'est pas trop lourd (4K interdit !). Restez en 720p maximum.

**L'expérience ne se lance pas (Loading infini) :**

* Vérifiez que vous êtes bien en **HTTPS** (le petit cadenas dans l'URL).
* Vérifiez que vous avez bien nommé vos fichiers `0.mp4` et pas `0.MP4` ou `video0.mp4`.

---

### Crédits

* **Moteur AR :** [MindAR.js](https://github.com/hiukim/mind-ar-js)
* **Moteur 3D :** [A-Frame](https://aframe.io/)
* **Shader Chromakey :** Basé sur les travaux de Nikolai Warner.

Projet Web AR créer avec Gemini 3 Pro.
