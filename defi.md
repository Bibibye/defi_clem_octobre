Bonjour à tous !

Pour ce  mois d'octobre, je vous présente un défi de programmation
assez classique : créer une calculatrice. Ce défi s'adresse à des
personnes de tous niveaux, et je pense qu'un débutant devrait être capable
de faire au moins le niveau 1 (si ce n'est pas le cas, n'hésitez pas à
poser vos questions !). Les niveaux 2 et 3 explorent des algorithmes et
des méthodes d'analyse syntaxique plus complexes, mais beaucoup de détails
seront donnés pour aider le lecteur à réaliser sa calculatrice.

[[information]]
| Pour chaque niveau, l'objectif sera indiqué dans un bloc d'information
| comme celui-ci. Si vous ne voulez pas de mes indications, vous pouvez
| sauter directement à ces blocs. De plus, vous n'êtes pas obligé de suivre
| mes indications jusqu'au bout. Je vous invite après chaque étape à essayer
| de trouver la solution par vous même.

Le langage est libre, vous pouvez donc utiliser celui avec lequel vous êtes le
plus à l'aise. Quant à la forme, l'idéal est de rester sur un programme
textuel (pas d'interface graphique), de manière à se concentrer sur le
coeur du problème et à faciliter le test de votre programme pour les autres
participants. C'est bon, vous avez choisi votre langage et ouvert votre
IDE/éditeur de texte ? Alors c'est parti !

#Niveau 1 : La calculatrice Polonaise

La plus grande difficulté lors de l'écriture d'une calculatrice, c'est la
gestion des priorités des opérations. Pour ce premier niveau, nous allons
contourner le problème en réalisant une calculatrice en notation Polonaise
inverse (NPI).

[[question]]
| En quoi ?

La NPI est une notation dite "post-fixée", c'est à dire que les opérateurs
sont toujours écris __après__ les opérandes. L'avantage est qu'il n'y a plus
besoin de parenthèses, et que tous les opérateurs ont la même priorité.
Concrètement, si on veut écrire `2 + 3` en NPI, on place simplement le `+`
à la fin de l'expression, comme ceci : ` 2 3 +`. Et pour des expressions
plus compliquées ?

- `(2 + 3) * 5` devient `5 2 3 + *` (le `+` s'applique aux deux opérandes qui
le précèdent, `2` et `3`, et le `*` s'applique au résulat du `+` et à
l'opérande qui précède, `5`), ou bien encore `2 3 + 5 *`.
- `(2 + 3) * (7 - 4)` devient `2 3 + 7 4 - *`.

[[question]]
| Pourquoi cette notation est-elle plus simple ? Je la trouve compliquée
| moi !

C'est vrai qu'elle est plus difficile à lire et à utiliser lorsque l'on est
habitué à la notation infixe classique. Cependant, l'algorithme d'évaluation
d'expression que je vais vous présenter est incroyablement simple comparé
à celui pour évaluer des expressions classique ! Le seul prérequis est de
savoir ce qu'est une pile. Vous pouvez essayer d'implémenter votre
calculatrice sans aucune aide, ou bien suivre les différentes étapes
ci-dessous.

[[information]]
| L'objectif est d'obtenir un programme ressemblant à quelque
| chose comme :
| 
| ```
| > 2 2 +
| 4
| > 3.4 2 / 4 +
| 5.5
| ```
|
| Les opérateurs `+`, `-`, `*`, `/` et `^` (puissance) doivent être
| implémentés, et votre
| calculatrice doit fonctionner avec des nombres flottants.

##Étape 1 : La pile
Une pile (stack en anglais) est une collection d'objets, au même titre qu'un tableau ou une
liste. C'est à dire que c'est une structure qui permet de stocker des objets,
et d'accéder ensuite à ces objets d'une certaine manière. Pour un tableau, on
accède aux objets qu'il contient en se servant de leur indice dans ce tableau.
Pour une pile, c'est différent : nous n'avons le droit d'accéder uniquement
à l'objet qui se trouve au sommet de la pile. La meilleure manière de se
représenter une pile est donc avec une pile d'assiettes :

- Si je veux ajouter une assiette sur la pile, je suis obligé de la mettre
au sommet pour ne pas tout faire tomber. On appelle cette action empiler (
push en anglais).
- De la même manière, si je veux accèder à une assiette de la pile, la seule
que je peux prendre est celle qui se trouve au sommet. Essayer d'en prendre
une au milieu n'est pas possible. On appelle cette action dépiler (pop en
anglais).

Selon le langage que vous utilisez, l'implémentation d'une pile peut être
plus ou moins difficile. En Python par exemple, l'implémentation est immédiate
puisqu'il suffit d'utiliser les listes avec les méthodes `append` et `pop`.

[[secret]]
|```Python
|# Une pile n'est rien d'autre qu'une liste
|pile = []
|
|# On ajoute le nombre "1" au sommet de la pile.
|# On a que pile = [1]
|pile.append(1)
|
|# On ajoute "2" au sommet de la pile.
|# Nous ne pouvons plus accéder à "1" car il est en dessous.
|# On a que pile = [1,2]
|pile.append(2)
|
|# La méhode pop retourne l'objet en sommet de pile (ici 2) et le supprime
|# de la liste.
|# On a donc que sommet = 2 et pile = [1]
|sommet = pile.pop()
|```

Les piles peuvent également être utilisées directement dans beaucoup de
langages modernes comme C++ :

[[secret]]
|```C++
|#include <iostream>
|#include <stack>
|
|int main(void) {
|  std::stack<float> pile;
|  pile.push(1);
|  pile.push(2);
|  std::cout << pile.top() << std::endl;
|  pile.pop();
|  std::cout << pile.top() << std::endl;
|  return 0;
|}
|
|```

Ou encore Java :

[[secret]]
|```Java
|import java.io.*;
|import java.util.Stack;
|
|public class TestPile{
|  public static void main(String[] args){
|    Stack<Float> pile = new Stack<Float>();
|    pile.push(new Float(1));
|    pile.push(new Float(2));
|    Float sommet = pile.pop();
|    System.out.println(sommet);
|  }
|}
|```

Si votre langage ne propose pas d'implémentation de pile, il va falloir en
bricoler vous même. La manière la plus simple est d'utiliser un tableau
accompagné d'une variable indiquant où se trouve le sommet de la pile dans
le tableau. Implémenter votre propre pile est un excellent exercice et peut faire
partie intégrante du défi. Une version très simple en C :

[[secret]]
|```C
|/* pile.h */
|
|#ifndef _PILE_H_
|#define _PILE_H_
|
|#define TAILLE_PILE 256
|
|typedef struct pile * pile;
|
|pile creer_pile(void);
|void detruire_pile(pile p);
|
|void empiler(pile p, float e);
|void depiler(pile p);
|
|float sommet(pile p);
|
|#endif /* _PILE_H_ */
|```
|
|```C
|/* pile.c */
|
|#include <stdlib.h>
|#include "pile.h"
|
|struct pile{
|  float contenu[TAILLE_PILE];
|  int sommet;
|};
|
|pile creer_pile(void){
|  pile new = malloc(sizeof(*new));
|  new->sommet = -1; // -1 lorsque la pile est vide
|  return new;
|}
|void detruire_pile(pile p){
|  free(p);
|}
|
|void empiler(pile p, float e){
|  p->contenu[++(p->sommet)] = e;
|}
|void depiler(pile p){
|  --(p->sommet);
|}
|
|float sommet(pile p){
|  return p->contenu[p->sommet];
|}
|```
|
|```C
|/* main.c */
|
|#include <stdio.h>
|#include "pile.h"
|
|int main(void){
|  pile p = NULL;
|  p = creer_pile();
|  empiler(p, 1);
|  empiler(p, 2);
|  printf("%f\n", sommet(p));
|  depiler(p);
|  printf("%f\n", sommet(p));
|  detruire_pile(p);
|  return 0;
|}
|```

Attention cependant, l'implémentation que je donne ici a une taille fixe, et
aucune vérification n'est faite dans les fonctions. Si vous choisissez de
faire l'exercice en C, je vous invite à essayer d'améliorer ce code pour
vous assurer d'avoir compris le fonctionnement des piles (commencez par
faire des vérifications de la taille aux endroits où il peut y avoir des
erreurs, et réfléchissez à une manière d'avoir des piles de taille
arbitrairement grande).

C'est bon, vous avez votre pile et vous savez vous en servir dans votre
langage ? Alors on peut passer à la suite !

##Étape 2 : Read
Notre programme de calculatrice va être implémenté sous la forme d'une
boucle REPL (Read-Eval-Print-Loop). C'est à dire d'une boucle qui lit
l'entrée de l'utilisateur, évalue cette entrée et affiche le résultat.
La première étape est donc de lire correctement l'entrée de l'utilisateur.
J'imagine que votre langage de programmation favori vous permet de lire
une chaîne de caractères de la part de l'utilisateur : c'est ce qui nous
intéresse. Dans un premier temps, considérez que votre utilisateur entre
une chaîne sous le bon format : c'est à dire des nombres (opérandes) et des
opérateurs séparés par des espaces. Une fois cette chaîne obtenue, nous
allons avoir besoin de traiter les opérandes et opérateurs séparéments. Le
plus simple est de découper la chaîne selon les espaces qu'elle contient.
La plupart des langages disposent d'une méthode `split` ou d'un équivalent
pour découper les chaînes de caractères, mais vous pouvez aussi le faire à
la main.

##Étape 3 : Eval
C'est ici que la magie opère. Vous disposez maintenant d'une liste de
nombres et d'opérateurs, ainsi que d'une pile. Ce qui nous intéresse est
de calculer le résultat de l'expression en utilisant la pile.
L'algorithme est le suivant :

[[secret]]
| ```
| Pour chaque mot w dans la liste
|   Si w est un nombre
|     Alors empiler(pile, w)
|   Sinon si w est un opérateur
|     opérande1 = dépiler(pile)
|     opérande2 = dépiler(pile)
|     Si w est '+'
|       empiler(pile,opérande2 + opérande1)
|     Sinon si w est '-'
|       empiler(pile,opérande2 - opérande1)
|     Sinon si w est '*'
|       empiler(pile,opérande2 * opérande1)
|     Sinon si w est '/'
|       empiler(pile,opérande2 / opérande1)
|     Fin si
|   Fin si
| Fin pour
| 
| résultat = dépiler(pile)
| ```

Et c'est tout ! Si on lit un nombre, on l'empile, si on lit un opérateur, on
dépile deux fois, on applique l'opérateur aux deux nombres et on empile le
résultat ! Il faut juste faire attention lors du calcul à mettre les
opérandes dans le bon ordre pour `-` et `/`.

##Étape 4 : Print
Bon, je ne vais pas vous expliquer comment afficher le résultat une fois
qu'il est calculé, je pense que vous en êtes capables.

##Correction
Si vous êtes curieux, je vous propose deux solutions en Python. La première
sans gestion des erreurs pour bien voir l'algorithme, et la seconde complète
avec gestion des erreurs. Cependant, je vous invite à regarder ces solutions
uniquement si vous avez terminé votre calculatrice ou si vous bloquez
vraiment. Pour quitter la calculatrice, appuyez sur Ctrl+d sous GNU/Linux,
ou entrez une ligne vide.

[[secret]]
| ```Python
| #!/usr/bin/env python3
| 
| # Pour éviter d'avoir plein de if ensuite, et pour pouvoir ajouter
| # d'autres opérateurs facilement par la suite, j'utilise un dictionnaire
| # d'opérateur. À chaque opérateur correspond une fonction du module
| # operator de la bibliothèque standard. Vous pouvez vous passer de cette
| # partie et utiliser des if plus tard comme je l'ai fait lors de
| # l'explication de l'algorithme.
| 
| import operator
| 
| operators = {"+": operator.add,
|              "-": operator.sub,
|              "*": operator.mul,
|              "/": operator.truediv,
| 		       "^": pow}
| 			 
| def _read():
|     return input("> ").split() # Je récupère directement la liste des mots
| 
| def _eval(expr):
|     result = [] # result est ma pile
|     for w in expr:
|         if w in operators.keys():
|             op1 = result.pop()
|             op2 = result.pop()
| 			# C'est ici que je gagne à utiliser mon dictionnaire
|             result.append(operators[w](op2,op1))
|         else:
|             result.append(float(w))
|     return result[0]
| 
| def _print(result):
|     print(result)
| 
| if __name__ == "__main__":
|     while True:
|         _print(_eval(_read()))
| 
| ```
| 
[[secret]]
| ```Python
| #!/usr/bin/env python3
| 
| # Le même programme avec la gestion des erreurs
| import operator
| 
| operators = {"+": operator.add,
|              "-": operator.sub,
|              "*": operator.mul,
|              "/": operator.truediv,
|              "^": pow}
| 
| def _read():
|     try:
|         expr = input("> ").split()
|         if len(expr) == 0:
|             raise EOFError
|     except EOFError:
|         exit()
|     return expr
| 
| def _eval(expr):
|     result = []
|     error = False
|     for w in expr:
|         if w in operators.keys():
|             try:
|                 op1 = result.pop()
|                 op2 = result.pop()
|             except IndexError:
|                 print("Error : Too few operands to operator "+w)
|                 error = True
|                 break
|             result.append(operators[w](op2,op1))
|         else:
|             try:
|                 result.append(float(w))
|             except ValueError:
|                 print("Error : "+w+" is not a number")
|                 error = True
|                 break
|     return result if not error else None
| 
| def _print(result):
|     if result:
|         if len(result) == 1:
|             print(result[0])
|         else:
|             print("Error : Too few operators")
| 
| if __name__ == "__main__":
|     while True:
|         _print(_eval(_read()))
| ```
 
#Niveau 2 : La calculatrice infixe
Vous avez réussi le niveau 1 mais vous aimeriez que votre calculatrice
puisse évaluer des expressions mathématiques infixes (c'est à dire avec
les opérateurs entre les opérandes, ce que vous avez l'habitude d'utiliser) ?

[[information]]
| L'objectif de ce niveau est d'obtenir un programme qui cette
| fois-ci ressemble à :
| 
| ```
| > (2 + 3) * 4
| 20
| > 42 / 0.1
| 420
| ```
| 
| Encore une fois, votre calculatrice doit fonctionner sur des nombres
| flottants, et les mêmes opérateurs `+`, `-`, `*`, `/` et `^` doivent
| être implémentés.

Pour cela, je vais vous proposer deux méthodes radicalement différentes. Vous
pouvez décider de suivre mes indications ou essayer votre propre méthode,
à vous de voir.

##Méthode 1 : Shunting-yard algorithm
La première méthode que je vais vous présenter consiste à se ramener à un
problème que nous avons déjà résolu : évaluer une expression en notation
polonaise inverse. Nous allons donc "traduire" notre expression d'un
système de notation à l'autre, puis utiliser ce que nous avons fait pour
le niveau 1 du défi. Pour cela, nous allons utiliser un algorithme du nom
de Shunting-yard que l'on doit à Edsger Dijkstra (si ce nom vous est inconnu,
je vous encourage à aller voir ce qu'a fait le monsieur sur Wikipédia).
"Shunting-yard" pourrait être traduit comme "aiguillage" ou "gare de triage",
et fait référence au fonctionnement de l'algorithme qui va trier les symboles
qui arrivent en les isolant éventuellement pendant un certain temps sur une
"voie d'attente".

Les prérequis pour comprendre cet algorithme sont :

- Savoir ce qu'est une pile (voir niveau 1).
- Savoir ce qu'est une file.
- Connaître la notion d'associativité et de priorité des opérateurs.

Sans plus tarder, attaquons nous aux deux derniers points.

###La file
Si vous avez compris ce qu'est une pile, vous n'aurez pas de mal à comprendre
ce qu'est une file. De la même manière, une file est une collection d'objets
où nous ne pouvons accèder qu'à un seul objet à la fois, sauf que cette fois ci,
au lieu de pouvoir accèder au dernier élément ajouté, nous n'avons accès qu'au
premier élément ajouté. La manière la plus simple de visualiser une file est
de considérer une file d'attente à la poste :

- Si une nouvelle personne arrive, elle ira se placer à la fin de la file. On
appelle cette action enfiler.
- Lorsque l'on veut servir une personne à un guichet, on sert la personne en
début de file (donc la première arrivée dans la file). On peut ensuite
enlever cette personne de la file pour servir la deuxième et les suivantes dans
leur ordre d'arrivée. On appelle cette action défiler.

Tout comme la pile, la file peut être implémentée à l'aide d'une liste.
Enfiler un objet consite alors à ajouter cet objet en fin de liste, et défiler
un objet consiste à retirer l'objet en tête de liste. La plupart des langages
modernes proposent une implémentation de file, ou bien une implémentation de
liste suffisante pour représenter une file. Si ce n'est pas le cas de votre
langage, implémenter vous même votre file peut être un bon exercice (utilisez un tableau et deux variables pour le début et la fin de file, ou regardez
du côté des listes chaînées). L'utilisation des files étant analogue à celle des
piles, je vous renvoie au niveau 1 ainsi qu'à la documentation de la bibliothèque
standard de votre langage pour voir comment les utiliser (en anglais, "file"
se dit "queue").

###Associativité et priorité
Chaque opérateur mathématique possède deux propriétés qui sont la priorité et
l'associativité. La priorité sera pour nous un nombre entier : plus ce nombre
est grand, plus la priorité est élevée. Par exemple, la multiplication est
plus prioritaire que l'addition, donc le nombre que nous allons utiliser
sera plus grand. Notez qu'il n'existe pas de priorité absolue, et qu'elles
sont forcément relatives les unes aux autres. Par exemple, on peut utiliser
les priorités suivantes pour les opérateurs :

opérateur | priorité
----------|---------
`+` | 2
`-` | 2
`*` | 3
`/` | 3
`^` | 4

Avec `^` l'opérateur de puissance.

L'associativité elle, peut être soit à gauche, soit à droite (pour les opérateurs
associatifs). Elle permet de savoir comment associer plusieurs opérateurs entre
eux. Par exemple, la division est associative à gauche. Cela signifie que
l'expression `3/4/5` sera parenthésée à gauche : `(3/4)/5`. Au contraire,
l'opérateur de puissance est associatif à droite. Cela signifie que l'expression
`3^4^5` sera parenthésée à droite : `3^(4^5)`. Pour nos opérateurs usuels,
la situation est assez simple puisqu'ils sont tous associatifs à gauche à
l'exception de la puissance.

###L'algorithme
Maintenant que toutes les notions sont connues, nous allons pouvoir passer à
l'algorithme. Je vous en présente ici une version simplifiée en pseudo-code.
Pour la version complète avec la gestion des fonctions, je vous renvoie à
Wikipédia. Cet algorithme utilise deux structures de données pour travailler :

- Une file `F` qui contient la sortie de l'algorithme et qui pourra ensuite être
lue dans l'ordre par notre algorithme du niveau 1 (elle est dans l'ordre post-fixe).
- Une pile `P` dans laquelle différents opérateurs seront placés en attendant d'être
envoyés dans la file.

[[secret]]
| ```
| tant qu'il y a des symboles à lire
|   lire un symbole
|   si c'est un nombre alors l'enfiler dans F
|   sinon si c'est un opérateur o1 alors
|     tant qu'il y a un opérateur o2 au sommet de la pile et que
| 	        o1 est associatif à gauche et sa priorité est inférieure ou égale à celle de o2
| 	     ou o1 est associatif à droite et sa priorité est strictement inférieure à celle de o2
|       dépiler o2 de P et l'enfiler dans F
| 	fin tant que
| 	empiler o1 sur P
|   sinon si c'est une parenthèse ouvrante alors l'empiler sur P
|   sinon si c'est une parenthèse fermante alors
|     tant que le symbole au sommet de P n'est pas une parenthèse ouvrante
| 	  dépiler l'opérateur au sommet de P et l'enfiler dans F
| 	fin tant que
| 	dépiler la parenthèse ouvrante de P
|   fin si
| fin tant que
| 
| tant qu'il reste des opérateurs sur P
|   les dépiler de P et les enfiler dans F
| fin tant que
| ```

Faites bien attention lors de l'implémentation de l'algorithme, je ne considère pas les
parenthèses comme des opérateurs mais comme des symboles de ponctuation. Lorsque je
parle d'opérateur, je parle uniquement de ` + - * / ^`.

L'ensemble de cet algorithme peut être placé entre la partie "read" et la partie "eval" du programme du niveau 1. `F`
peut ensuite être évaluée et affichée comme une expression en notation polonaise inverse.

Reste seulement une difficulté que j'ai passée sous silence : la gestion des nombres
négatifs. En effet, l'algorithme présenté ne concerne que les opérateurs binaires,
et le `-` unaire des nombres négatifs pose problème. Il y a plusieurs solutions à ça :

- Ignorer le problème et se contenter de l'algorithme basique.
- Régler le problème en amont lors du découpage de l'entrée pour que le `-` soit
reconnu en même temps que les nombres.
- Rajouter une règle dans l'algorithme qui traite les opérateurs unaires et
différencier le `-` unaire du `-` binaire. Le `-` unaire est associatif à droite
et a une priorité supérieure aux autres opérateurs.
- Utiliser la seconde méthode proposée plus bas pour implémenter ce niveau.

###Correction
Je vous propose une solution en Python utilisant le code de la solution du niveau 1.
Le code des fonctions `_read`, `_eval` et `_print` n'est pas exactement le même, mais
le fonctionnement reste analogue. Cette fois ci, je vous met directement le code avec
gestion des erreurs. Si vous voulez observer comment fonctionne l'algorithme, vous
pouvez décommenter l'appel à `print` dans la fonction `tokenize` et l'appel à `_debug`
dans la fonction `shunting_yard`. J'ai contourné le problème du `-` unaire lors de
la lecture des nombres (voir le fonctionnement de `read_float` et `tokenize`)..

[[secret]]
| ```Python
| #!/usr/bin/env python3
| 
| # Le module operator a la même utilité que pour le niveau 1
| # Le module string est utilisé pour sa constante string.digits
| # Le module numbers est utilisé pour le type numbers.Real
| 
| import operator
| import string
| import numbers
| 
| # Un opérateur est un triplet (fonction, priorité, associativité)
| operators = {"+": (operator.add, 2, "left"),
|              "-": (operator.sub, 2, "left"),
|              "*": (operator.mul, 3, "left"),
|              "/": (operator.truediv, 3, "left"),
|              "^": (pow, 4, "right")}
| 
| # Plus d'appel à split() car nos symboles ne sont plus séparés par
| # des espaces. Le découpage est fait à la main par tokenize et
| # readFloat.
| def _read():
|     try:
|         expr = input("> ")
|         if len(expr) == 0:
|             raise EOFError
|     except EOFError:
|         exit()
|     return expr
| 
| # Une simple modification dans cette fonction de manière à ce
| # que l'appel à l'opérateur corresponde à notre nouvelle définition
| # de operators.
| def _eval(expr):
|     if not expr:
|         return None
|     result = []
|     error = False
|     for w in expr:
|         if w in operators.keys():
|             try:
|                 op1 = result.pop()
|                 op2 = result.pop()
|             except IndexError:
|                 print("Error : Too few operands to operator "+w)
|                 error = True
|                 break
|             result.append(operators[w][0](op2,op1)) # Modification ici
|         else:
|             try:
|                 result.append(float(w))
|             except ValueError:
|                 print("Error : "+w+" is not a number")
|                 error = True
|                 break
|     return result if not error else None
| 
| def _print(result):
|     if result:
|         if len(result) == 1:
|             print(result[0])
|         else:
|             print("Error : Too few operators")
| 
| # Cette fonction retourne le premier nombre flottant trouvé
| # dans la chaîne de caractères expr. Cette chaîne doit commencer
| # par un nombre.
| def read_float(expr):
|     flt = 0
|     i = 0
|     decimal = False
|     nbDecimals = 0
|     negative = expr[0] == '-'
|     if negative:
|         expr = expr[1:]
|     while i < len(expr) and expr[i] in string.digits + "." :
|         if expr[i] == ".":
|             if decimal == True:
|                 raise ValueError
|             decimal = True
|         else:
|             if not decimal:
|                 flt *= 10
|                 flt += int(expr[i])
|             else:
|                 nbDecimals += 1
|                 flt += int(expr[i]) * 10 ** (-nbDecimals)
|         i += 1
|     return (-flt if negative else flt, expr[i:])
| 
| # Étant donnée une expression sous la forme d'une chaîne de
| # caractères, ce générateur va découper la chaîne en symboles
| # et les retourner les uns après les autres
| def tokenize(expr):
|     negative = True
|     while expr != "":
|         # print(expr)
|         if expr[0] in string.digits or expr[0] == '-' and negative:
|             token, expr = read_float(expr)
|             negative = False
|         else:
|             token = expr[0]
|             expr = expr[1:]
|             negative = True
|         yield token
| 
| def _debug(output, stack):
|     print("output : " + str(output))
|     print("stack : " + str(stack) + "\n")
| 
| # Le corps de l'exercice. Il s'agit d'une retranscription
| # du pseudo-code avec une gestion des erreurs.
| def shunting_yard(expr):
|     output = [] # La file de sortie
|     stack = [] # La pile d'attente
|     tokens = tokenize(expr)
|     try:
|         for token in tokens:
|             # _debug(output, stack)
|             if isinstance(token, numbers.Real):
|                 output.append(token)
|             elif token in operators.keys():
|                 fun1, prec1, assoc1 = operators[token]
|                 while stack != [] and stack[-1] in operators.keys():
|                     fun2, prec2, assoc2 = operators[stack[-1]]
|                     if (assoc1 == "left" and prec1 <= prec2 or 
|                         assoc1 == "right" and prec1 < prec2) :
|                         output.append(stack.pop())
|                     else:
|                         break
|                 stack.append(token)
|             elif token == "(":
|                 stack.append(token)
|             elif token == ")":
|                 try:
|                     while stack[-1] != "(":
|                         output.append(stack.pop())
|                     stack.pop()
|                 except IndexError:
|                     print("Mismatched parentheses")
|                     return None
|     except ValueError:
|         print("Value error")
|         return None
|     while stack != []:
|         if stack[-1] == "(":
|             print("Mismatched parentheses")
|             return None
|         else:
|             output.append(stack.pop())
|     return output
| 
| if __name__ == "__main__":
|     while True:
|         _print(_eval(shunting_yard(_read())))
| ```

##Méthode 2 : Utilisation d'outils spécifiques
Cette seconde méthode s'éloigne complètement de ce qui était proposé
jusque là, et consiste à utiliser des outils spécifiques à l'analyse
syntaxique. 

L'analyse de texte est un problème très récurrent en informatique, que
ce soit pour la compilation, la bio-informatique ou le traitement automatique des langues
naturelles. Il existe donc des outils dédiés à cette tâche, et ce dans
tous les langages populaires que je connais. Les plus connus sont le
couple lex/yacc et leurs équivalents (flex/bison en C, jflex/cup en
Java, ocamllex/menhir en OCaml etc...). Je ne vais pas vous apprendre
à vous servir de ces outils, car il me faudrait un tutoriel complet.
Cependant, si vous savez déjà les utiliser ou si vous êtes motivés
pour apprendre à partir de la documentation et des différentes
ressources disponibles sur Internet, je vous encourage à vous tourner
vers cette méthode. Les avantages sont nombreux :

- Leur principe de fonctionnement est très général et permet d'analyser
n'importe quel langage, pas seulement celui des expressions mathématiques.
- La manière dont sont conçus ces outils vous permettra d'étendre très
facilement votre calculatrice.
- Comme ils vous permettent d'écrire une transposition directe de la
grammaire de votre langage, les fichiers que vous écrirez seront bien
plus lisibles et compréhensibles que l'implémentation d'un algorithme
spécifique comme ceux présentés précédemment.

Pour les curieux, je vous propose une explication théorique du
fonctionnement de ces outils, et je vous donne une correction
en flex/bison avec laquelle vous pourriez essayer de résoudre le
niveau 3 si vous programmez en C.

Le principe est donc le suivant :

- Étant donnée une suite de caractères à analyser, la première chose à
faire est de regrouper ces caractères en lexèmes (token en anglais),
c'est à dire en unités sémantiques. Grossièrement, on transforme une
suite de caractères en une suite de mots. Cette étape est appelée
l'analyse lexicale. Par exemple, l'analyse lexicale de l'expression
`(23 + 43) * 256` donnera la suite de lexèmes
`LPAREN NUM PLUS NUM RPAREN TIMES NUM`. Ce qui nous intéresse est d'avoir
l'expression découpée correctement, avec un sens donné à chaque mot.
L'outil flex permet de décrire nos lexèmes explicitement dans un fichier
avec des expressions rationnelles.
- La deuxième chose à faire est de reconnaître la structure de la phrase
de manière à comprendre la relation entre chaque mot. C'est à cette étape
qu'est mise en évidence la priorité des opérateurs par exemple. Cette
étape s'appelle l'analyse syntaxique, et elle consiste grossièrement à
déduire un arbre de syntaxe abstrait d'une suite de lexèmes. L'arbre
est abstrait dans le sens où on ne le représentera pas explicitement,
mais sera induit par la manière dont nous écrirons notre grammaire.
Pour une expression mathématique, les feuilles de l'arbre correspondent
au nombres, et les noeuds internes aux opérateurs ou fonctions. Par
exemple, on peut déduire l'arbre suivant de l'expression précédente :

```
    TIMES
    /   \
  PLUS   \
  /  \    \
NUM  NUM  NUM
```
L'outil bison permet d'écrire directement une grammaire de notre langage.
Une grammaire est une suite de règles de dérivation qui décrivent les
différentes structures du langage. Par exemple, une grammaire simplifiée
de la langue française serait :

```
phrase         -> groupe_nominal groupe_verbal

groupe_nominal -> NOM_PROPRE
               ou DETERMINANT NOM_COMMUN

groupe_verbal  -> VERBE
```
Les noms en minuscules sont appelés les symboles non-terminaux de la grammaire
(il faut les construire à partir d'autres symboles), et ceux en majuscule
sont les symboles terminaux (et correspondent aux lexèmes).


- Enfin, la dernière étape consistera pour nous à évaluer l'expression
à l'aide (indirecte) de l'arbre. Cette étape correspond à l'analyse
sémantique de l'expression.

Je suis conscient que cette méthode sera difficilement utilisable pas les
gens qui ne la connaissent pas déjà. Cependant, je me suis dit qu'il
était intéressant même pour des débutants de savoir que de tels outils
existent (au moins pour la culture générale).

Voici donc un exemple simple de calculatrice réalisée avec flex et bison :

[[secret]]
| lexer.l
| 
| ```
| %{
| #include "parser.tab.h"
| %}
| 			
| %%
| 
| [[:digit:]]+ {yylval.value = atof(yytext);
|     return NB;}
| 
| \n return FIN_EXPR;
| 
| [[:space:]] ;
| 
| \+ return PLUS;
| \- return MINUS;
| \* return TIMES;
| \/ return DIV;
| \( return LPAR;
| \) return RPAR;
| \^ return POW;
| ```
| 
| parser.y
| 
| ```
| %{
| #include <stdlib.h>
| #include <stdio.h>
| #include <math.h>
| #include "parser.tab.h"
| #include "calc.h"
| %}
| 
| %union {
|   float value;
| }
| 
| 
| %token <value> NB
| %token FIN_EXPR LPAR RPAR
| 
| %left PLUS MINUS
| %left TIMES DIV
| %right POW
| 
| %type <value> e
| 
| %%
| 
| s:		s e FIN_EXPR {printf(">>> %f\n", $2);}
| 		| s FIN_EXPR {exit(EXIT_SUCCESS);}
| 		|
| 		;
| 
| e:		e PLUS e {$$ = $1 + $3;}
| 	|	e MINUS e {$$ = $1 - $3;}
| 	|	e TIMES e {$$ = $1 * $3;}
| 	|	e DIV e {$$ = $1 / $3;}
| 	|	e POW e {$$ = pow($1,$3);}
| 	|	LPAR e RPAR {$$ = $2;}
| 	|	NB {$$ = yylval.value;}
| 	;
| 
| %%
| 
| int main(void){
|   yyparse();
|   return EXIT_SUCCESS;
| }
| ```
| 
| calc.h
| 
| ```
| #ifndef CALC_H
| #define CALC_H
| 
| int yylex(void);
| int yyerror(char *);
| 
| #endif
| ```
| 
| Makefile
| 
| ```
| CFLAGS= -std=c99 -Wall -D_POSIX_C_SOURCE -Werror -Wno-unused
| LDFLAGS= -lfl -ly -lm
| 
| all: calc.out
| 
| calc.out: parser.tab.o lex.yy.o
| 	$(CC) $^ -o $@ $(LDFLAGS)
| 
| lexer: lex.yy.o
| 
| parser: parser.tab.o
| 
| parser.tab.c: parser.y
| 	bison -d -v parser.y
| 
| lex.yy.c: lexer.l
| 	flex lexer.l
| 
| clean:
| 	find . -name "*~" -delete
| 	$(RM) *.o calc.out parser.tab.c lex.yy.c parser.dot parser.output parser.tab.h
| 
| ```
| 
| Exemple d'utilisation :
| 
| ```
| $ make
| bison -d -v parser.y
| cc -std=c99 -Wall -D_POSIX_C_SOURCE -Werror -Wno-unused   -c -o parser.tab.o parser.tab.c
| flex lexer.l
| cc -std=c99 -Wall -D_POSIX_C_SOURCE -Werror -Wno-unused   -c -o lex.yy.o lex.yy.c
| cc parser.tab.o lex.yy.o -o calc.out -lfl -ly -lm
| 
| $ ./calc.out 
| 23**2/5
| >>> 105.800003
| 7/3
| >>> 2.333333
| ```

#Niveau 3 : Quelques améliorations
Pour ce troisième niveau, je vous propose d'apporter un certain nombre
d'améliorations à votre calculatrice. Toutes les améliorations sont
indépendantes, et vous pouvez choisir celles qui vous intéressent
(voire en inventer d'autres). Je ne vous guiderai pas en détail pour
ce niveau, et il n'y aura pas de correction globale. Si vous avez
des difficultés pour une amélioration particulière, posez vos
questions dans le topic et j'essaierai de vous aider.

##Amélioration 1 : Notation scientifique
Pourquoi ne pas ajouter le support de la notation scientifique à
votre calculatrice ? La plupart des langages de programmation
proposent d'écrire les nombres sous cette forme.

[[information]]
| Votre calculatrice devra donc lire et interpréter correctement
| les nombres sous cette forme. Pour rappel, la notation `42e-3`
| correspond au nombre $42 \times 10^{-3}$.

Pensez donc à prendre en compte correctement les "e" que vous
trouverez lors de la lecture de l'expression (`42e-3`
correspond à un lexème `NUM`).

##Amélioration 2 : Ajout d'un environnement
Lorsque l'on fait de gros calculs, il est souvent bien pratique
de pouvoir calculer des sous-résultats pour éviter de se tromper.

[[information]]
| L'objectif ici sera donc d'ajouter des variables à votre calculatrice.
| L'utilisation se fera de la manière suivante :
| 
| ```
| > a = 12 + 4
| a <- 16
| > 5 * a
| 80
| ```

Il faut donc :

- Ajouter un nouvel opérateur d'affectation `=` de priorité très faible
et associatif à droite (de manière à pouvoir écrire des choses comme
`a = b = 42`).
- Ajouter un nouveau lexème "identificateur" qui correspond aux noms
utilisés pour les variables. Un identificateur est une suite arbitraire
de caractères alphabétiques (expression régulière `[a-zA-Z]+`). Selon
la méthode que vous utilisez, faites attention aux "e" utilisés pour la
notation scientifique !
- Ajouter un environnement de variables globales. Vous pouvez utiliser
une table de hachage / un dictionnaire / une table associative, ou même
une simple liste. À chaque affectation `id = valeur`, on ajoute le couple
`(id, valeur)` en tête de liste. À chaque évaluation de variable, on
parcourt la liste jusqu'à tomber sur le premier couple avec le bon
identificateur.

##Amélioration 3 : Des fonctions
Votre calculatrice gagnerait à proposer des fonctions mathématiques
usuelles comme `sqrt` pour la racine, `sin`, `cos` et `tan` pour la
trigonométrie etc...

[[information]]
| Vous êtes ici libre d'ajouter toutes les fonctions mathématiques
| que vous voulez.
| Votre calculatrice doit ensuite pouvoir s'utiliser de la manière
| suivante :
| 
| ```
| > sqrt(4, 2)
| 2
| > cos(0)
| 1
| ```

Pour celà, il faut :

- Ajouter un nouveau symbole de ponctuation `,`.
- Si vous utilisez le Shunting-Yard Algorithm, allez voir la page
Wikipédia qui lui est dédiée, l'algorithme complet permet de traiter
les fonctions.
- Appeler les bonnes fonctions en vérifiant que le nombre de
paramètres passés est le bon.

[[information]]
| Bien plus compliqué, pourquoi ne pas permettre à votre utilisateur
| de définir lui même ses fonctions ? Par exemple, de la manière
| suivante :
|
| ```
| > f(x) = 3 * x
| f <- 3 * x
| > f(3)
| 9
| ```


Si vous avez fait l'amélioration
précédente, vous avez déjà un environnement prêt. Les variables
numériques sont des cas particuliers de fonctions d'arité nulle
(c'est à dire qui ne prennent pas d'argument), et votre environnement
devient donc une liste de couples `(id, fonction)`. Si on définit
une fonction f :

```
> f(x) = 3 * x
f <- 3 * x
```

Alors évaluer l'expression `f(42)` revient à évaluer l'expression
`3 * x` où `x` vaut `42`. Comment gérer ça ? Avec une pile
d'environnements. Si l'environnement global courant est le suivant :

```
[
{("f", 3 * x), ("pi", 3.14), ("x", 3)}
]
```

Alors à l'appel `f(42)`, on empile un nouvel environnement avec la
valeur des paramètres. On a donc la pile :

```
[
{("x", 42)},
{("f", 3 * x), ("pi", 3.14), ("x", 3)}
]
```

Lors de l'évaluation de `3 * x`, on cherche d'abord dans l'environnement
en haut de la pile, et c'est donc la valeur `42` qui est trouvée, et pas
la valeur `3` de l'environnement global.

La plus grosse difficulté de cette partie est donc de comprendre le
fonctionnement des différents environnements et de trouver une représentation
interne pour les fonctions déclarées par l'utilisateur. Vous pouvez la
garder sous la forme de chaîne de caractères accompagnée de la liste des
paramètres, ou bien la représenter par un arbre, à vous de voir.

[[information]]
| Enfin, si vous êtes motivés et que vous connaissez la programmation
| fonctionnelle,
| pourquoi ne pas vous débarrasser de ces parenthèses et implémenter les
| notions de curryfication et d'application partielle de fonction ? :)
