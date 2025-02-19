---
title: En savoir plus sur les encodeurs recommandés par Azure Media Services | Microsoft Docs
description: En savoir plus sur les encodeurs recommandés par Media Services
services: media-services
keywords: encodage;encodeurs;média
author: dbgeorge
manager: johndeu
ms.author: johndeu
ms.date: 03/20/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: fc481129e652c6dacd15a5a6d039a9118393e8f1
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854246"
---
# <a name="recommended-on-premises-encoders"></a>Encodeurs locaux recommandés
Lorsque vous êtes en diffusion continue avec Azure Media Services, vous pouvez spécifier la manière dont vous souhaitez que votre canal reçoive le flux d’entrée. Si vous choisissez d’utiliser un encodeur local avec un canal d’encodage live, votre encodeur doit transmettre un flux à débit unique de haute qualité en tant que sortie. Si vous choisissez d’utiliser un encodeur local avec un canal de transmission directe, votre encodeur doit transmettre un flux à multidébit en tant que sortie, avec toutes les qualités de sortie souhaitées. Pour plus d’informations, consultez [Vidéo en flux continu avec des encodeurs locaux](media-services-live-streaming-with-onprem-encoders.md).

Azure Media Services recommande l’utilisation d’un des encodeurs live suivants, qui possèdent une sortie RTMP :
- Adobe Flash Media Live Encoder 3.2
- Haivision Makito X HEVC
- Haivision KB
- Telestream Wirecast 8.1+
- Telestream Wirecast S
- Teradek Slice 756
- TriCaster 8000
- Tricaster Mini HD-4
- OBS Studio
- VMIX
- xStream
- Switcher Studio (iOS)

Azure Media Services recommande l’utilisation d’un des encodeurs live suivants, qui possèdent une sortie Smooth Streaming multidébit MP4 fragmentée :
- Media Excel Hero Live et Hero 4K (UHD/HEVC)
- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live
- Envivio 4Caster C4 Gen III
- Imagine Communications Selenio MCP3

> [!NOTE]
> Un encodeur live peut envoyer un flux unique à un canal de transmission directe, mais cette configuration n’est pas recommandée, car elle n’offre pas de streaming à débit adaptatif au client.

## <a name="how-to-become-an-on-premises-encoder-partner"></a>Comment devenir un partenaire d’encodeur local
En tant que partenaire d’encodeur local d’Azure Media Services, Media Services promeut votre produit en recommandant votre encodeur aux clients d’entreprise. Pour devenir un partenaire d’encodeur local, vous devez vérifier la compatibilité de votre encodeur local avec Media Services. Pour ce faire, effectuez les vérifications suivantes :

Vérification du canal de transmission directe
1. Créez ou visitez votre compte Azure Media Services
2. Créez et démarrez un canal de **transmission directe**
3. Configurez votre encodeur pour transmettre un flux live à multidébit.
4. Créez un événement en temps réel publié
5. Exécutez votre encodeur live pendant environ 10 minutes
6. Arrêtez l’événement en temps réel
7. Créez, démarrez un point de terminaison de streaming, utilisez un lecteur, par exemple [Lecteur multimédia Azure](https://aka.ms/azuremediaplayer) pour regarder l’élément multimédia archivé pour vous assurer que la lecture est dépourvue de problèmes visibles pour tous les niveaux de qualité (vous pouvez également regarder et valider du contenu via l’URL d’aperçu pendant la session active avant l’étape 6)
8. Enregistrez l’ID de la ressource, l’URL de streaming publié pour l’archive en temps réel, ainsi que les paramètres et la version utilisée à partir de votre encodeur live
9. Réinitialisez l’état du canal après la création de chaque exemple.
10. Répétez les étapes 3 à 9 pour toutes les configurations prises en charge par votre encodeur (avec et sans signalisation des annonces/légendes/différentes vitesses de codage)

Vérification du canal d’encodage live
1. Créez ou visitez votre compte Azure Media Services
2. Créer et démarrer un canal d’**encodage live**
3. Configurez votre encodeur pour transmettre un flux live à débit unique.
4. Créez un événement en temps réel publié
5. Exécutez votre encodeur live pendant environ 10 minutes
6. Arrêtez l’événement en temps réel
7. Créez, démarrez un point de terminaison de streaming, utilisez un lecteur, par exemple [Lecteur multimédia Azure](https://aka.ms/azuremediaplayer) pour regarder l’élément multimédia archivé pour vous assurer que la lecture est dépourvue de problèmes visibles pour tous les niveaux de qualité (vous pouvez également regarder et valider du contenu via l’URL d’aperçu pendant la session active avant l’étape 6)
8. Enregistrez l’ID de la ressource, l’URL de streaming publié pour l’archive en temps réel, ainsi que les paramètres et la version utilisée à partir de votre encodeur live
9. Réinitialisez l’état du canal après la création de chaque exemple.
10. Répétez les étapes 3 à 9 pour toutes les configurations prises en charge par votre encodeur (avec et sans signalisation des annonces/légendes/différentes vitesses de codage)

Vérification de longévité
1. Créez ou visitez votre compte Azure Media Services
2. Créez et démarrez un canal de **transmission directe**
3. Configurez votre encodeur pour transmettre un flux live à multidébit.
4. Créez un événement en temps réel publié
5. Exécutez votre encodeur en temps réel pendant une semaine ou plus
6. Utilisez un lecteur, par exemple [Lecteur multimédia Azure](https://aka.ms/azuremediaplayer) pour regarder de temps en temps le streaming en direct (ou l’élément multimédia archivé) afin de vous assurer que la lecture est dépourvue de problèmes visibles
7. Arrêtez l’événement en temps réel
8. Enregistrez l’ID de la ressource, l’URL de streaming publié pour l’archive en temps réel, ainsi que les paramètres et la version utilisée à partir de votre encodeur live

Enfin, envoyez vos paramètres enregistrés et paramètres d’archivage en temps réel à Media Services par courrier électronique à amsstreaming@microsoft.com. Lors de la réception, Media Services effectue les tests de vérification sur les exemples de votre encodeur live. Vous pouvez contacter Media Services pour toute question concernant ce processus.
