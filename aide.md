# Aide (minimaliste)

## Fonctions (code)

### `parser_date(raw_date_str)`
- Convertit une date en français/anglais en `datetime` (ex. "31 décembre 2024", "June 13, 2024").
- Retourne `None` si le parsing échoue.

### `nettoyer_nom_journal(nom_journal)`
- Normalise le nom du journal et retourne un tag `*source_*`.
- Remplace espaces/apostrophes par `_` et coupe après la première virgule.

### `extraire_texte_html(...)`
- Parse le HTML Europresse et reconstruit :
  - le texte final au format IRaMuTeQ,
  - une table CSV,
  - une liste d’articles pour la détection de doublons.
- Applique les options : source, dates, variable supplémentaire, méthode d’extraction, type de contenu, suppression de balises expérimentale.

### `afficher_interface_europresse()`
- Construit l’interface Streamlit (upload, options, exports, aperçu, ZIP).

### `compter_mots(texte)`
- Compte les mots d’un texte via regex.

### `detecter_doublons_articles(articles, longueur_minimale=...)`
- Détecte les doublons par hash du corps d’article.
- Signale aussi les articles trop courts (< longueur minimale).

### `reconstruire_texte(articles)`
- Reconstruit le corpus à partir d’articles filtrés (sans doublons/courts).

### `extraire_apercu(article, longueur=200)`
- Génère un court aperçu pour affichage dans l’UI (doublons/courts).

## Options (interface)

### Upload
- **Importer un fichier HTML Europresse** : charge le fichier source à traiter.

### Options de métadonnées (ligne `****`)
- **Inclure le nom du journal** (`*source_*`).
- **Inclure la date (année-mois-jour)** (`*date_YYYY-MM-DD`).
- **Inclure la date (année-mois)** (`*am_YYYY-MM`).
- **Inclure l'année uniquement** (`*annee_YYYY`).
- **Variable supplémentaire (optionnel)** (`*votre_tag`).

### Méthode d’extraction du journal
- **Méthode normale** : extraction brute du nom du journal.
- **Méthode clean** : extraction avec nettoyage/formatage du nom du journal.

### Nettoyage expérimental
- **Supprimer les balises contenant “Edito”, “AUTRE”, ...** : nettoyage supplémentaire (expérimental).

### Contenu à exporter
- **Texte complet**
- **Titre uniquement**
- **Chapeau uniquement**
- **Titre + chapeau**

### Recherche de doublons
- **Recherche de doublons** : active la détection par hash.
- **Longueur minimale (mots)** : seuil pour signaler les articles trop courts.

### Export
- **Exporter le corpus sans doublons** : supprime les doublons détectés.
- **Exporter le corpus sans les articles trop courts** : filtre par longueur minimale.
- **Télécharger les fichiers (ZIP)** : génère `.txt`, `.csv`, `.xlsx` dans un ZIP.
