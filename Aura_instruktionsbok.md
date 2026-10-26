# Aura – instruktionsbok

Den här guiden visar hur du ställer in Aura-dashboarden för ett nytt hem, med nya lampor, sensorer och enheter. **Allt du behöver ändra finns i ett och samma block: `mitt_hem` högst upp i filen.** Resten av filen (motorn) rör du aldrig.

---

## 1. Innan du börjar

1. **Installera button-card:** HACS → sök på *button-card* → Ladda ner. Kiosk-mode är valfritt; det döljer Home Assistants egen menyrad.
2. **Skapa en ny dashboard:** Inställningar → Instrumentpaneler → Lägg till → *Ny instrumentpanel från början*.
3. **Klistra in filen:** öppna dashboarden → ⋮ → Redigera → ⋮ → *Raw-konfigurationsredigerare*. Markera allt, klistra in hela `aura_dashboard.yaml` och tryck Spara.
4. **Ändra inställningarna:** gör dina ändringar i `mitt_hem` (samma ställe) och spara.
5. **Ladda om:** ladda om sidan efter varje ändring. Ladda om två gånger på surfplattan om den visar gamla saker.

### Så hittar du namnet på en enhet (entitets-id)

En entitet är det Home Assistant kallar en enskild lampa, sensor eller knapp. Aura behöver dess id, till exempel `light.koksbordet` eller `sensor.temp_vardagsrum_temperatur`. Du hittar det på två sätt:

- **Inställningar → Enheter och tjänster → Entiteter.** Sök på namnet och kopiera *Entitets-ID*.
- **Utvecklarverktyg → Tillstånd.** Här ser du alla entiteter och deras värden. Det är bra när du vill kontrollera att en sensor faktiskt har ett värde.

### Fem regler för att inte få fel

| Regel | Exempel |
|---|---|
| **Indraget är heligt.** Använd mellanslag, aldrig tab. Kopiera en befintlig rad och ändra den. | `      _plats: Borås` |
| **Tom text** skrivs med två apostrofer. | `_accent: ''` |
| **Tom lista** skrivs med hakparenteser. | `_fonster: []` |
| **Listor** har ett bindestreck per rad, indraget två steg mer än rubriken. | `        - binary_sensor.entredorr` |
| Allt efter **#** är en kommentar och ignoreras. | `_tema: mork   # mörkt tema` |

> **Tips:** saknar du en sak, till exempel solceller, larm eller kaffemaskin, lämnar du inställningen tom. Den delen av dashboarden döljs då automatiskt.

---

## 2. Utseende

```yaml
_hem_namn: Symfonigatan 7      # står stort i bilden på startsidan
_plats: Borås                  # rubrik på väderkortet
_tema: ljus                    # ljus = kräm och brons, mork = marinblått och indigo, noir = svart och champagne
_accent: ''                    # egen huvudfärg, t.ex. '#2F80ED'. Tomt = temats färg
_bakgrundsbild: /local/hem.jpg # stora bilden på startsidan. Tomt = mönster
```

**Egen bild:** lägg bilden i mappen `config/www/` (till exempel via File editor eller Samba). Den nås då som `/local/filnamn.jpg`. Med `www/rum/vardagsrum.jpg` blir det `/local/rum/vardagsrum.jpg`.

---

## 3. Rum och lampor (det viktigaste)

Varje rum blir en ruta på startsidan och en egen rad med lampor på **Ljus**-sidan.

```yaml
_rum:
  - namn: Vardagsrum
    ikon: mdi:sofa-outline
    bild: ''                                  # valfri egen bild för rummet
    temp: sensor.vardagsrum_temperatur        # valfri
    fukt: sensor.vardagsrum_luftfuktighet     # valfri
    lampor:
      - entity: light.taklampa_vardagsrum
        namn: Taklampan
        ikon: mdi:ceiling-light
        typ: tak
      - entity: light.golvlampa
        namn: Golvlampa
        ikon: mdi:floor-lamp-outline
      - switch.julgran_uttag                  # kortform: bara entiteten
```

| Nyckel | Betyder |
|---|---|
| `namn` | Rummets namn |
| `ikon` | Liten ikon för rummet (se kapitel 11) |
| `bild` | Egen bild som visas överst när rummet är valt. Tomt = rummets ikon |
| `temp` / `fukt` | Temperatur- och fuktsensor. Visas på rummets kort. Kan utelämnas |
| `lampor` | En lista med lampor. Det fungerar med `light.` och med `switch.` (smarta uttag) |

För varje lampa:

| Nyckel | Betyder |
|---|---|
| `entity` | Lampans entitets-id. **Krävs** |
| `namn` | Namnet på rutan. Utelämnat = namnet från Home Assistant |
| `ikon` | Den **lilla runda ikonen** uppe till vänster på rutan |
| `typ` | Den **stora bilden** på rutan (se nästa avsnitt) |

> **Låt Aura hitta rummen själv:** skriver du `_rum: []` hämtas rummen och lamporna från dina *Områden* i Home Assistant (Inställningar → Områden). Det är det snabbaste sättet i ett nytt hem. Du kan alltid skriva egna rum senare.

### 3.1 Välja rätt bild på lampan (`typ`)

Det här är det som styr den stora produktbilden. Bilderna är ritade i koden, så du behöver inte ladda ner några bildfiler.

| `typ:` | Bilden föreställer | Passar till |
|---|---|---|
| `tak` | Taklampa – pendel med metallskärm | Taklampor, köksbord, pendlar |
| `klot` | Glaskula – opalvit klotlampa med kromad kåpa | Klotlampor, glaspendlar |
| `krona` | Takkrona – mässingskrona med armar och glödtrådslampor | Takkronor, "spindel"-lampor |
| `golv` | Golvlampa – hög fot med skärm | Golvlampor, läslampor |
| `bord` | Bordslampa – rund fot med skärm | Fönsterlampor, sänglampor, bordslampor |
| `glod` | Glödlampa | Hall, allmänna lampor, LED-lister, "vet inte" |
| `stake` | Ljusbåge – adventsljusstake med ljuspunkter | Ljusstakar, ljusslingor, fönsterdekor |
| `mane` | Månlampa | Månlampor, nattlampor |
| `uttag` | Smart uttag (stickpropp) | Smarta uttag som styr något annat än en lampa |
| `/local/…png` | **Din egen bild** (se 3.2) | Lampor som inte liknar någon av bilderna |

**När lampan är tänd** lyser bilden i lampans egen färg. Ändrar du färgen i färgpanelen ändras skenet också.

**Om du inte skriver `typ`** gissar Aura utifrån ikonen, namnet och entitets-id:t. Den letar efter orden nedan. Första regeln som stämmer vinner:

| Om ikon/namn/entitet innehåller … | blir bilden |
|---|---|
| `switch.` / `uttag` / `power-plug` (+ *måne* → `mane`, + *fönster* → `stake`) | `uttag` |
| `mån`, `moon` | `mane` |
| `floor`, `golv` (t.ex. `mdi:floor-lamp`) | `golv` |
| `globe`, `klot`, `glob`, `opal` | `klot` |
| `chandelier`, `krona`, `spider` (t.ex. `mdi:chandelier`) | `krona` |
| `ceiling`, `taklampa`, `kök` | `tak` |
| `lamps`, `fönster`, `window`, `slinga`, `string` | `stake` |
| `lamp-outline`, `table`, `desk`, `säng`, `soffa` | `bord` |
| inget av ovanstående | `glod` |

> **Säkrast:** skriv alltid `typ:`. Då blir det rätt bild oavsett vad lampan heter.

### 3.2 Egen bild på en lampa

Om ingen av bilderna passar kan du använda ett eget foto av lampan:

1. **Hitta en bild.** Bäst blir en **PNG med genomskinlig bakgrund**, till exempel produktbilden från butikens webbplats. En bild med vit bakgrund fungerar också, men syns då som en ruta.
   - *Tips:* tjänster som remove.bg tar bort bakgrunden gratis.
2. **Spara bilden** i `config/www/lampor/`, till exempel som `kloten.png`.
3. **Skriv sökvägen** som `typ:`

```yaml
      - entity: light.kokslampa
        namn: Köksbordet
        typ: /local/lampor/kloten.png
```

Så ser den ut:
- **Tänd lampa:** bilden får ett sken i lampans färg bakom sig.
- **Släckt lampa:** bilden blir lite blekare.
- **Färgpanelen:** samma bild visas när du öppnar den.

### 3.3 Styra lamporna i dashboarden

- **Tryck** på rutan: tänd eller släck.
- **Dra fingret i sidled** på rutan: dimra (bara dimbara lampor).
- **Tryck på den lilla ikonen** (den har en färgprick) eller **håll inne** på rutan: färgpanelen öppnas med ljusstyrka, varm/kall ton och färg.

---

## 4. Scener, släck allt och ljuseffekter

```yaml
_scener:
  - entity: scene.mys
    namn: Mys
    ikon: mdi:candle
    farg: '#FBBF24'               # färgen på scenens ikonbricka
_slack_allt: script.slack_allt_om_5_minuter   # valfritt
_slack_minuter: 5
```

- **`_scener: []`** visar alla scener du har.
- **`_slack_allt`** blir knappen *Släck allt om 5 min*. Utan skript visas den inte.

**Ljuseffekter** (valfritt) kräver två saker i Home Assistant:

```yaml
_ljuseffekt: input_select.ljuseffekt          # visar vilken effekt som är på
_ljuseffekt_script: script.ljuseffekt_knapp   # körs med variabeln "effekt"
_ljuseffekter:
  - effekt: norrsken
    namn: Norrsken
    ikon: mdi:weather-night
```

Skriptet får värdet i `effekt`, till exempel `norrsken`, och bestämmer självt vad som ska hända. Vill du inte ha effekter skriver du `_ljuseffekter: []`.

**Partyläge** (knappen under Ljuseffekter):

```yaml
_party_script: script.partylage   # lägg in party_script.yaml som skript i Home Assistant
_party_lampor: []                 # tom = alla färglampor i dina rum
```

- **Färger:** lamporna byter färg i den takt du väljer (*Lugn*, *Medel* eller *Fest*). Färgerna hämtas från **skivomslaget** på låten som spelas, så en ny låt ger nya färger.
- **Slut:** när musiken pausas, eller när du trycker igen, blir lamporna som de var innan.
- **Takten följer inte trummorna exakt.** Det kräver något som lyssnar på ljudet, till exempel Philips Hue Sync eller WLED med mikrofon.

---

## 5. Media

```yaml
_mediaspelare:
  - media_player.vardagsrum_sonos
  - media_player.apple_tv_vardagsrum
_party_skarm: https://app.music-assistant.io/stable/#/party
_party_url: ''
```

- **Music Assistant-spelare** får sök, bibliotek, album, spellistor, kö och party.
- **Apple TV, Chromecast, smart-tv** får i stället en fjärrkontroll, en styrplatta och knappar för appar. Fjärrkontrollen (`remote.`) hittas automatiskt.
- **`_mediaspelare: []`** tar med alla spelare. Har du Music Assistant tas bara dess spelare med.
- **Byta spelare:** tryck på en spelare i raden under mediaspelaren. **Dubbeltrycker** du på en annan högtalare spelar den samma musik (gruppering).
- **`_party_url`:** fyll i gästlänken från Music Assistant. Då visas en QR-kod som gästerna kan skanna.

---

## 6. Energi (elpris)

```yaml
_elpris: sensor.tibber_prices
_el_prefix: symfonigatan_7     # början på dina Tibber-sensorer
_elomrade: SE3
_el_billig: 1                  # kr/kWh – under detta: grön stapel
_el_dyr: 2                     # kr/kWh – över detta: röd stapel
```

- **`_elpris`** ska vara en sensor som har timpriserna för idag och imorgon i sina attribut. Det fungerar med Tibber, Tibber Prices och Nordpool.
- **`_el_prefix`** används för att hitta månadskostnaden: `sensor.<prefix>_manadskostnad` eller `sensor.<prefix>_manatlig_kostnad`.
- Inget elpris och inga solceller? Lämna `_elpris: ''` så döljs Energi-sidan.

### 6.1 Solceller

Lämna allt tomt tills du har solceller. När du fyller i dem dyker ett **Solceller**-kort upp på Energi-sidan, bredvid nyckeltalen. Startsidans bild får också en etikett med vad panelerna ger just nu, till exempel "4,3 kW sol".

```yaml
_sol_effekt: sensor.solar_power              # effekt just nu (W eller kW)
_sol_idag: sensor.solar_energy_today         # producerat idag (kWh)
_sol_salt: sensor.solar_export_today         # sålt till nätet idag (kWh) – valfritt
_husets_effekt: sensor.house_power           # vad huset drar just nu (W) – valfritt
_sol_prognos: sensor.energy_production_today # Forecast.Solar, prognos idag – valfritt
_sol_max_kw: 10                              # anläggningens toppeffekt – valfritt
```

| Fyller du i … | visas |
|---|---|
| `_sol_effekt` | Stort tal "4,32 kW", en sol som lyser och etiketten *Producerar* eller *Vilar* |
| `_sol_max_kw` | En stapel som visar hur nära max du ligger |
| `_sol_idag` | *Producerat idag*, plus *Värde idag* (kWh × dagens snittpris) |
| `_sol_salt` | *Sålt idag* och *Eget bruk* i procent |
| `_husets_effekt` | "3,1 kW över till nätet" eller "Huset tar 0,8 kW från nätet" |
| `_sol_prognos` | *Prognos idag* (från integrationen Forecast.Solar) |

- **Enheter:** Aura räknar själv om W/kW och Wh/kWh, så det spelar ingen roll vilken enhet sensorn använder.
- **Var sensorerna finns:** de kommer oftast från växelriktarens integration, till exempel Huawei Solar, SolarEdge, Fronius eller Growatt, eller från din elmätare (Tibber Pulse, P1-mätare).
- **Bara en sensor räcker:** `_sol_effekt` eller `_sol_idag`.

### 6.2 Hembatteri

Har du ett batteri, med eller utan solceller, fyller du i de här. Lämna dem tomma annars, så syns inget.

```yaml
_batteri_niva: sensor.battery_state_of_charge   # laddnivå i %
_batteri_effekt: sensor.battery_power            # effekt in/ut (W eller kW)
_batteri_effekt_omvand: false                    # true om sensorn visar PLUS när batteriet laddar UR
_batteri_kapacitet: 10                           # storlek i kWh – valfritt
```

Så visas batteriet:
- **Med solceller:** solcellskortet byter namn till **Sol & batteri** och får ett batteri bredvid soleffekten. Det visar laddnivån, *Laddar 2,1 kW*, *Ger 1,4 kW* eller *Vilar*, och med `_batteri_kapacitet` även *Fullt om 1 h 10 min* eller *Räcker ca 5 h*.
- **Utan solceller:** kortet heter bara **Batteri** och visar även hur mycket som är lagrat just nu.
- **Startsidan:** bilden får en etikett med laddnivån.

### 6.3 Förbrukning och "Vad kostar det?"

Energi-sidan visar nu också mer om elpriset:
- **Just nu-kortet:**
  - hur dagens pris står sig mot dagens snitt, i procent
  - **Huset drar nu**, i kW och i kr per timme
  - en prognos för hela månaden
- **Nyckeltalen:**
  - snitt idag och imorgon (grönt om morgondagen är billigare)
  - högst och lägst idag, med klockslag
  - förbrukat idag, kostnad idag, månadskostnad och prognos för månaden
- **Vad kostar det?:** ett eget kort under diagrammet. Där ser du vad det kostar att köra till exempel diskmaskinen *nu* jämfört med *bästa tid*, och hur mycket du sparar på att vänta.
- **Billigaste tider:** i Just nu-kortet. Visar de billigaste perioderna på 1, 3 och 6 timmar i rad, till exempel för en kort tvätt, diskmaskinen eller elbilen.
- **Diagrammets etikett:** visar om morgondagen blir billigare eller dyrare än idag.

```yaml
_el_effekt: ''              # vad huset drar just nu (W)
_el_forbrukning_idag: ''    # förbrukat idag (kWh)
_el_kostnad_idag: ''        # kostnad idag (kr)
_el_apparater:
  - namn: Diskmaskin
    ikon: mdi:dishwasher
    kwh: 1.1                # ungefär hur mycket en körning drar
    timmar: 2               # hur länge den går
  - namn: Elbil
    ikon: mdi:car-electric
    kwh: 20
    timmar: 6
```

- **Tibber Pulse:** effekten och förbrukningen hittas automatiskt via `_el_prefix`, så de tre första raderna kan vara tomma.
- **Utan förbrukningssensorer** visas bara de delar som går att räkna ut från elpriset.

---

## 7. Säkerhet

```yaml
_larm: alarm_control_panel.alarmo
_dorrar: [binary_sensor.entredorr_oppning, binary_sensor.altandorr_oppning]
_fonster: []
_las: [lock.ytterdorr]
_rorelse: [binary_sensor.rorelse_hall]
_larmgivare: [binary_sensor.brandvarnare_kok, binary_sensor.vattenlacka_tvatt]
_kameror: []          # tom = alla kameror (Frigate först)
```

- **Listorna** kan skrivas antingen på en rad med `[a, b]` eller som vanliga rader med bindestreck.
- **Larmkoden** knappas in i dashboardens egen knappsats.
- **Sidan döljs** om du saknar både larm, kameror och dörrar.

### 7.0 Larmet som ett riktigt larm

**På surfplattan (Aura):** med `_larm_skarm: true` visas en helskärmsvy på alla enheter som har Aura öppen:

| Läge | Visas |
|---|---|
| **Larmet slås på** | Nedräkning till larmet är på, och knappen *Avbryt* |
| **Någon kommer in** | "Larmet är på!", nedräkning och *Slå av larmet* (knappsatsen öppnas) |
| **Larmet går** | Blinkande röd vy, vilken dörr eller sensor som löste ut, och kamerabilder som uppdateras |

Med *Dölj* stänger du vyn för just den händelsen.

**Ljud och notiser (Home Assistant):** lägg in automationen i `larm_automation.yaml`. Den gör följande:
- **Larmet slås på:** säger "Larmet aktiveras om 60 sekunder…" och sedan "Tio sekunder kvar".
- **Larmet är på:** säger "Larmet är på" och skickar en notis.
- **Någon kommer in:** säger "Larmet är på. Ange koden…" tills koden anges, och skickar en notis.
- **Larmet går:** säger "Inbrottslarm!" på hög volym och skickar en notis till varje telefon med **kamerabild**. Notisen låter även i tyst läge, och ett tryck öppnar larmet.
- **Larmet slås av:** säger "Välkommen hem! Larmet är avstängt" och skickar en notis.
- **Du kommer hem medan larmet är på:** skickar en notis med en knapp till larmet.

### 7.1 Smart förslag om öppna dörrar och fönster

```yaml
_forslag_oppen_minuter: 10
```

- **När förslaget visas:** när en dörr eller ett fönster i `_dorrar` eller `_fonster` har stått öppet så här länge, till exempel "Altandörr står öppen – den har varit öppen i 14 min". På kvällen och när larmet är på kommer förslaget direkt.
- **Ignorera:** tryck på **Ignorera** så kommer nästa förslag fram. Etiketten "1 av 3" visar hur många förslag som väntar. Öppnas dörren igen senare kommer förslaget tillbaka.

### 7.2 Smart förslag på kvällen: släck allt och stäng av tv:n

```yaml
_kvall_kl: 22          # från detta klockslag
_kvall_dagar: vardag   # vardag (mån–fre) eller alla
_kvall_minuter: 5      # nedräkning innan allt släcks
```

- **När förslaget visas:** efter kl 22 en vardag, om lampor är tända eller Apple TV:n är på. Förslaget lyder till exempel "Dags att runda av kvällen? 3 lampor och Apple TV är fortfarande på".
- **Släck om 5 min:** startar en nedräkning, som syns som "Godnatt-läge på – släcker allt om 4:32". Med **Avbryt** stoppar du den.
- **När tiden är ute:** alla lampor i dina rum släcks och tv-apparater bland `_mediaspelare` (Apple TV, Chromecast, smart-tv) stängs av.
- **Obs:** nedräkningen körs i dashboarden. Den enhet där du tryckte måste alltså ha Aura öppen tills tiden är ute, till exempel surfplattan på väggen. Stänger du appen och öppnar den igen inom en minut efter att tiden gått ut, utförs det ändå.

---

## 8. Väder och dagen

```yaml
_vader: weather.forecast_hem          # vilken weather.-entitet som helst
_pollen_prefix: sensor.pollen_goteborg_
_pollen_plats: Göteborg
_temp_inne: sensor.vardagsrum_temperatur
_att_gora: todo.att_komma_ihag        # listan "Kom ihåg"
_kalendrar: []                        # tom = alla kalendrar
```

- **Antal dagar i prognosen:** standardvädret (Met.no) ger 5 dagar framåt. Då visas *Idag* som första ruta. SMHI ger fler dagar.
- **Pollen:** alla sensorer vars id börjar med `_pollen_prefix` tas med, till exempel från integrationen *Pollenprognos*. De visas längst ner i **Väderkoll**, och bara när det finns pollen i luften.

### 8.1 Mer väderinfo: SMHI, varningar och månen

```yaml
_smhi_prefix: sensor.smhi_odp_home_     # början på SMHI-sensorerna
_smhi_varning: sensor.smhi_alerts_vastra_gotalands_lan
_smhi_varning_aktiv: binary_sensor.smhi_alerts_active_vastra_gotalands_lan
_mane: sensor.moon_fas
```

Vädersidan har nu de här delarna:

- **Väderkoll:**
  - **SMHI-varningar** överst, i gult, orange eller rött efter nivå, med område, tid och beskrivning. Utan varningar står det *Inga varningar från SMHI*.
  - **En sammanfattning,** till exempel *"Ikväll: växlande molnighet, 8° just nu och ner mot 3° i natt."*
  - **Punkter om det som är på väg:** regn, snö, åska eller hagel med tid och mängd, uppehåll, frost och halka, blåst och byar, dimma, UV-index, om morgondagen blir varmare eller kallare, och veckans finaste dag.
- **Just nu:** vind med riktning, fukt, tryck, UV, molnbas och nederbörd. Molnbasen och nederbörden kommer från SMHI.
- **Dagar:** en lista med temperaturstapel för varje dag, från blått för kallt till orange för varmt, och nederbörd.
- **Solen:** när den går upp eller ner, och hur mycket kortare eller längre dagarna blir.
- **Månen:** fas, hur stor del som är belyst, och nästa fullmåne och nymåne.
- **Vinden:** en kompass med vindens riktning, hastighet i m/s, SMHI:s benämning (svag, måttlig, frisk eller hård vind) och byar.

Vindsensorn i km/h räknas om till m/s automatiskt. En aktiv SMHI-varning visas också först i **Smart förslag** på startsidan.

### 8.2 Regnradar, natthimlen och himmelskortet

```yaml
_radar: false     # true = animerad regnradar överst i sidokortet (då behöver sidokortet skrollas)
_norrsken: true   # norrskensprognos (false = av)
```

- **Himmelskortet (Just nu):** bakgrunden följer vädret och tiden på dygnet. Den är solgul när det är soligt, gråblå vid regn och natthimmel med blinkande stjärnor när det är mörkt och klart. I mörka teman är färgerna djupare. Kortet visar också innetemperaturen bredvid utetemperaturen.
- **Regnradar:**
  - Visar de senaste radarbilderna som en animation på en karta runt hemmet. Kartan centreras med hjälp av hemmets position i Home Assistant.
  - Den pulserande pricken är ditt hem, och tidslinjen nedtill visar vilken bild som visas.
  - Radarn uppdateras var 10:e minut.
- **Natthimlen:**
  - månen i rätt fas
  - om det blir klart eller molnigt i natt
  - **norrskensprognos** med Kp-index från NOAA, bedömd för din breddgrad: *Liten chans*, *Kanske lågt mot norr* eller *Goda chanser – titta norrut!*
- **Solbågen:** på natten visar den månens väg fram till soluppgången.
- **Timme för timme:** blå staplar för nederbörd och en liten vindpil med m/s för varje timme.

**Om tjänsterna:** radarbilderna kommer från RainViewer, kartan från Esri och norrskensprognosen från NOAA. Webbläsaren hämtar kartbitar för området runt hemmet, alltså ett område på ungefär en kommuns storlek, inte din exakta adress.

---

## 9. Hushållet

```yaml
_kaffe: kaffemaskin
_kaffe_modell: Siemens EQ900
_dammsugare:                       # en eller flera robotar
  - entity: vacuum.robot_nere
    namn: Nedervåningen
    batteri: sensor.robot_nere_battery_level   # valfritt
  - entity: vacuum.robot_uppe
    namn: Övervåningen
_vaxter:
  - sensor: sensor.monstera_fuktighet
    namn: Monstera
    min: 30      # under detta: "behöver vatten" (Smart förslag)
    max: 60
```

**Flera robotdammsugare:** lägg till dem efter varandra i listan, som i exemplet ovan.
- **Startsidan:** varje robot får en egen ruta under *Mina enheter*. Trycker du på rutan öppnas just den roboten på Hushåll-sidan.
- **Hushåll-sidan:** robotkortet får flikar överst, en per robot. En pulserande prick visar vilken som städar just nu.
- **`batteri`** behövs bara om roboten inte själv rapporterar batterinivå.
- **`popup: '#dammsugare'`** gör att rutan, robotbilden och knappen *Karta* öppnar en egen pop-up i stället för "Mer info", till exempel bubble-card med Dreame-kartan. Pop-up-kortet läggs längst ner i filen under `views:`, bredvid Aura-kortet i samma `vertical-stack`. Dess `hash:` måste vara exakt samma som `popup:`.
- **Äldre format:** det fungerar fortfarande, alltså `_dammsugare: vacuum.x` tillsammans med `_dammsugare_namn` och `_dammsugare_batteri`.

**Kaffemaskin (Home Connect):** skriv bara *början* på entiteterna. Med `_kaffe: kaffemaskin` letar Aura efter:

- `switch.kaffemaskin_av_pa` (på/av)
- `sensor.kaffemaskin_driftstatus` (status)
- `select.kaffemaskin_aktivt_program` (välja dryck)

Har du ingen kaffemaskin, dammsugare eller växter lämnar du raden tom (`''` eller `[]`).

### 9.1 Bussen och röstpåminnelse

```yaml
_buss: sensor.mot_centrum      # t.ex. Västtrafik-sensorn
_buss_namn: Centrum            # visas som "Mot Centrum"
_buss_linje: 8                 # om sensorn inte själv anger linjen
_buss_forvarning: 10           # minuter innan avgång som högtalaren säger till
_buss_fonster: 30              # hur långt före önskad tid en buss räknas
_tts: ''                       # röst-tjänst, t.ex. tts.home_assistant_cloud (tomt = den första du har)
_tts_hogtalare: media_player.vardagsrum_3
```

- **Kortet:** visar linjen (8), riktningen, läget och nästa avgång stort, med "om 12 min".
- **Påminn om nästa buss:** ett tryck räcker. Högtalaren säger till 10 minuter innan nästa buss går.
- **Påminnelse om en senare buss:**
  1. Välj när du vill åka med **−** och **+** (en kvart i taget), till exempel 14:00, och tryck **Påminn**.
  2. Aura väljer den **sista bussen som går före 14:00**, upp till 30 minuter innan och högst 5 minuter efter. Det kan till exempel bli bussen 13:50.
  3. Kl 13:40 säger högtalaren: *"Dags att gå till bussen! Linje 8 mot Centrum går 13.50, om 10 minuter."*
  4. Västtrafik-sensorn känner bara till nästa avgång, så kortet visar "Väntar på en avgång strax före 14:00" tills den bussen är nästa på tur. Då visas "Bussen 13:50 · säger till 13:40".
- **Testa rösten:** länken *Testa rösten* läser upp nästa avgång direkt.
- **Obs:** påminnelsen sparas och körs i dashboarden. Ställ in den på surfplattan på väggen, som alltid har Aura öppen.

### 9.2 Servern

Kortet **Servern** hittar själv dina System Monitor-sensorer. Det visar:
- processor, minne och disk som ringar
- temperatur och upptid
- virtuellt minne
- vilken version av Home Assistant du kör
- hur många uppdateringar som väntar (tryck för att öppna uppdateringssidan)

Etiketten visar *Mår bra*, *Ansträngd* eller *Hög belastning*. Vill du dölja kortet skriver du `_server: false`.

### 9.3 Utseende: byt tema

Under **Hushåll → Utseende** byter du tema med ett tryck. Valet sparas i just den enheten, så surfplattan kan vara mörk medan mobilen är ljus.

| Tema | Färger |
|---|---|
| **Ljus** | Kräm och brons |
| **Mörk** | Marinblått och indigo |
| **Noir** | Djupt varmt svart och champagneguld, med jadegrönt för "allt bra" |

`_tema:` i `mitt_hem` (`ljus`, `mork` eller `noir`) bestämmer vad nya enheter startar med.

---

## 10. Bilder som väljs automatiskt

Utöver lamporna finns de här bilderna. De väljs av sig själva, du ställer inte in något:

| Bild | Används för |
|---|---|
| Robotdammsugare | `_dammsugare` (rör sig när den städar) |
| Högtalare | Mediaspelaren på startsidan |
| Kaffemaskin | `_kaffe` (ånga när den brygger) |
| Växt | `_vaxter` |

---

## 11. Ikoner du kan välja

Alla ikoner skrivs som `mdi:namn`. På **pictogrammers.com/library/mdi** kan du söka bland över 7 000 ikoner. Skriv in ett engelskt ord, till exempel *lamp*, och kopiera namnet.

**Lampor**

| Ikon | Passar till |
|---|---|
| `mdi:ceiling-light` / `mdi:ceiling-light-outline` | Taklampa |
| `mdi:chandelier` | Kristallkrona |
| `mdi:floor-lamp` / `mdi:floor-lamp-outline` | Golvlampa |
| `mdi:lamp` / `mdi:lamp-outline` | Bordslampa |
| `mdi:lamps` | Flera lampor, fönsterlampor |
| `mdi:desk-lamp` | Skrivbordslampa |
| `mdi:wall-sconce` | Vägglampa |
| `mdi:lightbulb` / `mdi:lightbulb-outline` | Glödlampa, allmänt |
| `mdi:led-strip-variant` | LED-list |
| `mdi:string-lights` | Ljusslinga |
| `mdi:candle` | Ljusstake |
| `mdi:moon-waning-crescent` | Månlampa |
| `mdi:power-socket-eu` | Uttag |
| `mdi:outdoor-lamp` | Utebelysning |
| `mdi:pine-tree` | Julgran |

**Rum**

| Ikon | Rum |
|---|---|
| `mdi:sofa-outline` | Vardagsrum |
| `mdi:silverware-fork-knife` | Kök |
| `mdi:bed-outline` / `mdi:bed-king-outline` | Sovrum |
| `mdi:human-baby-changing-table` / `mdi:teddy-bear` | Barnrum |
| `mdi:shower` / `mdi:bathtub-outline` | Badrum |
| `mdi:washing-machine` | Tvättstuga |
| `mdi:coat-rack` / `mdi:door` | Hall |
| `mdi:desk` | Kontor |
| `mdi:garage` | Garage |
| `mdi:flower-outline` / `mdi:grill` | Trädgård, altan |
| `mdi:stairs` | Trappa, övervåning |

**Scener**

| Ikon | Scen |
|---|---|
| `mdi:candle` | Mys |
| `mdi:movie-open-outline` | Film |
| `mdi:silverware-fork-knife` | Middag |
| `mdi:weather-night` | God natt |
| `mdi:weather-sunny` | Morgon |
| `mdi:party-popper` | Fest |
| `mdi:book-open-variant` | Läsa |

---

## 12. Felsökning

| Problem | Lösning |
|---|---|
| En ruta säger **"Hittas inte"** | Entitets-id:t är fel. Kontrollera stavningen i Utvecklarverktyg → Tillstånd. |
| **Ändringen syns inte** | Ladda om sidan, och gör det två gånger på surfplattan. Rensa appens cache i HA-appen om det inte hjälper. |
| **Röd text "Fel i …"** | Oftast fel indrag i YAML. Jämför med en rad som fungerar. |
| **En sida saknas i menyn** | Inställningen för den sidan är tom, till exempel `_elpris`, `_larm` eller `_vader`. |
| **Inget går att trycka på** | Kontrollera att button-card är installerat och uppdaterat. |
| **Fel bild på lampan** | Skriv `typ:` på lampan (kapitel 3.1). |
| **Elpriset eller sensorer visas inte efter en ny integration** | Starta om Home Assistant. |
