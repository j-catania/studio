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
- **Gestion du stockage :** Détection du firmware (2.x / 3.x) via `.md`, suppression et réorganisation des packs dans l'index `.pi`.
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

À la racine de la carte SD (firmwares 2.x et 3.x) :
- `.md` : Métadonnées de l'appareil (version du firmware, numéro de série, clés de chiffrement).
- `.pi` : Index binaire des histoires, une suite d'UUID de 16 octets dans l'ordre d'affichage.
- `.content/` : Un sous-dossier par histoire, nommé d'après les **8 derniers caractères hexadécimaux** de son UUID (ex. `23456789`).

### Transferts

La bibliothèque PC contient un dossier par pack, nommé `<uuid>` (ou `<uuid>.suffixe`), avec les fichiers `ni`, `li`, `ri`, `si`, `rf/` et `sf/` en clair (marqueur `.cleartext`). Comme dans STUdio, le chiffrement est appliqué pendant le transfert :

- **Firmware 2.x :** les 512 premiers octets de chaque fichier sont chiffrés en XXTEA (clé commune) ; le fichier `bt` est dérivé de la clé de l'appareil.
- **Firmware 3.x :** les 512 premiers octets sont chiffrés en AES-CBC avec la clé de l'appareil lue dans `.md` ; `bt` provient de `.md`.
- **Sauvegarde Lunii → PC :** les fichiers sont déchiffrés dans un dossier `<uuid>` de la bibliothèque.
- Les archives `.zip` / `.pack` de la bibliothèque ne sont pas transférables (conversion non portée) ; seuls les packs au format dossier le sont.

---

## Licence

Distribué sous licence MIT. Projet indépendant, non affilié à Lunii SAS.
