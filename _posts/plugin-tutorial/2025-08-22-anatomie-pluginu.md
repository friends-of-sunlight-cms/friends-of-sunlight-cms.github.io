---
title: "Anatomie pluginů"
author: "Jirka Daněk"
toc: true
toc_sticky: true
categories:
  - serie
  - plugin-tutorial
tags:
  - serie
  - plugin-tutorial
  - navody
---


Každý plugin, bez ohledu na typ, má jednu věc společnou: soubor ``plugin.json``. Každý typ pluginu má i své specifické klíče v jeho definici. Je to jako *průkaz totožnosti* pluginu - obsahuje všechny důležité informace o tom, kdo ho vytvořil, co dělá, jaké má požadavky na systém a jak má fungovat. Systém si díky tomuto souboru dokáže plugin načíst, zkontrolovat kompatibilitu a správně ho integrovat do webu. Také v něm definujete, které události má plugin zpracovávat, jaké má výchozí nastavení nebo které dodatečné soubory má načíst. Bez ``plugin.json`` souboru prostě žádný plugin neexistuje!


### Ukázka definice

Zde je ukázka kompletního ``plugin.json`` souboru pro nejvíce běžný typ ``extend`` pluginu:
    
```json
{
    "$schema": "../../../system/schema/extend.json",
    "name": "Můj pokročilý extend plugin",
    "description": "Plugin demonstruje všechny možnosti extend pluginů",
    "authors": [
        {
            "name": "Vaše jméno",
            "url": "https://adresa webu.tld"
        },
        {
            "name": "Spolupracovník",
            "url": "https://example.com"
        }
    ],
    "version": "1.2.0",
    "environment": {
        "system": "^8.3.0",
        "php": ">=7.4",
        "php_extensions": [
            "curl", 
            "iconv"
        ],
        "db_engine": "InnoDB",
        "debug": null
    },
    "dependencies": {
        "extend.twig": "^1.0",
        "template.some-template": "*"
    },
    "events": [
        {
            "event": "user.login",
            "script": "event/user_login.php"
        },
        {
            "event": "posts.submit", 
            "script": "event/post_submit.php"
        },
        {
            "event": "tpl.head",
            "script": "event/add_css.php",
            "group": "frontend"
        }
    ],
    "events.web": [
        {
            "event": "index.ready",
            "script": "event/web_only.php"
        }
    ],
    "events.admin": [
        {
            "event": "admin.init",
            "script": "event/admin_init.php"
        }
    ],
    "scripts": [
        "script/always_run.php"
    ],
    "scripts.web": [
        "script/web_init.php"
    ],
    "scripts.admin": [
        "script/admin_init.php"
    ],
    "routes": [
        {
            "pattern": "api/my-plugin/hello/([a-z]+)",
            "script": "script/api_hello.php",
            "attrs": {
                "custom_param": "value"
            }
        }
    ],
    "langs": {
        "myplugin": "lang"
    },
    "hcm": {
        "mygallery": {
            "script": "hcm/gallery.php"
        },
        "contact_form": {
            "script": "hcm/contact.php"
        }
    },
    "cron": {
        "cleanup": {
            "callback": "cron/cleanup.php",
            "interval": 3600
        },
        "daily_backup": {
            "callback": "cron/backup.php", 
            "interval": 86400
        }
    },
    "installer": "installer.php",
    "autoload": {
        "psr-4": {
            "MyPlugin\\Helpers\\": "helpers"
        },
        "classmap": ["legacy"]
    },
    "class": "MySuperPlugin",
    "namespace": "MyCompany\\Plugins\\MyPlugin",
    "inject_composer": true,
    "actions": {
        "config": "ConfigAction",
        "import-settings": "Action\\ImportAction"
    },
    "config_defaults": {
        "enabled": true,
        "api_key": "",
        "max_items": 50,
        "show_thumbnails": false,
        "gallery_columns": 3
    },
    "extra": {
        "integration_version": "2.1",
        "supported_formats": ["jpg", "png", "webp"]
    }
}
```

## Vysvětlení jednotlivých částí

Po prvotním vyděšení si pojďme vysvětlit jednotlivé části a jejich vlastnosti. Tento výše zmíněný příklad ukazuje úplně všechny dostupné možnosti tak jak se nacházejí v [dokumentaci](https://sunlight-cms.cz/dokumentace/pluginy/extend). Ve skutečnosti si však ve většině případů vystačíte jen s několika základními položkami: ``$schema``, ``name``, ``description``, ``authors``, ``version``, ``environment``, ``langs``, ``events``, ``events.web``, ``events.admin`` a ``hcm``. Zbytek jsou pokročilá nastavení a funkce, která budete potřebovat méně často. Běžně se setkáte s mnohem menší definicí pluginu a postupně ji budete rozšiřovat podle potřeby!


### Základní informace

> Klíče označeny `❗` jsou povinné

**``$schema``** - Pro ``extend`` pluginy používejte ``"../../../system/schema/extend.json"``. Pomáhá editorům s napovídáním.

**``name``** ❗ - Název pluginu, který uvidí správci v administraci.

**``description``** - Krátký popis funkcí pluginu.

**``authors``** - Pole autorů, lze kombinovat ``"name"`` a ``"url"`` jak chcete, použít oboje nebo jen jedno.
```json
{
    "authors": [
        {"name": "Author1", "url": "https://example.com/"},
        {"name": "Author2"},
        {"url": "https://example.com/"}
    ]
}
```

**``version``** ❗ - Verze ve formátu ``hlavní.vedlejší.oprava``. Viz. [Sémantické verzování](https://semver.org/lang/cs/)

### Prostředí a závislosti
**``environment``** ❗ - Co plugin potřebuje k běhu:
- ``system`` ❗: Verze SunLight CMS *(``~8.3.0`` = kompatibilní s 8.3.x)*  
- ``php``: Minimální PHP verze *(``>=7.4``)*
- ``php_extensions``: Potřebná PHP rozšíření
- ``db_engine``: Typ databáze *(``MyISAM``, výchozí: ``InnoDB``)* - od verze 8.2.0
- ``debug``: Kdy je plugin aktivní *(``true`` = jen debug, ``false`` = jen produkce, ``null`` = vždy)*

**``dependencies``** - Na jakých pluginech závisí:
- ``extend.nazev-pluginu``: Pro extend pluginy
- ``template.nazev-motivu``: Pro motivy  
- ``nazev-pluginu``: Automaticky se bere jako extend

### Události a skripty
**``events``** - Globální události, na které plugin reaguje:
```json
{
    "events": [
        {
            "event": "user.login",           // Název události
            "script": "event/login.php",     // Skript, který se spustí
            "group": "optional_group"        // Volitelná skupina - umožňuje například vkládání CSS do hlavičky, pouze pokud je potřeba
        }
    ]
}
```

**``events.web``** - Události pouze na veřejné části webu

**``events.admin``** - Události pouze v administraci

**``scripts``** - Skripty spouštěné při načtení pluginu *(všude)*
```json
{
    "scripts": [
        "script/init.php",
        "script/example.php"
    ]
}
```

**``scripts.web``** - Skripty pouze na webu *(frontend)*

**``scripts.admin``** - Skripty pouze v administraci

### Vlastní URL a API
**``routes``** - Vlastní URL adresy, které plugin zpracovává:
```json
{
    "routes": [
        {
            "pattern": "api/hello/([a-z]+)",     // Regex vzor
            "script": "script/api.php",          // Skript pro zpracování
            "attrs": {"custom": "value"},        // Vlastní parametry
            "type": "index"                      // Nepovinný typ routy (výchozí)
        }
    ]
}
```

### Překlady a moduly
**``langs``** - Slovníky s překlady:
- Klíč = prefix pro ``_lang()`` funkci
- Hodnota = adresář se soubory překladů
```json
{
    "langs": { "my_plugin": "lang" }
}
```

**``hcm``** - Vlastní HCM moduly:
```json
{
    "hcm": { "nazev_modulu": {"script": "hcm/my_hcm.php"} }
}
```

**``cron``** - CRON úlohy:
```json
{
    "cron": {
        "nazev_ulohy": {
            "callback": "cron/skript.php",
            "interval": 3600  // v sekundách
        }
    }
}
```

### Pokročilé nastavení
**``installer``** - Skript instalátoru pro vytváření databázových tabulek [příklad v dokumentaci](https://sunlight-cms.cz/dokumentace/pluginy/extend#option-installer)

**``autoload``** - Dodatečné autoloading pravidla *(jako u Composeru)*

**``class``** - Název hlavní třídy pluginu *(musí dědit ``Sunlight\Plugin\ExtendPlugin``)*

**``namespace``** - Vlastní namespace *(výchozí: ``SunlightExtend\NazevPluginu``)* [dokumentace](https://sunlight-cms.cz/dokumentace/pluginy/extend#option-namespace)

> Pokud neurčíte vlastní namespace, systém vám ho automaticky vygeneruje podle složky pluginu. Funguje to takto: vezme se prefix podle typu pluginu *(``SunlightExtend`` pro extend pluginy, ``SunlightTemplate`` pro motivy, ``SunlightLanguage`` pro jazykové balíčky)* a přidá se název pluginu převedený na CamelCase. 
>
> **Příklady:**
>  - složka ``mysupergallery`` → namespace ``SunlightExtend\Mysupergallery``
>  - složka ``my-super-gallery`` → namespace ``SunlightExtend\MySuperGallery``
>  - složka ``simple_contact`` → namespace ``SunlightExtend\SimpleContact``
>
> Automatické generování vám ve většině případů stačí, vlastní namespace zadáváte jen když chcete mít kontrolu nad strukturou nebo když vytváříte složitější plugin s více namespace úrovněmi.


**``inject_composer``** - Automaticky načítat Composer závislosti *(``true``/``false``)*

**``actions``** - Vlastní akce v administraci, může též přetěžovat stávající:
```json
{
    "actions": [
        "nazev-akce": "Nazev\\Tridy\\Akce"
    ]
}
```

**``config_defaults``** - Výchozí konfigurace:
- Systém automaticky vytvoří formulář v administraci
- Přístup přes ``$this->getConfig()`` v pluginu, případně přes instanci získanou z plugin manageru.

```php
<?php
use Sunlight\Core;
use SunlightExtend\MujPlugin\MySuperPlugin;

// instance pluginu
$instance = Core::$pluginManager->getPlugins()->getExtend('<identifikátor_pluginu>');

// nebo pokud plugin má nastavenou hlavní třídu
$instance = MySuperPlugin::getInstance();

// konfigurace pluginu
$config = $instance->getConfig();
```

 > A právě zde se například uplatní správně pojmenovaná složka pluginu *(viz. předchozí článek)*, která se použije právě jako `<identifikátor_pluginu>`.

**``extra``** - Vlastní data pro pluginy *(přístup přes ``getExtraOption()``)*


### Systémové události - jak plugin "poslouchá" co se děje

Jednou z nejsilnějších funkcí systému jsou události *(events)*. Představte si to jako upozornění - systém řekne "hele, právě se stala tahle věc" a váš plugin může zareagovat. V [dokumentaci](https://sunlight-cms.cz/dokumentace/pluginy/extend-udalosti) naleznete seznam všech událostí a jejich argumentů.

**Příklady událostí:**
- ``user.login`` - uživatel se přihlásil
- ``posts.submit`` - byl vytvořen nový příspěvek  
- ``form.render`` - vykreslování formuláře
- ``admin.init`` - spouští se administrace

#### Jak reagovat na události

V ``plugin.json`` přidáte sekci ``events``:

```json
{
    "events": [
        {
            "event": "posts.submit",
            "script": "events/article_created.php"
        },
        {
            "event": "user.login", 
            "script": "events/user_login.php"
        }
    ]
}
```

A pak vytvoříte odpovídající PHP soubory, které se spustí, když se událost stane.

#### Skupiny událostí

Některé události můžete zařadit do skupin, které se aktivují až když je potřebujete:

```json
{
    "events": [
        "event": "tpl.head", 
        "script": "event/add_css.php",
        "group": "frontend"
    ]
}
```

V kódu pak skupinu aktivujete:
```php
$this->enableEventGroup('frontend');
```

 > To je užitečné třeba pro CSS/JS soubory, které chcefe načíst jen na určitých stránkách.


## Praktický příklad struktury extend pluginu

Pojďme si ukázat, jak vypadá kompletní struktura pokročilého extend pluginu:

```none
plugins/extend/muj-plugin/
├── class/                    # PHP třídy (PSR-4 autoloading)
│   ├── MySuperPlugin.php     # Hlavní třída pluginu
│   ├── ConfigAction.php      # Třída konfigurace pluginu 
│   └── Helper/
│       └── CountHelper.php
├── event/                    # Zpracování událostí
│   ├── user_login.php        # Při přihlášení uživatele
│   ├── posts_submit.php      # Při vytvoření článku
│   └── add_css.php           # Přidání CSS do hlavičky
├── script/                   # Obecné skripty
│   ├── always_run.php        # Spouští se vždy
│   ├── web_init.php          # Jen na webu
│   ├── admin_init.php        # Jen v administraci
│   └── api_hello.php         # API endpoint
├── hcm/                      # HCM moduly
│   ├── gallery.php           # [hcm]my_gallery[/hcm]
│   └── contact.php           # [hcm]contact_form[/hcm]
├── cron/                     # CRON úlohy
│   ├── cleanup.php           # Pravidelné úklidy
│   └── backup.php            # Zálohy
├── lang/                     # Překlady
│   ├── cs.php                # Čeština
│   └── en.php                # Angličtina
├── public/                   # Veřejně přístupné soubory
│   ├── .htaccess             # Povolení přístupu
│   ├── style.css             # CSS styly
│   ├── script.js             # JavaScript
│   └── images/
│       └── icon.png
├── plugin.json               # Konfigurace pluginu
└── installer.php             # Instalátor databáze
```

### Důležité adresáře a jejich účel:

**``public/``** - Jediný adresář, ke kterému mají uživatelé přístup z webu. Musí obsahovat ``.htaccess``:
```apache
<IfModule mod_authz_core.c>
    Require all granted
</IfModule>
<IfModule !mod_authz_core.c>
    Order allow,deny
    Allow from all
</IfModule>
```

**``class/``** - PHP třídy s automatickým PSR-4 autoloadingem do namespace pluginu.

**``event/``** - Skripty reagující na systémové události. Každý soubor se spustí, když se příslušná událost stane.

**``hcm/``** - Vlastní HCM moduly, které můžete používat v článcích jako ``[hcm]nazev_modulu,param1,param2,...[/hcm]``.

**``lang/``** - Slovníky s překlady. Přístup přes ``_lang('prefix.klic')``.


## Kam dál?

Teď už jsme si ukázali základy, jaké typy pluginů existují a jak vypadá jejich struktura. V dalších článcích si ukážeme:

- Jak pracovat s událostmi a callbacky
- Jak vytvořit svůj první jednoduchý extend plugin
- Jak vytvořit vlastní HCM modul


Pokud si chcete prohlédnout fungující příklady, nahlédněte na tyto repozitáře:
- [Ukázkový extend plugin](https://github.com/friends-of-sunlight-cms/example-plugin)
- [Plugin s vlastním HCM](https://github.com/friends-of-sunlight-cms/example-hcm) 
- [Oficiální multi-jazykový plugin](https://github.com/sunlight-cms/multilang-plugin)
- [Komunitní pluginy](https://github.com/friends-of-sunlight-cms)

