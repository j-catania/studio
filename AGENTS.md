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

Référence : driver Java STUdio (`FsStoryTellerAsyncDriver`, `CipherUtils`). Les firmwares 2.x et 3.x partagent la même disposition :

- Racine : `.md` (métadonnées), `.pi` (index des UUID, 16 octets chacun) et `.content/`.
- Dossier d'un pack : 8 derniers caractères hexadécimaux de l'UUID, en majuscules.
- Fichiers `ni`, `nm` et `.cleartext` jamais chiffrés ; `.cleartext` n'est jamais copié.
- **Firmware 2.x** (`.md` version 1-3) : XXTEA sur les 512 premiers octets ; `bt` dérivé de l'UUID de l'appareil et de `ri`.
  - Audio : MP3 mono 44,1 kHz sans tags ID3.
  - Images : bitmap 320×240, 16 couleurs indexées (4-bits), encodage RLE.
- **Firmware 3.x** (`.md` version 6-7) : AES-CBC sur les 512 premiers octets avec la clé de l'appareil ; `bt` issu de `.md`.

---

## 4. Conventions de code

- **Langage :** JavaScript ES6+ moderne (async/await, modules ESM, Web Crypto API, TypedArrays).
- **Gestion des erreurs :** Ignorer silencieusement l'exception `AbortError` (lorsque l'utilisateur ferme la boîte de dialogue de sélection de dossier). Attraper et notifier explicitement toute erreur d'I/O (`DOMException`).
- **Performance de transfert :** Toujours implémenter les transferts de gros volumes de façon asynchrone sans bloquer la boucle d'événements du navigateur pour garantir la fluidité de l'interface utilisateur.
