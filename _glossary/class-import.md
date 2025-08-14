---
layout: single
title: "Import tříd"
toc: true
toc_sticky: true
---

Když píšete kód v PHP *(a obecně v objektově orientovaném programování)*, často používáte **třídy**. Jak a proč je dostat do vašeho kódu, to si nyní vysvětlíme .


### Proč se třídy importují? 

1. **Abyste nemuseli psát celou cestu k třídě pokaždé**  
   - Třídy jsou často uložené v různých složkách a jmenných prostorech *(namespaces)*.  
   - Místo psaní něčeho jako ``\Vendor\Project\Module\Trida`` stačí napsat jen ``Trida``.  

2. **Aby byl kód čistší a přehlednější**  
   - Když importujete třídy na začátku souboru, hned vidíte, jaké komponenty se používají.  

3. **Aby se předešlo kolizím názvů**  
   - Dvě různé knihovny můžou mít třídu se stejným jménem (např. ``Database``). Importováním přes ``use`` jasně říkáte, kterou přesně chcete použít.  

---

### Jak se třídy importují? 

Na začátku souboru (hned za ``<?php``) napíšete:  
```php
use JmennyProstor\NazevTridy;
```  
Pak můžete v kódu používat jen ``NazevTridy`` místo celé cesty.  

**Příklad:**  
```php
<?php
use Sunlight\Database\Database;  

// Místo:  
$query = \Sunlight\Database\Database::query();  
// Stačí:  
$query = Database::query();  
```  

---


**Aliasy tříd (as)**

Někdy má třída dlouhý název nebo chcete předejít kolizi názvů. V takovém případě můžete při importu použít alias pomocí ``as``:

```php
<?php
use Psr\Log\LoggerInterface as Logger;
use App\Logger\Logger as Log;

// Teď můžete používat zkrácené názvy:
function writeLog(Logger $logger) {
    $logger->info('Log message');
}

$logger = new Log('app');
```

V prostředí SunLightu nejčastěji narazíte na ``DB`` alias pro třídu ``Sunlight\Database\Database``

```php
<?php
use Sunlight\Database\Database as DB;

$query = DB::query('SELECT ...');
```

**Kdy aliasy použít:**
- Když máte třídy se stejným názvem z různých jmenných prostorů
- Pro zkrácení dlouhých názvů tříd
- Pro lepší čitelnost kódu


### Kde se třídy importují?  

- **Vždy na začátku souboru** *(nejlíp hned za ``<?php``)*.  
- Importujete jen to, co v daném souboru skutečně použijete.  
- Pokud potřebujete více tříd ze stejného jmenného prostoru, můžete je napsat pod sebe:

  ```php
  <?pho
  
  use Sunlight\Util\Form;  
  use Sunlight\Util\Html;  
  ```  

---

### Jak to funguje uvnitř? (ClassLoader)  

PHP **nečte všechny třídy naráz**, ale načítá je automaticky, až když jsou potřeba – k tomu slouží **autoloading** *(automatické načítání)*.  

1. **ClassLoader** *(např. Composer Autoloader)* sleduje, které třídy se používají.  
2. Když narazíte na ``new Trida()`` nebo ``Trida::metoda()``, ClassLoader:  
   - Podívá se, jestli je třída už načtená.  
   - Pokud ne, najde odpovídající soubor a načte ho.  
3. Díky tomu nemusíte ručně ``require`` nebo ``include`` pro každou třídu zvlášť.  

**Výhody:**  
- Kód je rychlejší *(načte se jen to, co se skutečně použije)*.  
- Nemusíte řešit pořadí načítání souborů.  
- Můžete používat třídy odkudkoli, aniž byste museli řešit složité cesty.  
