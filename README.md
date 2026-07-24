# Spektrál Stúdió

Hangfájlok spektrális elemzése és **újraépítése hangolható generátorokból**, közvetlenül a böngészőben.
Egyetlen önálló HTML fájl — nincs build, nincs függőség, nincs szerver. A hang nem hagyja el a gépedet.

## ▶ [Indítsd el itt: sid14925.github.io/spectral-studio](https://sid14925.github.io/spectral-studio/)

Vagy töltsd le az [`index.html`](index.html) fájlt, és nyisd meg helyben — ugyanúgy működik,
internet nélkül is.

## Mit csinál

A program FFT-vel felbontja a hangot, spektrális csúcsokat követ végig az időben, és
**szinusz-generátorokból + sávszűrt zajból** építi újra a felvételt sávonként. Minden sáv külön
hangolható, így ki lehet emelni vagy átírni egy-egy hangszert, illetve az énekhangot.

### Elemzés

- Saját radix-2 FFT, STFT 1024 / 2048 / 4096 mintás ablakkal (Hann, 75% átfedés)
- **HPSS** — harmonikus / ütős szétválasztás medián-szűréssel (idő- és frekvenciatengely mentén)
- Amplitúdó-kalibráció: `A = 4·|X| / N` (Hann-ablak), a végén globális RMS-illesztés az eredetihez

### Két üzemmód

**Frekvenciasávok** — logaritmikusan elosztott sávokra vágja a spektrumot. Sávonként kikeresi a
legerősebb spektrális csúcsokat (parabolikus interpolációval), és félhangnyi toleranciával
*részhang-pályákká* fűzi őket az időben. Ezek lesznek a generátorok. Amit a csúcsok nem
magyaráznak meg, az sávszűrt zajként kerül vissza — klasszikus szinuszos modellezés + reziduum.

**Hangszerkövetés** — harmonikus szorzat-spektrummal (HPS) alaphangokat keres keretenként,
a csúcsokat felharmonikus-sorokba rendezi (12 felharmonikusig), a sorokat hangfolyamokká fűzi,
majd k-means-szel hangszercsoportokba klaszterezi az alaphang-magasság és a spektrális fényesség
alapján. Külön sávot kap a **maradék zajréteg** és az **ütős / tranziens** réteg.

### Idővonal és szerkesztés

Sávonként egy idővonal, rajta a részhang-pályák log-frekvencia tengelyen, amplitúdó szerint
színezve, alattuk a zajréteg burkolója.

- **Kattintás** az idővonalra: hangerő-automatizálási pont hozzáadása / húzása
- **Alt + kattintás**: pont törlése
- **Space**: lejátszás / stop

Sávonkénti paraméterek: hangerő, panoráma, ±24 félhang hangolás, tónus (generátorok szintje),
zaj, kórus (cent-detune), torzítás, mute / solo, és „hangolás félhangrácsra" (a felharmonikusságot
megőrizve snappel a legközelebbi félhangra).

A hangerő, a panoráma és a burkológörbe élőben hat; a többi paraméter ~250 ms alatt újrarendereli
az érintett sávot.

### Export

WAV-export a teljes mixről vagy külön sávonként. Stem kinyeréséhez: szólózd a kívánt hangszersávot
és exportáld.

## Használat

1. Nyisd meg a [live verziót](https://sid14925.github.io/spectral-studio/) (Chrome / Edge / Firefox),
   vagy az `index.html` fájlt helyben.
2. Nyomj a **Demó hang** gombra, vagy húzz be egy saját hangfájlt.
3. **Elemzés + újraépítés**, aztán tekerj bele a sávokba.

### Tippek

- Valódi zenéhez a max hosszt érdemes 20–45 másodpercen hagyni — a HPSS medián-szűrés a legdrágább lépés.
- **FFT 4096**: szebben szétválnak a hangszerek. **FFT 2048**: pontosabbak a tranziensek.
- A hangszerkövetés a tisztán szóló, harmonikus anyagon működik a legjobban; sűrű, torzított mixben
  a csoportok összemosódnak.

## Korlátok

Ez spektrális modellezés, nem neurális forrásszétválasztás — a kiemelt „hangszer" mindig
közelítés, a szomszédos hangszerek felharmonikusai beleszólnak. Cserébe minden paraméter
átlátható és valós időben hangolható, és semmi nem fut a gépeden kívül.

## Licenc

MIT
