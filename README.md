# Lunii Web Studio (V2 & V3)

Application web autonome et statique conçue pour explorer, transférer et sauvegarder des packs d'histoires sur les boîtes à histoires Lunii (générations V2 et V3 en mode Mass Storage), sans installer Java ni exécuter de serveur local.

Ce projet s'inspire du fonctionnement de [STUdio](https://github.com/marian-m12l/studio) en tirant parti de la **File System Access API** des navigateurs modernes.

---

## Fonctionnalités

- **Sans serveur / 100 % client :** S'exécute directement dans le navigateur depuis un simple fichier `index.html` ou un hébergement statique (GitHub Pages, Cloudflare Pages).
- **Double explorateur :** Visualisation côte à côte de la bibliothèque locale (ordinateur) et du stockage de la Lunii.
- **Transfert bidirectionnel :**
  - Copie récursive de packs du PC vers la carte SD de la Lunii.
  - Extraction et sauvegarde de packs depuis la Lunii vers le PC.
- **Gestion du stockage :** Détection du modèle (V2 avec `md`, V3 avec `.md`) et suppression directe de packs.
- **Suivi en direct :** Indicateur d'état et affichage des fichiers traités en temps réel pendant les transferts.

---

## Prérequis et compatibilité

L'application repose sur la **File System Access API** (`showDirectoryPicker`) pour lire et modifier les répertoires locaux.

| Navigateur | Support |
| :--- | :--- |
| **Google Chrome / Chromium** | ✅ Pris en charge nativement |
| **Microsoft Edge** | ✅ Pris en charge nativement |
| **Brave** | ✅ Pris en charge nativement |
| **Firefox** | ❌ Non supporté (API non implémentée par Mozilla) |
| **Safari** | ❌ Non supporté |

> **Compatibilité matérielle :**
> - **Lunii V2 & V3 :** Fonctionne directement via le montage du stockage de masse USB (clé USB / carte SD).
> - **Lunii V1 :** Non supportée (protocole USB brut / `libusb`, nécessite WebUSB).

---

## Utilisation

1. Télécharge ou clone ce dépôt.
2. Ouvre le fichier `index.html` dans Chrome ou Edge (ou sers-le via n'importe quel serveur statique local si besoin).
3. **Bibliothèque PC :** Clique sur **« Choisir le dossier »** à gauche et sélectionne le dossier contenant tes packs d'histoires.
4. **Conteuse Lunii :**
   - Branche ta Lunii en USB.
   - Clique sur **« Connecter la Lunii »** à droite et sélectionne la racine du disque amovible (contenant les répertoires `.content` ou `content`).
5. Transfère ou sauvegarde tes histoires en cliquant sur les boutons d'action dédiés.

---

## Structure des données Lunii

À la racine de la carte SD :
- `.content/` ou `content/` : Répertoire contenant chaque histoire dans un sous-dossier portant son identifiant (UUID).
- `md` (Lunii V2) ou `.md` (Lunii V3) : Fichier binaire de métadonnées faisant office d'index des histoires reconnues par le microcontrôleur.

---

## Licence

Distribué sous licence MIT. Projet indépendant, non affilié à Lunii SAS.
