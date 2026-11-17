# FK PDF-viser – utvidelse for Trimble Connect 3D-viser

Viser en PDF som ligger innebygd som Base64 i egenskapen `FK_Vedlegg.Innhold`
på det valgte objektet. PDF-en vises i et sidepanel ved siden av modellen.
Alt skjer i nettleseren, og ingenting sendes til eksterne servere.

## Filer
| Fil | Innhold |
|---|---|
| `index.html` | Selve utvidelsen (HTML og JavaScript, ingen byggesteg) |
| `manifest.json` | Beskrivelsen Trimble Connect leser når utvidelsen legges til |
| `icon.png` | Ikon i menyen |

## 1. Publiser filene på en HTTPS-adresse
Alternativer: GitHub Pages, Azure Static Web Apps eller en intern webserver
som brukerne når. Manifest-fila må tillate CORS-oppslag fra Trimble Connect.
GitHub Pages og Azure gjør det som standard.

Med GitHub Pages:
1. Opprett et repo, for eksempel `tc-pdf-viser`, og last opp de tre filene.
2. Gå til Settings → Pages → Deploy from branch → `main` / root.
3. Adressen blir `https://<brukernavn>.github.io/tc-pdf-viser/`.

## 2. Oppdater manifest.json
Bytt `https://DIN-HOST/tc-pdf-viser/` med den faktiske adressen i `icon` og `url`.
Sjekk at `index.html`, `manifest.json` og `icon.png` kan åpnes direkte i nettleseren.

## 3. Legg utvidelsen til i Trimble Connect
Dette krever prosjektadministrator. Menyvalgene har endret navn opp gjennom tiden,
så de kan avvike litt fra det som står her:
- Åpne prosjektet i Trimble Connect for Browser → **3D-viser** → **Innstillinger** →
  **Extensions / Egendefinerte utvidelser**, eller **Settings → Extensions** for prosjektet.
- Lim inn URL-en til `manifest.json` og trykk Legg til / Add.
- Slå på utvidelsen. Den dukker opp som en egen fane eller et eget panel i 3D-viseren.

## 4. Test
1. Last opp `C_innebygd_base64_paa_dekker.ifc` til prosjektet og åpne den i 3D-viseren.
2. Åpne panelet **FK PDF-viser**.
3. Klikk på en plate (IfcSlab), for eksempel brudekket.
4. Panelet viser tegningen og en kontrollrad:
   - **Dekodet størrelse ✓ stemmer** og **SHA-256 ✓ identisk**: Trimble Connect har
     tatt vare på hele strengen, og løsningen fungerer.
   - **✗ avviker** eller **Dekoding feilet**: Trimble Connect kutter lange tekstverdier.
     Bruk da løsningen med lenke til en fil i prosjektmappa i stedet.
5. Står det at objektet ikke har `FK_Vedlegg.Innhold`, trykk **Vis egenskaper
   (feilsøking)** og send meg teksten som vises. Da ser vi hvordan Connect navngir
   egenskapssettet.

## Justering
Øverst i skriptet i `index.html`:
```js
const PSET_NAVN = "FK_Vedlegg";
const PROP_INNHOLD = "Innhold";
```
Finner ikke utvidelsen disse navnene, leter den automatisk etter en hvilken som helst
egenskap som starter med `JVBERi0` (Base64 for `%PDF-`).
