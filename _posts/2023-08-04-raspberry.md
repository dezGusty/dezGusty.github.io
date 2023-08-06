---
layout: post
title: Raspberry PI
---

## Apreciez eficiența, dar și frugalitatea

În hardware de multe ori duc către același lucru, dar nu întotdeauna.
De exemplu, un procesor ar putea fi foarte eficient, realizând multe operații pe secundă, la 100W.
Poate pentru majoritatea sarcinilor ar fi suficient și un procesor care face treaba de 100 de ori mai lent, dar la 5W.
Aparent ar fi de 5 ori mai ineficient, dar are și anumite avantaje:

1. Pune un sistem de alarmă problemelor de performanță.
  Dacă greșesc ceva ca developer (iar eu greșesc din belșug), nu este imediat aparent în performanță dacă procesorul poate duce lejer de 1000 de ori mai multe operații decât necesarul. Cu ceva lent, devine aparent mult mai repede.
2. Pune o limită prostiei
  Există șanse mari să fac prostii.
  Fie că scriu cod teribil și ajung la apeluri infinit recursive.
  Sau poate nu-mi trebuie mie securitate, că nu am cum să pățesc eu ceva, așa că ajunge să fie folosit pentru minat echipamentul hardware fără să bag de seamă.
  Sau un bug obscur din library-ul NONAME_338 duce la încărcarea procesorului la maxim când sunt în concediu.
  Limita este de 5W/oră.

## Mediu de producție vs testare

Desigur, limita se potrivește pentru mediul de producție.
Pentru mediul de dezvoltare / testare, o performanță scăzută va fi în detrimentul productivității.
Un procesor de generație nouă pe laptop -> 15W - 45W folosiți pentru a compila rapid tot ce e nevoie.
Un procesor lent pe sistemul de producție -> 5W pentru a face un deploy ocazional și pentru a ține în picioare cele necesare.

## Enter Raspberry PI

Raspberry Pi 4. Procesor vechi (2019), Quad-core, 1.5Ghz, preț oficial mic, practic mare, că nu s-a găsit multă vreme la prețul oficial.
Există și alternative.

Retroactiv, ce pare de asemenea o soluție de luat în calcul: un mobil (semi)-vechi.
Un monitor ar consuma mai mult decât PI-ul, bine că merge și în modul [headless](https://www.tomshardware.com/reviews/raspberry-pi-headless-setup-how-to,6028.html) în care devine un simplu server pe rețea.

## De știut înainte

Folosirea pe post de *web-server* trebuie să implice folosirea unui SSD, nu a unui SD card (scenariul implicit pentru Raspberry PI).
Cardul SD are un număr limitat de scrieri posibile.
Am observat asta pe cont propriu după cam 5 luni de utilizare a unui Raspberry PI ca web server cu încărcare foarte redusă.
Cardul a devenit *read-only*, ceea ce a făcut sistemul de operare și *web-server*-ul de pe el inutilizabile.

Un SSD merge mai bine, deoarece ar trebui să fie disponibil ușor în dimensiuni mai mari, accesibile ca preț și s-ar putea distribui (re)scrierile către mai multe celule de pe dispozitiv.

## Ce a vrut să spună autorul

Nu rulați servere web de pe carduri SD.
