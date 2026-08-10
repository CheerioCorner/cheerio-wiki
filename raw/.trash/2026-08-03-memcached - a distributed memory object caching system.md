---
title: "memcached - a distributed memory object caching system"
description: "memcached"
source_url: "https://memcached.org/about"
source_domain: "memcached.org"
author:
published:
clipped: 2026-08-03
type: "raw-source"
source_kind: "web"
immutable: "true"
tags:
  - "raw"
  - "web-clip"
---
# memcached - a distributed memory object caching system

> Source: [memcached - a distributed memory object caching system](https://memcached.org/about)
> Clipped: 2026-08-03

## About Memcached

memcached is a high-performance, distributed memory object caching system, generic in nature, but originally intended for use in speeding up dynamic web applications by alleviating database load.

You can think of it as a short-term memory for your applications.

## What it Does

![usage](https://memcached.org/images/memcached-usage.png)

memcached allows you to take memory from parts of your system where you have more than you need and make it accessible to areas where you have less than you need.

memcached also allows you to make better use of your memory. If you consider the diagram to the right, you can see two deployment scenarios:

1. Each node is completely independent (top).
2. Each node can make use of memory from other nodes (bottom).

The first scenario illustrates the classic deployment strategy, however you'll find that it's both wasteful in the sense that the total cache size is a fraction of the actual capacity of your web farm, but also in the amount of effort required to keep the cache consistent across all of those nodes.

With memcached, you can see that all of the servers are looking into the same virtual pool of memory. This means that a given item is always stored and always retrieved from the same location in your entire web cluster.

Also, as the demand for your application grows to the point where you need to have more servers, it generally also grows in terms of the data that must be regularly accessed. A deployment strategy where these two aspects of your system scale together just makes sense.

The illustration to the right only shows two web servers for simplicity, but the property remains the same as the number increases. If you had fifty web servers, you'd still have a usable cache size of 64MB in the first example, but in the second, you'd have 3.2GB of usable cache.

Of course, you aren't required to use your web server's memory for cache. Many memcached users have dedicated machines that are built to only be memcached servers.

### Origin

Memcached was originally developed by Brad Fitzpatrick for [LiveJournal](http://www.livejournal.com/) in 2003.

### Contributors

| dormando (1180) | Dustin Sallings (214) | Brad Fitzpatrick (164) | Trond Norbye (130) |
| --- | --- | --- | --- |
| Paul Lindner (58) | Toru Maesaka (34) | Steven Grimm (25) | David Carlier (23) |
| Kevin Lin (17) | Stanisław Pitucha (16) | Anatoly Vorobey (15) | Brian Aker (15) |
| Steve Yen (15) | Tomash Brechko (12) | minkikim89 (12) | Fei Hu (9) |
| Tharanga Gamaethige (9) | Bujna, Igor (8) | Kanak Kshetri (7) | Tomas Korbar (7) |
| hachi (7) | Aaron Stone (6) | Dan McGee (6) | Guillaume Delacour (6) |
| Ola Jeppsson (6) | Peter (Stig) Edwards (6) | Steve Wills (6) | Daniel Schemmel (5) |
| Evan Martin (5) | Fabrice Fontaine (5) | Finn Frankis (5) | Matt Ingenthron (5) |
| Miroslav Lichvar (5) | Sailesh Mukil (5) | xuesenliang (5) | Chris Goffinet (4) |
| Craig Andrews (4) | David CARLIER (4) | Eric McConville (4) | Jefty Negapatan (4) |
| Remi Collet (4) | Tianon Gravi (4) | Victor Kirkebo (4) | Andrei Nigmatulin (3) |
| Calin Iorgulescu (3) | Cameron Norman (3) | Daniel Pañeda (3) | Eric Lambert (3) |
| Jay Grizzard (3) | Paul Furtado (3) | Qu Chen (3) | Yufei Hu (3) |
| sergiocarlos (3) | Antony Dovgal (2) | Bernhard M. Wiedemann (2) | Brion Vibber (2) |
| Carl Myers (2) | Clint Byrum (2) | Colin Pitrat (2) | Cosimo Streppone (2) |
| David Bremner (2) | Dmitry Volodin (2) | Doug Porter (2) | Eric Hodel (2) |
| Evan Miller (2) | Giovanni Bechis (2) | Iqram Mahmud (2) | J. Grizzard (2) |
| Jason CHAN (2) | Jay Bonci (2) | Jean-Francois BUSTARRET (2) | Josh Soref (2) |
| Khem Raj (2) | Linkerist (2) | Mat Hostetter (2) | Monty Taylor (2) |
| Olof Nord (2) | Paolo Borelli (2) | Ricky Zhou (2) | Shiv Nagarajan (2) |
| Tyson Andre (2) | Vadim Pushtaev (2) | Zhou Qiankang (2) | jinyaoguo (2) |
| mugitya03 (2) | pkarumanchi9 (2) | 祁冰 (2) | Adam Chainz (1) |
| Adam Dixon (1) | Adam Szkoda (1) | Adam Thomason (1) | Alec Stewart (1) |
| Aleksandr (1) | Alex Leone (1) | Alexander Pyhalov (1) | Ali Saidi (1) |
| Alwayswithme (1) | Andre Azevedo Pinto (1) | Andrew Drake (1) | Andrew Glinskiy (1) |
| Andrey Niakhaichyk (1) | Anthony Ryan (1) | ArtemIsmagilov (1) | Artur Bergman (1) |
| Aswath Ilangovan (1) | Baptiste Mille-Mathias (1) | Ben Evans (1) | Caleb Shay (1) |
| CaptTofu (1) | Chang Song (1) | Charmander (1) | Chen-Yu Tsai (1) |
| Clinton Webb (1) | Dagobert Michelsen (1) | Dan Christian (1) | Daniel Byrne (1) |
| Daniel Vasquez-Lopez (1) | Danny Kopping (1) | David Bohman (1) | David J. M. Karlsen (1) |
| David Oliveira (1) | David Phillips (1) | David Schoen (1) | Dmitry Isaykin (1) |
| Don MacAskill (1) | Eiichi Tsukata (1) | Eli Bingham (1) | Elizabeth Mattijsen (1) |
| Evan Klitzke (1) | Fangrui Song (1) | Filipe Laborde (1) | Fordy (1) |
| Fumihiro Ito (1) | Gabe Van Engel (1) | Gabriel A. Samfira (1) | Gleicon Moraes (1) |
| Gordon Franke (1) | Grant Mathews (1) | Gregor Jasny (1) | Guido Iaquinti (1) |
| Hemal Shah (1) | Hervé Beraud (1) | Huzaifa Sidhpurwala (1) | Iain Wade (1) |
| Ian Miell (1) | Iliya (1) | Ing-eoking (1) | James Cohen (1) |
| Jamie McCarthy (1) | Jason Titus (1) | Jeff Lawson (1) | Jeremy Sowden (1) |
| Joe Orton (1) | Johan Bergström (1) | John Leslie (1) | Jon Jensen (1) |
| Jonathan Bastien-Filiatrault (1) | Jonathan Steinert (1) | Josh Kupershmidt (1) | Juliy V. Chirkov (1) |
| Junji Hashimoto (1) | Jørgen Austvik (1) | Kenneth Steele (1) | Keyur (1) |
| KissPeter (1) | Kleber (1) | LSmithx2 (1) | Levente Polyak (1) |
| Lisa Seelye (1) | Léon Brocard (1) | Maksim Zhylinski (1) | Manish Katiyar (1) |
| Mark Hagger (1) | Martin Tzvetanov Grigorov (1) | Mate Borcsok (1) | Mathieu CARBONNEAUX (1) |
| Matt Fowles Kulukundis (1) | Matthew Shafer (1) | Mattias Geniar (1) | Maxim Dounin (1) |
| Menghan (1) | Michael Alan Dorman (1) | Mike Dillon (1) | Miklos Vajna (1) |
| Natanael Copa (1) | Nate (1) | Nathan Neulinger (1) | Nick (1) |
| Nick Frost (1) | Nick Pillitteri (1) | NikitaNavrotskiy (1) | Ori Shalev (1) |
| Oskari Saarenmaa (1) | Patrice Duroux (1) | Paul Querna (1) | Peter van Dijk (1) |
| Petr Sumbera (1) | Pierre-Yves Rofes (1) | Piotr Balcer (1) | Prudhviraj K (1) |
| Qian Li (1) | Ramasai (1) | Raphael Isemann (1) | Richard Russo (1) |
| River Tarnell (1) | Roman Mueller (1) | Ryan McCullagh (1) | Ryan T. Dean (1) |
| Ryan Tomayko (1) | Ryuichi Watanabe (1) | Saman Barghi (1) | Sarthak Munshi (1) |
| Sergei Trofimovich (1) | Sergio Durigan Junior (1) | Sharif Nassar (1) | Simon Liu (1) |
| Sjon Hortensius (1) | Sridhar Samudrala (1) | Steve Peters (1) | Sunjeet (1) |
| Tao Hui (1) | Ted Schundler (1) | Theo Najim (1) | Thomas van Gulick (1) |
| Tim Yardley (1) | Tom Stellard (1) | Tomas Kalibera (1) | Torsten Foertsch (1) |
| Vladimir (1) | Wing Lian (1) | Yongyue Sun (1) | Zheng Gu (1) |
| ajccosta (1) | akisssa (1) | bitground (1) | clark.kang (1) |
| githublvv (1) | hanqing2025 (1) | hayashier (1) | hiracy (1) |
| iqr4m (1) | js (1) | jwbee (1) | kenvifire (1) |
| kokke (1) | kun (1) | liu bo (1) | liwenlong05 (1) |
| mckelvin (1) | mdl (1) | meteorgan (1) | miwasson (1) |
| neal-zhu (1) | nirvanazc (1) | phantom9999 (1) | prudhvi (1) |
| q66 (1) | sshetty (1) | theblop (1) | tom (1) |
| wangkang-xy (1) | yuryur (1) | zhoutai (1) | 伊藤洋也 (1) |