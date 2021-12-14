# bibliotobler
This project aims at analysing Tobler's scientific networks using bibliometrics 

# Data :
- `PoPCites.csv` contient le résultat de la requête *"Waldo T*"* dans Google Scholar obtenu via l'utilitaire [Publish or Perish de Ann-Wil Harzing](https://harzing.com/resources/publish-or-perish). Contenu brut : faux positifs (autres auteurs portant le nom de Tobler) et doublons sont présents dans le résultat.
- `scopus.csv` contient le résultat de la requête *"AU-ID ( "Tobler, Waldo R."   55944307900 )  OR  AU-ID ( "Tobler, Waldo R."   7006438074 )"* dans la base Scopus. Il s'agit des deux formes auteurs associées automatiquement à Waldo Tobler. Certaines publications de Scopus peuvent manquer car ne pas avoir été correctement attribué à l'une de ces deux formes.
- Le dossier `wos` contient l'extraction des données de la base Web of Science au format .ris (Zotero) et au format texte délimité. Les données en texte délimité comprennent aussi les références citées. Lien de la requête : [https://www.webofscience.com/wos/woscc/summary/d14383cb-5237-4aeb-b847-c6e9ad33fbd9-191e3645/relevance/1](https://www.webofscience.com/wos/woscc/summary/d14383cb-5237-4aeb-b847-c6e9ad33fbd9-191e3645/relevance/1). Regroupement de 3 formes auteur. Les publications de Tobler, WE ont été exclues.
![image](https://user-images.githubusercontent.com/57678444/146061389-550b2487-5525-4653-b67b-6421453c80ff.png)


# Sources :
-   Base "Base", la requête à effectuer comprendra *aut:'Tobler, W. R.'*. D'autres formes d'auteurs devront sûrement être ajoutées. L'extraction reste à faire.
