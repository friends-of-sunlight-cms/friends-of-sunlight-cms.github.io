---
title: "Moduly - 1. část"
author: "Golfin"
toc: true
toc_sticky: true
categories:
  - laikuv-pruvodce
tags:
  - laikuv-pruvodce
  - navody
  - moduly
---

# Jak vytvořit v administračním rozhraní nové hlavní menu?
Jistě mnozí z uživatelů Sunlight uvažovali nad tím, jak si vytvořit novou položku hlavního admin menu. Vlastně je to opravdu velmi jednoduché. SL, když ho prozkoumáte, najdete mnoho systémových a admin souborů, ve kterých se tuto informaci můžete dozvědět. Takže se podívejme na soubor gamedev\admin\modules.php. tento php skript obsahuje výchozí systémové položky modulů. Tak jak si otevřeme administrační rozhraní SL, vidíme horní lištu s menu (Přehled, Správa obsahu, Uživatelé a skupiny ... a tak dále.) To řeší tento soubor. A mi chceme do tohoto hlavního menu přidat další, svoji položku. Tak jdeme na to ...

Vytvoříme si nový plugin s názvem **examplemodul** (umístíme do plugins\extends\).

**plugin.json**
V naší složce examplemodul si vytvoříme soubor plugin.json, bez kterého se plugin nezaregistruje. Obsah vložte následující:
```json
{
    "$schema": "../../../system/schema/extend.json",
    "name": "Example modul",
    "description": "Example show Modul - Global admin menu",
    "authors": [
        {"name": "Golfin", "url": "https://github.com/Helwa-Surinen/"}
    ],
    "version": "1.0.0",
    "environment": {
        "system": "^8.3.1"
    },
    "langs": {
        "modul": "lang"
    },
    "events.admin": [
        {"event": "admin.init", "script": "event/admin/admin_init.php"}
    ]
}
```
**lang**

Pak si vytvoříme novou složku *lang*. Tam vložíme jazykové soubory pro tento plugin. V plugin.json vidíte, že pro tyto language řetězce budeme používat klíč *modul*.
Ve složce lang si vytvoříme soubory cs.php a en.php. Do nich vložíme následující kód pro cs:
```php
<?php

return [
    // admin
	'module.title' => 'Položka v Hlavním menu',
];
```

A pro anglickou verzi:

```php
<?php

return [
    // admin
	'module.title' => 'Example Global menu',
];
```
**event**
Potom v našem pluginu vytvoříme složku *event* (musí být přímo ve složce *examplemodul*). Ve složce *event* si vytvoříme další složku s názvem *admin*.
A v ní vytvoříme nový php soubor admin_init.php. Pozor, tento php skript musíme zaregistrovat v plugin.json. A to následujícím způsobem (mi to tam už máme):
```json
    "events.admin": [
        {"event": "admin.init", "script": "event/admin/admin_init.php"}
    ]
```
Vidíte, že nám tuto eventu odkazuje do složky admin na soubor admin_init.php.

**Vlastní skript, který zobrazí menu**
A to je jádro našeho snažení. Vlastní obsah skriptu, který umožní zobrazení nějaké naší hlavní položky v admin menu. Když se vrátím na začátek, prohlédli jste si soubor modules.php. Náš skript bude velmi podobný. Viz ukázka:
```php
<?php

use Sunlight\User;														// odkazujeme na třídu user z důvodu acces práv

return function (array $args) {
    $args['admin']->modules['example-modul'] = [						// example-modul se objeví v adresním řádku prohlížeče
        'title' => _lang('modul.module.title'),							// Název menu
        'access' => User::hasPrivilege('adminart'),						// přístupová práva
        'menu' => true,													// True znamená, že se menu objeví, false ho nezobrazí
        'menu_order' => 25,												// pořadí menu položky
        'script' => __DIR__ . '../../../script/example-modul.php',		// cesta k výkonnému skriptu
    ];
};
```
Je to velmi jednoduché. Ukázka, jak to může vypadat:
![Hlavní Menu](/assets/posts/mdl_hlavni_menu.jpg)

O modulech toho bude více. Toto je první část z miniseriálu, který chystám. Příště budeme pokračovat jak toto menu dostat do Hlavního Menu - Ostatní. Tento examplemodul si můžete stáhnout u mě na gitu: [ExampleModul](https://github.com/Helwa-Surinen/Sunligt-CMS-laikuv-pruvodce/tree/main/examplemodul-part1).
