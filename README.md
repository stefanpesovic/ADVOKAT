# Advokat đavola

> Misliš da ti je ideja super? Hajde da je izrešetamo.

Aplikacija iz serije **Trosku 30-day challenge** — svaki dan jedna aplikacija, živi 24 sata na Netlify-u, pa se briše.

Biraš tip biznisa, opišeš ideju, i prolaziš kroz razgovor u kom te advokat đavola namerno napada pitanjima koja bi ti postavio investitor — ili neko ko je taj biznis već pokušao. Na kraju dobiješ izveštaj: gde ti je ideja slaba, šta nisi promislio, i šta da uradiš pre nego što uložiš prvi dinar.

## Ovo NIJE AI

Razgovor je **stablo pitanja hardkodovano u JavaScript-u**. Nema API poziva, nema servera, nema baze, nema analytics-a. Pitanja koja investitor postavlja su ista već pedeset godina — stablo od stotinak pitanja pokriva skoro svaki slučaj.

Aplikacija nikad ne kaže da je ideja dobra ili loša. Kaže šta nije promišljeno.

## Pokretanje

Jedan fajl, nula zavisnosti (osim Google Fonts). Radi offline posle učitavanja.

```bash
# najbrže — otvori direktno
open index.html

# ili lokalni server
python3 -m http.server 8000
# → http://localhost:8000
```

## Deploy na Netlify

Prevuci folder na [app.netlify.com/drop](https://app.netlify.com/drop) — ili:

```bash
npx netlify-cli deploy --prod --dir .
```

## Struktura

Sve je u `index.html`:

| Deo | Gde |
|---|---|
| Stablo pitanja (CONFIG) | vrh `<script>` bloka, obeleženo **„MENJAJ SAMO OVDE"** |
| Logika razgovora i izveštaja | ispod CONFIG bloka |
| CSS | `<style>` u `<head>` |

### Stabla

Četiri putanje, svaka sa svojim stablom:

- **Ugostiteljstvo** — 17 čvorova, putanja 15–17 pitanja
- **Usluge** — 15 čvorova, putanja 14–15 pitanja
- **Online** — 15 čvorova, putanja 14–15 pitanja
- **Nešto drugo** — opšte stablo, 10 pitanja

Svako stablo pokriva 8 tema: novac, iskustvo, kupac, brojevi, konkurencija, vreme, najgori scenario, izlaz.

### Struktura čvora

```js
{
  id: "u7",
  tema: "brojevi",          // jedna od 8 tema
  pitanje: "Koliko kafa dnevno moraš da prodaš...?",
  ostro: true,              // opciono: vibracija kad pitanje stigne
  tip: "broj",              // opciono: polje za unos broja (traži odgovorBroj)
  rupa: {                   // aktivira se kad odgovor nosi 0 poena
    nivo: "crveno",         // crveno | narandzasto | sivo
    naslov: "Ne znaš break-even",
    zasto: "Zašto je to problem...",
    zadatak: "Konkretan zadatak za ovu nedelju..."
  },
  odgovori: [
    { tekst: "Znam tačno", reakcija: "...", sledeci: "u8", poeni: 1 },
    { tekst: "Okvirno",    reakcija: "...", sledeci: "u8", poeni: 0.5 },
    { tekst: "Nisam računao", reakcija: "...", sledeci: "u8", poeni: 0 }
  ]
}
```

- `poeni: 1` — ima odgovor · `0.5` — okvirno (pravi sivu rupu) · `0` — nema (pravi rupu iz `rupa`)
- `sledeci: null` — kraj razgovora
- Preskočeno pitanje nosi 0 poena i ide u posebnu sekciju izveštaja

### Automatska provera stabla

Pri svakom učitavanju, `proveriStabla()` prolazi kroz sve grane i javlja u konzolu ako:

- neki `sledeci` pokazuje na nepostojeći id
- neki čvor nema izlaz (ćorsokak)
- postoji ciklus
- grana ne stiže do kraja

Ako menjaš stablo, otvori konzolu — čista konzola znači validno stablo.

## Ocena spremnosti

Meri **koliko si promislio, ne da li će ideja uspeti** — i to piše u izveštaju.

```
ocena = osvojeni poeni / broj postavljenih pitanja × 100
```

| Ocena | Znači |
|---|---|
| 0–30 | Ideja je još u glavi. To je u redu, ali nemoj da ulažeš. |
| 31–55 | Imaš ideju, nemaš plan. |
| 56–75 | Promislio si glavno. Ostalo je nekoliko rupa. |
| 76–100 | Spreman si da pričaš sa nekim ko daje pare. |

## Privatnost

- Opis ideje ostaje na uređaju i **ne analizira se** — prikazuje se samo tebi, u izveštaju
- Slika za deljenje (1080×1350) sadrži ocenu, broj rupa i tip biznisa — **bez opisa ideje i bez iznosa**
- Poslednji izveštaj se čuva u `localStorage`
- Bez cookie banera, analytics-a, praćenja

## Tehnički okvir

- Jedan `index.html`, sav CSS i JS inline — ~85 KB (limit 250 KB)
- Bez `innerHTML` — samo sigurni DOM API-ji
- Mobilni prvo: 390×844, dugmad min. 52px, safe-area insets, `100dvh` sa fallbackom
- Animacije: samo `transform` i `opacity`; `prefers-reduced-motion` gasi sve osim opacity
- PDF preko print stylesheet-a (svetla verzija), slika preko `navigator.share` sa download fallbackom
- Napomena: `navigator.share` traži HTTPS — lokalno pada na download, na Netlify-u radi pun share

## Trosku

Deo serije **30 dana, 30 aplikacija**. Ova živi do ponoći — countdown je gore desno.
# ADVOKAT
