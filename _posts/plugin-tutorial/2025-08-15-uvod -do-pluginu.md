---
title: "Úvod do pluginů"
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

Než se pustíme do samotného vývoje pluginů, musím hned na úvod říct, že tato série článků se bude věnovat výhradně **SunLight CMS verze 8.x.x** *(dále jen "SL" nebo "systém")*. Pokud pracujete se starší verzí, věci zde popsané nebudou fungovat.

Předpokládám, že máte alespoň základní znalost programování v PHP a HTML/CSS nebo odhodlání to bastlit. Nebudu vysvětlovat každý detail od píky, ale důležité koncepty si samozřejmě rozebereme. Kde to bude potřeba, budu odkazovat na [oficiální dokumentaci](https://sunlight-cms.cz/dokumentace), nechci tyto články psát jako náhražku dokumentace, ale spíše jako přátelského průvodce, který vám pomůže pochopit, jak věci fungují v praxi.

Takže... Nudný úvod.


## Co jsou vlastně pluginy?

Pluginy *(někdy také nazývané zásuvné moduly)* jsou jednoduše řečeno malé balíčky kódu, které můžete do systému "zapojit" a tím ho rozšířit nebo změnit.

Filozofie systému je poměrně jasná: **cokoliv se dá vyřešit pluginem, nemusí být součástí základu**. Tenhle přístup udržuje systém štíhlý a rychlý, i když si s sebou samozřejmě táhne pár pozůstatků ze svých starších verzí.


### Proč používat pluginy?

Možná si říkáte: *"Proč si prostě neupravím soubory systému přímo?"* 

No, je to z několika důvodů:

1. **Aktualizace** - Když přijde nová verze systému, vaše změny se ztratí
2. **Přenositelnost** - Plugin můžete snadno přenést na jiný web
3. **Údržba** - Je jednodušší spravovat jeden plugin než změny roztroušené po celém systému


## Jaké typy pluginů systém podporuje?

SL rozpoznává celkem **tři typy pluginů**, každý s jiným účelem:


### Template pluginy *(motivy/šablony)*

Tohle jsou "kabáty" pro váš web. Mění pouze vzhled - barvy, rozložení, fonty, animace. Obsah zůstává stejný, jen vypadá jinak. Můžete mít tmavý motiv pro technologický blog nebo světlý minimalistický pro portfolio.


### Extend pluginy *(rozšíření)*

Tady se děje ta opravdová magie! Extend pluginy přidávají nové funkce nebo mění chování systému. Můžete vytvořit vlastní kontaktní formulář, přidat komentáře k obrázkům, integrovat platební bránu, nebo cokoliv jiného, co vás napadne.


### Language pluginy *(jazykové balíčky)*

Obsahují překlady celého systému do různých jazyků. Pokud chcete web v němčině, slovenštině nebo klingonštině, budete potřebovat právě language plugin.


## Kde pluginy najdete?

Všechny pluginy mají své místo ve složce ``plugins/`` v kořenu vaší SL instalace. Jsou pak rozdělené podle typu:

```none
plugins/
├── extend/        <- rozšíření funkcí
├── languages/     <- jazykové balíčky  
└── templates/     <- motivy/šablony
```


## Pravidla pro názvy pluginů

Tohle je nudné, ale důležité. Název vašeho pluginu:


### By měl

- **Být jedinečný** - nesmí se shodovat s jiným pluginem
- **Odrážet účel** - z názvu by mělo být jasné, co plugin dělá
- **Být malými písmeny** - ``mujplugin``


### Může

- **Používat pomlčky** - například ``better-forum``, `image-gallery`
- **Používat podtržítka** - ``muj_plugin``


### By neměl obsahovat

- **Mezery** - ``muj plugin``
- **Diakritika** - ``můj-plugin``
- **Velká písmena** - ``MujPlugin``
- **Nesmyslné názvy** - ``asdf123``, ``lvsnlghtfrvr`` - ne snad,  že by to nefungovalo, ale orientace ve složkách s takovými názvy bude velice náročná


### Proč je tohle důležité?

Název pluginu se používá v mnoha místech:
- **URL adresy** - různé cesty k souborům, stylům, obrázkům, které by se nemusely správně načíst
- **PHP třídy** - systém, pokud není uvedeno jinak z názvu generuje například jména pro namespace tříd
- **CSS třídy** - pro styly specifické pro plugin, odkazy tříd na například obrázky

Pokud použijete špatný název, můžete narazit na problémy s URL, nenačtení cest v CSS nebo dokonce PHP chyby.


## Co dál?

V tomto v zásadě nudném úvodu jsme si vysvětlili holé základy toho co jsou pluginy, jaké typy existují a jak by se měly jmenovat. V dalších článcích se ponoříme do detailů jednotlivých typů pluginů a ukážeme si, jak vytvořit první funkční plugin.


### Plán dalších článků:

1. **Anatomie pluginů** - základní definování dat nezbytných pro funkčnost pluginu
2. **Systémové události** - Jak reagovat na to, co se děje v systému
3. **Template pluginy** - Jak vytvořit vlastní motiv
4. **Extend pluginy** - Přidávání nových funkcí
5. **Praktické příklady** - Vytvoříme si plugin od začátku do konce