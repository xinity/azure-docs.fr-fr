---
title: Scènes, plans et images clés Video Indexer
titleSuffix: Azure Media Services
description: Cette rubrique offre une vue d'ensemble des scènes, captures et images clés Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: b24778434596f583be44572612c856fa4e0cecde
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860226"
---
# <a name="scenes-shots-and-keyframes"></a>Scènes, captures et images clés

Video Indexer prend en charge la segmentation de vidéos en unités temporelles basées sur des propriétés sémantiques et structurelles. Cette fonctionnalité permet aux clients de facilement parcourir, gérer et modifier leur contenu vidéo en fonction des niveaux de détail. Par exemple, en fonction des scènes, des captures et des images clés, décrites dans cette rubrique.   

![Scènes, captures et images clés](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)
 
## <a name="scene-detection"></a>Détection de scène  
 
Video Indexer détermine quand une scène change dans la vidéo via des signaux visuels. Une scène représente un événement unique et est composée d’une série de captures consécutives, qui sont sémantiquement liées. Une miniature de la scène constitue la première image clé de sa capture sous-jacente. Video Indexer segmente une vidéo en scènes en fonction de la cohérence des couleurs sur des captures consécutives et récupère le début et la fin de chaque scène. La détection de scène est considérée comme étant une tâche ardue, car elle implique de quantifier des aspects sémantiques d’une vidéo.

> [!NOTE]
> S’applique aux vidéos qui contiennent au moins 3 scènes.

## <a name="shot-detection"></a>Détection de plan

Video Indexer détermine quand une capture change dans la vidéo selon des signaux visuels, en effectuant le suivi des transitions brusques et progressives du jeu de couleurs des images adjacentes. Les métadonnées de la capture incluent un temps de début et de fin, ainsi que la liste des images clés inclues dans cette capture. Les captures sont des images consécutives prises de la même caméra et en même temps.

## <a name="keyframe-detection"></a>Détection d’une image clé

Sélectionne les images représentant au mieux la capture. Les images clés sont des images représentatives sélectionnées à partir de la vidéo complète, en fonction des propriétés esthétiques (par exemple, contraste et stabilité). Video Indexer récupère une liste d’ID d’images clés faisant partie des métadonnées de la capture, en fonction des clients pouvant extraire la miniature de l’image clé. 

Les images clés sont associées à des captures dans la sortie JSON. 

## <a name="editorial-shot-type-detection"></a>Détection du type de plan éditorial

Le type de plan associé à un plan individuel dans les insights JSON représente son type éditorial. Vous pouvez trouver ces caractéristiques de type de plan utiles lors de la modification de vidéos dans des clips ou bandes-annonces, ou lors de la recherche d’un style spécifique d’image clé à des fins artistiques. Les différents types sont déterminés en fonction de l’analyse de la première image clé de chaque plan. Les plans sont identifiés par l’échelle, la taille et l’emplacement des visages apparaissant dans leur première image clé. 

La taille et l’échelle du plan sont déterminées en fonction de la distance entre l’appareil photo et les visages apparaissant dans l’image. À l’aide de ces propriétés, Video Indexer détecte les types de plans suivants :

* Large : montre le corps tout entier d’une personne.
* Moyen : montre le haut du corps et le visage d’une personne.
* Gros plan : montre principalement le visage d’une personne.
* Très gros plan : montre le visage d’une personne qui remplit l’écran. 

Les types de plans peuvent également être déterminés par l’emplacement des personnages par rapport au centre de l’image. Cette propriété définit les types de plans suivants dans Video Indexer :

* Visage à gauche : une personne apparaît sur la gauche de l’image.
* Visage au centre : une personne apparaît dans la partie centrale de l’image.
* Visage à droite : une personne apparaît sur la droite de l’image.
* Extérieur : une personne apparaît dans un décor extérieur.
* Intérieur : une personne apparaît dans un décor intérieur.

Caractéristiques supplémentaires :

* Deux plans : montre les visages de taille moyenne de deux personnes.
* Plusieurs visages : plus de deux personnes.

## <a name="next-steps"></a>Étapes suivantes

[Examiner la sortie de Video Indexer générée par l’API](video-indexer-output-json-v2.md#scenes)
