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

Képzeld el, hogy a HTML-ed tiszta marad, mentes minden sallangtól, és tényleg csak annyi lesz benne, amennyit a tördelő beleálmodott. Vagy amennyit a stackoverflow-ról lemásoltál :)

Képzeld el, hogy a javascript kódod tiszta marad, csak annyi lesz benne, amennyi a tényleges üzleti logikádhoz kell.

Képzeld el, hogy úgy a HTML-ed, CSS-ed, és a JS-ed is újra felhasználható lesz, akár külön külön, akár egyben.

### Hogyan?

Tudom, hogy senki sem ír a való életben helloworld-szerű programokat, de mégis, azon keresztül a legegyszerűbb bemutatni. Különösen egy ennyire más megközelítés esetén.

hello.js
```
alert('hello world')
```
hello.html
```
<html>
	<head>
		<script src='clean.js?hello.json=body input[type="button"]'></script>
	</head>

	<body>
		<input type="button" value="Say hello" />
	</body>
</html>
```
hello.json
```
{
	"&": {
		"click": "hello"
	}
}
```
Tehát a JS fájlunk mindössze azt az üzleti logikát tartalmazta, amire az hivatott, egyetlen alert-et.
A HTML sem hemzseg a sallangoktól. Behúztuk a [clean.js](clean.js)-t, és...

és összekötöttük a HTML egy részét a JS-el. Egészen pontosan egy összekötő leíró fájl nevét, mint paraméter nevéhez értékül egy CSS selectort rendeltünk értékül. Ennek jelentése pedig az, hogy "ezt az összekötő fájlt használd a következő szelektoron".

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
```
{
	"input[name=\"username\"]": {
		"bind": "myscript.username"
	}
}
```
