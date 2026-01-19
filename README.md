-----------------------------------------
### Projet : Europresse to IRaMuTeQ - Appli streamlit
- Auteur : Stéphane Meurisse
- Contact : stephane.meurisse@gmail.com
- Site Web : https://www.codeandcortex.fr
- LinkedIn : https://www.linkedin.com/in/st%C3%A9phane-meurisse-27339055/
- Date : 17 janvier 2026
- Version : 4
- Licence : Ce programme est un logiciel libre : vous pouvez le redistribuer selon les termes de la Licence Publique Générale GNU v3

-----------------------------------------
### Librairies 
- pip install streamlit beautifulsoup4 pandas lxml html5lib

-----------------------------------------
### Licence
Cette application est distribuée sous la licence GNU v3.  
Toute réutilisation ou modification de ce projet doit respecter les termes de cette licence.  
L'exploitation commerciale de ce projet est interdite sans autorisation.

-----------------------------------------
### Fonctionnalités
- Reconstruction des noms de journaux (version longue et abrégée).
- Conversion des dates en plusieurs formats.
- Nettoyage de multiples balises inutiles, URL et noms d’auteurs
- Export au format texte et CSV.

-----------------------------------------
### Fonctionnement du script (règles et options)

Cette application Streamlit lit un fichier HTML exporté depuis Europresse, extrait chaque article, nettoie le contenu et construit
un corpus compatible IRaMuTeQ. Les sorties sont générées en **TXT**, **CSV** et **XLSX** (dans un ZIP). Les règles principales sont
décrites ci-dessous.

#### 1) Détection et parsing des dates
- La fonction `parser_date` convertit une date française (ex. `31 décembre 2024`) ou anglaise (ex. `June 13, 2024`) en objet `datetime`.
- Un dictionnaire mappe les mois français vers les mois anglais pour permettre l’usage de `datetime.strptime`.
- Les dates sont ensuite converties en trois formats IRaMuTeQ :
  - `*date_YYYY-MM-DD` (année-mois-jour)
  - `*am_YYYY-MM` (année-mois)
  - `*annee_YYYY` (année)

#### 2) Extraction du nom du journal
- Le nom du journal est récupéré dans `div.rdp__DocPublicationName > span.DocPublicationName`.
- Il est normalisé via `nettoyer_nom_journal` :
  - suppression de tout ce qui suit une virgule,
  - remplacement des espaces/apostrophes par `_`,
  - préfixe `*source_` pour IRaMuTeQ.
- Deux méthodes existent :
  - **Méthode normale (0)** : extraction directe du texte (formatage minimal),
  - **Méthode clean (1)** : extraction par liste de fragments (`stripped_strings`) pour éviter des résidus de mise en forme.

#### 3) Extraction et nettoyage du contenu article
Pour chaque `<article>` :
- Le texte brut est récupéré via `article.get_text` puis nettoyé. Les sections non pertinentes (header, aside, footer, images, liens)
  sont supprimées. Des balises `<i>` et `<em>` sont conservées mais déroulées (unwrap).
- Option **expérimentale** : suppression conditionnelle de blocs contenant des rubriques (ex. “Edito”, “Opinions”, “Débats”, ...)
- Le titre est conservé dans le texte final (pas de suppression de la balise titre).
- Les mentions de journal/date brut sont retirées du texte (`re.sub`).
- Les sauts de lignes sont aplatis, les URLs “(lien : …)” sont retirées, et la première ligne reçoit un point final si besoin.

#### 4) En-tête IRaMuTeQ et construction du corpus
- Chaque article est précédé d’une ligne “étoilée” commençant par `****`, puis des variables (`*source_`, `*date_`, `*am_`,
  `*annee_`, variable utilisateur).
- Le corpus final est la concaténation des articles avec un saut de ligne entre chaque bloc.

#### 5) Détection des doublons (optionnel)
- Activée via “Recherche de doublons”.
- Un hash SHA-256 est calculé sur le corps de chaque article pour identifier les doublons. Le plus long article est conservé si
  deux articles partagent le même hash.
- Les articles “trop courts” sont listés si leur nombre de mots est inférieur au seuil configurable (300 mots par défaut).
- L’utilisateur peut exporter un corpus sans doublons et/ou sans articles courts. Le texte est reconstruit à partir des articles
  retenus.

### Aide regex

Ces motifs s’utilisent dans le champ Regex additionnelle (sans préfixe r) pour filtrer davantage les balises lors du nettoyage expérimental.
Principales règles

  -`Début/fin de ligne : ^ (début), $ (fin).`
  -`Ex. ^Le Figaro (commence par “Le Figaro”).`
  -`Alternatives (OU) : motif1|motif2.`
  -`Ex. \bPage\s*3\b|\bAnnexe\b.`

Exemples utiles

  -`Supprimer “Page 3”, “Page 12” : \bPage\s*\d+\b`



#### 6) Exports générés
- **TXT** : corpus IRaMuTeQ complet.
- **CSV** : colonnes `Journal`, `Année-mois-jour`, `Année-mois`, `Année`, `Article`.
- **XLSX** : export Excel des mêmes colonnes (via pandas).
- Les trois fichiers sont dans un ZIP, nommé à partir du fichier HTML d’origine.

-----------------------------------------
### Utilisation
1. Rendez-vous sur l'application [Europresse to IRaMuTeQ] : https://europresse-to-iramuteq.streamlit.app/
2. Glissez-déposez vos fichiers HTML pour les traiter.
3. Téléchargez les résultats au format texte ou CSV.
