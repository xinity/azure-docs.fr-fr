---
title: Ajout ou modification des administrateurs d’abonnements Azure
description: Explique comment ajouter ou modifier un administrateur d'abonnement Azure à l'aide du contrôle d'accès en fonction du rôle (RBAC).
author: genlin
manager: dcscontentpm
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: banders
ms.openlocfilehash: 2054fbb7d0a9f450ad487fc0f03d0af920c6cc4b
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71260925"
---
# <a name="add-or-change-azure-subscription-administrators"></a>Ajout ou modification des administrateurs d’abonnements Azure


Pour gérer l’accès aux ressources Azure, vous devez disposer du rôle d’administrateur approprié. Azure a un système d’autorisation appelé [contrôle d’accès en fonction du rôle](../role-based-access-control/overview.md) (RBAC) avec plusieurs rôles intégrés parmi lesquels vous pouvez choisir. Vous pouvez attribuer ces rôles à différents niveaux (ou étendues) : groupe de gestion, abonnement, groupe de ressources, etc. Par défaut, la personne qui crée un abonnement Azure peut attribuer à d’autres utilisateurs un accès d’administration à un abonnement.

Cet article explique comment ajouter ou modifier le rôle d'administrateur pour un utilisateur qui a recours au RBAC au niveau de l'abonnement.

Microsoft vous recommande de gérer l'accès aux ressources à l'aide du RBAC. Toutefois, si vous utilisez encore le modèle de déploiement classique et que vous gérez les ressources classiques à l'aide du [module PowerShell d'Azure Service Management](https://docs.microsoft.com/powershell/module/servicemanagement/azure), vous devez utiliser un administrateur classique.

> [!TIP]
> Si vous utilisez le portail Azure seulement pour gérer les ressources classiques, vous n’avez pas besoin d’utiliser l’administrateur classique.

Pour plus d'informations, consultez [Déploiement Azure Resource Manager et déploiement classique](../azure-resource-manager/resource-manager-deployment-model.md) et [Administrateurs d'abonnements classiques Azure](../role-based-access-control/classic-administrators.md).

<a name="add-an-admin-for-a-subscription"></a>

## <a name="assign-a-subscription-administrator"></a>Attribuer un administrateur d’abonnement

Pour qu’un utilisateur devienne administrateur d’un abonnement Azure, un administrateur existant lui attribue le rôle de [Propriétaire](../role-based-access-control/built-in-roles.md#owner) (un rôle RBAC) au niveau de l’abonnement. Le rôle Propriétaire donne à l’utilisateur un accès total à toutes les ressources de l’abonnement, ainsi que le droit de déléguer l’accès à d’autres personnes. Ces étapes sont les mêmes que celles de n’importe quelle autre attribution de rôle.

Si vous ne savez pas qui est l’administrateur de compte d’un abonnement, procédez comme suit pour le découvrir.

1. Ouvrez la [page Abonnements dans le portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Sélectionnez l’abonnement que vous souhaitez vérifier, puis regardez sous **Paramètres**.
1. Sélectionner **Propriétés**. L’administrateur de compte de l’abonnement s’affiche dans la zone **Administrateur de compte** .

### <a name="to-assign-a-user-as-an-administrator"></a>Pour attribuer un utilisateur en tant qu’administrateur

1. Connectez-vous au portail Azure en tant que propriétaire de l’abonnement et ouvrez [Abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Cliquez sur l’abonnement pour lequel vous souhaitez accorder l’accès.

1. Cliquez sur **Contrôle d’accès (IAM)** .

1. Cliquez sur l’onglet **Attributions de rôles** afin d’afficher toutes les attributions de rôles pour cet abonnement.

    ![Capture d'écran illustrant les attributions de rôles](./media/billing-add-change-azure-subscription-administrator/role-assignments.png)

1. Cliquez sur **Ajouter** > **Ajouter une attribution de rôle** pour ouvrir le volet **Ajouter une attribution de rôle**.

    Si vous ne disposez pas des autorisations nécessaires pour attribuer des rôles, l'option est désactivée.

1. Dans la liste déroulante **Rôle**, sélectionnez le rôle **Propriétaire**.

1. Dans la liste **Sélectionner**, sélectionnez un utilisateur. Si vous ne voyez pas l’utilisateur dans la liste, vous pouvez taper dans la zone **Sélectionner** pour rechercher des noms d’affichage et des adresses e-mail dans l’annuaire.

    ![Capture d’écran montrant le rôle Propriétaire sélectionné](./media/billing-add-change-azure-subscription-administrator/add-role.png)

1. Cliquez sur **Enregistrer** pour attribuer le rôle.

    Après quelques instants, le rôle Propriétaire est attribué à l’utilisateur au niveau de l’abonnement.

## <a name="next-steps"></a>Étapes suivantes

* [Qu’est-ce que le contrôle d’accès en fonction du rôle (RBAC) ?](../role-based-access-control/overview.md)
* [Comprendre les différents rôles dans Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Guide pratique pour Associer ou ajouter un abonnement Azure à Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
* [Autorisations des rôles d'administrateur dans Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md)

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contacter le support technique

Si vous avez besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.
