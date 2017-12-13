# Workshop strings.

## Objectif
Dans ce kata, on va bosser sur les chaines de caractères, et en profiter pour voir comment on peut simplifier la lecture du code avec un peu d'abstraction, et en utilisant plein de fonctions très utiles en PHP.

C'est parti !

## Exercices

### Pour commencer
Pour commencer, on va prendre un exemple assez classique : la transformation d'une string de `snake_case` à `camelCase`.
Je vous laisse d'abord chercher un peu, et après on en discute...

```php
function camelCase ($snake_case) {














}
```
Aller c'est parti
- D'abord, on va remplacer tous les `_` par des ` ` : `str_replace('_', ' ', 'my_string')` -> `my string`.
- Ensuite on mets la première lettre de tous les mots en majuscule `ucwords('my string')` -> `My String`.
- On vire tous les espaces `str_replace(' ', '', 'My String')` -> `MyString`.
- Et on met la première en minuscule `lcfirst('MyString')` -> `myString`.

Ce qui nous donne très exactement, sans variables intermédiaires : 

```php
function camelCase ($snake_case) {
    return lcfirst(str_replace(' ','', ucwords(str_replace('_', ' ', $string))));
}
```

Ce qui est, avouons-le, super compréhensible pour le pékin qui passe et qui lit le code. :trollface:

Et si on veut l'écrire, on est obligé de faire ça depuis l'intérieur, puis appliquer chaque méthode en couche...

Et oui, c'est notre fardeau à nous qui nous entêtons à faire du PHP... les primitives ! Ces types de variables qui ne sont pas des objets, qui n'implémentent pas de méthodes, et donc où le chainage nous est interdit.  

Sur ce genre de chose, allez voir du côté des langages où tout est objet, ça fait rêver :

```python
def camel_case(snake_case):
    snake_case.replace('_', ' ').ucwords().replace(' ', '').lcfirst()
```
Bon ok, j'ai inventé le nom des fonctions, mais on est pas loin quoi...

Ça serait cool chez nous non ?

Et bah c'est parti, et... c'est à vous !

### Nota bene
Dans les exos je vérifie les résultats par des lignes du type
```php
foo('bar') === 'baz'; // true
foo('bar'); // "baz"
isFoo('bar'); // false
```

En gros vous pouvez soit `var_dump(foo('bar') === 'baz');` et voir si c'est bien true,
soit (et c'est mieux) vous pouvez mettre en place rapidement des tests et faire un vrai `assertSame('baz', foo('bar'))`
ou `assertTrue/assertFalse(isFoo('bar'))`.

L'idéal serait de bosser en TDD évidemment, et ça devrait faire l'objet d'un autre workshop (si si, j'y crois).

### Exercice 1
Construire une classe qui permet de faire :
```php
$string = (string) Str::on('my_string')
    ->replace('_', ' ')
    ->ucwords()
    ->replace(' ', '')
    ->lcfirst();

$string === 'myString'; // true
```
### Exercice 2

Ecrire `camelCase` et `toString` dans cette classe pour que :
```php
Str::on('my_string')->camelCase()->toString() === 'myString'; // true
```

Ecrire `toCamelCase` dans cette classe pour que :
```php
Str::toCamelCase('my_string') === 'myString'; // true
```

Indice : l'idéal ici serait d'utiliser une magic method histoire de pas avoir à écrire X fois le même genre de méthode statique ! 

### Exercice 2.5
Faire en sorte que toutes les cases passent dans la même méthode.
```php
Str::toCamelCase('my_string') === 'myString'; // true
Str::toCamelCase('myString') === 'myString'; // true
Str::toCamelCase('my-string') === 'myString'; // true
Str::toCamelCase('my string') === 'myString'; // true
Str::toCamelCase('My String') === 'myString'; // true
```

### Exercice 3
Mettre en place la fonction `toSnakeCase` qui fonctionne de la même façon :

```php
Str::toSnakeCase('my_string') === 'my_string'; // true
Str::toSnakeCase('myString') === 'my_string'; // true
Str::toSnakeCase('my-string') === 'my_string'; // true
Str::toSnakeCase('my string') === 'my_string'; // true
Str::toSnakeCase('My String') === 'my_string'; // true
```

Indices : 

1. un peu de regex peut vous aider...
2. Si vraiment vous avez du mal : http://regexr.com/3ghnd 

### Exercice 4
Mettre en place la fonction `toSlugCase`:

```php
Str::toSlugCase('my_string') === 'my-string'; // true
Str::toSlugCase('myString') === 'my-string'; // true
Str::toSlugCase('my-string') === 'my-string'; // true
Str::toSlugCase('my string') === 'my-string'; // true
Str::toSlugCase('My String') === 'my-string'; // true
```

Indice : on n'est vraiment pas loin de `snakeCase`, ça peut valoir le coup de factoriser.

### Exercice 4.5
`slug-case` ça s'appelle aussi `kebab-case`.

### Exercice 5
Mettre en place la fonction `studlyCase`:

```php
Str::toStudlyCase('my_string') === 'MyString'; // true
Str::toStudlyCase('myString') === 'MyString'; // true
Str::toStudlyCase('my-string') === 'MyString'; // true
Str::toStudlyCase('my string') === 'MyString'; // true
Str::toStudlyCase('My String') === 'MyString'; // true
```

Indice : on est vraiment pas loin de `camelCase`, ça peut valoir le coup de refactoriser.

### Exercice 5.5
`StudlyCase` ça s'appelle aussi `TitleCase`.

### Exercice 6

On est bien d'accord que : 
```php
$str = Str::on('mY StrIng');
$str->camelCase()->toString() === 'myString'; // true
$str->snakeCase()->toString() === 'my_string'; // true
$str->studlyCase()->toString() === 'MyString'; // true
$str->titleCase()->toString() === 'MyString'; // true
$str->slugCase()->toString() === 'my-string'; // true
$str->kebabCase()->toString() === 'my-string'; // true
$str->toString() === 'mY StrIng'; // true
(string) $str === 'mY StrIng'; // true
```

non ?

### Exercice 7

Ça pourrait être cool de simplifier le process et de pas avoir à appeler `Str::on()` la méthode `toString` à chaque fois non ?

Moi je verrais bien un truc comme ça, c'est simple, et ça donne l'occasion de s'amuser un peu avec des [magic methods](https://duckduckgo.com/?q=magic+methods+php).
```php
$str = str('mY StrIng');
$str->camelCase === 'myString'; // true
$str->snakeCase === 'my_string'; // true
$str->studlyCase === 'MyString'; // true
$str->titleCase === 'MyString'; // true
$str->slugCase === 'my-string'; // true
$str->kebabCase === 'my-string'; // true
$str() === 'mY StrIng'; // true
```

Attention à bien veiller que :
```php
$kebab = str('mY StrIng')
    ->camelCase()
    ->snakeCase()
    ->studlyCase()
    ->titleCase()
    ->slugCase()
    ->kebabCase;
$kebab  === 'my-string' //true
```

### Exercice 8
Bon vous êtes grand maintenant, du coup on peut passer à d'autres méthodes ?
Si on avait envie de vérifier un peu ce qu'il y a dans notre string ?

```php
    Str::checkIf('mY StrIng')->contains('str'); // true
    Str::checkIf('mY StrIng')->contains('str', [strict] true ); // false
    Str::checkIf('mY StrIng')->contains('Str', [strict] true ); // true
    Str::checkIf('mY StrIng')->snake_case()->contains('str', [strict] true ); // true
    Str::on('mY StrIng')->snake_case()->contains('str', [strict] true ); // true
    str('mY StrIng')->snake_case()->contains('str', [strict] true ); // true
    Str::checkIf('mY StrIng')->startsWith('my'); // true
    Str::checkIf('mY StrIng')->startsWith('my', [strict] true ); // false
    Str::checkIf('mY StrIng')->endsWith('ing'); // true
    Str::checkIf('mY StrIng')->endsWith('ing', [strict] true ); // false
    Str::checkIf('mY StrIng')->is('*ing'); // true
    Str::checkIf('mY StrIng')->is('my*'); // true
    Str::checkIf('mY StrIng')->is('my*ng'); // true
    Str::checkIf('mY StrIng')->is('my*ng', [strict] true); // false
```

### Exercice implode(range(9, 13), ', ')
Il reste quelques méthodes à implémenter, comme `finish`, `after`, `before`, `between`, `limit`, etc...

Quelques exemples dans la doc Laravel : https://laravel.com/docs/master/helpers#strings

Pas bien compliqué mais beaucoup moins intéressant que...

### Exercice "Des mots, toujours des mots !" 
Bon là je commence à être à cours d'idée, donc je balance un peu tout ce qui me passe par la tête et qui pourrait être fun..!

Et puis je me dis que bosser sur des mots en particulier ça peut être cool.

Ce ne sont que des exemples, en vrac il faut bien sur interpréter un peu et développer ce que ça implique pour que ça marche. 

Pour les exemples avec `collect()` voir le workshop sur les collections, ou la méthode du même nom dans Laravel !

```php
    str('mY StrIng')->word(2)->ucfirst; // "String"
    
    str('mY StrIng')->firstWord; // "mY"
    str('mY StrIng')->firstWord()->lower; // "my"
    
    str('mY StrIng')->lastWord; // "StrIng"
    (string) str('mY StrIng')->snake_case()->lastWord('_'); // "string"
    
    $str = str('mY StrIng');
    $str2 = $str->lastWord()->upper()->prepend('amazing') // str('amazingSTRING')
    $str->firstWord()->upper()->append('$')->append($str2); // str('MY$amazingSTRING)
    
    str('mY StrIng')->words; // collect(['mY', 'StrIng']) 
    str('mY StrIng')->words(); // collect([str('mY'), str('StrIng')])
    
    str('mY amaZing StrIng rocks')->words(' ', [1, 3]); // collect([str('mY'), str('StrIng')])
    str('mY amaZing StrIng rocks')->snakecase()->words('_')->nth(2)->map->toString()->toArray(); // ['my', 'string']
    str('mY amaZing StrIng rocks')->snakecase()->words('_')->only(1, 3)->toArray(); // [str('amazing'), str('rocks')]
```

## C'est la fin...

Voilà voilà pour ce premier workshop. 

En espérant que ça vous a plus, n'hésitez pas à faire vos retours,
une PR sur le tuto pour modifier ou ajouter des trucs ou même pour poster vos résultats si vous en êtes fiers (ça peut aider les suivants ?) ! 

[Mathieu](https://github.com/mathieutu).
