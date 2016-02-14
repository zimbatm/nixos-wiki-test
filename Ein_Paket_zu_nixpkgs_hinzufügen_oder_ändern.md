---
title: Ein Paket zu nixpkgs hinzufügen oder ändern
permalink: /Ein_Paket_zu_nixpkgs_hinzufügen_oder_ändern/
---

Um ein Packet zu den \`nixpkgs\` hinzuzufügen musst du mit \`git\` und \`github\` grundlegend vertraut sein.

In folgendem Beispiel fügen wir \`scim\` zu den \`nixpkgs\` hinzu (dieses Paket befindet sich bereits in den nixpkgs, der Prozess ist aber immer der gleiche).

Vorraussetzungen
----------------

Um eine Software paketieren zu können müssen folgende Dinge bekannt sein:

-   Quellcoderepository der Software
-   Abhängigkeiten der Software
-   Lizenz der Software

Und eventuell die Sprache, in welcher das Paket geschrieben ist, da sich der Paketierungsvorgang für zum Beispiel Python-Pakete von dem für in C geschriebenen Paketen unterscheidet.

In unserem Fall:

-   Repository: <https://github.com/andmarti1424/sc-im>
-   Abhängigkeiten:
    -   yacc
    -   ncurses
    -   libxml2
-   Lizenz: SCIM Lizenz (custom)

Die Abhängigkeiten sind oft in der \`README.md\`-Datei des Projektes aufgelistet, manchmal muss auch ein \`Makefile\` oder eine \`CMakeLists.txt\`-Datei durchsucht werden. In seltenen Fällen sind die Abhängigkeiten einer Software nicht zu finden. Sollte dies vorkommen kann man versuchen die Software zu bauen, bis ein Build-Error auftritt, welcher dann aussagt welche Abhängigkeiten fehlen. Diesen Prozess muss man dann wiederholen bis die Software beim Build-Vorgang erfolgreich gebaut werden kann.

Holen des \`nixpkgs\`-Repos
---------------------------

Dieser Schritt muss durchgeführt werden um in den \`nixpkgs\` ein neues Paket anzulegen:

`   git clone `[`https://github.com/nixos/nixpkgs`](https://github.com/nixos/nixpkgs)
`   cd nixpkgs`

Erstellen eines Paketes
-----------------------

Beim Erstellen eines Paketes in den \`nixpkgs\` stellt sich als erstes die Frage, wo die Datei für das Paket denn hin kommen soll. Dies ist oft nicht eindeutig zu sagen. Sollte das Paket an einem völlig falschen Platz landen wird beim Review-Prozess jemand einen besseren Platz vorschlagen.

Im Fall von \`scim\` wurde \`pkgs/applications/misc/scim/default.nix\` als Platz gewählt. Beachte, dass jedes Paket einen Ordner bekommt, in welchem dann eine Datei mit dem Namen \`default.nix\` liegt.

Diese Datei ist wie folgt aufgebaut:

`   { stdenv, fetchurl, `<Abhängigkeiten des Paketes>` }:`
`   stdenv.mkDerivation rec {`
`     version = "`<version des Paketes>`";`
`     name = "`<name des paketes>`-${version}";`
`   `
`     src = fetchurl {`
`       url = "<URL des .tar.gz-Files (oder ähnlich) welches das Projekt enthält";`
`       sha256 = "`<SHA256-Summe des Paketes>`";`
`     };`
`   `
`     buildInputs = [ `<Abhängigkeiten des Paketes>` ];`
`   `
`     meta = {`
`       homepage = "`<Homepage des Projektes>`";`
`       description = "`<Kurze Beschreibung des Paketes>`";`
`       license = `<Lizens des Paketes>`;`
`       maintainers = with stdenv.lib.maintainers; [ `<Maintainer>` ];`
`       platforms = with stdenv.lib.platforms; `<Platformen>`;`
`     };`
`   }`

-   In Fall von \`scim\`:
    -   NAME: \`scim\`
    -   VERSION: \`0.1.9\` (zum Zeitpunkt der Erstellung dieses Artikels)
    -   URL: \`"<https://github.com/andmarti1424/scim/archive/v$%7Bversion%7D.tar.gz>"\`. Hier wird die \`version\`-Variable eingebunden um Updates einfacher zu machen, da nur noch an einer Stelle der Quellcode des Paketes geändert werden muss um ein Update zu machen
    -   BUILDINPUTS: In diesem Fall \`yacc ncurses libxml2\`
    -   HOMEPAGE: \`"<https://github.com/andmarti1424/scim>"\`
    -   DESCRIPTION: \`"SCIM - Spreadsheet Calculator Improvised - SC fork"\`
    -   LICENSE: Die \`nixpkgs\` beinhalten viele Lizensen wie zum Beispiel "GPLv2", welche dann mit \`stdenv.lib.licenses.gpl2\` spezifiziert werden kann. \`scim\` wird allerdings unter einer custom-Lizenz veröffentlicht, weswegen hier die Lizenz wie folgt angegeben wurde: \`{ fullName = "SCIM License"; url = "<https://github.com/andmarti1424/scim/raw/master/LICENSE>"; };\`
    -   MAINTAINERS: Hier wurde \`matthiasbeyer\` als Maintainer eingefügt.
    -   PLATFORMS: \`linux\`, da der Maintainer nur auf dieser Platform testen konnte.

Manipulieren der Erstellung des Paketes
---------------------------------------

Oft muss bei einem Paket von Hand nachjustiert werden, wie dieses aus dem Quellcode übersetzt oder installiert wird. Im Fall von \`scim\` musste der Übersetzungprozess und der Installationsprozess verändert werden.

Der Übersetzungsprozess wird vom Ersteller des Paketes mittels eines \`Makefile\`s gemacht, in welchem allerdings die Abhängigkeit \`libxml2\` und der \`prefix\`, also die Stelle an welche das Paket installiert wird, hart codiert ist. Da \`nix\` die Pakete allerdings in einem globalen Store hält und nicht unter \`/usr/bin/\`, muss der Übersetzungsprozess hier leicht angepasst werden:

`   buildPhase = ''`
`     cd src`

`     sed "s,prefix=/usr,prefix=$out," Makefile`
`     sed "s,-I/usr/include/libxml2,-I$libxml2," Makefile`

`     make`
`     export DESTDIR=$out`
`   '';`

Das Gleiche gilt für den Installationsprozess:

`   installPhase = ''`
`     make install prefix=`
`   '';`

Veröffentlichen des neuen Paketes
---------------------------------

Nachdem das Paket hinzugefügt wurde muss es noch in die Liste aller Pakete (\`pkgs/top-level/all-packages.nix\`) eingetragen werden:

`   scim = callPackage ../applications/misc/scim { };`

Nun kann die Änderung committed werden. Am besten sollte man hierfür einen neuen Branch im \`nixpkgs\`-Repository erstellen, welcher dann als Pull-Request an das offizielle \`nixpkgs\`-Repository gestellt werden kann:

`   git checkout -b add-scim # Der Name des Branches sollte aussagen was passiert`
`   git add pkgs/applications/misc/scim/default.nix`

`   # Die Commit-Message sollte den Guidelines entsprechen (https://hydra.nixos.org/build/29220730/download/1/nixpkgs/manual.html#idm140253274541904)`
`   git commit pkgs/applications/misc/scim/default.nix pkgs/top-level/all-packages.nix -m 'scim: init at 0.1.9' `

Nun kann mittels \`git push\` der Branch zum github-Repository gesendet werden und dann über das übliche Verfahren ein Pull-Request an die offiziellen \`nixpkgs\` gestellt werden.