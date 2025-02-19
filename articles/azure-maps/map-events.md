---
title: Gérer les événements avec Azure Maps | Microsoft Docs
description: Guide pratique pour créer une carte interactive du SDK web avec des événements de carte
author: jingjing-z
ms.author: jinzh
ms.date: 09/10/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: c5b4ed73d7bc4d89a67280a0bb183f374ae093d8
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70899429"
---
# <a name="interact-with-the-map"></a>Interagir avec la carte

Cet article vous montre comment utiliser la propriété [events de la classe Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?#events) pour mettre en évidence des événements sur la carte et sur différentes couches de la carte. Il montre également comment utiliser cette propriété pour mettre en évidence des événements lorsque vous interagissez avec un marqueur HTML.

## <a name="interact-with-the-map"></a>Interagir avec la carte

Manipulez la carte ci-dessous et observez les événements de souris correspondants mis en surbrillance sur la droite. Cliquez sur l’**onglet JS** pour afficher et modifier le code JavaScript. Vous pouvez également cliquer sur le bouton de **modification sur CodePen** afin de modifier le code par ce biais.

<br/>

<iframe height='600' scrolling='no' title='Interaction avec la carte - Événements de souris' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>Interact with the map – mouse events (Interagir avec la carte - Événements de souris)</a> par Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-map-layers"></a>Interagir avec les couches de la carte

Le code suivant met en évidence le nom des événements qui sont déclenchés quand vous interagissez avec la couche de symboles. Les couches de symboles, de bulles, de lignes et de polygones prennent toutes en charge le même ensemble d’événements. Les couches de carte thermique et de mosaïques ne prennent en charge aucun de ces événements.

<br/>

<iframe height='600' scrolling='no' title='Interaction avec la carte - Événements de couches' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>Interacting with the map – Layer Events</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-html-marker"></a>Interagir avec un marqueur HTML

Le code suivant ajoute des événements de carte JavaScript à un marqueur HTML. Il met également en surbrillance le nom des événements qui sont déclenchés quand vous interagissez avec le marqueur HTML.

<br/>

<iframe height='500' scrolling='no' title='Interaction avec la carte - Événements de marqueurs HTML' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>Interacting with the map - HTML Marker events</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Le tableau suivant répertorie tous les événements de classe Map pris en charge.

| Événement             | Description |
|-------------------|-------------|
| boxzoomend        | Déclenché lorsqu’une interaction « zoom zone » se termine.|
| boxzoomstart      | Déclenché lorsqu’une interaction « zoom zone » commence.|
| click             | Déclenché lorsqu’un dispositif de pointage est enfoncé et relâché au même point sur la carte.|
| close             | Déclenché lorsque la fenêtre contextuelle est fermée manuellement ou programmatiquement.|
| contextmenu       | Déclenché suite à un clic sur le bouton droit de la souris.|
| dataadded         | Déclenché lorsque des formes sont ajoutées à la source de données.|
| dataremoved       | Déclenché lorsque des formes sont supprimées de la source de données.|
| datasourceupdated | Déclenché lorsque l’objet Source de données est mis à jour.|
| dblclick          | Déclenché suite au double clic avec un dispositif de pointage au même point sur la carte.|
| drag              | Déclenché à plusieurs reprises pendant une interaction « glisser pour obtenir une vue panoramique » sur la carte, la fenêtre contextuelle ou le marqueur HTML.|
| dragend           | Déclenché quand une interaction « glisser pour obtenir une vue panoramique » se termine sur la carte, la fenêtre contextuelle ou le marqueur HTML.|
| dragstart         | Déclenché quand une interaction « glisser pour obtenir une vue panoramique » commence sur la carte, la fenêtre contextuelle ou le marqueur HTML.|
| error             | Déclenché quand une erreur se produit.|
| keydown           | Déclenché lorsqu’une touche est enfoncée.|
| keypress          | Déclenché lorsqu’une touche qui produit un caractère tapable (touche ANSI) est enfoncée.|
| keyup             | Déclenché lorsqu’une touche est relâchée.|
| layeradded        | Déclenché lorsqu’une couche est ajoutée à la carte.|
| load              | Déclenché immédiatement après que toutes les ressources nécessaires ont été téléchargées et que le premier rendu visuellement complet du mappage s’est produit.|
| mousedown         | Déclenché lorsqu’un dispositif de pointage est enfoncé sur la carte.|
| mousemove         | Déclenché lorsqu’un dispositif de pointage est déplacé sur la carte.|
| mouseout          | Déclenché lorsqu’un dispositif de pointage quitte le canevas de la carte.|
| mouseover         | Déclenché lorsqu’un dispositif de pointage est déplacé sur la carte.|
| mouseup           | Déclenché lorsqu’un dispositif de pointage est relâché sur la carte.|
| déplacer              | Déclenché à plusieurs reprises pendant une transition animée d’une vue à l’autre, en raison de l’interaction de l’utilisateur ou de méthodes.|
| moveend           | Déclenché dès que la carte termine une transition animée d’une vue à l’autre, en raison de l’interaction de l’utilisateur ou de méthodes.|
| movestart         | Déclenché avant que la carte ne commence une transition animée d’une vue à l’autre, en raison de l’interaction de l’utilisateur ou de méthodes.|
| ouvert              | Déclenché lorsque la fenêtre contextuelle est ouverte manuellement ou programmatiquement.|
| pitch             | Déclenché à chaque modification de l’inclinaison de la carte, en raison de l’interaction de l’utilisateur ou de méthodes.|
| pitchend          | Déclenché dès que la modification de l’inclinaison de la carte est terminée, en raison de l’interaction de l’utilisateur ou de méthodes.|
| pitchstart        | Déclenché dès que la modification de l’inclinaison de la carte commence, en raison de l’interaction de l’utilisateur ou de méthodes.|
| ready             | Déclenché lorsque les ressources de carte minimales requises sont chargées avant que la carte soit prête pour une interaction programmatique.|
| render            | <p> Déclenché à chaque fois que la carte est dessinée à l’écran, résultant de :<ul><li>Modification de la position, du zoom, de l’inclinaison ou du repère de la carte.</li><li>Modification du style de la carte.</li><li>Modification d’une source DataSource.</li><li>Chargement d’une vignette vectorielle, d’un fichier GeoJSON, d’un glyphe ou d’un sprite.</li></ul></p>|
| resize            | Déclenché immédiatement après le redimensionnement de la carte.|
| rotate            | Déclenché à plusieurs reprises pendant une interaction « faire glisser pour faire pivoter ».|
| rotateend         | Déclenché lorsqu’une interaction « faire glisser pour faire pivoter » se termine.|
| rotatestart       | Déclenché lorsqu’une interaction « faire glisser pour faire pivoter » commence.|
| shapechanged      | Déclenché lors de la modification d’une propriété d’objet de forme.|
| sourceadded       | Déclenché si DataSource ou VectorTileSource est ajouté à la carte.|
| sourceremoved     | Déclenché si DataSource ou VectorTileSource est supprimé de la carte.|
| styledata         | Déclenché lorsque le style de la carte est chargé ou modifié.|
| tokenacquired     | Déclenché lorsqu’un jeton d’accès AAD est obtenu.|
| touchcancel       | Déclenché lorsqu’un événement touchcancel se produit dans la carte.|
| touchend          | Déclenché lorsqu’un événement touchend se produit dans la carte.|
| touchmove         | Déclenché lorsqu’un événement touchmove se produit dans la carte.|
| touchstart        | Déclenché lorsqu’un événement touchstart se produit dans la carte.|
| wheel             | Déclenché lorsqu’un événement de roulette de souris se produit dans la carte.|
| zoom              | Déclenché à plusieurs reprises pendant une transition animée d’un niveau de zoom à l’autre, en raison de l’interaction de l’utilisateur ou de méthodes.|
| zoomend           | Déclenché dès que la carte termine une transition animée d’un niveau de zoom à l’autre, en raison de l’interaction de l’utilisateur ou de méthodes.|
| zoomstart         | Déclenché avant que la carte ne commence une transition animée d’un niveau de zoom à l’autre, en raison de l’interaction de l’utilisateur ou de méthodes.|


## <a name="next-steps"></a>Étapes suivantes

Pour voir des exemples de codes complets, consultez les articles suivants :

> [!div class="nextstepaction"]
> [Utiliser le module de service Azure Maps](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Exemples de code](https://docs.microsoft.com/samples/browse/?products=azure-maps)
