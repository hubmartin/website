---
title: "Solární elektrárna"
date: 2022-08-12T12:27:38+06:00
description : "Vše co jsem se naučil o solární elektrárně sepsáno, abych to nezapomněl."
type: post
image: blog/solarni-elektrarna/image.png
author: Martin Hubáček
tags: ["solar"]
---

Soupis všeho kolem mého solárního počinu. Bude postupně sepisováno.. snad.
<!--more-->

Pokud naleznete překlepy nebo máte návrhy na úpravy, vytvořte k tomuto článku pull request na GitHubu přes odkaz níže.

{{% github-url %}}

TODO:
* měniče napětí, modif/čistý sinus
* bezpečnost požární/elektrická (svodiče, chrnana I a II třídy, odpojovače DC, pojistky)
* Cenová kalkulace
* tipy na vytvoření okruhů spotřebičů (nezapojení světel a trouby), řešení souběhu více spotřebičů


{{% toc %}}


## Úvod

Elektrárnu nosím roky v hlavě, sem tam jsem si něco přečetl, roky měřím spotřebu chytrým elektroměrem a každých 30 sekund ukládám asi 50 veličin. Tehdy jsem ještě netušil proč, ale proč ne 🙂 Takže mám představu v čase a na kterých fázích co se děje v různých ročních obdobích.

Navíc z grafů je dost patrné a po použití (neumělé) inteligence lze rozlišit v nočních hodinách cyklování ledničky, oběhového čerpadla kotle. Jiné věci co zase jedou furt (rack se serverem, switchi, WiFi AP, kamery) lze taky vidět a uděláte si tak představu o jakési "idle" spotřebě. Začít řešit tuhle "idle" spotřebu je asi nejsnažší na výpočet a představu jak uspořit. Možná se 100W trvale celý rok moc nezdá, ale v roce je to 870 kWh což může být třeba 5 kKč ročně. (snad moc nekecám)

## Požadavky

No a protože se říká, že elektrárnu chcete, když ji má soused, tak došlo i na mě 🙂 Navíc teď vše leze nahoru, komponent moc není, už to dlouho nosím v hlavě a nic nedělám. Je třeba začít.

Jo, ještě poznámka že primární důvod je soběstačnost, pak nějaká "návratnost", jak si ji každý počítá trošku jinak.

Slaboproudou a low power elektronikou se zabývám už od roku 2007. Trochu jsem se dovzdělal z Internetu, nachytřil od známých co už na střeše nějaký ten větší křemík mají a samozřejmě sledoval [ampéráka](https://www.youtube.com/amperak).

Od začátku jsem chtěl systém, který:

- bych si nejprve s nižší investicí vyzkoušel jako "technologický demonstrátor"
- po ověření základní funkce a prvních zkušenostech bych klidně už po pár týdnech dokoupil další baterky, druhý měnič
- nechci prodávat elektřinu
- nechci dotace, projekty, byrokracii

## Obhlídka terénu a poloha slunce v zimě⛄

V našich podmínkách je v zimě slunce nad obzorem asi 19°. Pro základní obhlídku plochy garáže jsem použil Android appku [Clinometer Camera](https://play.google.com/store/apps/details?id=com.marathon.clinometer.vs&hl=cs&gl=US) kde je možné kameru natočit na překážku a z akcelerometru se určí úhel (elevace?). Dovede si tak udělat představu která místa jsou při nízkém slunci v zimě zastíněna.

Dalším krokem byla aplikace která vám přes "Augumented reality" doplní do obrazu z kamery polohu slunce. Používá akcelerometr i magnetometr (pro azimut) a je to překvapivě dost přesné. Můžete zkusit že když foťák namíříte na slunce, tak se predikovaná pozice slunce fakt překrývá.

Doporučím tyhle dvě Android appky

- [SunPath](https://play.google.com/store/apps/details?id=nl.uva.sunpath&hl=cs&gl=US)
- [Sun's Path](https://play.google.com/store/apps/details?id=jp.gr.java_conf.siranet.sunshine&hl=cs&gl=US)

![Sun's Path](sunspath.png)

## Návrh rozmístění panelů

Pro první verzi elektrárny jsem si říkal, že nahodím panely na plochou garáž. Je to trochu komplikovanější, protože v zimě je asi půlka zakrytá sousedním domem, ale vymyslel jsem to tak, že jedna řada panelů bude pod úhlem 30° vždy na slunci.

Tato první vyšší řada je zoptimalizovaná, že panely jsou naležato a jsou 2 řady nad sebou, 4 vedle sebe. Panel 300Wp, takže celkových 2,4 kWp.

Druhá, menší řada, která je v zimě zastíněná, má menší sklon asi 20° a vejdou se tam čtyři 300 Wp panely naležato vedle sebe - 1200 Wp. Menší úhel je zvolený tak, aby nestínil první řadě panelů.

![Vizualizace rozmístění panelů na garáži](garaz-letecky.png)

Na výpočet rozměrů a úhlů postavení panelů s ohledem na nejnižší úhel slunce v zimě jsem si udělal ve FreeCADu hezký parametrický model pohledu z boku.

![Parametrický model úhlů solárních panelů](freecad.jfif)

Tak jsem si zjistil zda se mi ty 2 řady za sebou na 4 metrovou šířku garáže vejdou.

Poměrně dlouho jsem si s rozložením hrál. Ta menší druhá řada panelů je v zimě zastíněná sousedním domem. Takže pokud ji chci vyřadit přes zimu, musím ji nějak odpojovat. Oboje zapojení ale musí vyhovovat min/max napětí měniče. Takže 8 panelů měnič dá, 10 tak tak a když jich je více, tak sice v létě to maximální nezatížené napětí vychází pod 450, ale když klesne teplota a článek má vyšší efektivitu tak mohou nastat světelné podmínky kdy to vyrobí i 110% výkonu a hned máte na stringu 500 V i přesto, že vám dle štítku na panelu vycházelo krásných 440 V.

Zde je tabulka výpočtů počtu panelů v sérii a různé projekce maximálního napětí při nízkých teplotách.

Na výpočty napětí při různých teplotách jsem použil [tuhle online kalkulačku](https://www.wrdsystems.com/max_voc_calc.php), pokud znáte lepší, pošlete PR na github.

![](vypocty.png)

Z tabulky je patrné, že 11 panelů by v létě šlo, ale při nižších teplotách by to mohlo bouchnout. Někde jsem četl, že i v létě po chladném dešti a rozložení mraku může nastat situace, kdy panel dostane přímý zásah slunce, ale od okolních mraků ještě další fotony difuzním osvětlením a jste na hraně s napětím.

Trochu jsem si říkal, jestli nezapojím těch 11 a nedám k měniči nějaký výkonový rezistor, který by zaručil že tam úplně otevřené napětí nikdy nebude. Možná existuje i nějaký MOSFET/transil? Nevím.

## Parametry

Vybral jsem tyto komponenty:

* Axpert MKS III 5kW, 450V string
* Pylontech US2000C 2.4kW

### Měnič a dobíječ Axpert🔌

Tato čínská bedýnka od firmy Voltronic v sobě spojuje jeden MPPT dobíječ pro string s napětím 120-430V a 5 kW invertor. Maximální napětí je 450V. Tohle se mi dost líbí, protože spojíte spoustu panelů a nemusíte je spojovat sérioparalelně. Navíc nejste připraveni o elektrický oblouk když nesprávným způsobem odpojujete string pod proudem 🙂

Zatím funguje parádně, pokud mě jednou zklame, půjde z baráku a další zařízení by bylo asi od Victronu.
Tento Axpert se dá spojovat paralelně, takže je možné druhým kusem posílit výkon na 10 kW a získám tím druhý MPPT vstup z dalšího solárního stringu. Idle spotřeba je ve 10W, ale to je Standby s vypnutým invertorem. Běžně jste na 60 W. Je proto na zvážení jestli mít jeden výkonnější měnič co bere 90W v idle, nebo dva stejné kde součet bude 120W.

### Pylontech US2000C🔋

Tady jsem dost řešil jestli olovo, nebo lithium. Naštěstí mi bylo olovo rozmluveno jedním solárníkem, dokonce i Ampérák to někde počítal, že pokud nemáte přísun levných baterií tak lithium vydrží déle. Já v tom nejsem úplný expert takže můžete mít jiný názor 🙂

Další rozhodování bylo zda si baterku postavit, nebo jít do hotových. Trochu jsem to studoval, ale řešil jsem hromadu jiných technickcýh záležitostí a návrh baterky jsem taky outsourcoval na čínského výrobce Pylontech.

Baterie v sobě nemá jen BMS, ale i FET tranzistory odpojující svorky. Takže v případě vybití, přebití, nadproudu dovnitř nebo ven to bude neustále chránit baterii.
Něco co se blíží tomuto konceptu nabízí [JK BMS](https://www.aliexpress.com/item/1005003104573871.html). Je možné, že se na něj ještě dostane v budoucnu.

Ohledně kapacity je zas nutné si uvědomit, že pokud má měnič idle spotřebu 60W, tak přes období třeba 14 hodin kdy od večera do rána nesvítí spotřebuje z baterek 840 Wh.

Pořídil jsem pro začátek 2 ks, ale už teď nějak cítím, že bude třeba přikoupit. Navíc dělat elektrárnu teď před podzimem, kdy každým dnem je energie méně a méně, je dost depresivní 🙂

## Monitoring do MQTT

Doma mám centrální server na kterém mi běží tyto služby. Celkem známá trojkombinace kterou používám již roky s naší IoT stavebnicí [HARDWARIO TOWER](https://tower.hardwario.com/).

* Node-RED
* Grafana
* InfluxDB (1.8!)
* Mosquitto MQTT broker

Technické vybavení solární elektrárny mám v jiné místnosti a zde jsem umístil Raspberry Pi.

Do tohoto RPI je zapojený měnič přes USB-RS232 převodník. V měniči je RJ45 protikus. Prvně jsem to provozoval přes USB ale po pár dnech se USB odmlčelo. Po rebootu se opět zprovoznilo, ale je tam zřejmě něco špatně s USB stackem který není kvalitní.

Druhý USB-RS232 převodník je zapojený do Console RJ45 portu v hlavní baterii Pytlontech.

Pro každé zařízení/převodník mám v RPI jednu službu která přetransforumuje data z obou zařízení do JSONu a přes MQTT je zašle na server do MQTT brokeru.

### Axpert monitoring

Rozšířil jsem projekt [axpert-monitor](https://github.com/b48736/axpert-monitor) a přidal si k němu MQTT posílání a možnost ovládání.
Je to nodejs aplikace. Moc v tom neumím a je to rozšířené narychlo, takže se není čím chlubit. Ale prošel jsem hromadu různých projektů v různých jazycích a tohle prostě fungovalo.

Můj projekt [axpert-monitor-mqtt naleznete zde](https://github.com/hubmartin/axpert-monitor-mqtt).

Vyčítá se zatím jen dotaz na parametry `QPIGS`, ale můžete přidat další dotazy díky originální knihovně `axpert-monitor`.

### Pylontech monitoring

Hledal jsem něco funkčního po RS232 Console portu. Na RS485 mám již komunikaci do měniče a pokud jsou RS485 linky všechny interně propojené (jsou?) tak jsem nechtěl ovlivňovat rychlým logováním běžnou komunikaci s měničem.
Takže jsem šel cestou přes Cosnole port kde je textová komunikace.

Všude se píše o nějaké rychlost 2400 baud. Já chvíli řešil proč nekomunikuju, tak jsem zkoušel různé rychlosti chytl jsem se na 115200 na článcích přímo z výroby.

Zase jsem prošel mnoho projektů ale většina je na RS485 a na Console port byla jedna v node-red. Takže jsem na RPI nahodil moloch s node-red a možná to vylepším do předchozího skriptu, když bude čas.

Projekt [nodered-pylontech-console-reader](https://github.com/juanhaywood/nodered-pylontech-console-reader/) má úplně na konci ke stažení flow. Tak stačí importovat. Ještě je potřeba doinstalovat z Manage Palette možná Serial interface node.

## Vizualizace📈

### Node-RED Dashboard

Pro nějaké základní realtime přehledy a nějaké jednoduché grafy pár dní zpět bez možnosti zoomu lze použít v Node-RED integrovaný Dashboard.

![Node-RED Dashboard](dashboard.png)

Na následujícím obrázku je ukázka flow, nyní nás zajímá jen vlevo vstup `axpter/test` a čtyři modré `gauge` bloky.

![Node-RED Dashboard](dashboard-flow.png)

V Gauge prvku mám pak v poli přímo objekt `payload` ze kterého rovnou vyčítám z JSONu parametr `outputPowerActive` a další. Jinak byste museli mezi tyto dva nody vkládat ještě blok Change nebo Function.

Pokud kromě Gauge chcete i jednoduché grafy a nechcete si komplikvoat setup s Grafanou, taky to lze. Ale je zde možnost z MQTT jen fixního počtu bodů/data a nelze zoomovat. Taky po resetu Node-RED se vám ztratí historie. Tady doporučím node [persist](https://flows.nodered.org/node/node-red-contrib-persist), který vám může pomoci graf uložit a po startu zase vyčíst.

![Gauge nastavení](gauge-settings.png)

Pro pokročilejší grafy a analýzy používám (starší) InfluxDB 1.8 a nástroj Grafana. Obojí se dá nainstalovat i přes docker. Právě v obrázku výš kde bylo Node-RED flow jste mohli vidět i část, která přeposílá JSONy do InfluxDB databáze. Je před ním ještě blok `throttle` aby se do DB nesypaly data příliš rychle.

Po MQTT chci aktualizace každou vteřinu (proč ne) ale do DB je to zbytečné. Občas tento interval trochu zkrátím když dělám pokusy jako test zatížení apod., abych měl víc bodů k pozdější analýze.

Kompletní flow z ukázky výše si můžete do Node-RED importovat následujícím JSONem. Pokud nepoužíváte InfluxDB a Grafanu, tak horní část můžete vymazat. Blok function obsahuje transformaci, aby JSON upravil do jedné úrovně, aby šla poslat přímo do InfluxDB.

```
[{"id":"4a04d7eda726bfed","type":"mqtt in","z":"07cff771031e69d3","name":"","topic":"axpert/test","qos":"2","datatype":"json","broker":"efb90626.1e64b8","nl":false,"rap":true,"rh":0,"inputs":0,"x":200,"y":300,"wires":[["631854f2f315b7af","a284db87f09675c4","d6e10f9c505e472a","9d611699a56a9a86","5aa7e291b1b3be59","e323f3e10f3225ed"]]},{"id":"6c841eb30e9d1cf8","type":"influxdb out","z":"07cff771031e69d3","influxdb":"4aa793cd0438b3f3","name":"","measurement":"main","precision":"","retentionPolicy":"","x":850,"y":220,"wires":[]},{"id":"40dfa3996fabc5c8","type":"function","z":"07cff771031e69d3","name":"","func":"\nconst flattenJSON = (obj = {}, res = {}, extraKey = '') => {\n   for(key in obj){\n      if(typeof obj[key] !== 'object'){\n         res[extraKey + key] = obj[key];\n      }else{\n         flattenJSON(obj[key], res, `${extraKey}${key}.`);\n      };\n   };\n   return res;\n};\n\n\nmsg.payload = flattenJSON(msg.payload);\n\nreturn msg;","outputs":1,"noerr":0,"initialize":"","finalize":"","libs":[],"x":620,"y":220,"wires":[["6c841eb30e9d1cf8","cc277891fb385f5a"]]},{"id":"cc277891fb385f5a","type":"debug","z":"07cff771031e69d3","name":"","active":false,"tosidebar":true,"console":false,"tostatus":false,"complete":"false","statusVal":"","statusType":"auto","x":810,"y":280,"wires":[]},{"id":"631854f2f315b7af","type":"throttle","z":"07cff771031e69d3","name":"","throttleType":"time","timeLimit":"15","timeLimitType":"seconds","countLimit":0,"blockSize":0,"locked":false,"x":470,"y":220,"wires":[["40dfa3996fabc5c8"]]},{"id":"a284db87f09675c4","type":"debug","z":"07cff771031e69d3","name":"","active":false,"tosidebar":true,"console":false,"tostatus":false,"complete":"false","statusVal":"","statusType":"auto","x":490,"y":280,"wires":[]},{"id":"d6e10f9c505e472a","type":"ui_gauge","z":"07cff771031e69d3","name":"","group":"e3f86af73aa4cc91","order":1,"width":"2","height":"2","gtype":"donut","title":"Output","label":"W","format":"{{payload.outputPowerActive}}","min":0,"max":"5000","colors":["#00b500","#e6e600","#ca3838"],"seg1":"","seg2":"","className":"","x":470,"y":340,"wires":[]},{"id":"9d611699a56a9a86","type":"ui_gauge","z":"07cff771031e69d3","name":"","group":"e3f86af73aa4cc91","order":4,"width":"2","height":"2","gtype":"donut","title":"Battery","label":"%","format":"{{payload.batteryCapacity}}","min":0,"max":"100","colors":["#00b500","#e6e600","#ca3838"],"seg1":"","seg2":"","className":"","x":480,"y":380,"wires":[]},{"id":"5aa7e291b1b3be59","type":"ui_gauge","z":"07cff771031e69d3","name":"","group":"e3f86af73aa4cc91","order":3,"width":"2","height":"2","gtype":"donut","title":"Load","label":"%","format":"{{payload.outputLoadPercent}}","min":0,"max":"100","colors":["#00b500","#e6e600","#ca3838"],"seg1":"","seg2":"","className":"","x":470,"y":420,"wires":[]},{"id":"e323f3e10f3225ed","type":"ui_gauge","z":"07cff771031e69d3","name":"","group":"e3f86af73aa4cc91","order":2,"width":"2","height":"2","gtype":"donut","title":"PV Power","label":"W","format":"{{payload.pvPower}}","min":0,"max":"2000","colors":["#00b500","#e6e600","#ca3838"],"seg1":"","seg2":"","className":"","x":480,"y":460,"wires":[]},{"id":"efb90626.1e64b8","type":"mqtt-broker","broker":"127.0.0.1","port":"1883","clientid":"","autoConnect":true,"usetls":false,"protocolVersion":4,"keepalive":"60","cleansession":true,"birthTopic":"","birthQos":"0","birthPayload":"","willTopic":"","willQos":"0","willPayload":""},{"id":"4aa793cd0438b3f3","type":"influxdb","hostname":"127.0.0.1","port":"8086","protocol":"http","database":"fve","name":"","usetls":false,"tls":""},{"id":"e3f86af73aa4cc91","type":"ui_group","name":"Electricity","tab":"e8a3e7671183bf0f","order":1,"disp":true,"width":"6","collapse":false,"className":""},{"id":"e8a3e7671183bf0f","type":"ui_tab","name":"FVE","icon":"dashboard","order":7,"disabled":false,"hidden":false}]
```

### Grafana
InfluxDB a Grafana umožňují komfortnější analýzy a měření. Do databáze se vám hrne spousta informací a vy si pak naklikáte které a jak chcete zobrazovat. Ideální je logovat toho více, protože někdy chcete zpětně vizualizovat další parametr a je fajn, když tam ty data máte.

Provedl jsem export [konfiguračního JSONu pro můj dashboard](grafana.json), který načtěte v Grafaně vlevo symbolem `+` kde je `Create > Import`.

Jak to zprovoznit neuvedu protože InfluxDB/Grafana návodů je všude spousta. Navíc v tom nejsem nějaký velký expert.

Pokud máte RPI a chcete co nejméně práce a mít Influx, Grafanu a Node-RED nainstalovaný hned, můžete využít z naší HARDWARIO dílny přímo image pro RPI na [Githubu](https://github.com/hardwario/hio-raspbian/releases). Ten používáme i pro naši opensource low-power stavebnici  [HARDWARIO TOWER](https://tower.hardwario.com/).

![Vizualizace s Grafanou](grafana.png)

## Poznatky z praxe

### Komunikace invertoru s BMS

Ačkoliv s Pylontechy přišel RJ45 kabel označený PYLON a INVERTER na obou koncích, vůbec není správně zapojený. Je to obyčejný RJ45 kabel zapojený 1:1. Trvalo několik iterací, než jsem asi z 5 zdrojů ověřil zapojení na jedné a druhé straně.

To, že Axpert s bateriemi komunikuje signalizuje blikáním šestihranného symbolu baterie na displeji. Pokud do cca 3 minut měnič baterky nenajde, zobrazí chybu 61. Pokud nezapojíte GND, bude vám občas komunikace vypadávat a v grafech z dat Axpertu vám bude občas SOC skákat na hodnotu "0" 🙂.

![Axpert Pylontech pinout](pylontech-rj45.jpg)

Dále jsem zjistil, že položku nastavení Maximálního nabíjecího proudu (02) si měnič nastavuje přes tento datový kabel. Takže když jej změníte, hned se zase přenastaví zpátky. Ale prý se plynule mění podle SOC a zrovna jestli BMS třeba balancuje. Viděl jsem stav kdy do 90% to kleslo na 20A a v 95% stouplo na 25A.
