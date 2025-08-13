---
title: "Jak lokálně spustit SunLight CMS 8"
author: "Jirka Daněk"
toc: true
toc_sticky: true
categories:
  - blog
tags:
  - blog
  - návody
---


Pokud chcete pro SunLight CMS 8 vyvíjet nebo ho testovat lokálně, je potřeba mít funkční webový server s podporou PHP a databáze MySQL. Existuje několik možností, jak to zajistit, já sám spíš z lenosti preferuji XAMPP, protože jej rychle nainstalujete a spustíte bez složitých konfigurací. Navíc nabízí nástroje jako phpMyAdmin, které usnadňují správu databází. Tento článek se bude věnovat spuštění na Windows.


## Různé možnosti webserverů a Docker

Webový server je základ, bez kterého CMS neběží. Nejčastěji můžete použít:

 - **XAMPP** – kompletní balík Apache, MySQL, PHP a dalších komponent.
 - **WAMP, MAMP, LAMP** – další oblíbené balíčky, které mohou být různé pro jednotlivé operační systémy.
 - **Docker** – moderní způsob, jak vytvořit přesné izolované prostředí s webserverem a databází. Výhoda je přenositelnost a opakovatelnost nastavení, ale pro začátečníky může být trochu komplikovanější.


## Krok 1: Instalace XAMPP a vytvoření databáze

1. Stáhněte a nainstalujte XAMPP podle pokynů na [https://www.apachefriends.org/](https://www.apachefriends.org/).
2. Spusťte XAMPP ovládací panel a zapněte Apache a MySQL.
3. V prohlížeči otevřete adresu: ``http://localhost/phpmyadmin/`` - tady spravujete své databáze.
4. Vytvořte novou databázi pro váš SunLight CMS projekt, například ``sunlight8dev``.

Při instalaci SunLight CMS pak použijte tyto údaje:

 - Server: ``localhost``
 - Uživatelské jméno: ``root``
 - Heslo: **(ponechte prázdné)**
 - Název databáze: ``sunlight8dev`` *(nebo vámi vytvořený název)*

 > Na rozdíl od produkčního hostingu není databáze předpřipravená, takže ji musíte založit sami.


## Krok 2: Umístění souborů SunLight CMS

Ve složce XAMPP, například ``C:\xampp\htdocs``, vytvořte podsložku například ``sunlight-8-dev``. Do ní rozbalte nebo zkopírujte soubory SunLight CMS 8. Díky tomu budete mít projekt pěkně oddělený od ostatních.


## Krok 3: Úprava PHP.ini – aktivace rozšíření GD a ZIP

Nyní přichází důležitý krok, který může být pro nováčky nejasný, ale já vám to vysvětlím.

SunLight CMS využívá rozšíření ``GD`` pro práci s obrázky – například při nahrávání, změně velikosti nebo vytváření náhledů, captcha. Bez aktivního GD tyto funkce jednoduše nebudou správně fungovat.

Dále potřebujete rozšíření ``ZIP``, protože SunLight CMS pracuje s archivovanými soubory – třeba při instalaci pluginů nebo zálohování dat. Pokud ZIP není aktivní, některé operace se nezdaří a můžete narazit na chyby.

Tyto moduly jsou v PHP standardně zakomentované, proto je potřeba je aktivovat:

1. V XAMPP ovládacím panelu klikněte u Apache na **Config -> PHP (php.ini)**.
2. Vyberte PHP (php.ini), tím se otevře konfigurační soubor v textovém editoru.
3. Vyhledejte řádky:
```ini
;extension=gd
;extension=zip
```
4. Odstraňte středník ``;`` na začátku řádků, aby vypadaly takto:
```ini
extension=gd
extension=zip
```
5. Uložte změny a restartujte Apache *(v XAMPP ovládacím panelu klikněte na Stop a pak znovu Start u Apache)*

 > Bez aktivace těchto rozšíření by některé funkce SunLight CMS nefungovaly správně.


## Krok 4: Přístup k SunLight CMS a dokončení instalace

1. Otevřete prohlížeč a přejděte na adresu: ``http://localhost/sunlight-8-dev/``
2. Měla by se vám zobrazit úvodní stránka instalace SunLight CMS. 
3. Postupujte podle pokynů, připojte se k databázi, kterou jste si vytvořili a nainstalujte systém.


## Doporučení a tipy
 - Pokud používáte firewall, ujistěte se, že neblokuje port ``80`` *(standardní port pro HTTP)*.
 - V případě potřeby můžete změnit port Apache *(např. na 8080)*, v takovém případě pak do URL použijete ``http://localhost:8080/sunlight-8-dev/``.
 - Nezapomeňte mít zapnutou MySQL databázi, protože SunLightCMS databázi používá.
 - Místo ``http://localhost`` se někdy můžete setkat s adresou ``http://127.0.0.1``. Obě tyto adresy odkazují na váš vlastní počítač *(tzv. loopback)*. Rozdíl je čistě technický – ``localhost`` je název, který váš systém překládá na IP adresu ``127.0.0.1``. V praxi to tedy znamená totéž a při přístupu k lokálním serverům můžete použít obě varianty.
 
**Upozornění:**
Lokální prostředí nemá nastaven SSL certifikát, takže přístup přes ``http://localhost`` **nebude** mít ``HTTPS``. Moderní prohlížeče vás proto budou upozorňovat na nezabezpečené připojení. SSL certifikát lze nastavit i lokálně, ale to není cílem tohoto článku.
{: .notice--warning}
 

## Závěrem
Ačkoliv existují i jiné způsoby, jak spustit SunLight CMS lokálně *(třeba přes Docker)*, XAMPP je skvělá volba pro začátečníky i pokročilé. Je jednoduchý, rychlý a nabízí všechny nástroje, které k vývoji budete potřebovat.


