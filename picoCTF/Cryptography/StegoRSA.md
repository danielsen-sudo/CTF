# StegoRSA

* av Tor Danielsen

## Innhold

- [Kommandoer i bruk](#kommandoer-i-bruk)
- [Oppgave](#oppgave)
- [Utførelse](#utførelse)
  - [OpenSSL](#openssl)
  - [Fra HEX til privatnøkkel](#fra-hex-til-privatnøkkel)
  - [Dekrypter flagget](#dekrypter-flagget)

## Kommandoer i bruk

| Kommando | Bruk |
| --- | --- |
| `file *` | Viser filtypene og metadata som `file` finner i mappen |
| `exiftool -b -Comment image.jpg` | Henter kommentar-feltet fra bildet |
| `xxd -r -p` | Gjør HEX-teksten om til innholdet i privatnøkkelen |
| `head -n 1 private.pem` | Viser første linje i nøkkelfilen |
| `openssl pkey -in private.pem -noout` | Kontrollerer at OpenSSL kan lese nøkkelen |
| `openssl pkeyutl -decrypt -inkey private.pem -in flag.enc -out flag.txt` | Dekrypterer filen med privatnøkkelen |
| `ls -la` | Viser filene i mappen, også størrelse og detaljer |
| `cat flag.txt` | Viser innholdet i den dekrypterte filen |

## Oppgave

```text
StegoRSA - Cryptography - Easy - by Yahaya Meddy - picoCTF 2026
A message has been encrypted using RSA. The public key is gone… but someone might have been careless with the private key. Can you recover it and decrypt the message?
Download the flag  and image
Hints:
  Metadata can tell you more than you expect.
  Hex can be turned back into a key file.
```

Vi får en fil som heter `flag.eng` og ett bilde `image.png`

## Utførelse

Etter filene er lastet ned, åpner jeg lokasjonen til filene i terminalvinduet og kjører kommandoen:

```bash
➜  CTF file *
flag.enc:  data
image.jpg: JPEG image data, JFIF standard 1.01, aspect ratio, density 1x1, segment length 16, comment: "2d2d2d2d2d424547494e2050524956415445204b45592d2d2d2d2d0a4d494945766749424144414e42676b71686b6947397730424151454641415343424b67", baseline, precision 8, 512x512, components 3
➜  CTF 
```

Etter hva jeg vet av hintene så er `2d2d2d2d2d424547494e2050524956415445204b45592d2d2d2d2d0a4d494945766749424144414e42676b71686b6947397730424151454641415343424b67` RSA nøkkelen som vi et på utkikk etter for dekryptering og her stopper min kunskap på denne oppgaven, men AI hjelper meg til løsningen meget enkelt. 

Det vi trenger er å fylle ut en kommando som heter `openssl` og så har vi dekryptert RSA oppgaven. 
Her er en forklaring over `OpenSSL`. 

### OpenSSL

`openssl` er et terminalprogram for blant annet kryptering, dekryptering og håndtering av nøkler. I denne oppgaven bruker jeg det til å dekryptere `flag.enc` med RSA-privatnøkkelen jeg hentet fra bildet.

```bash
openssl pkeyutl -decrypt -inkey private.pem -in flag.enc -out flag.txt
```

| Del | Betydning |
| --- | --- |
| `openssl` | Starter OpenSSL |
| `pkeyutl` | Utfører en operasjon med en offentlig eller privat nøkkel |
| `-decrypt` | Dekrypterer innholdet |
| `-inkey private.pem` | Bruker privatnøkkelen i `private.pem` |
| `-in flag.enc` | Leser den krypterte filen |
| `-out flag.txt` | Skriver resultatet til `flag.txt` |

### Fra HEX til privatnøkkel

Så det er løsningen, nå er det neste å trekke ut HEX nøkkelen.

For å finne ut av det så bruker jeg følgende script som AI gav meg, med `exiftool` 

```bash
exiftool -b -Comment image.jpg | xxd -r -p > private.pem
head -n 1 private.pem
openssl pkey -in private.pem -noout
```

`exiftool` er ett program som lester METADATA fra ett bilde og emd denne kommandoen så henter vi ut HEX nøkkelen og legger den inn i filen `private.pem`. 

| Del | Forklaring |
| --- | --- |
| `exiftool -b -Comment image.jpg` | Henter kommentaren som er skjult i bildets metadata. `-b` skriver ut selve innholdet. |
| `\|` | Sender kommentaren videre til neste kommando. |
| `xxd -r -p` | Gjør HEX-teksten om til vanlige bytes. `-r` betyr *reverse*, og `-p` betyr at input er ren HEX. |
| `> private.pem` | Lagrer resultatet som nøkkelfilen `private.pem`. |
| `head -n 1 private.pem` | Viser første linje, slik at du kan sjekke om den starter med `-----BEGIN PRIVATE KEY-----`. |
| `openssl pkey -in private.pem -noout` | Prøver å lese privatnøkkelen. `-noout` gjør at selve nøkkelen ikke skrives ut. Ingen feilmelding betyr at OpenSSL kunne lese den. |

**Kort fortalt:** 

Bildets kommentar inneholder en privatnøkkel skrevet som HEX. Den første kommandoen gjør HEX om til en nøkkelfil; de to neste sjekker filen.

Du får ingen "output" etter denne kommandoen, den ser du når du kjører `ls -la`.

```bash
➜  CTF exiftool -b -Comment image.jpg | xxd -r -p > private.pem              
head -n 1 private.pem
openssl pkey -in private.pem -noout
-----BEGIN PRIVATE KEY-----
➜  CTF ls -la
total 48
drwxrwxr-x 2 agz agz  4096 Sep 19 17:36 .
drwxr-xr-x 8 agz agz 12288 Sep 19 16:12 ..
-rw-rw-r-- 1 agz agz   256 Sep 19 16:12 flag.enc
-rw-rw-r-- 1 agz agz 20794 Sep 19 16:12 image.jpg
-rw-rw-r-- 1 agz agz  1704 Sep 19 17:36 private.pem
➜  CTF 
```

### Dekrypter flagget

Vi har fått filen `private.pem`. 
Neste steg er selve dekrypteringen med `openssl`.

Vi kjører de siste kommandoer i termnalvinduet. 

```bash
➜  CTF openssl pkeyutl -decrypt -inkey private.pem -in flag.enc -out flag.txt

➜  CTF ls -la
total 52
drwxrwxr-x 2 agz agz  4096 Sep 19 17:39 .
drwxr-xr-x 8 agz agz 12288 Sep 19 16:12 ..
-rw-rw-r-- 1 agz agz   256 Sep 19 16:12 flag.enc
-rw-rw-r-- 1 agz agz    33 Sep 19 17:39 flag.txt
-rw-rw-r-- 1 agz agz 20794 Sep 19 16:12 image.jpg
-rw-rw-r-- 1 agz agz  1704 Sep 19 17:36 private.pem
➜  CTF cat flag.txt 
picoCTF{RESULTAT}
➜  CTF 
```

Kommandoen `openssl` gav oss filen `flag.txt` og det er PicoCTF flagget.

Oppgave er løst, med god hjelp fra AI om verktøy og hvordan de skal snekres sammens.
