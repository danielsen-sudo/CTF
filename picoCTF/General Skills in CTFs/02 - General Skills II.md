# General Skills in CTF II

*av Tor Danielsen*

## Innhold
- [Kommandoer i bruk](#kommandoer-i-bruk)
- [Problem set 1](#problem-set-1)
  - [Nice netcat...](#nice-netcat)
  - [Tab, Tab, Attack](#tab-tab-attack)
  - [Python Wrangling](#python-wrangling)
  - [Magikarp Ground Mission](#magikarp-ground-mission)
- [Problem set 2](#problem-set-2)
  - [First Grep](#first-grep)
  - [First Find](#first-find)
  - [Big Zip](#big-zip)
- [Problem set 3](#problem-set-3)
  - [Static ain't always noise](#static-aint-always-noise)
  - [strings it](#strings-it)
  - [plumbing](#plumbing)
- [Slutten](#slutten)
  - [Hva har jeg lært](#hva-har-jeg-lært)

## Kommandoer i bruk

| Kommando             | Bruk                                        |
| -------------------- | ------------------------------------------- |
| `nc`                 | Kobler til en adresse og port med Netcat    |
| `python3 -c`         | Kjører Python-kode direkte fra terminalen   |
| `tree`               | Viser mapper og filer som trestruktur       |
| `cd`                 | Bytter katalog                              |
| `ls`                 | Viser innholdet i en katalog                |
| `cat`                | Skriver innholdet i en fil til terminalen   |
| `./filnavn`          | Kjører en kjørbar fil                       |
| `python3 ende.py -d` | Kjører Python-scriptet i dekrypteringsmodus |
| `ssh`                | Kobler til en ekstern maskin via SSH        |
| `sshpass`            | Sender passord automatisk til SSH            |
| `file`               | Identifiserer filtype                        |
| `ls -la`             | Viser alle filer med detaljert informasjon   |
| `grep`               | Søker etter tekst eller mønstre              |
| `find`               | Søker etter filer og mapper                   |
| `strings`            | Henter ut lesbare tekststrenger fra en fil   |
| `tee`                | Viser output og lagrer den samtidig til fil   |
| `exit`               | Avslutter gjeldende shell eller SSH-tilkobling |


## Problem set 1

### Nice netcat...

**Oppgave**

```text
There is a nice program that you can talk to by using this command in a shell:
nc wily-courier.picoctf.net 57392, but it doesn't speak English...
```

**Løsning**

Vi må logge på Netcat på følgende adresse og port.
Den er kun aktiv 30 minutter av gangen. 

```bash
nc wily-courier.picoctf.net 57392
```

Det som kom ut var følgende: 
```text
112 
105 
99 
111 
67 
84 
70 
123 
103 
48 
48 
100 
95 
107 
49 
116 
116 
121 
33 
95 
110 
49 
99 
51 
95 
107 
49 
116 
116 
121 
33 
95 
100 
57 
52 
55 
54 
125 
10 
```

Dette er ASCII-numre som ikke er konvertert til tekst.

**Resultat**

ChatGPT gir følgende kode i Python som vi kan kjøre i terminalvinduet.

```bash
python3 -c 'print("".join(map(chr,[112,105,99,111,67,84,70,123,103,48,48,100,95,107,49,116,116,121,33,95,110,49,99,51,95,107,49,116,116,121,33,95,100,57,52,55,54,125,10])))'
```

Da vil teksten komme fram som følgende: 
```text
picoCTF{resultat}
```

#### Hvordan kommandoen fungerer

```bash
python3 -c 'print("".join(map(chr,[112,105,99,111,67,84,70,123])))'
```

Resultat:

```text
picoCTF{
```

Kommandoen kan leses **innenfra og utover**:

```text
tall → chr → tegn → join → tekst → print → terminal
```

| Del               | Betydning                                               |
| ----------------- | ------------------------------------------------------- |
| `python3`         | Starter Python 3                                        |
| `-c`              | Kjører Python-koden som står rett etter                 |
| `chr()`           | Konverterer et tall til tilsvarende ASCII-/Unicode-tegn |
| `map(chr, [...])` | Kjører `chr()` på hvert tall i listen                   |
| `"".join(...)`    | Setter alle tegnene sammen til én tekststreng           |
| `print(...)`      | Skriver resultatet til terminalen                       |


### Tab, Tab, Attack

**Oppgave**

```text
Using tabcomplete in the Terminal will add years to your life, esp. when dealing with long rambling directory structures and filenames.
```

Vi skal laste ned filen `Addadshashanammu.zip`.
Når vi pakker den ut, får vi en heftig mappestruktur.
Denne oppgaven går nok ut på å søke i masse mapper og finne resultatet.
For å få oversikt brukte jeg `tree`, som jeg måtte installere via `apt`.

```bash
tree
```

Resultatet er følgende:

```text
➜  CTF tree
.
├── Addadshashanammu
│   └── Almurbalarammi
│       └── Ashalmimilkala
│           └── Assurnabitashpi
│               └── Maelkashishi
│                   └── Onnissiralis
│                       └── Ularradallaku
│                           ├── fang-of-haynekhtnamet
│                           └── fang-of-haynekhtnamet.c
└── Addadshashanammu.zip

8 directories, 3 files
➜  CTF 
```

Vi går til den katalogen:

```bash
➜  CTF cd Addadshashanammu/Almurbalarammi/Ashalmimilkala/Assurnabitashpi/Maelkashishi/Onnissiralis/Ularradallaku 
➜  Ularradallaku ls
fang-of-haynekhtnamet  fang-of-haynekhtnamet.c
➜  Ularradallaku 
```

**Løsning**

Når vi er i denne katalogen, har vi to valg.

1. Kjør filen 
2. Bruk `cat` på `.c`-filen

```bash
./fang-of-haynekhtnamet
```

```bash
cat fang-of-haynekhtnamet.c
```

**Resultat**

Med begge kommandoene vil du se i klartekst:

```text
picoCTF{resultat}
```

#### Hvordan `tree` fungerer

Viser mapper og filer som en trestruktur.

```bash
tree
```

Eksempel:

```text
.
├── CTF
│   ├── picoCTF
│   └── README.md
└── terminalkommandoer.md
```

Nyttige varianter:

```bash
tree -L 2
```

Viser bare 2 nivåer ned.

```bash
tree -a
```

Viser også skjulte filer og mapper.

**Poenget...**
Poenget med oppgaven er egentlig å bruke `Tab` for automatisk fullføring av de lange mappe- og filnavnene. Jeg brukte også `tree` for å få oversikt over hele strukturen.

ops... :D

### Python Wrangling

**Oppgave**

```text
Python scripts are invoked kind of like programs in the Terminal...
Can you run `ende.py` using `password.txt` to get `flag.txt.en` ?
```

Nå har vi følgende tre filer:

`ende.py`

```python
import sys
import base64
from cryptography.fernet import Fernet

usage_msg = "Usage: "+ sys.argv[0] +" (-e/-d) [file]"
help_msg = usage_msg + "\n" +\
        "Examples:\n" +\
        "  To decrypt a file named 'pole.txt', do: " +\
        "'$ python "+ sys.argv[0] +" -d pole.txt'\n"
        
if len(sys.argv) < 2 or len(sys.argv) > 4:
    print(usage_msg)
    sys.exit(1)
if sys.argv[1] == "-e":
    if len(sys.argv) < 4:
        sim_sala_bim = input("Please enter the password:")
    else:
        sim_sala_bim = sys.argv[3]

    ssb_b64 = base64.b64encode(sim_sala_bim.encode())
    c = Fernet(ssb_b64)
    with open(sys.argv[2], "rb") as f:
        data = f.read()
        data_c = c.encrypt(data)
        sys.stdout.write(data_c.decode())
elif sys.argv[1] == "-d":
    if len(sys.argv) < 4:
        sim_sala_bim = input("Please enter the password:")
    else:
        sim_sala_bim = sys.argv[3]
    ssb_b64 = base64.b64encode(sim_sala_bim.encode())
    c = Fernet(ssb_b64)

    with open(sys.argv[2], "r") as f:
        data = f.read()
        data_c = c.decrypt(data.encode())
        sys.stdout.buffer.write(data_c)
elif sys.argv[1] == "-h" or sys.argv[1] == "--help":
    print(help_msg)
    sys.exit(1)
else:
    print("Unrecognized first argument: "+ sys.argv[1])
    print("Please use '-e', '-d', or '-h'.")
```
`password.txt`

```text
720b6ad346f84cd483c60c7464dd95d4
```

`flag.txt.en`

```text
gAAAAABpRaHLJvQHNKx7S5bkBbCbLRygnKBNN2x32PTowWwOk2iIsCAgGdGgp_g-lIbghg4z6VSdljq5-moyXGu-5aQcrz5iaUEjHJWDAvd2xSZCeNVfUSJoUfj_wuZyjP3gQB5LdglQ
```

Nå begynner det å bli morsomt.
Vi jobber med kryptering, og filen heter `ende`. Encrypt/Decrypt regner jeg med at det står for.
Vi har også følgende kode i Python-scriptet:

Vi bruker [Fernet](https://cryptography.io/en/latest/fernet/) fra Python-biblioteket `cryptography` for kryptering og dekryptering.

```python
usage_msg = "Usage: "+ sys.argv[0] +" (-e/-d) [file]"
help_msg = usage_msg + "\n" +\
        "Examples:\n" +\
        "  To decrypt a file named 'pole.txt', do: " +\
        "'$ python "+ sys.argv[0] +" -d pole.txt'\n"
```

Med disse valgene antar jeg at vi kan kryptere og dekryptere.
Etter litt leking med `python3` i terminalvinduet fungerer denne:

**Løsning**

```bash
python3 ende.py -d flag.txt.en 720b6ad346f84cd483c60c7464dd95d4
```
Da kjører vi `ende.py` med `-d` for dekryptering, sammen med den krypterte fila og passordet.

| Del           | Betydning                       |
| ------------- | ------------------------------- |
| `python3`     | Starter Python 3                |
| `ende.py`     | Python-scriptet som skal kjøres |
| `-d`          | Dekrypter                       |
| `flag.txt.en` | Fila som skal dekrypteres       |
| `720b6ad...`  | Passordet                       |

Resultatet er gode gamle

```text
picoCTF{resultat}
```

### Magikarp Ground Mission

**Oppgave**

```text
Do you know how to move between directories and read files in the shell? Start the container, ssh to it, and then ls once connected to begin.

Login via ssh as ctf-player with the password, 8c606eb1 on the host wily-courier.picoctf.net and port 53401.
```

Nå er vi inne på `ssh`, så vi kjører kommandoen i terminalvinduet.
- brukernavn er `ctf-player`
- passord er `8c606eb1`
- host er `wily-courier.picoctf.net`
- port er `53401`

Siden jeg er lat, liker jeg `sshpass`, som du kan laste ned via `apt`.

```bash
sshpass -p '8c606eb1' ssh -p 53401 ctf-player@wily-courier.picoctf.net
```

Funket ikke... 
Så det ble:

```bash
ssh -p 53401 ctf-player@wily-courier.picoctf.net
```

| Del                        | Betydning                                              |
| -------------------------- | ------------------------------------------------------ |
| `ssh`                      | Starter en SSH-tilkobling til en ekstern maskin        |
| `-p 53401`                 | Kobler til port `53401` i stedet for standardport `22` |
| `ctf-player`               | Brukernavnet på den eksterne maskinen                  |
| `@`                        | Skiller brukernavn fra adressen til serveren           |
| `wily-courier.picoctf.net` | Adressen til SSH-serveren                              |


```text
➜  CTF ssh -p 53401 ctf-player@wily-courier.picoctf.net   
The authenticity of host '[wily-courier.picoctf.net]:53401 ([18.189.99.27]:53401)' can't be established.
ED25519 key fingerprint is SHA256:ErlUUvYlrAxfSW1tIdzfOnGTBSr5OFkZvz0nMN4Vodw.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? `yes`
Warning: Permanently added '[wily-courier.picoctf.net]:53401' (ED25519) to the list of known hosts.
ctf-player@wily-courier.picoctf.net's password: 
Welcome to Ubuntu 18.04.6 LTS (GNU/Linux 7.0.0-1011-aws x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage
This system has been minimized by removing packages and content that are
not required on a system that users do not log into.

To restore this content, you can run the 'unminimize' command.

The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

ctf-player@pico-chall$ 
```
Vel, vi er inne. Måtte skrive inn `yes` og `passord` da det var påkrevd.
Jeg vil teste noe, så jeg tar `exit` og logger inn igjen.

```bash
exit
sshpass -p '8c606eb1' ssh -p 53401 ctf-player@wily-courier.picoctf.net
```

Heisann, det fungerte. 

Måtte ha en første tilkobling.

```bash 
ctf-player@pico-chall$ ls -la
total 8
drwxr-xr-x 1 ctf-player ctf-player 59 Sep 12  2025 .
drwxr-xr-x 1 ctf-player ctf-player 41 Sep  6 21:11 ..
-rw-r--r-- 1 ctf-player ctf-player 14 Aug 14  2025 1of3.flag.txt
-rw-r--r-- 1 ctf-player ctf-player 56 Aug 14  2025 instructions-to-2of3.txt
ctf-player@pico-chall$ file *
1of3.flag.txt:            ASCII text
instructions-to-2of3.txt: ASCII text
ctf-player@pico-chall$ cat *
picoCTF{RESULTAT
Next, go to the root of all things, more succinctly `/`
ctf-player@pico-chall$ cd /
ctf-player@pico-chall$ ls -la
total 12
drwxr-xr-x   1 root   root      40 Sep  6 21:05 .
drwxr-xr-x   1 root   root      40 Sep  6 21:05 ..
-rwxr-xr-x   1 root   root       0 Sep  6 21:05 .dockerenv
-rw-r--r--   1 root   root      15 Aug 14  2025 2of3.flag.txt
drwxr-xr-x   1 root   root    4096 Sep 12  2025 bin
drwxr-xr-x   2 root   root       6 Apr 24  2018 boot
drwxr-xr-x   2 root   root      27 Sep 12  2025 challenge
drwxr-xr-x   5 root   root     340 Sep  6 21:05 dev
drwxr-xr-x   1 root   root      66 Sep  6 21:05 etc
drwxr-xr-x   1 root   root      24 Sep 12  2025 home
-rw-r--r--   1 root   root      51 Aug 14  2025 instructions-to-3of3.txt
drwxr-xr-x   1 root   root      86 Sep 12  2025 lib
drwxr-xr-x   2 root   root      34 May 30  2023 lib64
drwxr-xr-x   2 root   root       6 May 30  2023 media
drwxr-xr-x   2 root   root       6 May 30  2023 mnt
drwxr-xr-x   1 root   root      22 Sep 12  2025 opt
dr-xr-xr-x 203 nobody nogroup    0 Sep  6 21:05 proc
drwx------   2 root   root      37 May 30  2023 root
drwxr-xr-x   1 root   root      66 Sep  6 21:11 run
drwxr-xr-x   1 root   root     158 Sep 12  2025 sbin
drwxr-xr-x   2 root   root       6 May 30  2023 srv
dr-xr-xr-x  13 nobody nogroup    0 Sep  6 21:05 sys
drwxrwxrwt   1 root   root       6 Sep 12  2025 tmp
drwxr-xr-x   1 root   root      66 May 30  2023 usr
drwxr-xr-x   1 root   root      17 May 30  2023 var
ctf-player@pico-chall$ cat 2of3.flag.txt 
RESULTAT
ctf-player@pico-chall$ cat instructions-to-3of3.txt 
Lastly, ctf-player, go home... more succinctly `~`
ctf-player@pico-chall$ 
ctf-player@pico-chall$ cd /home/
ctf-player@pico-chall$ ls
ctf-player
ctf-player@pico-chall$ cd ctf-player/
ctf-player@pico-chall$ ls
3of3.flag.txt  drop-in
ctf-player@pico-chall$ cat 3of3.flag.txt 
RESULTAT
ctf-player@pico-chall$ exit
logout
Connection to wily-courier.picoctf.net closed.
➜  CTF 
```

Da har vi funnet koden vår. Den var fordelt på tre forskjellige filer og lå i klartekst.
`cat` gjorde jobben sin, med bare litt `cd` hit og dit.

## Problem set 2

### First Grep

**Oppgave**

```text
Can you find the flag in the file? This would be really tedious to look through manually, something tells me there is a better way.
```

Vi skal laste ned en fil som heter `file`, og så kjøre følgende i terminalvinduet:

**Løsning**

```bash
➜  CTF file file
file: ASCII text, with very long lines (14545)
```

Vel, no shit.
På tide å teste ut `grep`.

```bash
grep -o 'picoCTF{[^}]*}' file
```

#### Hvordan kommandoen fungerer

| Del                | Betydning                                         |
| ------------------ | ------------------------------------------------- |
| `grep`             | Søker etter tekst eller mønstre i en fil          |
| `-o`               | Skriver bare ut delen av linjen som matcher søket |
| `'picoCTF{[^}]*}'` | Søkemønsteret                                     |
| `file`             | Filen det søkes i                                 |

#### Søkemønsteret

```text
picoCTF{[^}]*}
```

kan leses slik:

| Del        | Betydning                               |
| ---------- | --------------------------------------- |
| `picoCTF{` | Søker etter teksten `picoCTF{`          |
| `[^}]`     | Matcher alle tegn bortsett fra `}`      |
| `*`        | Matcher null eller flere av tegnene     |
| `}`        | Stopper når avsluttende `}` blir funnet |

Kommandoen henter dermed ut hele flagget:

```text
picoCTF{RESULTAT}
```

Og vipps, så har vi løsningen i terminalen. 

### First Find

**Oppgave**

```text
Unzip this archive and find the file named 'uber-secret.txt'
```
Vi skal laste ned filen `files.zip`, pakke den ut og så finne en fil som heter `uber-secret.txt` med kommandoen `find`.
Når filen er lastet ned, pakk den ut.

**Løsning**

`find` fungerer på følgende måte:

#### `find`

`find` brukes til å **søke etter filer og mapper** i terminalen.

Eksempel:

```bash
find . -name "flag.txt"
```

Så i terminalen kjører vi følgende kommando:

```bash
find . -name "uber-secret.txt"
```

Du vil så se at filen er funnet på følgende lokasjon og så kjører du `cat` og får se flagget.

```bash
CTF find . -name "uber-secret.txt"
./files/adequate_books/more_books/.secret/deeper_secrets/deepest_secrets/uber-secret.txt
➜  CTF cat files/adequate_books/more_books/.secret/deeper_secrets/deepest_secrets/uber-secret.txt 
picoCTF{RESULTAT}
➜  CTF 
```

### Big Zip


**Oppgave**

```text
Unzip this archive and find the flag.
```

Vi skal laste ned filen `big-zip-files.zip`, pakke den ut og så finne flagget.
Det er så mange filer og mapper at `tree` blir ubrukelig i dette sammenhenget. 
Vi får sjekke om `find` og `grep` kan jobbe sammen.
Siden vi vet at `picoCTF` finnes i absolutt alle flagg, kan vi bruke `find` etter den linja og bruke `grep` til å hente den ut.

**Løsning**

Kommandoen med `find` og `grep` blir følgende:

```bash
find . -type f -exec grep -H 'picoCTF' {} \;
```

| Del                 | Betydning                                |
| ------------------- | ---------------------------------------- |
| `find .`            | Søker fra mappen du står i               |
| `-type f`           | Søker kun etter filer                    |
| `-exec`             | Kjører en kommando på hvert resultat     |
| `grep -H 'picoCTF'` | Søker etter `picoCTF` og viser filnavnet |
| `{}`                | Erstattes med filen `find` har funnet    |
| `\;`                | Avslutter `-exec`-kommandoen             |


Dette tok litt tid, for det var mye å søke gjennom, men resultatet kom.

**Resultat**

```text
resultat➜  CTF find . -type f -exec grep -H 'picoCTF' {} \;
./big-zip-files/folder_pmbymkjcya/folder_cawigcwvgv/folder_ltdayfmktr/folder_fnpfclfyee/whzxrpivpqld.txt:information on the record will last a billion years. Genes and brains and books encode picoCTF{RESULTAT}
➜  CTF 
```

## Problem set 3

### Static ain't always noise

**Oppgave**

```text
Can you look at the data in this binary? The bash script might help!
```

Nå skal vi laste ned filene `static` og `ltdis.sh`.

**Løsning**

Vi kjører `cat` på `ltdis.sh` og får følgende output:

```bash
➜  CTF cat ltdis.sh 
#!/bin/bash

echo "Attempting disassembly of $1 ..."

#This usage of "objdump" disassembles all (-D) of the first file given by 
#invoker, but only prints out the ".text" section (-j .text) (only section
#that matters in almost any compiled program...
objdump -Dj .text $1 > $1.ltdis.x86_64.txt

#Check that $1.ltdis.x86_64.txt is non-empty
#Continue if it is, otherwise print error and eject
if [ -s "$1.ltdis.x86_64.txt" ]
then
	echo "Disassembly successful! Available at: $1.ltdis.x86_64.txt"

	echo "Ripping strings from binary with file offsets..."
	strings -a -t x $1 > $1.ltdis.strings.txt
	echo "Any strings found in $1 have been written to $1.ltdis.strings.txt with file offset"

else
	echo "Disassembly failed!"
	echo "Usage: ltdis.sh <program-file>"
	echo "Bye!"
fi
➜  CTF 
```

Vi kjører dette scriptet opp mot `static`-filen og ser hva `1.ltdis.x86_64.txt` har å by på.

```bash
➜  CTF ./ltdis.sh static
Attempting disassembly of static ...
Disassembly successful! Available at: static.ltdis.x86_64.txt
Ripping strings from binary with file offsets...
Any strings found in static have been written to static.ltdis.strings.txt with file offset
➜  CTF 
```

Med kommandoen `ls` ser vi to nye filer som heter `static.ltdis.strings.txt` og `static.ltdis.x86_64.txt`.
Nå er jeg lat og skal bruke tidligere "kunnskaper" med `find` og `grep` etter `picoCTF`, og muligens er vi heldige.

**Løsning**

```bash
find . -type f \( -name "static.ltdis.strings.txt" -o -name "static.ltdis.x86_64.txt" \) -exec grep "picoCTF" {} +
```

| Del              | Betydning                                    |
| ---------------- | -------------------------------------------- |
| `find .`         | Søker fra gjeldende mappe                    |
| `-type f`        | Søker kun etter filer                        |
| `-name`          | Søker etter et bestemt filnavn               |
| `-o`             | Betyr **eller**                              |
| `-exec`          | Kjører en kommando på filene som blir funnet |
| `grep "picoCTF"` | Søker etter teksten `picoCTF`                |
| `{}`             | Representerer filene som `find` finner       |
| `+`              | Sender de funne filene samlet til `grep`     |

Resultatet kom:

```bash
➜  CTF find . -type f \( -name "static.ltdis.strings.txt" -o -name "static.ltdis.x86_64.txt" \) -exec grep "picoCTF" {} +
./static.ltdis.strings.txt:   3020 picoCTF{RESULTAT}
➜  CTF 
```

Løsningen var i filen `static.ltdis.strings.txt`.

### strings it

**Oppgave**

```text
Can you find the flag in file  without running it?
```

Vi skal laste ned filen/programmet `strings` og finne flagget uten å kjøre programmet. 

**Løsning**

Vi kjører `strings` sammen med `grep`, som søker etter `picoCTF`.

```bash
strings fil | grep "picoCTF"
```

| Del                 | Betydning                                  |
| ------------------- | ------------------------------------------ |
| `strings fil`       | Leser ut lesbare tekststrenger fra filen   |
| `\|`                | Sender resultatet videre til neste kommando |
| `grep "picoCTF"`    | Viser bare linjer som inneholder `picoCTF` |

Kommandoen kan leses slik:

```text
fil → strings → lesbar tekst → grep → linjer med "picoCTF"
```

### plumbing

**Oppgave**

```text
Sometimes you need to handle process data outside of a file. Can you find a way to keep the output from this program and search for the flag?
Connect to fickle-tempest.picoctf.net 59463.
```

Vi må bruke `nc` for å koble til serveren, og vi får opp masse tekst som gjør det umulig å finne flagget.
`grep` er utelukket i dette sammenhenget, men det er mulig å få det logget til en egen fil.

```bash
nc HOST PORT | tee netcat.log
```

**Løsning**

Vi kjører følgende kommando.

```bash
nc fickle-tempest.picoctf.net 59463 | tee log
```

Bare avslutt Netcat når du er på slutten. Da har du en fil som heter `log`, som vi kjører følgende kommando på.

```bash
strings fil | grep "picoCTF"
```

Da har du resultatet og oppgaven er ferdig. 

## Slutten

Dette var siste utfordring i "General Skills in CTF's II".
Dette har vært morsomt med nye utfordringer igjen, og siden vi lærer oss å fly mens vi er i luften, går tiden fort.
Det som har vært mest spennende, er å legge inn flere kommandoer på én linje. Da har Google og AI vært flittig brukt.

### Hva har jeg lært

#### Magikarp Ground Mission
Første gang jeg kobler til en ny SSH-server kan `sshpass` stoppe fordi SSH først ber om godkjenning av serverens host key:

```bash
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

`sshpass` svarer på passordprompten, men ikke automatisk på denne bekreftelsen.

Etter at serverens host key er lagret i `~/.ssh/known_hosts`, fungerer `sshpass` som normalt.

Det er også mulig å godta nye host keys automatisk:

```bash
sshpass -p '8c606eb1' ssh -o StrictHostKeyChecking=accept-new -p 53401 ctf-player@wily-courier.picoctf.net
```

| Del                                   | Betydning                                                               |
| ------------------------------------- | ----------------------------------------------------------------------- |
| `sshpass`                             | Sender passordet automatisk til `ssh`                                   |
| `-p '8c606eb1'`                       | Angir passordet som `sshpass` skal bruke                                |
| `ssh`                                 | Starter SSH-tilkoblingen                                                |
| `-o StrictHostKeyChecking=accept-new` | Godtar automatisk nye SSH host keys                                     |
| `-p 53401`                            | Bruker port `53401` i stedet for standardport `22`                      |
| `ctf-player@wily-courier.picoctf.net` | Kobler til brukeren `ctf-player` på serveren `wily-courier.picoctf.net` |

#### First Grep

```bash
grep -o 'picoCTF{[^}]*}' file
```

kan leses slik:

| Del        | Betydning                               |
| ---------- | --------------------------------------- |
| `picoCTF{` | Søker etter teksten `picoCTF{`          |
| `[^}]`     | Matcher alle tegn bortsett fra `}`      |
| `*`        | Matcher null eller flere av tegnene     |
| `}`        | Stopper når avsluttende `}` blir funnet |

Veldig morsomt å lære slike måter å søke på. Dette må testes på andre ting i framtiden.

#### Big Zip

Det å bruke flere kommandoer på én linje er uvant. `find` og `grep` er som hånd i en perfekt hanske, men syntaksen kan være vanskelig å lære seg først.
Da er det greit med Google eller AI for å få litt hjelp.
Dersom vi vet hvilket format et flagg skal ha, f.eks. `picoCTF`, vil dette være en "juksekode".

```bash
find . -type f -exec grep -Ho 'picoCTF{[^}]*}' {} \;
```

Da er vi ferdig med "General Skills in CTF's"

EOF
