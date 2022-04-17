# Diviser pour régner

## 1. Retour sur l'algorithme de recherche par dichotomie

Nous avons vu en classe de Première l'algorithme de **dichotomie** (du grec *dikhotomia*, « division en deux parties »).

Notre but ici est la recherche de la présence (ou non) d'un élément dans un tableau **triée**.  
Notre fonction renverra donc un booléen.

La recherche *naïve* (élément par élément) est naturellement de complexité linéaire. Nous allons voir que la méthode dichotomique est plus efficace.

### 1.1 Version impérative

!!! note "Dichotomie version impérative"
    ```python linenums='1'
    def recherche_dichotomique(tableau: list, valeur_cherchee: int) -> bool:
        """renvoie True si valeur_cherchee est dans le tableau, déjà trié,
        et False si valeur_cherchee ne s'y trouve pas.
        """
        indice_debut = 0
        indice_fin = len(tableau) - 1
        while indice_debut <= indice_fin:
            indice_central = (indice_debut + indice_fin) // 2   # 1
            valeur_centrale = tableau[indice_central]           # 2
            if valeur_centrale == valeur_cherchee:              # 3
                return True
            if valeur_centrale < valeur_cherchee:               # 4
                indice_debut = indice_central + 1
            else:
                indice_fin = indice_central - 1
        return False                                            # 5
    ```

    1. on calcule l'indice central
    2. on détermine la valeur centrale
    3. si la valeur centrale est la valeur cherchée...
    4. si la valeur centrale est trop petite...
    5. la valeur_cherchee ne se trouve pas dans tableau

Exemple d'utilisation :

```python
>>> t = [2, 3, 6, 7, 11, 14, 18, 19, 24]
>>> recherche_dichotomique(t, 7)
True
>>> recherche_dichotomique(t, 35)
False
```

À chaque tour de la boucle ```while```, la taille de la liste est divisée par 2. Ceci confère à cet algorithme une **complexité logarithmique** (bien meilleure qu'une complexité linéaire).

### 1.2 Version récursive

Il est possible de programmer de manière récursive la recherche dichotomique sans toucher à la liste, et donc en jouant uniquement sur les indices :

!!! note "Dichotomie version récursive sans slicing"
    ```python linenums='1'
    def dicho_rec(tableau, valeur_cherchee, indice_debut=0, indice_fin=None):
        if indice_fin is None:
            indice_fin = len(tableau) - 1
        if indice_debut > indice_fin:
            return False
        indice_central = (indice_debut + indice_fin) // 2
        if tableau[indice_central] < valeur_cherchee:
            return dicho_rec(tableau, valeur_cherchee, indice_central + 1, indice_fin)
        elif tableau[indice_central] > valeur_cherchee:
            return dicho_rec(tableau, valeur_cherchee, indice_debut, indice_central - 1)
        else:
            return True
    ```

    1. Pour pouvoir appeler simplement la fonction sans avoir à préciser les indices, on leur donne des paramètres par défaut.
    2. Il est impossible de donner ```j=len(tab)-1``` par défaut (car ```tab``` est aussi un paramètre). On passe donc par une autre valeur (ici ```None```) qu'on va ici intercepter.

Exemple d'utilisation :

```python
>>> tab = [1, 5, 7, 9, 12, 13]
>>> dicho_rec_2(tab, 12)
True
>>> dicho_rec_2(tab, 17)
False
```

??? aide "Visualisation grâce à PythonTutor:"
<iframe width="800" height="500" frameborder="0" src="https://pythontutor.com/iframe-embed.html#code=def%20dicho_rec%28tableau,%20valeur_cherchee,%20indice_debut%3D0,%20indice_fin%3DNone%29%3A%20%0A%20%20%20%20if%20indice_fin%20is%20None%3A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20indice_fin%20%3D%20len%28tableau%29%20-%201%0A%20%20%20%20if%20indice_debut%20%3E%20indice_fin%3A%0A%20%20%20%20%20%20%20%20return%20False%0A%20%20%20%20indice_central%20%3D%20%28indice_debut%20%2B%20indice_fin%29%20//%202%0A%20%20%20%20if%20tableau%5Bindice_central%5D%20%3C%20valeur_cherchee%3A%0A%20%20%20%20%20%20%20%20return%20dicho_rec%28tableau,%20valeur_cherchee,%20indice_central%20%2B%201,%20indice_fin%29%0A%20%20%20%20elif%20tableau%5Bindice_central%5D%20%3E%20valeur_cherchee%3A%0A%20%20%20%20%20%20%20%20return%20dicho_rec%28tableau,%20valeur_cherchee,%20indice_debut,%20indice_central%20-%201%29%0A%20%20%20%20else%3A%0A%20%20%20%20%20%20%20%20return%20True%0A%0At%20%3D%20%5B2,%203,%206,%207,%2011,%2014,%2018,%2019,%2024%5D%0Adicho_rec%28t,%207%29%0Adicho_rec%28t,%2035%29&codeDivHeight=400&codeDivWidth=350&cumulative=false&curInstr=0&heapPrimitives=nevernest&origin=opt-frontend.js&py=3&rawInputLstJSON=%5B%5D&textReferences=false"> </iframe>

## 2. Diviser pour régner

Les algorithmes de dichotomie présentés ci-dessus ont tous en commun de diviser par deux la taille des données de travail à chaque étape. Cette méthode de résolution d'un problème est connue sous le nom de *diviser pour régner*, ou *divide and conquer* en anglais.  

Une définition pourrait être :

!!! abstract "Définition"
    Un problème peut se résoudre en employant le paradigme *diviser pour régner* lorsque :  
    - il est possible de décomposer ce problème en sous-problèmes **indépendants**  
    - la taille de ces sous-problèmes est une **fraction** du problème initial

**Remarques :**

- Les sous-problèmes peuvent nécessiter d'être ensuite **recombinés** entre eux (voir plus bas le tri fusion).
- Considérons l'écriture récursive de la fonction `factorielle` ci-dessous :

```python
def factorielle(n):
    if n == 0:
        return 1
    else:
        return n * factorielle(n-1)
```

On ne peut pas parler ici de *diviser pour régner* car la taille des données à traiter est passée de *n* à *n-1*. C'est bien une diminution (qui fait que l'algorithme fonctionne) mais il n'y a pas de **division** de la taille des données.  
C'est cette division (par 2 dans le cas de la dichotomie) qui donne son efficacité à ce paradigme.

- Le paradigme *diviser pour régner* va naturellement amener à rédiger des programmes récursifs.

## 3. L'exponentiation rapide

On appelle *exponentiation* le fait de mettre en puissance un nombre. On va donc coder, de deux manières différentes, la puissance d'un nombre.

### 3.1 Algorithme classique

!!! note "Exponentiation classique"
    ```python linenums='1'
    def puissance(a, n):
        if n == 0:
            return 1
        else:
            return a * puissance(a, n-1)
    ```

### 3.2 Algorithme utilisant *diviser pour régner*

Nous allons nous appuyer sur la remarque mathématique suivante :  
Pour tout nombre $a$,

- si $n$ est pair, $a^n = (a^2)^{\frac{n}{2}}$

- si $n$ est impair, $a^n = a \times a^{n-1} = a \times (a^2)^{\frac{n-1}{2}}$

Ainsi, dans le cas où $n$ est pair, il suffit d'élever $a$ au carré (une seule opération) pour que l'exposant diminue de **moitié**. On peut donc programmer la fonction `puissance` en utilisant le paradigme *diviser pour régner* :

!!! note "Exponentiation rapide"
    ```python linenums='1'
    def puissance_mod(a, n):
        if n == 0:
            return 1
        if n % 2 == 0:
            return puissance_mod(a**2, n//2)
        else:
            return a * puissance_mod(a**2, (n-1)//2)
    ```


### 3.3 Comparaison de la vitesse d'exécution des deux algorithmes

![image](data/puiss.png)
!!! example "Exercice"
    === "Énoncé"
        Recréer le graphique ci-dessus, qui compare les temps d'exécution des deux fonctions ```puissance``` et ```puissance_mod```.

        **Aide pour Matplotlib :** le code ci-dessous

        ```python linenums='1'
        import matplotlib.pyplot as plt

        def carre(x):
            return x*x

        x = list(range(10))
        y = [carre(k) for k in x]
        plt.plot(x, y)
        plt.show()
        ```

        donne le graphique suivant :

        ![image](data/carre.png)

## 4. Le tri-fusion

En anglais le *merge sort*.

### 4.1 Preambule : l'interclassement

Le mécanisme principal du tri fusion est la **fusion** de deux listes triées en une nouvelle liste elle aussi triée.

On appelera ce mécanisme l'**interclassement**.

Principe de l'interclassement de deux listes ```lst1``` et ```lst2```.

- on part d'une liste vide ```lst_totale```
- on y ajoute alternativement les éléments de ```lst1``` et ```lst2```. Il faut pour cela gérer séparément un indice ```i1``` pour la liste ```lst1```  et un indice ```i2```  pour la liste ```i2```.
- quand une liste est épuisée, on y ajoute la totalité restante de l'autre liste.

!!! example "Exercice"
    === "Énoncé"
        Coder la fonction ```interclassement```.
    === "Correction"
        {{ correction(True,
        "
        ```python
        def interclassement(lst1, lst2):
            i1 = 0
            i2 = 0
            lst_totale = []
            while i1 != len(lst1) and i2 != len(lst2):
                if lst1[i1] < lst2[i2]:
                    lst_totale.append(lst1[i1])
                    i1 += 1
                else:
                    lst_totale.append(lst2[i2])
                    i2 += 1
            return lst_totale + lst1[i1:] + lst2[i2:]
        ```
        "
        ) }}

### 4.2 La fusion

#### 4.2.1 Principe

L'idée du tri fusion est le découpage de la liste originale en une multitude de listes ne contenant qu'un seul élément. Ces listes élémentaires seront ensuite interclassées avec la fonction précédente.

![image](data/fusion.png)

**Principe de l'algorithme du tri fusion :**

- pour trier une liste, on interclasse les deux moitiés de cette liste, précédémment elles-mêmes triées par le tri fusion.
- si une liste à trier est réduite à un élément, elle est déjà triée.

#### 4.2.2 Implémentation

La grande force de ce tri va être qu'il se programme simplement de manière **récursive**, en appelant à chaque étape la même fonction mais avec une taille de liste divisée par deux, ce qui justifie son classement parmi les algorithmes utilisants «diviser pour régner».

!!! abstract "Algorithme de tri fusion (*merge sort*)"
    ```python
    def interclassement(lst1, lst2):
        lst_totale = []
        n1, n2 = len(lst1), len(lst2)
        i1, i2 = 0, 0
        while i1 < n1 and i2 < n2:
            if lst1[i1] < lst2[i2]:
                lst_totale.append(lst1[i1])
                i1 += 1
            else:
                lst_totale.append(lst2[i2])
                i2 += 1
        return lst_totale + lst1[i1:] + lst2[i2:]

    def tri_fusion(lst):
        if len(lst) <= 1:
            return lst
        else:
            m = len(lst) // 2
            return interclassement(tri_fusion(lst[:m]), tri_fusion(lst[m:]))
    ```

#### 4.2.3 Visualisation

Une erreur classique avec les fonctions récursives est de considérer que les appels récursifs sont simultanés. C'est faux !

L'animation suivante montre la progression du tri :

![image](data/gif_fusion.gif)

Il est aussi conseillé d'observer l'évolution de l'algorithme grâce à PythonTutor :

<iframe width="1000" height="700" frameborder="0" src="https://pythontutor.com/iframe-embed.html#code=def%20interclassement%28lst1,%20lst2%29%3A%0A%20%20%20%20lst_totale%20%3D%20%5B%5D%0A%20%20%20%20n1,%20n2%20%3D%20len%28lst1%29,%20len%28lst2%29%0A%20%20%20%20i1,%20i2%20%3D%200,%200%0A%20%20%20%20while%20i1%20%3C%20n1%20and%20i2%20%3C%20n2%3A%0A%20%20%20%20%20%20%20%20if%20lst1%5Bi1%5D%20%3C%20lst2%5Bi2%5D%3A%0A%20%20%20%20%20%20%20%20%20%20%20%20lst_totale.append%28lst1%5Bi1%5D%29%0A%20%20%20%20%20%20%20%20%20%20%20%20i1%20%2B%3D%201%0A%20%20%20%20%20%20%20%20else%3A%0A%20%20%20%20%20%20%20%20%20%20%20%20lst_totale.append%28lst2%5Bi2%5D%29%0A%20%20%20%20%20%20%20%20%20%20%20%20i2%20%2B%3D%201%0A%20%20%20%20return%20lst_totale%20%2B%20lst1%5Bi1%3A%5D%20%2B%20lst2%5Bi2%3A%5D%0A%0Adef%20tri_fusion%28lst%29%3A%0A%20%20%20%20if%20len%28lst%29%20%3C%3D%201%3A%0A%20%20%20%20%20%20%20%20return%20lst%0A%20%20%20%20else%3A%0A%20%20%20%20%20%20%20%20m%20%3D%20len%28lst%29%20//%202%0A%20%20%20%20%20%20%20%20return%20interclassement%28tri_fusion%28lst%5B%3Am%5D%29,%20tri_fusion%28lst%5Bm%3A%5D%29%29%0A%0Alst%20%3D%20%5B4,%203,%208,%202,%207,%201,%205%5D%0Aprint%28tri_fusion%28lst%29%29&codeDivHeight=400&codeDivWidth=350&cumulative=false&curInstr=0&heapPrimitives=nevernest&origin=opt-frontend.js&py=3&rawInputLstJSON=%5B%5D&textReferences=false"> </iframe>

## 4.3 Complexité

La division par 2 de la taille de la liste pourrait nous amener à penser que le tri fusion est de complexité logarithmique, comme l'algorithme de dichotomie. Il n'en est rien.

En effet, l'instruction finale ```interclassement(tri_fusion(lst[:m]), tri_fusion(lst[m:]))``` lance **deux** appels à la fonction ```tri_fusion``` (avec certe des données d'entrée deux fois plus petites).

On peut montrer que :

!!! note "Complexité du tri fusion"
    L'algorithme de tri fusion est en $O(n \log n)$.

    On dit qu'il est **semi-logarithmique**.

Une complexité semi-logarithmique (en $O(n \log n)$) se situe «entre» une complexité linéaire (en $O(n)$) et une complexité quadratique (en $O(n^2)$).

![image](data/comparaison.png)
