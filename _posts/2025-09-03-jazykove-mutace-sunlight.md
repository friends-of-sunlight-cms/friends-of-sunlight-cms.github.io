---
title: "Jazykové mutace v SunLight CMS 8"
author: "Golfin"
toc: true
toc_sticky: true
categories:
  - blog
tags:
  - blog
  - návody
---

# Práce s jazykovımi balíèky

Sunlight je jeden ze systémù, kterı pracuje s jazykem. A u se jedná o celı CMS, nebo o pluginy, které ho rozšiøují - vdy je monost pøizpùsobit web do rùznıch jazykù.

### Jak to funguje?

Systém pouívá jednoduché pole v php souboru, kde je na kadém øádku klíè k danému pøekladu. Základní jazykové balíèky (extendy) jsou v èeském a anglickém jazyce a jsou umístìny ve sloce Languages (\název vašeho webu\plugins\languages). Kadı jazyk má svoji sloku (adresáø) pojmenovanı podle zásad ISO Code. Napøíklad èeština má název adresáøe **cs** a angliètina **en**.
Velmi zajímavá informace ohlednì ISO code je zde: [RSS-Specific Language Codes](https://www.rssboard.org/rss-language-codes). Zde se napøíklad dozvíme, jak rozlišit evropskou francouzštinu od kanadské.
Ale podívejme se, jak to funguje. Ve sloce **cs** najdeme tøi soubory: admin_dictionary.php, dictionary.php a plugin.json. admin_dictionary.php je php skript pro administraèní èást Sunlight, dictionary.php pro veøejnou a soubor plugin.json inicializuje tento jazyk v celém systému Sunlight, tedy jako extend (plugin). U dalších jazykovıch balíèkù (napø. en) pøibude další sloka **class**, ve které je soubor EnglishLanguage.php. Je to tøída, která urèuje vlastnost jazyka, kterı se do dystému pøidává. V tomto pøípadì má název EnglishLanguage. Pro nìmèinu by to bylo DeutscheLanguage, pro francouzštinu FrancaisLanguage. A tak podobnì. Proè to není u èeského jazyka? protoe to je systémovı jazyk v èeském CMS systému.

*Poznámka od Jirky Daòka: Ty tøídy navíc jsou tam proto, protoe jiné státy pouívají jiné oddìlovaèe mìn, desetinnıch èísel a tohle øeší správnı vıpis*

Podívejte se na zápis v souboru plugin.json na decimal_point a thousands_separator.
```json
{
    
    "class": "EnglishLanguage",
    "iso_code": "en",
    "decimal_point": ".",
    "thousands_separator": ","
}
```

###Ukázka kódu php souborù
```php
<?php

return [
    'admin.link.site' => 'Zobrazit stránky',
    'admin.link.newwin' => 'Nové okno',
    'admin.link.home' => 'zpìt na hlavní stranu',
    'admin.denied' => 'Nemáte dostateèná práva pro vstup do administrace.',
    'admin.busy_overlay.text' => 'Prosím èekejte',
    'admin.iplog' => 'Log IP adres',
    'admin.moduleunavailable' => 'Modul není dostupnı.',
    'admin.menu.index' => 'Pøehled',
	.
	.
	.
	
];
```
Vidíme zde pole, kde na kadém øádku je jednotlivı klíè k dannému vırazu nebo textu. Je to vlastnì velmi jednoduché. Z tìchto language souborù pak èerpá kadı skript, nebo funkce a tak dále. Soubory jazykovıch balíèkù se ukládají v UTF8 (bez BOM) kódování. To umoní pouívat diakritiku a další speciální znaky u jinıch jazykù, ne je angliètina (italština, španìlština ...). Èeština, nìmèina, francouzština, ale napøíklad také tureètina a další jazyky obsahují své speciální znaky a diakritiku. Proto je nanejvıš vhodné soubory uloit jako UTF8.

###Jak vypadá zápis v nìjakém pluginu nebo v základním systému

Stìejní je funkce lang zabudovaná v Sunlight. Napøíklad název tlaèítka pro chystanı extend bude vypadat takto:
> _lang('downloadmanager.settings.download')

Všimìme si, pøed slovem lang je podtrítko. To je velmi dùleité.

*Poznámka od Jirky Daòka: Funkce _lang() je dostupná globálnì bez nutnosti importu*



A nyní se podíváme na zápis *langs* v souboru plugin.json. K èemu to vlastnì slouí? Kdy se podíváme do dokumentace jak má vypadat klíè  [langs](https://sunlight-cms.cz/dokumentace/pluginy/extend#option-langs), vidíme pøesnì popsáno a jaké vlastnosti zápis obsahuje. Ale pro lepší ujasnìní bych rád napsal, jak mi to vysvìtlil Jirka Danìk. 

**Ukázka struktury kódu:**

```json
"langs": {
    "klic": "slozka"
},
```
V dokumentaci vidíme  jako *klíè* název *my_plugin*. Vìtšinou to mùe bıt název vámi vytváøeného pluginu. Dále tam vidíme název *lang*, co v tomto pøípadì je název sloky umístìné v koøenu pluginu. Mùete si ji pojmenovat i jinak, ale je dobré dodret urèité zásady psaní pluginù a pojmenovat takovou sloku jako *lang*.
Uvedu to na pøíkladu u extendu pro Download Manager (ještì neexistuje veøejnì).  V pøedchozím obrázku jsme vidìli rùzná tlaèítka s èeskımi názvy. V jakémkoliv pluginu je potøeba pouít funkci _lang(). Kdy chcete zobrazit nìjakı pøeloenı text, do této funkce vloíte klíè (øetìzec) z pøekladovıch slovníkù. Napøíklad takto:
```php
_lang('downloadmanager.module.title')
```
V tomto pøípadì jsem pouil pøeklad názvu pluginu (Download Manager) z jazykového slovníku pluginu. Proto je pøed textem module.title klíè (uvedenı v souboru plugin.json) - downloadmanager.  Ale v jazykovém souboru klíè uveden není:
```php
'module.title' => 'Download Manager',
```

Ovšem, pokud bychom chtìli pouít nìjakı systémovı pøeklad (napø. pro všeobecná tlaèítka a tak podobnì), není potøeba klíè pluginu vkládat. Systém SL si dannı øetìzec najde sám.
Napøíklad název tlaèítka **Nahrát soubor** má v admin_dictionary.php øetìzec:
```php
'admin.fman.menu.upload' => 'Nahrát soubor',
```
V tomto pøípadì vyuíváme systémového pøekladu a klíè *downloadmanager* není potøeba vkládat. Zápis bude vypadat takto:
```php
_lang('admin.fman.menu.upload')
```
Kdy si to shrneme. Pro vyuití systémovıch pøekladù není potøeba vkládat klíè uvedenı v plugin.json. Ale pro pøeklady konkrétní a specifické vlastnosti pluginu (které jsou naskriptovány pøímo v jazykovıch souborech danného pluginu - vìtšinou ve sloce lang) u klíè musíme pouít. Tím systém SL ví, kam má šáhnout a kde dannı øetìzec najít.