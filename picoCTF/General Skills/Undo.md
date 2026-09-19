# Undo

* av Tor Danielsen

## Innhold

- [Kommandoer brukt](#kommandoer-brukt)
- [Oppgave](#oppgave)
- [Utførelse](#utførelse)
  - [Steg 1 – Dekode Base64](#steg-1--dekode-base64)
  - [Steg 2 – Snu teksten](#steg-2--snu-teksten)
  - [Steg 3 – Gjenopprette understreker](#steg-3--gjenopprette-understreker)
  - [Steg 4 – Gjenopprette krøllparenteser](#steg-4--gjenopprette-krøllparenteser)
  - [Steg 5 – Dekode ROT13](#steg-5--dekode-rot13)
- [Hva har jeg lært](#hva-har-jeg-lært)
  - [`tr`](#tr)

## Kommandoer brukt

| Kommando | Forklaring |
|---|---|
| `nc foggy-cliff.picoctf.net 58707` | Kobler til oppgavens server med Netcat. |
| `base64 -d` | Dekoder Base64-kodet tekst. |
| `rev` | Snur rekkefølgen på tegnene i teksten. |
| `tr '-' '_'` | Erstatter bindestreker med understreker. |
| `tr '()' '{}'` | Erstatter parenteser med krøllparenteser. |
| `tr 'A-Za-z' 'N-ZA-Mn-za-m'` | Dekoder bokstavene med ROT13. |

## Oppgave

```text
Can you reverse a series of Linux text transformations to recover the original flag?

Start searching for the flag here nc foggy-cliff.picoctf.net 58707
```

## Utførelse

Vi logger oss inn på serveren i terminalen:

```bash
nc foggy-cliff.picoctf.net 58707
```

### Steg 1 – Dekode Base64

Da får vi opp denne meldingen:

```text
--- Step 1 ---
Current flag: KTgxMzkzOW4zLWZhMDFnQHplMHNmYTRlRy1nazNnLXRhMWZlcmlyRShTR1BicHZj
Hint: Base64 encoded the string.
Enter the Linux command to reverse it:
```

Svaret her er `base64 -d`.

**Forklaring av `base64`**

```bash
base64 -d
```

- `base64` starter programmet for koding og dekoding av Base64.
- `-d` betyr **decode** og dekoder teksten tilbake til originalt innhold.
- Teksten leses fra standard input, behandles og skrives ut i terminalen.

I denne oppgaven sendte picoCTF automatisk den kodede teksten til kommandoen.

Da er vi videre på neste utfordring:

### Steg 2 – Snu teksten

```text
--- Step 2 ---
Current flag: )813939n3-fa01g@ze0sfa4eG-gk3g-ta1ferirE(SGPbpvc
Hint: Reversed the text.
Enter the Linux command to reverse it:
```

Svaret her er `rev`, som sender oss videre til steg 3.

**Forklaring av `rev`**

```bash
rev
```

- `rev` er en forkortelse for **reverse**.
- Kommandoen snur rekkefølgen på tegnene i hver tekstlinje.
- Det siste tegnet blir først, og det første tegnet blir sist.

I denne oppgaven sendte picoCTF automatisk den baklengse teksten til kommandoen.

### Steg 3 – Gjenopprette understreker

```text
--- Step 3 ---
Current flag: cvpbPGS(Eriref1at-g3kg-Ge4afs0ez@g10af-3n939318)
Hint: Replaced underscores with dashes.
Enter the Linux command to reverse it:
```

Kommandoen er `tr '-' '_'`, og den sender oss videre til steg 4.

**Forklaring av `tr`**

```bash
tr '-' '_'
```

- `tr` er en forkortelse for **translate** og brukes til å erstatte tegn.
- `'-'` er tegnet som skal erstattes.
- `'_'` er tegnet bindestreken skal erstattes med.
- Alle bindestreker i teksten blir dermed gjort om til understreker.

I denne oppgaven sendte picoCTF automatisk teksten til kommandoen.

### Steg 4 – Gjenopprette krøllparenteser

```text
--- Step 4 ---
Current flag: cvpbPGS(Eriref1at_g3kg_Ge4afs0ez@g10af_3n939318)
Hint: Replaced curly braces with parentheses.
Enter the Linux command to reverse it:
```

Vi fortsetter med samme kommando nå som vi har lært oss litt om den `tr '()' '{}'` hvor vi nå er på Step 5:

**Forklaring `tr`**

```bash
tr '()' '{}'
```

- `tr` brukes til å erstatte tegn.
- `()` inneholder tegnene som skal erstattes.
- `{}` inneholder tegnene de skal erstattes med.
- `(` erstattes med `{`.
- `)` erstattes med `}`.

Kommandoen endrer dermed:

```text
picoCTF(flagg)
```

til:

```text
picoCTF{flagg}
```

### Steg 5 – Dekode ROT13

```text
--- Step 5 ---
Current flag: cvpbPGS{Eriref1at_g3kg_Ge4afs0ez@g10af_3n939318}
Hint: Applied ROT13 to letters.
Enter the Linux command to reverse it: 
```

Nå måtte AI inn i bildet, og vi endte opp med følgende kommando: `tr 'A-Za-z' 'N-ZA-Mn-za-m'`

**Forklaring av ROT13-dekoding med `tr`**

```bash
tr 'A-Za-z' 'N-ZA-Mn-za-m'
```

- `tr` brukes til å erstatte tegn.
- `'A-Za-z'` angir alle store og små bokstaver.
- `'N-ZA-Mn-za-m'` flytter hver bokstav 13 plasser i alfabetet.
- Store bokstaver forblir store, og små bokstaver forblir små.
- Tall, symboler og mellomrom blir ikke endret.

ROT13 bruker samme kommando til både koding og dekoding, fordi teksten blir tilbakeført når bokstavene flyttes 13 plasser én gang til.

Dette ga oss flagget:

```text
Congratulations! You've recovered the original flag:
>>> picoCTF{RESULTAT}
```

## Hva har jeg lært

Kommandoen `tr` er et kraftig verktøy for å manipulere tekststrenger.

### `tr`

`tr` er en forkortelse for **translate** og brukes til å erstatte eller fjerne tegn i tekst.

Grunnleggende syntaks:

```bash
tr 'TEGN_SOM_SKAL_ERSTATTES' 'NYE_TEGN'
```

Tegnene erstattes etter plasseringen sin i de to settene:

```text
Første tegn  → første tegn
Andre tegn   → andre tegn
Tredje tegn  → tredje tegn
```

Eksempel:

```bash
tr 'abc' '123'
```

Her utføres følgende erstatninger:

```text
a → 1
b → 2
c → 3
```

`tr` leser teksten fra standard input og skriver den endrede teksten til standard output. I denne oppgaven sender picoCTF automatisk teksten til kommandoen.
