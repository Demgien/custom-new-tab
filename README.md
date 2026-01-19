# 📰 Custom News Dashboard - Page d'Accueil Firefox

Page HTML personnalisée pour afficher vos flux RSS d'actualités : monde, tech/IT, et releases/dev.

## ✨ Fonctionnalités

- **3 colonnes de news** : Actualités Monde, Tech & IT, Releases & Dev
- **Interface de configuration** : Ajoutez/supprimez des flux RSS sans toucher au code
- **Thème sombre/clair** : Basculez entre les thèmes avec le bouton 🌓
- **Rafraîchissement auto** : Actualisation toutes les 10 minutes
- **Sauvegarde automatique** : Vos préférences sont persistées dans le navigateur
- **Design responsive** : S'adapte à toutes les tailles d'écran

## 🚀 Installation

### 1. Ouvrir le fichier localement

```bash
firefox /home/damien/git/scripts/custom-new-tab/index.html
```

### 2. Définir comme page d'accueil Firefox

**Méthode 1 : Via les paramètres**
1. Ouvrez Firefox
2. Allez dans `Menu ☰` → `Paramètres` → `Accueil`
3. Dans "Nouvelles fenêtres et nouveaux onglets", choisissez `Adresses web personnalisées`
4. Collez le chemin : `file:///home/damien/git/scripts/custom-new-tab/index.html`

**Méthode 2 : Via about:config**
1. Tapez `about:config` dans la barre d'adresse
2. Cherchez `browser.startup.homepage`
3. Définissez : `file:///home/damien/git/scripts/custom-new-tab/index.html`
4. Cherchez `browser.newtab.url` (si disponible)
5. Définissez la même valeur

**Méthode 3 : Avec l'addon New Tab Override**
1. Installez [New Tab Override](https://addons.mozilla.org/fr/firefox/addon/new-tab-override/)
2. Configurez l'URL locale : `file:///home/damien/git/scripts/custom-new-tab/index.html`

⚠️ **Note CSP** : Si la page se charge mais les flux RSS ne fonctionnent pas (erreurs CSP dans la console), utilisez plutôt la **Méthode 4** ci-dessous.

**Méthode 4 : Serveur HTTP local (recommandé pour éviter les problèmes CSP)**

Lancez un serveur HTTP simple dans le dossier :

```bash
cd /home/damien/git/scripts/custom-new-tab
python3 -m http.server 8080
```

Puis configurez New Tab Override avec : `http://localhost:8080/index.html`

Pour automatiser le démarrage du serveur au boot :

```bash
# Créer un service systemd
sudo tee /etc/systemd/system/custom-new-tab.service <<EOF
[Unit]
Description=Custom New Tab HTTP Server
After=network.target

[Service]
Type=simple
User=$USER
WorkingDirectory=/home/damien/git/scripts/custom-new-tab
ExecStart=/usr/bin/python3 -m http.server 8080
Restart=on-failure

[Install]
WantedBy=multi-user.target
EOF

# Activer et démarrer
sudo systemctl enable custom-new-tab.service
sudo systemctl start custom-new-tab.service
```

## 🛠️ Personnalisation

### Ajouter/Modifier des flux RSS via l'interface

**Méthode recommandée** : Utilisez le bouton **⚙️ Configuration** directement dans la page !

1. Cliquez sur le bouton **⚙️ Configuration**
2. Dans chaque section (🌍 Monde, 💻 Tech, 🚀 Dev) :
   - Remplissez le **nom du flux** (ex: "Le Monde")
   - Collez l'**URL du flux RSS**
   - Cochez **"Utiliser le proxy CORS"** si nécessaire (voir note ci-dessous)
   - Cliquez sur **+ Ajouter**
3. Pour supprimer un flux, cliquez sur **🗑️ Supprimer** à côté du flux
4. Cliquez sur **💾 Enregistrer et fermer**

**Note sur le proxy CORS** :
- ✅ **Coché** : Pour la plupart des flux (Le Monde, TechCrunch, GitHub, etc.)
- ⬜ **Décoché** : Pour les flux qui acceptent déjà CORS (Hacker News, Reddit, Dev.to)
- Si un flux ne charge pas, essayez de basculer cette option

**Réinitialisation** : Le bouton **🔄 Réinitialiser par défaut** restaure les flux d'origine.

### Édition manuelle avancée

Si vous préférez modifier directement le code, éditez `index.html` et modifiez `DEFAULT_RSS_FEEDS` (ligne ~241) :

```javascript
const DEFAULT_RSS_FEEDS = {
    world: [
        { url: 'https://...', name: '...', proxy: true },
    ],
    // ...
};
```

### Flux RSS recommandés

**Tech/IT :**
- Hacker News : `https://news.ycombinator.com/rss`
- Ars Technica : `https://feeds.arstechnica.com/arstechnica/index`
- Silicon.fr : `https://www.silicon.fr/feed`
- Next INpact : `https://www.nextinpact.com/rss/news.xml`

**Dev/Releases :**
- GitHub Blog : `https://github.blog/feed/`
- Dev.to : `https://dev.to/feed`
- Changelog : `https://changelog.com/feed`
- Releases GitHub spécifiques : `https://github.com/{user}/{repo}/releases.atom`

**Actualités Monde :**
- Le Monde : `https://www.lemonde.fr/rss/une.xml`
- France 24 : `https://www.france24.com/fr/rss`
- BBC : `https://feeds.bbci.co.uk/news/world/rss.xml`

### Changer les couleurs

Modifiez les variables CSS dans `:root` (ligne ~13) :

```css
:root {
    --accent: #3b82f6;  /* Couleur principale */
    /* ... autres couleurs */
}
```

## 🔧 Fonctionnalités

- **🔄 Rafraîchir** : Recharge tous les flux manuellement
- **🌓 Thème** : Bascule entre mode sombre et clair (sauvegardé automatiquement)
- **⚙️ Configuration** : Interface complète pour gérer vos flux RSS
  - Ajouter des flux personnalisés
  - Supprimer des flux existants
  - Régler les paramètres de proxy CORS
  - Réinitialiser aux valeurs par défaut

## 📝 Notes

- La page utilise `allorigins.win` comme proxy CORS pour les flux qui le nécessitent
- Le rafraîchissement automatique se fait toutes les 10 minutes
- Les flux affichent les 15 articles les plus récents par catégorie
- **Sauvegarde locale** : Vos configurations sont stockées dans le localStorage du navigateur
- Les préférences persistent entre les sessions (thème + flux RSS personnalisés)

## 🐛 Dépannage

**Problème CSP avec New Tab Override (flux RSS bloqués) :**

Si vous voyez des erreurs comme `Content Security Policy` dans la console Firefox :

✅ **Solution recommandée** : Utilisez un serveur HTTP local (voir Méthode 4 dans Installation)

Pourquoi ? Les fichiers `file://` ont des restrictions de sécurité plus strictes que `http://localhost`.

**Alternative rapide** : Serveur temporaire
```bash
cd /home/damien/git/scripts/custom-new-tab
python3 -m http.server 8080
# Puis configurez New Tab Override : http://localhost:8080/index.html
```

**Les flux ne se chargent pas :**
- Vérifiez votre connexion internet
- Certains flux peuvent être temporairement indisponibles
- Via **⚙️ Configuration**, essayez de cocher/décocher "Utiliser le proxy CORS"
- Testez l'URL du flux RSS dans votre navigateur pour vérifier qu'elle est valide

**Mes modifications ont disparu :**
- Les configurations sont sauvegardées dans le localStorage du navigateur
- Si vous effacez les données du navigateur, vos configurations seront perdues
- Utilisez **🔄 Réinitialiser par défaut** pour restaurer les flux d'origine

**La page ne s'affiche pas comme nouvel onglet :**
- Utilisez l'addon "New Tab Override" pour forcer l'URL locale
- Vérifiez que le chemin du fichier est correct

## 🎨 Captures d'écran

La page affiche :
- Horloge en temps réel
- 3 colonnes scrollables
- Compteur d'articles par catégorie
- Tags de source pour chaque article
- Descriptions tronquées avec lien vers l'article complet

Enjoy your custom news dashboard! 🚀
