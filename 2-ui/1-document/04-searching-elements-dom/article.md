# Rechercher: getElement*, querySelector*

DOM navigation properties are great when elements are close to each other. What if they are not? How to get an arbitrary element of the page?

Les propriétés de navigation d'un DOM sont utiles lorsque les éléments sont proches l'un de l'autre. Comment sélectionner un élement arbitraire de la page? 

There are additional searching methods for that.
Il a d'autres methodes de recherche pour cela.

## document.getElementById ou simplement id

If an element has the `id` attribute, we can get the element using the method `document.getElementById(id)`, no matter where it is.
Si un élement a un attribut "id", il est possible de sélectionner cet élément en utilisant la methode `document.getElementById(id)` , peu importe où se trouve cette élément dans la page.

For instance:
Par exemple:

```html run
<div id="elem">
  <div id="elem-content">Element</div>
</div>

<script>
  // sélectionner l'élément
*!*
  let elem = document.getElementById('elem');
*/!*

  // make its background red
  // rendre son fond rouge
  elem.style.background = 'red';
</script>
```
Also, there's a global variable named by `id` that references the element:
Egalement, il y a une variable plus générale qui, sélectionnant l'`id`, référence l'élément: 

```html run
<div id="*!*elem*/!*">
  <div id="*!*elem-content*/!*">Element</div>
</div>

<script>
  // elem is a reference to DOM-element with id="elem"
  // elem est référencé comme un élément du DOM grâce à son id="elem"
  elem.style.background = 'red';

  // id="elem-content" has a hyphen inside, so it can't be a variable name
  // ...but we can access it using square brackets: window['elem-content']
  
  // id="elem-content" contient un trait d'union, donc il ne peut pas s'agir d'un nom de variable
  // ...mais nous pouvons y accéder en utilisant des crochets: window['elem-content']
</script>
```

...That's unless we declare a JavaScript variable with the same name, then it takes precedence:
...Ceci est valable que si nous ne declarons une variable JavaScript avec le même nom, auquel cas celle-ci aurez la priorité:

```html run untrusted height=0
<div id="elem"></div>

<script>
  let elem = 5; // maintenant elem vaut 5, et il ne s'agit plus d'une référance à <div id="elem">

  alert(elem); // 5
</script>
```

```warn header="Ne pas utiliser des variables dont le nom est le même que leur id pour acceder aux éléments Please don't use id-named global variables to access elements"

This behavior is described [in the specification](http://www.whatwg.org/specs/web-apps/current-work/#dom-window-nameditem), so it's kind of standard. But it is supported mainly for compatibility.
Ce comportement est décrit [dans les specifications](http://www.whatwg.org/specs/web-apps/current-work/#dom-window-nameditem), donc il s'agit d'un comportement plutôt standard. Cependant, il est majoritairement supporter pour des raisons de compatibilité.

The browser tries to help us by mixing namespaces of JS and DOM. That's fine for simple scripts, inlined into HTML, but generally isn't a good thing. There may be naming conflicts. Also, when one reads JS code and doesn't have HTML in view, it's not obvious where the variable comes from.
Le navigateur tente de nous aider en mélangeant les espaces-nom de JS et du DOM. Ce n'est pas problématique pour de simples scripts reliés à HTML, mais en général ce n'est pas une bonne pratique. Il peut y avoir des confusions liées aux noms. De plus, lorsqu'une personne lit le code JS sans avoir accés au HTML, ce n'est pas toujours clair d'où vient la variable. 

Here in the tutorial we use `id` to directly reference an element for brevity, when it's obvious where the element comes from.
Dans ce tutoriel, on utilise `id` pour référencer directement l'élément par souci de brièveté, là où il est évident d'où l'élément vient.

In real life `document.getElementById` is the preferred method.
Dans la réalité `document.getElementById` serait la meilleure méthode.
```

```smart header="L' `id` doit être unique"
L' `id` doit être unique. Il ne peut y avoir qu'un seul élément dans la page avec cet `id`.

S'il y a plusieurs éléments avec le même `id`, alors comportement de la methode qui utilise cet `id` est imprévisible. Par exemple `document.getElementById` pourrait retourner n'importe quel élément de manière aléatoire. C'est pourquoi il est mieux de respecter cette rêgle et de garder l' `id` unique.
```

```warn header="Uniquement `document.getElementById`, et non pas `anyElem.getElementById`"
La methode `getElementById` ne peut être utiliser qu'avec l'object `document`. Elle recherche pour l' `id` dans tout le document.
```

## querySelectorAll [#querySelectorAll]

By far, the most versatile method, `elem.querySelectorAll(css)` returns all elements inside `elem` matching the given CSS selector.
De loin la méthode la plus polivalente, `elem.querySelectorAll(css)` retourne tous les éléments situés à l'intérieur de `elem` qui répondent au sélecteur CSS donné.

Here we look for all `<li>` elements that are last children:
Ici nous voyons tous les éléments `<li>` qui sont le dernier enfant :

```html run
<ul>
  <li>The</li>
  <li>test</li>
</ul>
<ul>
  <li>has</li>
  <li>passed</li>
</ul>
<script>
*!*
  let elements = document.querySelectorAll('ul > li:last-child');
*/!*

  for (let elem of elements) {
    alert(elem.innerHTML); // "test", "passed"
  }
</script>
```

This method is indeed powerful, because any CSS selector can be used.
En effet, cette méthode est très utiles car n'importe quel selecteur CSS peut être utilisé.

```smart header="Les pseudo-classes peuvent également être utilisées"
Pseudo-classes in the CSS selector like `:hover` and `:active` are also supported. For instance, `document.querySelectorAll(':hover')` will return the collection with elements that the pointer is  over now (in nesting order: from the outermost `<html>` to the most nested one).
Les pseudo-classes des selecteurs CSS comme `:hover` et `:active` sont également supportés. Par exemple, `document.querySelectorAll(':hover')` retournera la collection avec les élements dont le  
```
/!\

## querySelector [#querySelector]

The call to `elem.querySelector(css)` returns the first element for the given CSS selector.
Utiliser `elem.querySelector(css)` retourne le premier élément d'un selecteur CSS donné. 

In other words, the result is the same as `elem.querySelectorAll(css)[0]`, but the latter is looking for *all* elements and picking one, while `elem.querySelector` just looks for one. So it's faster and also shorter to write.

En d'autres termes, le resultat est le même que pour `elem.querySelectorAll(css)[0]`, mais le dernier recherche pour tous les éléments *all* et en choisi un, alors que `elem.querySelector` ne recherche que pour un élément. C'est donc plus rapide et aussi plus court à écrire.

## matches

Previous methods were searching the DOM.
Les methodes que nous avons vu précedement cherchaient dans le DOM.

The [elem.matches(css)](http://dom.spec.whatwg.org/#dom-element-matches) does not look for anything, it merely checks if `elem` matches the given CSS-selector. It returns `true` or `false`.
L'élément [elem.matches(css)](http://dom.spec.whatwg.org/#dom-element-matches) ne recherche pour rien en particulier, il vérifie simplement que `elem` matche ou non avec le sélecteur CSS donné. Il retourne `true` ou `false`.

The method comes in handy when we are iterating over elements (like in an array or something) and trying to filter out those that interest us.
Cette méthode est pratique lorsque l'on répète plusieurs fois un même élément (comme dans une array par exemple) et lorsque nous essayons de filter par ce qui nous intéresse le plus.

For instance:
Par exemple:
```html run
<a href="http://example.com/file.zip">...</a>
<a href="http://ya.ru">...</a>

<script>
  // can be any collection instead of document.body.children
  // il peut s'agir de n'importe quelle collection à l'exception de document.body.children
  for (let elem of document.body.children) {
*!*
    if (elem.matches('a[href$="zip"]')) {
*/!*
      alert("The archive reference: " + elem.href );
    }
  }
</script>
```

## closest

*Ancestors* of an element are: parent, the parent of parent, its parent and so on. The ancestors together form the chain of parents from the element to the top.
Les *ancestors* d'un élément sont: un parent, le parent d'un parent, son parent et ainsi de suite. Les ancestors ensemle forment une chaîne de parents, en partant de l'élément jusqu'au sommet. 

The method `elem.closest(css)` looks the nearest ancestor that matches the CSS-selector. The `elem` itself is also included in the search.
La méthode `elem.closest(css)` recherche pour l'ancêtre le plus proche qui matche avec le selecteur CSS. L' `elem` lui-même est inclu dans la recherche.

In other words, the method `closest` goes up from the element and checks each of parents. If it matches the selector, then the search stops, and the ancestor is returned.
En d'autres termes, la methode `closest` va, en partant de l'élément, de ses parents les plus proches à ses parents les plus lointain. Si un élément matche avec le selecteur, alors la recherche s'arrête et le parent est retourné.

Par example:

```html run
<h1>Contents</h1>

<div class="contents">
  <ul class="book">
    <li class="chapter">Chapter 1</li>
    <li class="chapter">Chapter 1</li>
  </ul>
</div>

<script>
  let chapter = document.querySelector('.chapter'); // LI

  alert(chapter.closest('.book')); // UL
  alert(chapter.closest('.contents')); // DIV

  alert(chapter.closest('h1')); // null (car h1 n'est pas un ancêtre)
</script>
```

## getElementsBy*

There are also other methods to look for nodes by a tag, class, etc.

Today, they are mostly history, as `querySelector` is more powerful and shorter to write.

So here we cover them mainly for completeness, while you can still find them in the old scripts.

- `elem.getElementsByTagName(tag)` looks for elements with the given tag and returns the collection of them. The `tag` parameter can also be a star `"*"` for "any tags".
- `elem.getElementsByClassName(className)` returns elements that have the given CSS class.
- `document.getElementsByName(name)` returns elements with the given `name` attribute, document-wide. very rarely used.

For instance:
```js
// get all divs in the document
let divs = document.getElementsByTagName('div');
```

Let's find all `input` tags inside the table:

```html run height=50
<table id="table">
  <tr>
    <td>Your age:</td>

    <td>
      <label>
        <input type="radio" name="age" value="young" checked> less than 18
      </label>
      <label>
        <input type="radio" name="age" value="mature"> from 18 to 50
      </label>
      <label>
        <input type="radio" name="age" value="senior"> more than 60
      </label>
    </td>
  </tr>
</table>

<script>
*!*
  let inputs = table.getElementsByTagName('input');
*/!*

  for (let input of inputs) {
    alert( input.value + ': ' + input.checked );
  }
</script>
```

```warn header="Don't forget the `\"s\"` letter!"
Novice developers sometimes forget the letter `"s"`. That is, they try to call `getElementByTagName` instead of <code>getElement<b>s</b>ByTagName</code>.

The `"s"` letter is absent in `getElementById`, because it returns a single element. But `getElementsByTagName` returns a collection of elements, so there's `"s"` inside.
```

````warn header="It returns a collection, not an element!"
Another widespread novice mistake is to write:

```js
// doesn't work
document.getElementsByTagName('input').value = 5;
```

That won't work, because it takes a *collection* of inputs and assigns the value to it rather than to elements inside it.

We should either iterate over the collection or get an element by its index, and then assign, like this:

```js
// should work (if there's an input)
document.getElementsByTagName('input')[0].value = 5;
```
````

Looking for `.article` elements:

```html run height=50
<form name="my-form">
  <div class="article">Article</div>
  <div class="long article">Long article</div>
</form>

<script>
  // find by name attribute
  let form = document.getElementsByName('my-form')[0];

  // find by class inside the form
  let articles = form.getElementsByClassName('article');
  alert(articles.length); // 2, found two elements with class "article"
</script>
```

## Live collections

All methods `"getElementsBy*"` return a *live* collection. Such collections always reflect the current state of the document and "auto-update" when it changes.

In the example below, there are two scripts.

1. The first one creates a reference to the collection of `<div>`. As of now, its length is `1`.
2. The second scripts runs after the browser meets one more `<div>`, so its length is `2`.

```html run
<div>First div</div>

<script>
  let divs = document.getElementsByTagName('div');
  alert(divs.length); // 1
</script>

<div>Second div</div>

<script>
*!*
  alert(divs.length); // 2
*/!*
</script>
```

In contrast, `querySelectorAll` returns a *static* collection. It's like a fixed array of elements.

If we use it instead, then both scripts output `1`:


```html run
<div>First div</div>

<script>
  let divs = document.querySelectorAll('div');
  alert(divs.length); // 1
</script>

<div>Second div</div>

<script>
*!*
  alert(divs.length); // 1
*/!*
</script>
```

Now we can easily see the difference. The static collection did not increase after the appearance of a new `div` in the document.

## Summary

There are 6 main methods to search for nodes in DOM:

<table>
<thead>
<tr>
<td>Method</td>
<td>Searches by...</td>
<td>Can call on an element?</td>
<td>Live?</td>
</tr>
</thead>
<tbody>
<tr>
<td><code>querySelector</code></td>
<td>CSS-selector</td>
<td>✔</td>
<td>-</td>
</tr>
<tr>
<td><code>querySelectorAll</code></td>
<td>CSS-selector</td>
<td>✔</td>
<td>-</td>
</tr>
<tr>
<td><code>getElementById</code></td>
<td><code>id</code></td>
<td>-</td>
<td>-</td>
</tr>
<tr>
<td><code>getElementsByName</code></td>
<td><code>name</code></td>
<td>-</td>
<td>✔</td>
</tr>
<tr>
<td><code>getElementsByTagName</code></td>
<td>tag or <code>'*'</code></td>
<td>✔</td>
<td>✔</td>
</tr>
<tr>
<td><code>getElementsByClassName</code></td>
<td>class</td>
<td>✔</td>
<td>✔</td>
</tr>
</tbody>
</table>

By far the most used are `querySelector` and `querySelectorAll`, but `getElementBy*` can be sporadically helpful or found in the old scripts.

Besides that:

- There is `elem.matches(css)` to check if `elem` matches the given CSS selector.
- There is `elem.closest(css)` to look for the nearest ancestor that matches the given CSS-selector. The `elem` itself is also checked.

And let's mention one more method here to check for the child-parent relationship, as it's sometimes useful:
-  `elemA.contains(elemB)` returns true if `elemB` is inside `elemA` (a descendant of `elemA`) or when `elemA==elemB`.
