---
title: Disponibilité de scénario – Service Speech
titleSuffix: Azure Cognitive Services
description: Informations de référence pour les régions du service Speech.
services: cognitive-services
author: chrisbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cbasoglu
ms.openlocfilehash: af5bb7126af65a755cb1d58788d39cb8bdcbbb3b
ms.sourcegitcommit: 7868d1c40f6feb1abcafbffcddca952438a3472d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/04/2019
ms.locfileid: "71959023"
---
# <a name="scenario-availability"></a>Disponibilité de scénario

Le SDK du service Speech comprend de nombreux scénarios dans une vaste gamme de langages et d’environnements de programmation.  Les scénarios ne sont actuellement pas tous disponibles dans tous les langages ou environnements de programmation.  Vous trouverez ci-dessous la disponibilité de chaque scénario.

- **Reconnaissance vocale (SR), Liste d’expressions, Intention, Traduction et Conteneurs locaux**
  - Tous les langages/environnements de programmation où il existe un lien fléché <img src="media/index/link.jpg" height="15" width="15"></img> dans la table de démarrage rapide [ici](https://aka.ms/csspeech).
- **Synthèse vocale (TTS)**
  - C++/Windows & Linux
  - C#/Windows & UWP & Unity
  - Java (JRE et Android)
  - Python
  - Swift
  - Objective-C
  - L’API REST TTS peut être utilisée dans tous les autres cas.
- **Mot déclencheur (Détecteur de mots clés/KWS)**
  - C++/Windows & Linux
  - C#/Windows & Linux
  - Python/Windows & Linux
  - Java/Windows & Linux & Android (SDK Speech Devices)
  - La fonctionnalité Mot déclencheur (Détecteur de mots clés/KWS) peut fonctionner avec n'importe quel type de microphone, mais la prise en charge officielle de KWS est actuellement limitée aux réseaux de microphones présents dans le matériel Azure Kinect DK ou dans le Kit de développement logiciel (SDK) Speech Devices
- **Assistant virtuel « voice-first »**
  - C++/Windows & Linux & macOS
  - C#/Windows
  - Java/Windows & Linux & macOS & Android (SDK Speech Devices)
- **Transcription de conversation**
  - C++/Windows & Linux
  - C# (Framework & .NET Core)/Windows & UWP & Linux
  - Java/Windows & Linux & Android (SDK Speech Devices)
- **Transcription de centre d’appel**
  - L’API REST peut être utilisée dans n’importe quelle situation
- **Entrée audio compressée par codec**
  - C++/Linux
  - C#/Linux
  - Java/Linux, Android et iOS
