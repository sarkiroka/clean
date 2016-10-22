# Clean

## A probléma

Amikor jQuery-t használsz, érezted már, hogy beköltözik a dizájn / tördelés egy része a javascript fájljaidba? Erre gondolok:
```
$('.button').click( ()=>{ $(this).addClass('clicked'); });
```
Mégis mit keres ott mindenféle CSS szelektor? Miért kell CSS szelektorokat a kódba tennünk?

Amikor AngularJS-t használsz, érezted már, hogy beköltözik az üzleti logika a HTML markup-ba? Erre gondolok:
```
<div ng-controller="xy as yx">
	<input class="form-input" type="password" ng-model="xy.userPassword" ng-class="{error:xy.$errors.userPassword, strong:xy.isStrength(xy.userPassword)}" show-password="true" maxlength="20" min-length="8" compare-to="xy.userPasswordAgain" validate-strength ng-keyup="checkStrength()">
</div>
```
Mit keres a HTML közepén néhány funkcióhívás? Tiszta `onclick` feeling. És arról még nem is esett szó, amikor egy bonyolultabb kifejezést írunk bele.

Amikor komponens alapon fejlesztesz, érezted már, hogy indokolatlanul összekeverednek a nyelvek? Lényegtelen, hogy melyik keretrendszert használod, valahol az a vége, hogy egy problémát egy olyan helyen próbálsz megoldani, ami nem arra való. És ide lehet érteni azokat a szerver oldali komponenseket, amik kliens oldali technológiákat próbálnak meg reprezentálni, és természetesen azokat a csak kliensre korlátozódó keretrendszereket is, amik kellőképpen vegyítik a CSS-HTML-JS hármast.

## Az új remény

Képzeld el, hogy a HTML-ed tiszta marad, mentes minden sallangtól, és tényleg csak annyi lesz benne, amennyit a tördelő beleálmodott. Vagy amennyit a stackoverflow-ról lemásoltál :) Ha valami probléma folytán elmaradna a css/js betöltése, akkor is viszonylag élvezhető marad, sőt, még akár müködő is!

Képzeld el, hogy a javascript kódod tiszta marad, csak annyi lesz benne, amennyi a tényleges üzleti logikádhoz kell. Nem fog kelleni bele injektálás, annotálás, DOM-részlet, mágja.

Képzeld el, hogy úgy a HTML-ed, CSS-ed, és a JS-ed is újra felhasználható lesz, akár külön külön, akár egyben. Annak ellenére így van, hogy minden ügyfél kicsit máshogy akarja.

### Hogyan?

Tudom, hogy senki sem ír a való életben helloworld-szerű programokat, de mégis, azon keresztül a legegyszerűbb bemutatni. Különösen egy ennyire szokatlan megközelítés esetén fontos, hogy az alapokkal kezdjük.

hello.js
```javascript
alert('hello world')
```
hello.html
```html
<html>
	<head>
		<script src='clean.js?hello.json=body'></script>
	</head>

	<body>
		<input type="button" value="Say hello" />
	</body>
</html>
```
hello.json
```json
{
	"input[type='button']": {
		"click": "hello"
	}
}
```
Tehát a JS fájlunk mindössze azt az üzleti logikát tartalmazta, amire az hivatott, egyetlen alert-et.
A HTML sem hemzseg a sallangoktól. Behúztuk a [clean.js](clean.js)-t, és...

és összekötöttük a HTML egy részét a JS-el. Egészen pontosan egy összekötő leíró fájl nevét, mint paraméter nevéhez értékül egy CSS selectort rendeltünk értékül. Ennek jelentése pedig az, hogy "ezt az összekötő fájlt használd a következő szelektoron, mint root elemen".

A JSON fájlunk egy kétszintű leíró:
 * Első szinten egy - a kapott paraméterhez képest relatív - CSS szelektor van
 * Második szinten pedig jelen esetben egy esemény. Ennek értéke az üzleti logikát megvalósító fájl.

### Sok kis apró fájl?

Nem feltétlen. Természetesen lehetőség van kiajánlott függvény használatára is. Ekkor a fájlok a következőképpen módosulnak:
myscript.js
```
if(!module){
	module={exports:{}};
}
module.exports.hello=function(){
	alert('hello world');
}
```
hello.json
```
{
	"&": {
		"click": "myscript.hello"
	}
}
```

### Tisztán, nem keverve!

Nem akarok egy új jQuery-t, vagy AngularJS-t bemutatni. Viszont a fenti példából is láthattad, hogy ez a rendszer segít, hogy minden érintett rész a legjobbat hozhassa ki magából, minden rész maradjon tisztán, nem keverve.

## Adatkötés

binding-example.json
```json
{
	"input[name=\"username\"]": {
		"bind": "myscript.username"
	}
}
```

Van lehetőség az angularban már úgy megszokott "beeírok az inputba és megjelenik egy div-ben" dologra:
```json
{
	"input[name=\"username\"]": {
		"bind": "myscript.username"
	},
	"div.feedback": {
		"bind": "myscript.username"
	}
}
```


## Lista, template, stb
myscript.js
```javascript
var data=['alma', 'körte', 'szilva'];
```
html
```html
<body>
	<ul>
		<li>
			<span>Lorem</span>
		</li>
		<li>
			<span>ipsum</span>
		</li>
	</ul>
</body>
```
json
```json
{
	"body ul li": {
		"foreach": "myscript.data"
	},
	"body ul li span": {
		"bind": "myscript.data/$item"
	}
}
```
A példa ugyan nem igényel magyarázatot, mégis elmondanám, hogy a html önmagában csak "sitebuild" és a benne lévő két listaelem csak mint template szolgál.

A json-ban lévő foreach is egyértelmű, a megadott dolog elemein fog végigiterálni. Ez lehet tömb vagy objektum.

A második elem a json-ban egy `span` amelynek értékébe szeretnénk az iteráláshoz használt dolog aktuális elemét betenni.

## json API
- bind
- foreach
- click
- focus
- blur
- hover
- leave

## javascript export lehetőségek
Lehet a már mutatott module.exports-al és egyelőre maradjunk is ennyiben.

