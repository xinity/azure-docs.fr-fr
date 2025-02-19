---
title: Créer un package d’accès dans la gestion des droits d’utilisation d’Azure Active Directory (préversion) - Azure Active Directory
description: Découvrez comment créer un package d’accès de ressources que vous souhaitez partager dans la gestion des droits d’utilisation Azure Active Directory (préversion).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10a278fdd194b841cbb8620999fe79c3affb4e0b
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389513"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management-preview"></a>Créer un package d’accès dans la gestion des droits d’utilisation Azure AD (préversion)

> [!IMPORTANT]
> La gestion des droits d’utilisation Azure Active Directory (Azure AD) est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Un package d’accès vous permet d’effectuer une configuration unique des ressources et des stratégies qui gèrent automatiquement l’accès pendant toute la durée de vie du package d’accès. Cet article décrit comment créer un package d’accès.

## <a name="overview"></a>Vue d'ensemble

Tous les packages d’accès doivent être placés dans un conteneur appelé catalogue. Un catalogue définit les ressources que vous pouvez ajouter à votre package d’accès. Si vous ne spécifiez aucun catalogue, votre package d’accès est placé dans le catalogue Général. Actuellement, vous ne pouvez pas déplacer un package d’accès existant vers un autre catalogue.

Si vous êtes un gestionnaire de package Access, vous ne pouvez pas ajouter des ressources que vous possédez à un catalogue. Vous ne pouvez utiliser que les ressources disponibles dans le catalogue. Pour ajouter des ressources à un catalogue, vous pouvez contacter le propriétaire du catalogue.

Tous les packages d’accès doivent avoir au moins une stratégie. Les stratégies spécifient qui peut demander le package d’accès, ainsi que les paramètres d’approbation et d’expiration. Lorsque vous créez un nouveau package d’accès, vous pouvez créer une stratégie initiale pour les utilisateurs présents dans votre répertoire, pour les utilisateurs non présents dans votre répertoire, pour les attributions directes d’administrateur uniquement, ou vous pouvez choisir de créer la stratégie ultérieurement.

![Créer un package d’accès](./media/entitlement-management-access-package-create/access-package-create.png)

Voici les principales étapes à suivre pour créer un package d’accès.

1. Dans Identity Governance, démarrez le processus de création d’un nouveau package d’accès.

1. Sélectionnez le catalogue dans lequel vous souhaitez créer le package d’accès.

1. Ajoutez des ressources du catalogue à votre package d’accès.

1. Assignez des rôles de ressource pour chaque ressource.

1. Spécifiez les utilisateurs qui peuvent demander l’accès.

1. Spécifiez les paramètres d’approbation.

1. Changez les paramètres de cycle de vie.

## <a name="start-new-access-package"></a>Démarrer le nouveau package d’accès

**Rôle prérequis :** Administrateur général, administrateur d’utilisateurs ou gestionnaire de package d’accès

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

1. Cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1. Dans le menu gauche, cliquez sur **Packages d’accès**.

    ![Gestion des droits d’utilisation dans le portail Azure](./media/entitlement-management-shared/elm-access-packages.png)

1. Cliquez sur **Nouveau package d’accès**.

## <a name="basics"></a>Concepts de base

Sur l’onglet **Concepts de base**, vous donnez un nom au package d’accès et spécifiez le catalogue dans lequel vous le créez.

1. Entrez un nom d’affichage et une description pour le package d’accès. Les utilisateurs voient ces informations lorsqu’ils envoient une demande pour le package d’accès.

1. Dans la liste déroulante **Catalogue**, sélectionnez le catalogue dans lequel vous souhaitez créer le package d’accès. Par exemple, vous pouvez avoir un propriétaire du catalogue qui gère toutes les ressources marketing qui peuvent être demandées. Dans ce cas, vous pouvez sélectionner le catalogue marketing.

    Vous verrez seulement les catalogues dans lesquels vous êtes autorisé à créer des packages d’accès. Pour créer un package d’accès dans un catalogue existant, vous devez être au moins un Administrateur général, un Administrateur d’utilisateurs, le propriétaire du catalogue ou le gestionnaire de package d’accès dans ce catalogue.

    ![Package d’accès - Concepts de base](./media/entitlement-management-access-package-create/basics.png)

    Si vous êtes un Administrateur général, un Administrateur d’utilisateurs ou un créateur de catalogue, et si vous souhaitez créer votre package d’accès dans un nouveau catalogue qui ne figure pas dans la liste, cliquez sur **Créer un catalogue**. Entrez le nom du catalogue et sa description, puis cliquez sur **Créer**.

    Le package d’accès que vous créez et toutes les ressources incluses sont ajoutés dans le nouveau catalogue. Vous pouvez également ajouter d’autres propriétaires de catalogue plus tard.

1. Cliquez sur **Suivant**.

## <a name="resource-roles"></a>Rôles de ressources

Sous l’onglet **Rôles de ressources**, sélectionnez les ressources à inclure dans le package d’accès. Les utilisateurs qui demandent et reçoivent le package d’accès recevront tous les rôles de ressource dans le package d’accès.

1. Cliquez sur le type de ressource que vous souhaitez ajouter (**Groupes et équipes**, **Applications** ou **Sites SharePoint**).

1. Dans le volet de sélection qui s’affiche, sélectionnez une ou plusieurs ressources dans la liste.

    ![Package d’accès - Rôles des ressources](./media/entitlement-management-access-package-create/resource-roles.png)

    Si vous créez le package d’accès dans le catalogue Général ou dans un nouveau catalogue, vous pouvez récupérer n’importe quelle ressource à partir du répertoire que vous possédez. Vous devez être au moins un Administrateur général, un Administrateur d’utilisateurs ou le Créateur du catalogue.

    Si vous créez le package d’accès dans un catalogue existant, vous pouvez sélectionner n’importe quelle ressource déjà présente dans le catalogue sans avoir à en être propriétaire.

    Si vous êtes un Administrateur général, un Administrateur d’utilisateurs ou un propriétaire de catalogue, vous avez également la possibilité de sélectionner des ressources que vous possédez et qui ne sont pas encore dans le catalogue. Si vous sélectionnez des ressources qui ne sont actuellement pas présentes dans le catalogue sélectionné, ces ressources sont ajoutées au catalogue pour que d’autres administrateurs du catalogue puissent créer des packages d’accès à partir de ces dernières. Si vous souhaitez uniquement sélectionner les ressources qui se trouvent actuellement dans le catalogue sélectionné, cochez la case **Voir uniquement** en haut du volet Sélectionner.

1. Une fois que vous avez sélectionné les ressources, dans la liste **Rôle**, sélectionnez le rôle à assigner aux utilisateurs pour les ressources.

    ![Package d’accès - Sélection du rôle des ressources](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. Cliquez sur **Suivant**.

## <a name="requests"></a>Demandes

Sous l’onglet **Requêtes**, vous créez la première stratégie afin de spécifier qui peut demander le package d’accès, ainsi que les paramètres d’approbation et d’expiration. Plus tard, vous pourrez créer plus de stratégies de requêtes afin d’autoriser des groupes d’utilisateurs supplémentaires à demander le package d’accès avec leurs propres paramètres d’approbation.

![Onglet Requêtes de package d’accès](./media/entitlement-management-access-package-create/requests.png)

Suivez la procédure décrite dans l’une des sections suivantes.

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="review--create"></a>Vérifier + créer

Sous l’onglet **Vérifier + créer**, vous pouvez consulter vos paramètres et vérifier qu’ils ne contiennent aucune erreur de validation.

1. Vérifier les paramètres du package d’accès

    ![Package d’accès - Stratégie - Paramètre Activer la stratégie](./media/entitlement-management-access-package-create/review-create.png)

1. Cliquez sur **Créer** pour créer le package d’accès.

    Le nouveau package d’accès apparaît dans la liste des packages d’accès.

## <a name="next-steps"></a>Étapes suivantes

- [Partager un lien pour demander un package d’accès](entitlement-management-access-package-settings.md)
- [Modifier les rôles de ressources d’un package d’accès](entitlement-management-access-package-resources.md)
