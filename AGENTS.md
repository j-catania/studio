# Directives pour agents IA (AGENTS.md)

Ce document décrit les contraintes techniques, les choix d'architecture et les règles de développement pour toute modification ou extension du projet **Lunii Web Studio**.

---

## 1. Principes d'architecture

- **Zéro backend :** Tout le code doit s'exécuter strictement côté client dans le navigateur. Ne jamais introduire de dépendance nécessitant un runtime serveur (Node.js, Bun, Python, Go) à l'exécution.
- **Zéro bundler obligatoire :** Conserver la capacité de tester et distribuer le projet via un fichier HTML autonome intégrant le CSS (via Tailwind CDN) et les modules JavaScript natifs (ESM).
- **API système :** Tous les accès aux disques (PC et Lunii) doivent passer par la spécification standard **File System Access API** (`window.showDirectoryPicker`, `FileSystemDirectoryHandle`, `FileSystemFileHandle`).

---

## 2. Règles sur la File System Access API

1. **Gestes utilisateur :** Les méthodes `showDirectoryPicker()` et `showOpenFilePicker()` ne peuvent être déclenchées que par une interaction directe (clic sur un bouton). Ne jamais tenter de les appeler au chargement de page.
2. **Permissions et verrous :**
   - Demander le mode `'readwrite'` lors de l'acquisition des handles.
   - Toujours fermer les flux d'écriture (`writable.close()`) pour libérer les verrous de fichiers sur la carte SD.
3. **Parcours récursif :**
   - Manipuler les dossiers via les itérateurs asynchrones (`for await (const entry of handle.values())`).
   - Pour la copie récursive, recréer manuellement les dossiers et sous-fichiers avec les options `{ create: true }`.

---

## 3. Spécifications du matériel Lunii

Toute logique bas niveau ajoutée doit respecter le formatage attendu par le firmware Lunii :

- **Lunii V2 :**
  - Dossier de données : `.content` ou `content`
  - Index binaire : `md`
  - Audio : MP3 ou Vorbis échantillonné à 44,1 kHz (ou WAV mono 32 kHz).
  - Images : Format bitmap 320×240, 16 couleurs indexées (4-bits).
- **Lunii V3 :**
  - Fichier d'indexation préfixé par un point : `.md`
  - Métadonnées et contrôles de sécurité alignés sur le firmware V3.

---

## 4. Conventions de code

- **Langage :** JavaScript ES6+ moderne (async/await, modules ESM, Web Crypto API, TypedArrays).
- **Gestion des erreurs :** Ignorer silencieusement l'exception `AbortError` (lorsque l'utilisateur ferme la boîte de dialogue de sélection de dossier). Attraper et notifier explicitement toute erreur d'I/O (`DOMException`).
- **Performance de transfert :** Toujours implémenter les transferts de gros volumes de façon asynchrone sans bloquer la boucle d'événements du navigateur pour garantir la fluidité de l'interface utilisateur.
