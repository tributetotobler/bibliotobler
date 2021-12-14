# bibliotobler
This project aims at analysing Tobler's scientific networks using bibliometrics 

# Data :
- `PoPCites.csv` contient le résultat de la requête *"Waldo T*"* dans Google Scholar obtenu via l'utilitaire [Publish or Perish de Ann-Wil Harzing](https://harzing.com/resources/publish-or-perish). Contenu brut : faux positifs (autres auteurs portant le nom de Tobler) et doublons sont présents dans le résultat.
- `scopus.csv` contient le résultat de la requête *"AU-ID ( "Tobler, Waldo R."   55944307900 )  OR  AU-ID ( "Tobler, Waldo R."   7006438074 )"* dans la base Scopus. Il s'agit des deux formes auteurs associées automatiquement à Waldo Tobler. Certaines publications de Scopus peuvent manquer car ne pas avoir été correctement attribué à l'une de ces deux formes.

# Sources :
-   Base "Base", la requête à effectuer comprendra *aut:'Tobler, W. R.'*. D'autres formes d'auteurs devront sûrement être ajoutées. L'extraction reste à faire.
