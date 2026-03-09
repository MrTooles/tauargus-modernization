\# 64-Bit Migration Guide \& Architektur-Analyse für tauArgus



\*\*Status:\*\* Aktuell pausiert (Java 17 / 32-Bit ist der stabile Standard).

\*\*Grund:\*\* Die Migration auf eine 64-Bit Java Virtual Machine (JVM) erfordert zwingend, dass \*\*alle\*\* C++-Abhängigkeiten (`.dll` Dateien) als 64-Bit-Versionen vorliegen. Der aktuelle offizielle `64bitdlls` Ordner ist unvollständig.



Diese Dokumentation dient als Fahrplan und Risikobewertung für zukünftige Entwickler, die das 64-Bit-Projekt (und damit die Aufhebung des RAM-Limits) angehen möchten.



---



\## Die "Endgegner" (Höchstes Risiko)

Bevor C++ Code angefasst wird, müssen diese historischen Altlasten aus der 32-Bit-Ära geklärt werden:

\* \*\*`dforrt.dll`:\*\* Compaq Visual Fortran Run-Time. Dieser alte Fortran-Compiler war rein 32-Bit. Der zugrundeliegende Mathe-Code muss entweder mit einem modernen 64-Bit-Fortran-Compiler (z.B. GNU Fortran) neu gebaut, in C++ neu geschrieben oder aus dem Projekt entfernt werden.

\* \*\*`crtdll.dll`:\*\* Eine veraltete C-Laufzeitbibliothek, die in 64-Bit-Umgebungen problematisch sein kann.



---



\## Inventarliste: Was für 64-Bit fehlt



\### 1. Selbst kompilieren (aus den sdcTools Repositories)

Diese Kern-Module müssen in einer IDE wie Visual Studio oder CLion als 64-Bit (`x64`) `.dll` neu kompiliert werden (Achtung: Pointer/Datentypen für 64-Bit Speicheradressen prüfen!):

\* `libtauargus.dll` (Repo: libtauargus)

\* `libCRP.dll` (Repo: CRP)

\* `libCTA\_bc.dll` (Repo: CTA)

\* `hypercube.dll` (Repo: hypercube)

\* `network.dll`, `dijkstra.dll`, `pprn.dll` (Repo: network)

\* `intervalle.dll` (Repo: intervalle)

\* `csp\_nf\_1H2D.dll`, `csp\_nf\_2D.dll` (Gehören zu CSP / network)



\### 2. Open-Source Third-Party (Herunterladen)

Diese Dateien nicht selbst kompilieren! Einfach die fertigen 64-Bit Windows Binaries besorgen:

\* `glpk\_4\_45.dll` / `glpk\_4\_46.dll` (GNU Linear Programming Kit)

\* `minisat.dll` (SAT-Solver)

\* `wxbase294u\_vc100.dll`, `wxmsw294u\_core\_vc100.dll` (wxWidgets Framework)

\* \*Optional:\* `msvcp100.dll`, `msvcr100.dll` (Microsoft Visual C++ Runtimes - entfallen oft, wenn mit modernem MSVC neu kompiliert wird).



\### 3. Kommerzielle Solver (Lizenz / Installation prüfen)

\* `cplex125.dll` (IBM ILOG CPLEX)

\* `xprs.dll`, `xprl.dll` (FICO Xpress)



\### 4. Bereits in 64-Bit vorhanden (im Repo unter `/64bitdlls`)

\* `TauRounder.dll`, `TauArgusJava.dll`, `TauHitas.dll`

\* `CsplibCPLEX.dll`, `CsplibSCIP.dll`, `CsplibXPRESS.dll`

\* `libgcc\_s\_seh-1.dll`, `libstdc++-6.dll`



---



\## Der 4-Phasen Plan für die Umsetzung



1\. \*\*Phase 1: Machbarkeits-Test (Risk-Driven):\*\* Fortran-Altlasten (`dforrt.dll`) analysieren. Kann dieser Code portiert oder ersetzt werden? Wenn nein, stoppt das Projekt hier.

2\. \*\*Phase 2: Kern-Bibliotheken neu bauen:\*\* Die fehlenden Repositories (Gruppe 1) in Visual Studio klonen, auf `x64` umstellen, Pointer-Fehler beheben und kompilieren.

3\. \*\*Phase 3: Ökosystem aktualisieren:\*\* Die 64-Bit Open-Source-Bibliotheken (Gruppe 2) herunterladen und Lizenzen für kommerzielle Solver (Gruppe 3) im Haus klären.

4\. \*\*Phase 4: Die Hochzeit:\*\* Alle neuen 64-Bit `.dlls` in den `/dlls` Ordner des Java-Projekts kopieren, in IntelliJ das JDK auf Java 17 (64-Bit) umstellen und starten.

