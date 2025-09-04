---
title: "Jazykové mutace v SunLight CMS 8"
author: "Golfin"
toc: true
toc_sticky: true
categories:
  - blog
tags:
  - blog
  - navody
---

# Práce s jazykovými balíčky

Sunlight je jeden ze systémů, který pracuje s jazykem. Ať už se jedná o celý CMS, nebo o pluginy, které ho rozšiřují - vždy je možnost přizpůsobit web do různých jazyků.

### Jak to funguje?

Systém používá jednoduché pole v php souboru, kde je na každém řádku klíč k danému překladu. Základní jazykové balíčky (extendy) jsou v českém a anglickém jazyce a jsou umístěny ve složce Languages ``(\plugins\languages)``. Každý jazyk má svoji složku (adresář) pojmenovaný podle zásad [ISO Code](https://cs.wikipedia.org/wiki/Seznam_k%C3%B3d%C5%AF_ISO_639-2). Například čeština má název adresáře ``cs`` a angličtina ``en``.
Velmi zajímavá informace ohledně ISO code je zde: [RSS-Specific Language Codes](https://www.rssboard.org/rss-language-codes). Zde se například dozvíme, jak rozlišit evropskou francouzštinu od kanadské.
Ale podívejme se, jak to funguje. Ve složce ``cs`` najdeme tři soubory: admin_dictionary.php, dictionary.php a plugin.json. admin_dictionary.php je php skript pro administrační část Sunlight, dictionary.php pro veřejnou a soubor plugin.json inicializuje tento jazyk v celém systému Sunlight, tedy jako extend (plugin). U dalších jazykových balíčků *(např. en)* přibude další složka ``class``, ve které je soubor ``EnglishLanguage.php``. Je to třída, která určuje vlastnost jazyka, který se do dystému přidává. V tomto případě má název EnglishLanguage. Pro němčinu by to bylo DeutscheLanguage, pro francouzštinu FrancaisLanguage. A tak podobně. Proč to není u českého jazyka? protože to je systémový jazyk v českém CMS systému.

*Poznámka od Jirky Daňka: Ty třídy navíc jsou tam proto, protože jiné státy používají jiné oddělovače měn, desetinných čísel a tohle řeší správné formátování výpisu*

Podívejte se na zápis v souboru ``plugin.json`` na ``decimal_point`` a ``thousands_separator``.
```json
{
    
    "class": "EnglishLanguage",
    "iso_code": "en",
    "decimal_point": ".",
    "thousands_separator": ","
}
```

### Ukázka kódu php souborů

```php
<?php

return [
    'admin.link.site' => 'Zobrazit stránky',
    'admin.link.newwin' => 'Nové okno',
    'admin.link.home' => 'zpět na hlavní stranu',
    'admin.denied' => 'Nemáte dostatečná práva pro vstup do administrace.',
    'admin.busy_overlay.text' => 'Prosím čekejte',
    'admin.iplog' => 'Log IP adres',
    'admin.moduleunavailable' => 'Modul není dostupný.',
    'admin.menu.index' => 'Přehled',
	  //...
	  //...
	  //...
];
```
Vidíme zde pole, kde na každém řádku je jednotlivý klíč k dannému výrazu nebo textu. Je to vlastně velmi jednoduché. Z těchto language souborů pak čerpá každý skript, nebo funkce a tak dále. Soubory jazykových balíčků se ukládají v UTF8 bez [BOM](https://cs.wikipedia.org/wiki/Byte_order_mark) kódování. To umožní používat diakritiku a další speciální znaky u jiných jazyků, než je angličtina *(italština, španělština ...)*. Čeština, němčina, francouzština, ale například také turečtina a další jazyky obsahují své speciální znaky a diakritiku. Proto je nanejvýš vhodné soubory uložit jako UTF8.

###Jak vypadá zápis v nějakém pluginu nebo v základním systému

Stěžejní je funkce ``_lang()`` zabudovaná v Sunlight. Například název tlačítka pro chystaný extend bude vypadat takto: ``_lang('downloadmanager.settings.download')``

Všiměme si, před slovem ``lang`` je podtržítko. To je velmi důležité.

> Poznámka od Jirky Daňka: Funkce _lang() je dostupná globálně bez nutnosti importu



A nyní se podíváme na zápis ``langs`` v souboru ``plugin.json``. K čemu to vlastně slouží? Když se podíváme do dokumentace jak má vypadat klíč  [langs](https://sunlight-cms.cz/dokumentace/pluginy/extend#option-langs), vidíme přesně popsáno a jaké vlastnosti zápis obsahuje. Ale pro lepší ujasnění bych rád napsal, jak mi to vysvětlil Jirka Daněk. 

**Ukázka struktury kódu:**

```json
"langs": {
    "klic": "slozka"
},
```

V dokumentaci vidíme  jako ``klíč`` název ``my_plugin``. Většinou to může být název vámi vytvářeného pluginu. Dále tam vidíme název ``lang``, což v tomto případě je název složky umístěné v kořenu pluginu. Můžete si ji pojmenovat i jinak, ale je dobré dodržet určité zásady psaní pluginů a pojmenovat takovou složku jako ``lang``.
Uvedu to na příkladu u extendu pro Download Manager *(ještě neexistuje veřejně)*. V předchozím obrázku jsme viděli různá tlačítka s českými názvy. V jakémkoliv pluginu je potřeba použít funkci ``_lang()``. Když chcete zobrazit nějaký přeložený text, do této funkce vložíte klíč *(řetězec)* z překladových slovníků. Například takto:

```php
_lang('downloadmanager.module.title')
```

V tomto případě jsem použil překlad názvu pluginu Download Manager z jazykového slovníku pluginu. Proto je před textem ``module.title`` klíč *(uvedený v souboru plugin.json)* - ``downloadmanager``. Jedná se o prefix daného jazykové sady. Ale v jazykovém souboru klíč uveden není:

```php
'module.title' => 'Download Manager',
```

Ovšem, pokud bychom chtěli použít nějaký systémový překlad *(např. pro všeobecná tlačítka a tak podobně)*, není potřeba klíč pluginu vkládat. Systém SL si danný řetězec najde sám.
Například název tlačítka **Nahrát soubor** má v ``admin_dictionary.php`` řetězec:

```php
'admin.fman.menu.upload' => 'Nahrát soubor',
```

V tomto případě využíváme systémového překladu a klíč ``downloadmanager`` není potřeba vkládat. Zápis bude vypadat takto:

```php
_lang('admin.fman.menu.upload')
```

Když si to shrneme. Pro využití systémových překladů není potřeba vkládat klíč uvedený v ``plugin.json``. Ale pro překlady konkrétní a specifické vlastnosti pluginu (které jsou naskriptovány přímo v jazykových souborech danného pluginu - většinou ve složce lang) už klíč musíme použít. Tím systém SL ví, kam má šáhnout a kde danný řetězec najít.