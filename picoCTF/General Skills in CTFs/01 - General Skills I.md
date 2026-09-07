# General Skills in CTF

*av Tor Danielsen*

## Innhold
- [Kommandoer i bruk](#kommandoer-i-bruk)
- [Problem set 1](#problem-set-1)
  - [Lets Warm Up](#lets-warm-up)
  - [2warm](#2warm)
  - [Warmed Up](#warmed-up)
- [Problem set 2](#problem-set-2)
  - [Obedient Cat](#obedient-cat)
  - [Wave a flag](#wave-a-flag)
  - [convertme.py](#convertmepy)
  - [what's a net cat?](#whats-a-net-cat)
- [Slutten](#slutten)

## Kommandoer i bruk

| Kommando | Bruk |
|---|---|
| `xxd -r -p` | Konverter HEX til ASCII |
| `bc` | Konverter desimal til binær |
| `printf "%d"` | Konverter HEX til desimal |
| `cat` | Vis innholdet i en fil |
| `strings` | Finn lesbar tekst i en binærfil |
| `nc` | Koble til en nettverkstjeneste |

## Problem set 1

### Lets Warm Up

**Oppgave**

```text
If I told you a word started with 0x70 in hexadecimal, what would it start with in ASCII?
```

**Løsning**

I terminalen kjører du følgende for å konvertere HEX til ASCII.

```bash
echo "70" | xxd -r -p
```

Dette er en kommando som fungerer med og uten mellomrom så følgende gir samme resultat.

```bash
echo "48 65 6c 6c 6f" | xxd -r -p
echo "48656c6c6f" | xxd -r -p
```

**Resultat**

```text
Hello
```
    
### 2warm

**Oppgave**

```text
Can you convert the number 42 (base 10) to binary (base 2)?
```

**Løsning**

I terminalen kjører vi følgende kommando:

```bash
echo "obase=2; 42" | bc
```

**Resultat**

```text
101010
```

### Warmed Up

**Oppgave**

```text
What is 0x3D (base 16) in decimal (base 10)?
```

**Løsning**

For å få hex til desimal i terminalvinduet bruker vi følgende kommando:

```bash
printf "%d\n" 0x3D
```

**Resultat**

```text
61
```

Dette er siste utfordring i oppvarmingen.

## Problem set 2

### Obedient Cat

**Oppgave**

```text
This file has a flag in plain sight (aka "in-the-clear").
```

**Løsning**

Last ned filen som heter `flag` og kjør følgende kommando:

```bash
cat flag
```

**Resultat**

```text
picoCTF{kode}
```

Koden er redigert bort. 

### Wave a flag

**Oppgave**

Last ned filen som heter `warm`. Dette er en 64-bit ELF-fil.

**Løsning**

Kjør:

```bash
strings warm
```

Når du scroller i resultatet vil du finne `picoCTF`, og der ligger flagget.

### convertme.py

**Oppgave**

Last ned `convertme.py`

Da vil du ha følgende Python-script:

```python
import random
def str_xor(secret, key):
    #extend key to secret length
    new_key = key
    i = 0
    while len(new_key) < len(secret):
        new_key = new_key + key[i]
        i = (i + 1) % len(key)        
    return "".join([chr(ord(secret_c) ^ ord(new_key_c)) for (secret_c,new_key_c) in zip(secret,new_key)])

flag_enc = chr(0x15) + chr(0x07) + chr(0x08) + chr(0x06) + chr(0x27) + chr(0x21) + chr(0x23) + chr(0x15) + chr(0x5f) + chr(0x05) + chr(0x08) + chr(0x2a) + chr(0x1c) + chr(0x5e) + chr(0x1e) + chr(0x1b) + chr(0x3b) + chr(0x17) + chr(0x51) + chr(0x5b) + chr(0x58) + chr(0x5c) + chr(0x3b) + chr(0x42) + chr(0x53) + chr(0x5c) + chr(0x0d) + chr(0x5e) + chr(0x50) + chr(0x4d) + chr(0x00) + chr(0x13)

num = random.choice(range(10,101))

print('If ' + str(num) + ' is in decimal base, what is it in binary base?')

ans = input('Answer: ')

try:
  ans_num = int(ans, base=2)
  
  if ans_num == num:
    flag = str_xor(flag_enc, 'enkidu')
    print('That is correct! Here\'s your flag: ' + flag)
  else:
    print(str(ans_num) + ' and ' + str(num) + ' are not equal.')
  
except ValueError:
  print('That isn\'t a binary number. Binary numbers contain only 1\'s and 0\'s')
```

**Hintene er følgende**
  
1. Look up a decimal to binary number conversion app on the web or use your computer's calculator!
2. The str_xor function does not need to be reverse engineered for this challenge.
3. If you have Python on your computer, you can download the script normally and run it. Otherwise, use the wget command in the webshell.
4. To use wget in the webshell, first right click on the download link and select 'Copy Link' or 'Copy Link Address'.
5. Type everything after the dollar sign in the webshell: `$ wget`, then paste the link after the space after wget and press enter. This will download the script for you in the webshell so you can run it!
6. Finally, to run the script, type everything after the dollar sign and then press enter: `$ python3 convertme.py`

**Løsning**

Først henter Python-scriptet et tilfeldig tall mellom 10 og 100:

```python
num = random.choice(range(10,101))
```

Det tallet må du konvertere til binær med følgende kommando i terminalen fra tidligere utfordring.

```bash
echo "obase=2; 33" | bc
```
Hvor `33` er tallet som kommer opp.
Da får du opp flagget ditt som resultat etterpå. 

**Alternativ løsning**

Du kan også modifisere Python-scriptet til følgende for å vise flagget. 

```python
flag_enc = [
    0x15,0x07,0x08,0x06,0x27,0x21,0x23,0x15,
    0x5f,0x05,0x08,0x2a,0x1c,0x5e,0x1e,0x1b,
    0x3b,0x17,0x51,0x5b,0x58,0x5c,0x3b,0x42,
    0x53,0x5c,0x0d,0x5e,0x50,0x4d,0x00,0x13
]

key = "enkidu"

flag = ''.join(
    chr(value ^ ord(key[i % len(key)]))
    for i, value in enumerate(flag_enc)
)

print(flag)
```

### what's a net cat?

**Oppgave**

```text
Using netcat (nc) is going to be pretty important. Can you connect to fickle-tempest.picoctf.net at port 63319 to get the flag?
```

**Løsning**

Her må vi bare bruke Netcat for å få flagget.

```bash
nc fickle-tempest.picoctf.net 63319
```

Så vil flagget stå i terminalen når du har koblet deg opp.

## Slutten
Dette var siste utfordring i "General Skills in CTF's 1"

Neste blir "General Skills in CTF's II".

EOF
