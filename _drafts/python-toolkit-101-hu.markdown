---
layout: post
title: Python Toolkit 101
lang: hu
date: 2018-01-03 2:21:00 +0100
ref: python101
categories: python DevOps tox pip virtualenv
---
Python Toolkit 101
==================

Python eszköztárról üzemeltetőknek, avagy TL;DR;

pip
---

A pip mára mondhatni a de-facto standard csomagkezelő a python világban
 (habár nem az egyetlen), mely alapvető featurei (a teljesség igénye nélkül):
- install
- uninstall
- search (package index, azaz repóban)
- info (bővebb infó egy packageről)
- freeze (installált package-ek listája, verzióval!)
- batch install (via requirements.txt)

*(secret tip: -i kapcsolóval meg lehet adni saját package index url-t is, rtfm)*
pár példa:

### search

Egyszerű kulcsszavas kereséseket tesz lehetővé. A package index (vagy
 repository) amelyben alapértelmezetten keres a [pypi](https://pypi.org/) (nem összekeverendő
  a __pypy__-al!!!).
  
 Számok:
 
| Projects | Releases | Files     | Users |
|:--------:|:--------:|:---------:|:-----:|
| 121,695  | 823,883  | 1,074,125 |247,667|
 
Tehát nagyon sokminden van itt, érdemes körültekintően utánajárni egy
 packagenek telepítés előtt, hogy tényleg az-e amire gondolunk a neve alapján.
 Sőt! csupán a search-el kidob egy csomó mindent, lásd:
 
[![asciicast](https://asciinema.org/a/XTgQSDnIobIluzL56yyc0OjBM.png)](https://asciinema.org/a/XTgQSDnIobIluzL56yyc0OjBM)

Egyszóval van miből mazsolázni, érdemes csekkolni a package-eket CLI-on kívül a
 [pypi](https://pypi.org/) package index oldalán is, további leírás, repó,
 dokumentációért (python projektek dokumentációja általában 
 [readthedocs](https://readthedocs.org/)-on van hosztolva - erre az Ansible egy
 jó kivétel).
 
### install
Igen package installálására való, de az Advanced Package Toolkit-nél egy
 hangya****nyit kifinomultabb, mivel lehet neki egzakt verziószámot is megadni:
 
#### default installálja a legfrissebbet

#### == operátorral installál egy fix verziót és uninstallál bármilyen más verziót ha már van

#### < operátor

#### >= operátor (you get it...)

    Ha túl sok az operátor, akkor bele lehet zavarodni.
    
#### de -r kapcsolóval meg lehet adni egy txt fájlban több csomagot is operátorral

#### freeze

#### --user
> Install to the Python user install directory for your platform. Typically ~/.local/, or %APPDATA%\Python on Windows. (See the Python
> documentation for site.USER_BASE for full details.)
                
*pip --help | grep '--user'; rtfm*

virtualenv
----------

**Q:** _OK, jó a pip, de mi ezzel a gond?_

**A:** Ha sok eszközt akarunk felrakni egy gépre, a függőségek ütközni fognak,
a pip nem támogat egymás mellett több verziót is (mint pl a ruby bundler-e),
viszont az egyes komponensek lehet fejreállnak, ha nem a requirements.txt-ben 
meghatározott verziót kapják. Ráadásul az OS disztribúciókban is előfordulnak
python package-ek (ergo ha root-ként pip-ázunk, akkor könnyen tökön szúrjuk
magunkat).


**Q:** _Hogy lehetek jó kiscserkész ebben a környezetben?_

**A:** virtuelenv-ekkel 


A virtualenv alapvetően olyan, mint a chroot, csak python csomagokra, libekre,
parancsokra szűkítve. Azaz egy megadott könyvtár alá felinstallál pip-el egy
alapvető python disztribúciót (szóval egy python interpreter + pip).

Miután aktiváltuk a venv-ünket, máris telepíthetünk bele pip-el csomagokat,
amik ha utility-k akkor a _PATH_-unkba kerülnek automatikusan, ha pedig lib-ek
akkor a site packages-be (azaz python scriptből, vagy REPL-ből tudjuk importálni).

Szokás repónként/projektenként létrehozni venv-et a használatkor, a repo rootba,
`.venv` néven, és ide installálni a függőségeket.

Konvenció szerint, python projectek rootjában szokás tenni egy:
- requirements.txt: a használati függőségeknek
- test-requirements.txt: az automata tesztek futtatásához szükséges függőségek.

A requirements- és a test-requirements.txt fájlok tartalma diszjunkt kell hogy
legyen, különben a pip hibát dob, ha egyszerre szeretnénk mindkét függőséget
telepíteni. `pip install -r requirements.txt -r test-requirements.txt`

**Caution!** a 3-as pythonban sokminden változott, többek között a venv is. 
Mostmár a python disztribúció része (, így ha egy Xenial-on apt-get install
python-venv -et installálunk, az nagy meglepetésre nem fogja majd a virtualenv
parancsot adni, mivel az csak a 2.x-es pythonhoz egy 3rd party utility), mivel 
a pythonban írt admin toolok nagy része 2.7-es pythonban íródott így érdemes
explicit azt installálni ami python2 vagy python2.7-el kezdődik.
  
Használat után szokás a `deactivate` paranccsal kikapcsolni a venv-et, de egy
ctrl+d vagy exit is megteszi, mivel az activate/deactivate csak a megfelelő
környezeti változókat állítja be (néha ez nekem úgy hiányzik Java-nál...).

tox
---
#### Ez meg minek?
#### tox fejlác, envlist
TL;DR; itt definiáljuk a különbőző teszt environmenteket,
 amiket futtatni akarunk
#### testenvek
venv-eket ír le, melyeket specifikus tesztek futtatására hoz létre a tox

##### deps
a teszt függőségei

##### commands 
megadja meg milyen CLI parancsokat fog futtatni a tox, az adott venvben

#### factor-ok, generált envlist
Itt bonyolódik el egy csöppet!

##### Az egész az envlist-nél kezdődött...
Egész pontosan azzal, hogy esetlegesen több lib verzióval, vagy esetleg python
verzióval szeretnénk tesztelni. pl:
    
    [tox]
    envlist = py27-A23
    
    [tox:jenkins]
    envlist = py{27,35}-A{23,24}
    
    [testenv]
    deps =
        -r requirements.txt
        -r test-requirements.txt
        A23: ansible==2.3
        A24: ansible==2.4
        
    commands =
        !jenkins: molecule {posargs} check
        jenkins: molecule test --destroy always
        
        
