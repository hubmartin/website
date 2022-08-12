---
title: "Solární elektrárna"
date: 2022-08-12T12:27:38+06:00
description : "Vše co jsem se naučil o solární elektrárně sepsáno, abych to nezapomněl."
type: post
image: blog/lcd-menu-v1/image.jpg
author: Martin Hubáček
tags: ["solar"]
---


Soupis všeho kolem mého solárního počinu. Bude postupně sepisováno.. snad.
<!--more-->


# Úvod

Elektrárnu nosím roky v hlavě, sem tam jsem si něco přečetl, roky měřím spotřebu chytrým elektroměrem a každých 30 sekund ukládám asi 50 veličin. Tehdy jsem ještě netušil proč, ale proč ne :) Takže mám představu v čase a na kterých fázích co se děje v různých ročních obdobích.

Navíc z grafů je dost patrné a po použití (neumělé) inteligence lze rozlišit v nočních hodinách cyklování ledničky, čerpadla kotle. Jiné věci co zase jedou furt (rack se serverem, switchi, WiFi AP, kamery) lze taky vidět a uděláte si tak představu o jakési "idle" spotřebě. Začít řešit tuhle "idle" spotřebu je asi nejsnažší na výpočet a představu jak uspořit. Možná se 100W trvale celý rok moc nezdá, ale v roce je to 870 kWh což může být třeba 5 kKč ročně. (snad moc nekecám)

# Zápletka

No a protože se říká, že elektrárnu chcete, když ji má soused, tak došlo i na mě :) Navíc teď vše leze nahoru, komponent moc není, už to dlouho nosím v hlavě a nic nedělám. Je třeba začít.

Jo, ještě poznámka že primární důvod je soběstačnost, pak nějaká "návratnost", jak si ji každý počítá trošku jinak.

Slaboproudou a low power elektronikou se zabývám už od roku 2007. Trochu jsem se dovzdělal z Internetu, nachytřil od známých co už na střeše nějaký ten větší křemík mají a samozřejmě sledoval [ampéráka](https://www.youtube.com/amperak).

Od začátku jsem chtěl systém, který:

- bych si nejprve s nižší investicí vyzkoušel jako "technologický demonstrátor"
- po ověření základní funkce a prvních zkušenostech bych klidně už po pár týdnech dokoupil další baterky, druhý měnič
- nechci prodávat elektřinu
- nechci dotace, projekty, byrokracii

# Vyvrcholení

Pro první verzi elektrárny jsem si říkal, že nahodím panely na plochou garáž. Je to trochu komplikovanější protože v zimě je asi půlka zakrytá, ale vymyslel jsem to tak že jedna řada panelů bude pod úhlem 30° vždy na slunci. 

Tato druhá vyšší řada je zoptimalizovaná že panely jsou naležat oa jsou 2 řady nad sebou, 4 vedle sebe. Panel 300Wp, takže celkových 2,4 kWp.

První řada, která je v zimě zastíněná, má menší sklon asi 20° a vejdou se tam čtyři 300 Wp panely naležato vedle sebe - 1200 Wp. Menší úhel je zvolený aby nestínil druhé řadě panelů.

![](garaz-letecky.png)

Na výpočet rozměrů a úhlů postavení panelů s ohledem na nejnižší úhel slunce v zimě jsem si udělal ve FreeCADu hezký parametrický model pohledu z boku.

![](freecad.jfif)

Tak jsem si zjistil zda se mi ty 2 řady za sebou na 4 metrovou šířku garáže vejdou.

### Parametry

* Axpert MKS III 5kW, 450V string
* Pylontech US2000C 2.4kW

### Měnič a dobíječ Axpert

Tato čínská bedýnka od firmy Voltronic v sobě spojuje jeden MPPT dobíječ pro string s napětím 120-430V a 5 kW invertor. Maximální napětí je 450V. Tohle se mi dost líbí, protože spojít spoustu panelů a nemusíte je spojovat sérioparalelně. Navíc nejste připraveni o elektrický oblouk když nesprávným způsobem odpojujete string pod proudem :)

Zatím funguje parádně, pokud mě jednou zklame, půjde z baráku a další zařízení by bylo asi od Victronu.
Tento Axpert se dá spojovat paralelně, takže je možné druhým kusem posílit výkon na 10 kW a získám tím druhý MPPT vstup z dalšího solárního stringu. Idle spotřeba je ve 10W, ale to je Standby s vypnutým invertorem. Běžně jste na 60 W. Je proto na zvážení jestli mít jeden výkonnější měnič co bere 90W v idle, nebo dva stejné kde součet bude 120W.

### Pylontech US2000C

Tady jsem dost řešil jestli olovo, nebo lithium. Naštěstí mi bylo olovo rozmluveno jedním solárníkem, dokonce i Ampérák to někde počítal, že pokud nemáte přísun levných baterií tak lithium vydrží déle. Já v tom nejsem úplný expert takže můžete mít jiný názor :)

Další rozhodování bylo zda si baterku postavit, nebo jít do hotových. Trochu jsem to studoval, ale řešil jsem hromadu jiných technickcýh záležitostí a návrh baterky jsem taky outsourcoval na čínského výrobce Pylontech.

Baterie v sobě nemá jen BMS, ale i FET tranzistory odpojující svorky. Takže v případě vybití, přebití, nadproudu dovnitř nebo ven to bude neustále chránit baterii.
Něco co se blíží tomuto konceptu nabízí [JK BMS](https://www.aliexpress.com/item/1005003104573871.html). Je možné, že se na něj ještě dostane v budoucnu.

Ohledně kapacity je zas nutné si uvědomit, že pokud má měnič idle spotřebu 60W, tak přes období třeba 14 hodin kdy od večera do rána nesvítí spotřebuje z baterek 840 Wh.

## Vizualizace

Doma mám centrální server na kterém mi běží tyto služby. Celkem známá trojkombinace kterou používám již roky s naší IoT stavebnicí [HARDWARIO TOWER](https://tower.hardwario.com/).

* Node-RED
* Grafana
* InfluxDB (1.8!)
* Mosquitto MQTT broker

Technické vybavení solární elektrárny mám v jiné místnosti a zde jsem umístil Raspberry Pi.

Do tohoto RPI je zapojený měnič přes USB-RS232 převodník. V měniči je RJ45 protikus. Prvně jsem to provozoval přes USB ale po pár dnech se USB odmlčelo. Po rebootu se opět zprovoznilo, ale je tam zřejmě něco špatně s USB stackem který není kvalitní.

Druhý USB-RS232 převodník je zapojený do Console RJ45 portu v hlavní baterii Pytlontech.

Pro každé zařízení/převodník mám v RPI jednu službu která přetransforumuje data z obou zařízení do JSONu a přes MQTT je zašle na server do MQTT brokeru.

### Axpert-monitor-mqtt

Rozšířil jsem projekt [axpert-monitor](https://github.com/b48736/axpert-monitor) a přidal si k němu MQTT posílání a možnost ovládání.
Je to nodejs aplikace. Moc v tom neumím a je to rozšířené narychlo, takže se není čím chlubit. Ale prošel jsem hromadu různých projektů v různých jazycích a tohle prostě fungovalo.

Můj projekt naleznete [na Githubu](https://github.com/hubmartin/axpert-monitor-mqtt).

### Pylontech monitoring

Hledal jsem něco funkčního po RS232 Console portu. Na RS485 mám již komunikaci do měniče a pokud jsou RS485 linky všechny interně propojené (jsou?) tak jsem nechtěl ovlivňovat rychlým logováním běžnou komunikaci s měničem.
Takže jsem šel cestou přes Cosnole port kde je textová komunikace.

Všude se píše o nějaké rychlost 2400 baud. Já chvíli řešil proč nekomunikuju, tak jsem zkoušel různé rychlosti chytl jsem se na 115200 na článcích přímo z výroby.

Zase jsem prošel mnoho projektů ale většina je na RS485 a na Console port byla jedna v node-red. Takže jsem na RPI nahodil moloch s node-red a možná to vylepším do předchozího skriptu, když bude čas.

Projekt [nodered-pylontech-console-reader
](https://github.com/juanhaywood/nodered-pylontech-console-reader/) má úplně na konci ke stažení flow. Tak stačí importovat. Ještě je potřeba doinstalovat z Manage Palette možná Serial interface node.

### Vizualizace

Pro nějaké základní realtime přehledy a nějaké jednoduché grafy pár dní zpět bez možnosti zoomu lze použít v Node-RED integrovaný Dashboard.

![Node-RED Dashboard](dashboard.png)

Na následujícím obrázku je ukázka flow, nyní nás zajímá jen vlevo vstup `axpter/test` a čtyři modré `gauge` bloky.

![Node-RED Dashboard](dashboard-flow.png)

V Gauge prvku mám pak v poli přímo objekt `payload` ze kterého rovnou vyčítám z JSONu parametr `outputPowerActive` a další. Jinak byste museli mezi tyto dva nody vkládat ještě blok Change nebo Function.

![Gauge nastavení](gauge-settings.png)

Pro pokročilejší grafy a analýzy používám (starší) InfluxDB 1.8 a nástroj Grafana. Obojí se dá nainstalovat i přes docker. Právě v obrázku výš kde bylo Node-RED flow jste mohli vidět i část, která přeposílá JSONy do InfluxDB databáze. Je před ním ještě blok `throttle` aby se do DB nesypaly data příliš rychle.

Po MQTT chci aktualizace každou vteřinu (proč ne) ale do DB je to zbytečné. Občas tento interval trochu zkrátím když dělám pokusy jako test zatížení apod., abych měl víc bodů k pozdější analýze.



