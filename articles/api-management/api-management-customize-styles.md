---
title: Personnaliser le style des pages du portail des développeurs Gestion des API Azure | Microsoft Docs
description: Suivez les étapes de ce démarrage rapide pour personnaliser le style des éléments du portail des développeurs Gestion des API Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: 047e724fe3e1c2e4738e5964326bf7719281f4af
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073707"
---
# <a name="customize-the-style-of-the-developer-portal-pages"></a>Personnaliser le style des pages du portail des développeurs

Il existe essentiellement trois manières courantes de personnaliser le portail des développeurs dans la Gestion des API Azure :
 
* [Modifier le contenu des pages statiques et des éléments de mise en page](api-management-modify-content-layout.md)
* Mettre à jour les styles utilisés pour les éléments de page dans le portail des développeurs (procédure expliquée dans ce guide)
* [Modifier les modèles utilisés pour les pages générées par le portail](api-management-developer-portal-templates.md) (par exemple, documents API, produits, authentification des utilisateurs.)

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Personnaliser le style des éléments des pages du portail des **développeurs**
> * Afficher votre modification

![Personnaliser le style](./media/modify-developer-portal-style/developer_portal.png)

## <a name="prerequisites"></a>Prérequis

+ Apprenez la [terminologie relative à Gestion des API Azure](api-management-terminology.md).
+ Suivez ce guide de démarrage rapide : [Créer une instance du service Gestion des API Azure](get-started-create-service-instance.md).
+ Effectuez également toutes les étapes du tutoriel suivant : [Importer et publier votre première API](import-and-publish.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="customize-the-developer-portal"></a>Personnaliser le portail des développeurs

1. Sélectionnez **Vue d’ensemble**.
2. Cliquez sur le bouton **Portail des développeurs** en haut de la fenêtre **Vue d’ensemble**. Vous pouvez également cliquer sur le lien **URL du portail des développeurs**.
3. Sur le côté supérieur gauche de l’écran, une icône composée de deux pinceaux apparaît. Placez le curseur de la souris au-dessus de cette icône pour ouvrir le menu de personnalisation du portail.

    ![Personnaliser le style](./media/modify-developer-portal-style/modify-developer-portal-style01.png)
4. Sélectionnez **Styles** dans le menu pour ouvrir le volet de personnalisation du style.

    Tous les éléments que vous pouvez personnaliser à l’aide de **Styles** apparaissent dans la page.
5. Entrez « headings-color » dans le champ **Changez les valeurs des variables pour personnaliser l’apparence du portail des développeurs**.

    L’élément **\@headings-color** apparaît dans la page. Cette variable contrôle la couleur du texte.

    ![Personnaliser le style](./media/modify-developer-portal-style/modify-developer-portal-style02.png)
    
6. Cliquez sur le champ associé à la variable **\@headings-color**. 
    
    Une liste déroulante de sélecteur de couleurs s’ouvre.
7. Dans cette liste déroulante, sélectionnez une nouvelle couleur.

    > [!TIP]
    > Un aperçu en temps réel est disponible pour toutes les modifications. Un indicateur de progression s’affiche en haut du volet de personnalisation. Après quelques secondes, la couleur nouvellement sélectionnée est appliquée au texte d’en-tête.

8. Sélectionnez **Publier** dans la partie inférieure gauche du menu du volet de personnalisation.
9. Sélectionnez **Publier les personnalisations** pour publier les modifications.

## <a name="view-your-change"></a>Afficher votre modification

1. Accédez au portail des développeurs.
2. Vous pouvez voir la modification que vous avez apportée.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Personnaliser le style des éléments des pages du portail des **développeurs**
> * Afficher votre modification

Nous vous recommandons également d’apprendre à [personnaliser le portail des développeurs Gestion des API Azure à l’aide de modèles](api-management-developer-portal-templates.md).