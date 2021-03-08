# High Performance JavaScript: Build Faster Web Application Interfaces (Nicholas C. Zakas)
## Kapitola 1: Loading and Execution
* Skripty na konec stránky.
* Seskupovat skripty do minimálního počtu souborů.
* Nevkládat inline skripty hned za element <link>, kde se načítá CSS soubor, prohlížeč změní své chování a blokuje vykreslování, dokud se CSS soubor nenačte a nezpracuje.
* Více skriptů najednou lze vkládat třeba uváděním více cest do jednoho elementu <script>, např. src=”load.php?script1.js&script2.js&script3.js”.
* Element <script> obsahuje atribut defer, který říká, že skript se má začít načítat až se nahraje obsah stránky, nedojde tedy k blokování prohlížeče při načítání js. Bohužel funguje jen ve FF 3.5+ a v IE 4+.
* Velmi dobré je načítat JS přes dynamické vytváření elementu script. Ten je pak kvůli IE potřeba vložit do hlavičky a ne do body.
* Dynamický vytvořený element script má událost onload, která je vykonána, jakmile je skript načten, tím lze tedy ohlídat, aby nebyl spuštěn skript, který potřebuje nějaký dynamicky načítaný JavaScript. IE to podporuje také, ale používá různé názvy pro metody a proměnné.
* Nové skripty je možné načítat i přes Ajax a po načtení dat se vytvoří inline element script. Určitá výhoda je v tom, že oproti dynamicky vytvořenému elementu script s externím souborem není v tomto případě js spuštěn hned, ale můžu po vykreslení stránky na pozadí načíst zbylé JavaScripty a ty pak spustit, až bude potřeba.
* Je doporučováno rozdělit JavaScripty na dva soubory, ty důležité, bez kterých se stránka neobejde ihned (např. init záložek) dát do jednoho souboru a všechny ostatní JavaScripty načíst až později.
* Je možné definovat vlastní metodu loadScript() nebo použít některá hotová řešení, např. jQuery.getScript(), YUI, LazyLoad library nebo LABjs library. Všechny přinášejí další výhody.
* Nedoporučuje se příliš ale vkládat loader do samostatného souboru, je to další požadavek. Buď ho přidat k nějakému existujícímu souboru, který se načte vždy, nebo ho minifikovat a vkládat před </body> do každé stránky, čímž se ušetří jeden požadavek.

## Kapitola 2: Data Access
* Přístup k proměnným v poli a objektu je obvykle pomalejší než přístup k proměnným deklarovaným přes var.
* Každá funkce má interní proměnnou [[Scope]], která reprezentuje scope, ve kterým byla funkce vytvořena, nazývá se scope chain a určuje data, ke kterým má funkce přístup. Každý objekt v scope chain se nazývá variable object a obsahuje seznam dostupných proměnných ve formě objektů klíč-hodnota.
* Jakmile je funkce vytvořena, ve scope chain je jeden Global object, který má seznam globálních proměnných (this, window, document atd.).
* Jakmile je funkce vykonána, do scope chain se přidá další objekt nazvaný execution context (pod názvem Activation object), který obsahuje variable objects pro execution context (lokální proměnné, pojmenované argumenty, pole arguments a this). Podstatné je, že je tento objekt vložen před Global object.
* Když se pak uvnitř funkce odkazujeme na nějakou proměnnou, nejprve je prohledáván Activation object a až poté Global object. Z toho vyplývá, že používání lokálních proměnných je rychlejší než používání globálních proměnných, kterým je dobré se vyhýbat nebo je předat funkci a vytvořit z nich lokální proměnné. Ač se to nezdá, třeba v případě IE může jít i o výrazné zpomalení. Odkazujeme-li se třeba v nějaké funkci vícekrát na object document (document.body, document.getElementByTagName ap.), je lepší ho vložit na začátku funkce do lokální proměnné.
* Příkazy with a try-catch a funkce eval vytváří dynamic scope, které nemůže být determinováno statickou analýzou. With se nemá používat vůbec, eval pouze v krajních případech. Try-catch se nesmí používat jinak než na zpracování kritických chyb a v bloku catch se doporučuje zavolat pouze funkci, které je předán chybový objekt, žádný další kód by zde být neměl.
* Pokud používáme closure, jeho proměnná [[Scope]] odkazuje na Activation object i Global object podobně jako scope chain execution context. Na Activation object i Global object nyní existují dva odkazy místo jednoho. To znamená, že je-li execution context zničen, Activation object zůstává v paměti, protože na něj vede další odkaz. Closures tedy potřebují více paměti než funkce, které closures nevyužívají. Proto se doporučuje ukládat časté out-of-scope proměnné do lokálních proměnných.
* Operátor in lze použít pro zjištění, zda objekt obsahuje atribut, např: alert(“test” in {test:1}); //true
* Čím hlouběji ve struktuře prototypů budeme hledat, tím bude kód pomalejší, tedy location.href je rychlejší než window.location.href, což je rychlejší než window.location.href.toString().

## Kapitola 3: DOM Scripting
* DOM je v prohlížečích oddělen od interpreta JavaScriptu (třeba v IE dvě samostatné .dll knihovny).
* Snažit se přistupovat k DOM co nejméně a vždy všechno cachovat.
* Vyhýbat se změnám DOM v cyklech, jedna změna najednou vs. mnoho změn v cyklu je v případě IE na 15 tis. změnách pomalejší 275x.
* Používání DOM metod je pravděpodobně o trochu rychlejší než používání innerHTML (rozdíl je nepatrný), podobně klonování nodu místo jejich vytváření příliš výkon nezlepší.
* Kolekce DOM elementů získané třeba přes getElementByTagName() není klasické pole, ale nějaký hybrid, podstatné je, že přístup k proměnné length kolekce stojí hodně času a musí být cachován (v IE 200x pomalejší necachovaná verze než cachovaná).
* DOM vlastnost nextSibling je rychlejší než childNodes.
* Moderní prohlížeče mají vlastní implementace některých DOM properties, třeba property children vedle childNodes. Práce s těmito proměnnými je pak rychlejší (např. childNodes iteruje i přes poznámky, zatímco children ne).
* Moderní prohlížeče podporují DOM metodu document.querySelectorAll(), kde je možné používat CSS selektory pro hledání nodů v DOMu, podpora je od IE8+, všechny ostatní moderní prohlížeče metodu podporují již v dřívějších verzích.
* Prohlížeč udržuje dvě struktury:
* DOM tree (reprezentace struktury stránky)
* render tree (obsahuje informace, jak bude strom zobrazen v prohlížeči)
* Pokud měníme strukturu DOM stránky, tomuto procesu se říká reflow. Následně prohlížeč upravuje i vizuální zobrazení stránky a tomu se říká repaint. Ne vždy musí reflow proběhnout, pokud měníme např. barvu pozadí, reflow neproběhne. Reflow i repaint jsou časově velmi náročné operace a je dobré se jim vyhýbat, jak to jen jde.
* Pozor na dotazy na proměnné offsetX, scrollX, clientX, getComputedStyle(), musí být aktuální a tak může být vykonáno reflow zbytečně. Podobně změny stylu provádět najednou, neměnit každou vlastnost zvlášť.
* Redukce procesu reflow a repaint může být omezena tak, že se nejprve vybere element z dokumentu, změní se a pak se přidá zpět do dokumentu místo aplikace několika dílčích změn. Element může být vybrán skrytím, použitím fragmentu (nejlepší) či kopií originálního elementu.
* Kvůli reflow a repaint má IE velký problém s pseudoselektorem :hover, který se aplikuje na velké množství elementů, např. na tabulky či seznamy (nikoliv na odkazy), zpracování může výrazně zpomalit.
* Problém je i s přiřazováním událostí k jednotlivým elementům. Samotné přiřazení je docela časově náročné a často i zbytečné, nemá smysl přiřazovat třeba 100x onlick, když uživatel klikne jen na jeden element a použije se tedy jen jedna událost. Místo toho je lepší dát třeba událost onclick na rodiče elementů a tam si to ošéfovat.

## Kapitola 4: Algoritms and Flow Control
* Nepoužívat for-in loop pro procházení prvků pole.
* Cachovat vlastnost length (zrychlení o 25% - 50%).
* Pokud je to možné obrátit podmínku, místo i++ používat i-- (zrychlení až o 50%).
* V případě více cykl (více než 1000) je vhodné použít techniku Duff’s Device, která spočívá v procházení cyklů třeba po 10 a v rámci cyklu zpracuje 10 hodnot.
* Switch je rychlejší než if-else, ale kvůli přehlednosti je lepší switch používat v případě 3+ podmínek.
* Místo mnoha podmínek u switch je možné využít techniku array lookup, kdy se vytvoří pole všech hodnot.
* Rekurze mají limitovaný počet volání, většinou v řádech jednotek tisíců, při překročení vyskočí výjimka Stack owerflow, Too much recursion ap.
* Rekurze by se měly cachovat.

## Kapitola 5: String and Regular Expression
* Spojení přes str += “a” + “b” je pomalé, protože se vytváří dočasná proměnná v paměti, která obsahuje spojení řetězce “ab”. Lepší je str += “a”; str += “b”; nebo str = str + “a” + “b”.
* Spojování přes += “str” nefunguje dobře v IE7 a mladších IE prohlížečích, mají problémy s pamětí a např. 20 tis. takových spojení pak trvá a i půl minuty (kvůli opakovanému alokování paměti). Pro IE7 a mladší IE se doporučuje spojení přes metodu join(“”).
* Pokud u regulárních výrazů používíme alternativy (operátor |), jako první je v reguálním výrazu alternativy dobré zapsat nejpravděpodobnější výsledek.
* Lookaheads: 
* matches only if there is a following pattern: (?=pattern)
* matches only if there is not a following pattern: (?!pattern)
* …

## Kapitola 6: Responsive Interfaces
* Limity prohlížeče:
* call stack size limit (kap. 4)
* long-running script limit
* Limity jednotlivých prohlížečů:
* IE max. 5 mil. operací
* Firefox max. 10 vteřin
* Safari max. 5 vteřin
* Chrome ani Opera limity nemají (záleží na op. systému)
* Eliminace úkolu JavaScriptu max. na 100 milisekund.
* Funkce pro timing: setInterval, setTimeout.
* Pro výše uvedené funkce se nastavuje čas v ms, po této době bude teprve task přidán do UI queue, neznamená to, že bude hned vykonán (záleží na tom, kolik požadavků bylo již v řadě před ním).
* Timer nikdy nebude vykonán, dokud funkce, ve které byl definován, nebude kompletně dokončena. Pokud by měl být timer vykonán dříve než daná funkce, ve které byl definován, timer kód bude vykonán téměř okamžitě po dokončení dané funkce. Timer v tomto případě vyresetuje všechna nastavení prohlížeče pro dlouho trvající skripty, takže se nezobrazí dialog oznamující dlouhý běh skriptu (str 113).
* Nejmenší nastavitelná hodnota pro setTimeout je 25 ms (menší hodnota může zablokovat IE).
* Příklad nastavení timeru pro dlouhotrvající loop:
* místo:
* for (var i = 0, len = items.length; i < len; i++) {
*     process(items[i]);
* }
* se použije tohle:
* var todo = items.concat(); //vytvoření klonu originílního pole
* setTimeout(function(){
*     process(todo.shift());
*     //pokud zbyva vykonat jeste nejaky cyklus, zavolat stejnou funkci
*     if (todo.length > 0) {
*         setTimeout(arguments.callee, 25); //callee odkazuje na anon. funkci
*     }
* }, 25);
* Pro Chrome, Firefox a Safari je možné použít Web Workers z HTML5, které umožní vykonat daný JS kód mimo vlákno prohlížeče.
* Protože jsou Web Workers mimo hlavní vlákno, jsou zde mnohá omezení (mnoho vlastností je read-only, ap.). Komunikace se skriptem pomocí onmessage události. 

## Kapitola 7: Ajax
* Průběžné načítání dat přes ajax je možné realizovat přes req.readyState == 3.
* Multipart XHR (MXHR) umožňuje načítat mnoho zdrojů jedním požadavkem. Na straně serveru načteme několik objektů (např. obrázky) a konvertujeme je na řetězec (base64), na straně JavaScriptu zase řetězec rozdělíme na samostatné objekty a např. obrázek JPG načteme přes img.src = ‘data:image/jpeg;base64, ’ + data; Podobně lze načítat i další objekty. Problém MXHR je, že data nejsou cachovány, takže je potřeba je stahovat pokaždé. Výhoda ovšem je, že lze zrychlit kompletní načtení stránky podle testů 4x - 10x.
* Data z klienta na server je možné zasílat přes tzv. beacons (v JS např. new Image().src = xyz). Je to rychlejší než posílat data přes ajax. Server by měl odpovídat přes hlavičku 204 No Content.
* Můžeme využívat mnoho formátu dat pro inline načítání javascriptu:
* XML
* Nejpomalejší, ukecaný. Používat raději atributy než samostatné elementy (výrazně rychlejší parsování na straně klienta)
* XPath je často mnohem rychlejší než metoda getElementByTagName().
* JSON
* Z hlediska rychlosti je lepší posílat data v JSON jako pole, ne jako objekt: [‘a’,’b’,’c’]] místo [‘a1’:’a’, ‘b1’:’b’, ‘c1’: ‘c’]. Formát je ale hůře čitelnější.
* JSONP
* Vytvoření dynamického tagu <script> a načtení dat v JSON.
* Extrémně rychlé (10x rychlejší než klasický JSON), netřeba parsovat data přes eval() jako v případě přijetí JSON přes ajax.
* HTML
* Pouze pokud chceme data přímo vložit přes innerHTML, velmi ukecaný formát.
* Vlastní formát
* Např. posílání dat ve formátu a;b;c;d;. Na straně klienta se pak data získají přes data.split(‘;’);
* Extrémně rychlé, nejlepší pro velké objemy dat.
* Používat hlavičku Expire pro cachování dat na straně serveru.

## Kapitola 8: Programming Practices
* Funkce eval(): var a = eval(“array[0]”); je 100x pomalejší než var a = array[0];
* Bitwise operátory jsou extrémně rychlé, hodí se třeba pro techniku bitmasking pro konfiguraci:
* var OPTION_A = 1;
* var OPTION_B = 2;
* var OPTION_C = 4;
* var OPTION_D = 8;
* var OPTION_E = 16;
* var options = OPTION_A | OPTION_C | OPTION_D;
* if (options & OPTION_A) {
*     //do something
* }

## Kapitola 9: Building and Deploying High-Performace JavaSctript Application
* Apache Ant, preprocesing pro develepoment.
* Kombinovat JavaScriptové soubory, minimalizovat požadavky
* Minifikace: YUI Compressor nebo Google Closure
* Komprese přes gzip, mod_reflate & hlavička Accept-Encoding
* Je-li nekomprimovaný JS větší než 25KB nebo komprimovaný větší než 15KB, prohlížeč si ho neuloží do cache a bude ho stahovat vždy, spojování souborů nemusí být vždy výhodné.
* Hlavičku Expires možno nastavit přes Apache.
* Smasher (https://github.com/jlecomte/smasher) zkombinuje js soubory, “předprocesuje je” a minifikuje je.
* Využívat CDN

## Kapitola 10: Tools
* Profilování přes new Date().
* YUI Profiler
* Ideálně pojmenovávat anonymní funkce pro log.
* Firebug
* Console API pro Firebug: console.profile(), console.profileEnd(), console.time(), console.timeEnd().
* IE Developer Tools, Safari Web Inspector, Chrome Developer Tools
* Page Speed
* Fiddler a dynaTrace Ajax Edition: velmi podrobná analýza všech požadavků
