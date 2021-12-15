# 1. bibliotobler
This project aims at analysing Tobler's scientific networks using bibliometrics 

# 2. Data :

- `PoPCites.csv` contient le résultat de la requête *"Waldo T*"* dans Google Scholar obtenu via l'utilitaire [Publish or Perish de Ann-Wil Harzing](https://harzing.com/resources/publish-or-perish). Contenu brut : faux positifs (autres auteurs portant le nom de Tobler) et doublons sont présents dans le résultat.
- `scopus.csv` et `scopus.ris` contiennent le résultat de la requête *"AU-ID ( "Tobler, Waldo R."   55944307900 )  OR  AU-ID ( "Tobler, Waldo R."   7006438074 )"* dans la base Scopus. Il s'agit des deux formes auteurs associées automatiquement à Waldo Tobler. Certaines publications de Scopus peuvent manquer car elles peuvent ne pas avoir été correctement attribuée à l'une de ces deux formes. Inversement, il peut se trouver des faux positifs dans ces deux formes. Format Zotero et texte délimité.
- Le dossier `wos` contient l'extraction des données de la base Web of Science au format .ris (Zotero) et au format texte délimité. Les données en texte délimité comprennent aussi les références citées. Lien de la requête : [https://www.webofscience.com/wos/woscc/summary/d14383cb-5237-4aeb-b847-c6e9ad33fbd9-191e3645/relevance/1](https://www.webofscience.com/wos/woscc/summary/d14383cb-5237-4aeb-b847-c6e9ad33fbd9-191e3645/relevance/1). Regroupement de 3 formes auteur. Les publications de Tobler, WE ont été exclues. La table adresse obtenue à partir de NETSCITY sur la base de ce corpus est aussi présente dans ce dossier.
![image](https://user-images.githubusercontent.com/57678444/146061389-550b2487-5525-4653-b67b-6421453c80ff.png)
- `lens-export.csv` contient le résultat de la requête *Tobler* dans la base Lens après application des filtres : *Author Display Name = ( Tobler , Waldo R Tobler , W Tobler )*. Attention, il y a des faux positifs en raison de l'intégration des Tobler sans prénom. En tout 252 travaux académiques sont compris dans ce corpus.
- Le dossier `references_citee` comprend les métadonnées de l'ensemble des références citées par les documents de la requête Scopus au format Zotero et texte délimité.
- Un fichier zippé `istex` contient le corpus résultant de la requête "author.name:"Tobler" AND publicationDate:[1940 \*]". Attention aux faux positifs car le prénom n'a pas été précisé. 656 documents en tout avec plein texte et métadonnées enrichies. Sont disponibles les pdf océrisés en TEI. Les métadonnées enrichies au format JSON et XML. Le tout fait plus de 600 Mo et n'a donc pas été pushé sur le Git. Il devra être mis à disposition sur l'espace Sharedocs.


Le petit fichier word et pdf d'analyse exploratoire comprend des captures d'écran des analyses disponibles en lignes sur les corpus WoS et Scopus.
 


# 3. Sources :
-   Base "Base", la requête à effectuer comprendra *aut:'Tobler, W. R.'*. D'autres formes d'auteurs devront sûrement être ajoutées. L'extraction reste à faire.

# 4. Méthode de travail

Hypothèse : la base Zotero recense normalement toutes les publications de Tobler (hypothèse), même les preprints, et diverses formes de communication (powerpoints, figures). 
Anne-Christine a aspiré les notices bibliographiques dans zotero depuis la base crossref par les doi. Mais crossref est moins complète que scopus qui permet d'avoir en plus les affiliations. 
NB : L'export au format csv zotero perd les liens connexes, or les différents "produits" du corpus sont pour partie liés entre eux (des images ou des présentations liés à des publis). Il faudra les reprendre.

Cette base a été précédemment travaillée par Colette Cauvin (CC) qui a ajouté les éléments suivants : 
- **Call number** : *CC4* par exemple qui indique le lieu de rangement du papier dans les archives de CC
- **Extra** : indique parfois la cote de rangement du papier dans les archives de CC
- **Notes** : des résumés personnels de CC. (qui contiennent alors la mention *Résumé C. Reymond-Cauvin*) ou bien des remarques concernant le document et sa présence/absence dans le stuff.
- **Manual Tags** : marqueurs, qui sont de 2 catégories
  - un pour indiquer l'emplacement du fichier dans les archives de CC, et il commence par *Loc* toujours. Le marqueur  particulier **LocCC-ABS** indique que le papier n'est pas actuellement trouvé en format PDF (vérifier dans le stuff ? vérifier sur le Web ?). 
  - plusieurs autres séparés par des points-virgule (4 max) pour situer le thème général de l'article d'après CC 


Pour les documents sous format papier ou PDF scanné uniquement, nous pourrons nous appuyer sur le projet [paperETL](https://github.com/neuml/paperetl/) et les outils d'humanum qui dispose d'un service en ligne pour l'ocerisation de PDF.

Voici la chaîne de traitement pour extraire assez facilement les résumés des papiers dont nous disposerons dans ce cas : 
```
scan de papier -> humanum tool
                    -> PDF océrisé 
                        -> grobid 
                            -> fichier TEI 
                                -> paperETL 
                                    -> decomposition du papier (metadata + contenu) en format JSON
                                    -> insertion des (metadata + contenu) dans une base sqlite 
                                    -> export en CSV des (metadata + contenu) dans une base papersETLdata.csv
```

Les metadonnées contiennent les champs suivants
- le titre
- les auteurs
- les affiliations
- la date de publication
- la réference DOI

## 4.1. Etapes

### 4.1.1. Constitution de la base de données


Proposition : avant de nettoyer une base de données, d'abord collecter toutes les données de chaque source dans un fichier ASCII différent. Chaque source (Zotero, scopus, paperETL et wos) doivent être alignées par la suite pour leur comparaison et l'examen des manquants, des doublons ou des informations contradictoires : quel champs correspond entre chaque base ?

1. Exporter la base **zotero** en fichier csv : `data/Zotero_Tobler.csv`
2. Interroger **scopus** pour in fine compléter la base Zotero : `data/scopus.csv`
3. Interroger **wos** pour in fine compléter la base Zotero : `data/wos/savedrecs_without_WE.csv`
4. Interroger **google scholar** pour in fine compléter la base Zotero : `data/PoPCites.csv`
5. Interroger **istex** pour récupérer les papiers PDF si possible (API.istex.fr permet de récupérer des papiers de Tobler au format Tei.) : `data/istext/fichiers TEI`
6. Faire le bilan des manquants par rapport au listing Zotero. En particulier avec le marqueur *Lo- CC-ABS*. 
7. Chercher les manquants dans le stuff et ou le centre de Doc de Migrinter.  Et les ocriser via humanum. Les placer dans `data/OCR/fichiers PDF océrisés`
8. Traiter ces documents (`data/istext/fichiers TEI` et `data/OCR/fichiers PDF océrisés`) avec le programme python PaperETL pour les serialiser dans 2 formats : 
   -  `data/papersETLdata.csv` 
   -  `data/papersETLdata.sqlite`
9. Analyser les divergences entre bases. Créer une base harmonisée. 
10. Vérifier qu'il n'existe pas non plus des docs dans stuff absents de zotero


Les exports de **papersETLdata** proposent des métadonnées qui sont exportés dans ces colonnes : 

        - "Id": "TEXT PRIMARY KEY",
        - "Source": "TEXT",
        - "Published": "DATETIME",
        - "Publication": "TEXT",
        - "Authors": "TEXT",
        - "Affiliations": "TEXT",
        - "Affiliation": "TEXT",
        - "Title": "TEXT",
        - "Tags": "TEXT",
        - "Reference": "TEXT",
        - "Entry": "DATETIME",
Le contenu contient le résumé dans une section à part qui a pour nom "ABSTRACT"

Par exemple, les exports de **Zotero** et de **wos** en format CSV décrivent les notices avec cette liste d'attributs : 
```
Key	Item Type	Publication Year	Author	Title	Publication Title	ISBN	ISSN	DOI	Url	Abstract Note	Date	Date Added	Date Modified	Access Date	Pages	Num Pages	Issue	Volume	Number Of Volumes	Journal Abbreviation	Short Title	Series	Series Number	Series Text	Series Title	Publisher	Place	Language	Rights	Type	Archive	Archive Location	Library Catalog	Call Number	Extra	Notes	File Attachments	Link Attachments	Manual Tags	Automatic Tags	Editor	Series Editor	Translator	Contributor	Attorney Agent	Book Author	Cast Member	Commenter	Composer	Cosponsor	Counsel	Interviewer	Producer	Recipient	Reviewed Author	Scriptwriter	Words By	Guest	Number	Edition	Running Time	Scale	Medium	Artwork Size	Filing Date	Application Number	Assignee	Issuing Authority	Country	Meeting Name	Conference Name	Court	References	Reporter	Legal Status	Priority Numbers	Programming Language	Version	System	Code	Code Number	Section	Session	Committee	History	Legislative Body
```

Dans cette liste, **Author** doit être analysé pour retirer les faux Tobler, **Abstract** peut souvent être récupéré d'autres sources pour compléter la base zotero harmonisée.


La base harmonisée peut s'appeler 'zotero_harmonised' et contiendra en plus les attributs décrivant  indiquant : 
- la provenance de l'abstract : source_of_abstract (scopus, wos, google scholar, istex, PDF, etc.)
- la provenance des tags automatiques : source_of_Automatic_Tags
- la cote ou chemin de rangement dans les archives (CC ou stuff) du papier scanné : store_location
- une raison de l'absence du papier : remark_about_missing_PDF
- une remarque autre : remark_other
- la note de résumé de CC : CC_abstract
- les marqueurs de CC dédoublonnés : CC_manual_tags
- si la notice est parfaitement nettoyée (résolution des non-concordances pour les noms d'auteurs, de pages, nom de publication, etc.) : is_notice_clean



###  4.1.2. Piste d'analyse : 

- Analyser la correspondance entre les marqueurs de Colette Cauvin (*CC_manual_tags* comme - CC-flux) et les abstracts et tag automatics (*Automatic Tags*). 
- Qui inspire Tobler ? Ses références ?
- Qui cite Tobler ? L'héritage scientifique. 
- Les mutations des idées de Tobler durant sa vie et ensuite ? 

#### 4.1.2.1. liste des tags manuels de - CC

 ⛔ No DOI found : quand le DOI n'a pu être trouvé

Ils doivent aussi être dédoublonnés

- azimuthal directions
- Azimuthal projections
- Bivariate interpolation
- CC-Analyse spatiale
- CC-Analyse_surface
- CC-Anamorphose (sauf cartogramme)
- CC-autres themes
- CC-BDL
- CC-Cartogramme
- CC-Cartographie_analytique
- CC-Concept_cartographie
- CC-Déplacement-Transports
- CC-Enseignement_cartographie
- CC-Filtrage
- CC-Flux
- CC-Généralisation
- CC-Geographie, cartographie, etc.
- CC-Histoire_cartographie
- CC-Image, carte, etc.
- CC-Lissage, interpolation
- CC-Loi de Tobler
- CC-Lois géographiques
- CC-Migration, Flux
- CC-Modelisation (mouvement)
- CC-Mouvement et Migration
- CC-Mouvements
- CC-Photointerprétation
- CC-programmes
- CC-Réflexion-informatique
- CC-SIG (GIS)
- CC-Systeme_projection
- CC-Transformation
- CC-Visualisation
- computer cartography
- Density estimation
- density smoothing
- Dirichlet integral
- equal area maps
- geographic movement
- geographical movement
- Helmholz equation
- interruption
- LEAST SQUARES
- MAP COMPARISON
- map projections
- migration theory
- Numerical methods
- Numerical methods
- Population density
- quadratic programming
- quadratic programming
- simplifying coordinates
- simplifying coordinates
- spatial interaction
- thematic maps
- uniformizing maps
- world maps
- CC-Analyse spatiale
- CC-Anamorphose (sauf cartogramme)
- CC-BDL
- CC-Cartogramme
- CC-Déplacement-Transports
- CC-Enseignement_cartographie
- CC-Filtrage
- CC-Généralisation
- CC-Geographie, cartographie, etc.
- CC-Histoire_cartographie
- CC-Image, carte, etc.
- CC-Lissage, interpolation
- CC-Loi de Tobler
- CC-Modelisation (mouvement)
- CC-Modelisation mathématique
- CC-Mouvements
- CC-programmes
- CC-Réflexion-informatique
- CC-SIG (GIS)
- CC-Systeme_projection
- classification
- distance
- Migrations
