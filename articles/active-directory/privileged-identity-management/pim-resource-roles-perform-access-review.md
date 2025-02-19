---
title: Réviser l’accès aux rôles de ressources Azure dans PIM - Azure Active Directory | Microsoft Docs
description: Découvrez comment réviser les rôles de ressources Azure dans Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 03/30/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22c0ce1a5eee4b8d4cc40c47dadd4bcdc74d03ba
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804101"
---
# <a name="review-access-to-azure-resource-roles-in-pim"></a>Réviser l’accès aux rôles de ressources Azure dans PIM
Azure Active Directory (Azure AD) Privileged Identity Management (PIM) simplifie la gestion des accès privilégiés aux ressources dans Azure pour les grandes entreprises. 

Si vous avez un rôle d’administrateur, l’administrateur des rôles privilégiés de votre organisation est susceptible de vous demander régulièrement de confirmer que vous avez toujours besoin de ce rôle dans le cadre de votre travail. Vous pouvez recevoir un e-mail contenant un lien ou accéder directement au [portail Azure](https://portal.azure.com). Suivez les étapes décrites dans cet article pour effectuer un auto-examen de vos rôles attribués.

Si vous êtes un administrateur de rôle privilégié intéressé par les révisions d’accès, consultez [Comment démarrer une révision de sécurité](pim-resource-roles-start-access-review.md)pour plus de détails.

## <a name="add-the-privileged-identity-management-application"></a>Ajout de l’application Privileged Identity Management
Vous pouvez utiliser l’application Azure Active Directory (Azure AD) PIM dans le [Portail Azure](https://portal.azure.com/) pour effectuer votre révision. Si vous ne disposez pas de l’application sur votre portail, suivez ces étapes pour commencer.

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
2. Sélectionnez votre nom d’utilisateur dans le coin supérieur droit du Portail Azure, puis sélectionnez le répertoire que vous allez utiliser.
3. Sélectionnez **Tous les services** et utilisez la zone **Filtre** pour rechercher *Azure AD Privileged Identity Management*.
4. Cochez **Épingler au tableau de bord**, puis sélectionnez **Créer**. L’application PIM s’ouvre.

## <a name="approve-or-deny-access"></a>Approuver ou refuser l'accès
Lorsque vous acceptez ou refusez l’accès, vous indiquez simplement au réviseur si vous utilisez toujours ce rôle ou non. Choisissez **Approuver** si vous souhaitez conserver le rôle, ou **Refuser** si vous n’avez plus besoin de l’accès. La modification de l’état prendra effet une fois que le réviseur aura appliqué les résultats.

Procédez comme suit pour rechercher et terminer la révision de l’accès :
1. Accédez à l’application Azure AD PIM.
2. Sélectionnez le panneau **Réviser l’accès**.

   ![Capture d’écran de l’application PIM, avec sélection du panneau Réviser l’accès](media/pim-resource-roles-perform-access-review/rbac-access-review-complete.png)

3. Sélectionnez la révision à terminer. 
4. Choisissez **Approuver** ou **Refuser**. Vous devrez peut-être motiver votre choix dans la zone **Indiquez une raison**.

   ![Capture d’écran de la page Détails de la révision](media/pim-resource-roles-perform-access-review/rbac-access-review-choice.png)

## <a name="next-steps"></a>Étapes suivantes

- [Effectuer une révision d’accès des rôles Azure AD dans PIM](pim-how-to-perform-security-review.md)
