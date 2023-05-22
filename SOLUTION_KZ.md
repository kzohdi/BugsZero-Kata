# BUGSZERO-KATA KARIM ZOHDI
 
## 1. Nombre minimum et maximum de joueurs
### 1.1. Nombre minimum de joueurs
Une IndexOutOfBoundsException est facilement levée en commentant les 3 lignes suivantes :
```
aGame.add("Chet");
aGame.add("Pat");
aGame.add("Sue");
```
Il y a une méthode qui vérifie le nombre de joueurs minimum mais elle n'est jamais utilisée :
```
public boolean isPlayable() {
	return (howManyPlayers() >= 2);
}
```
Je propose d'utiliser cette méthode avant de "roll" pour pouvoir arrêter le jeu si le nombre de joueurs minimum n'est pas le bon.

### 1.2. Nombre maximum de joueurs
Une IndexOutOfBoundsException est également levée en dupliquant ces mêmes lignes quand on dépasse 5 joueurs.
À première vue on se dit que le code devrait fonctionner pour 6 joueurs vu la taille des tableaux suivants :
```
int[] places = new int[6];
int[] purses  = new int[6];
boolean[] inPenaltyBox  = new boolean[6];
```

Mais ce n'est pas le cas, et donc en regardant de plus près on se rend compte que l'utilisation des indices n'est pas bonne ici :
```
places[howManyPlayers()] = 0;
purses[howManyPlayers()] = 0;
inPenaltyBox[howManyPlayers()] = false;
```
La méthode "howManyPlayers()" retourne la taille de l'ArrayList contenant les joueurs, et donc pour ajouter le 6ème joueur cette même méthode renvoie 6, et places[6] n'existe pas (index de 0 à 5 car tableau de taille 6), d'où l'IndexOutOfBoundException.

Je propose également d'inclure une condition dans la méthode "isPlayable" pour vérifier le nombre maximum de joueurs avant de lancer le jeu.
Il faut également revoir toutes les parties du code où "howManyPlayers()" est utilisé comme indice d'un tableau.

### 1.3. Solution plus optimisée
On peut bien évidemment utiliser "isPlayable" pour arrêter le jeu si le nombre minimum/maximum n'est pas respecté, mais plutôt que le faire en Runtime, on peut biensûr le forcer au niveau du constructeur de la classe Game en lui passant directement le nom des joueurs à ajouter, par exemple :
````
Game aGame = new Game("P1", "P2");
Game aGame = new Game("P1", "P2", "P3");
// ....
Game aGame = new Game("P1", "P2", "P3", "P4", "P5", "P6");
````
Certes on aura à créer plusieurs constructeurs de la classe Game à la main avant de pouvoir les utiliser, mais c'est au moins une façon sûre de forcer la façon dont on veut que notre classe soit créée.
Un exemple de constructeur à 3 joueurs qui utilise le constructeur à 2 joueurs :
````
public Game(String joueur1, String joueur2, String joueur3) {
  this(joueur1, joueur2);
  this.add(joueur3);
}
````
Il faut aussi penser à rendre la méthode add privée car c'est nos constructeurs qui se chargent de l'ajout de joueurs.

## 2. Couplage entre les différentes propriétés
L'utilisation de plusieurs propriétés couplées (joueurs, places, purse et inPenaltyBox) n'est pas du tout bon, on peut très facilement oublier de modifier l'une de ces propriétés dans une des méthodes qui les utilise, et de créer donc un bug dans l'application.

Le mieux est de tout encapsuler dans un objet Player qui aura ces mêmes propriétés.

Cette solution nous permet également de gérer toutes les règles liées au joueur dans la même classe Player, par exemple le fait d'avancer, le fait d'entrer ou sortir de prison, la gestion du score de chaque joueur etc..

## 3. Syntaxes
- La déclaration de la liste de joueurs peut être améliorée :
````
List<Player> players = new ArrayList<>();
`````

- La gestion des questions peut également être améliorée en créant un objet Question avec le titre et le type de la question, ce qui permettra d'avoir une seule liste de question dans la classe Game et éviter d'avoir du code comme : 
````
popQuestions.addLast("Pop Question " + i);
scienceQuestions.addLast(("Science Question " + i));
sportsQuestions.addLast(("Sports Question " + i));
rockQuestions.addLast(createRockQuestion(i));
````
D'ailleurs les trois premières catégories de questions sont ajoutées directement alors que la dernière catégorie est ajouter en utilisant la méthode "createRockQuestion", chose que je trouve pas très nécessaire.

- L'utilisation de plusieurs if dans les méthodes "askQuestion" et "currentCategory" n'est pas très lisible, autant utiliser un switch au lieu du if pour plus de lisibilité ou même une HashMap pour tout stocker plus proprement.

- Les indentations et les espaces sont à revoir, je propose de reformater le code avec l'IDE.

- La comparaison des String se fait avec un equals et non == comme ici :
````
currentCategory() == "Pop"
````

### 4. Gérer des scénarios plus poussés
- Il se passe quoi si un joueur quitte en milieu de partie ?
- Il se passe quoi si les joueurs enchaînent les mauvaises réponses jusqu'à ce qu'il n'y ait plus de questions ?


