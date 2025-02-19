---
title: Ajouter des utilisateurs individuels sous licence vers une licence basée sur le groupe - Azure Active Directory | Microsoft Docs
description: Comment migrer de licences utilisateur individuelles vers des licences basées sur le groupe avec Azure Active Directory
services: active-directory
keywords: Gestion des licences Azure AD
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 09/26/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: seohack1;it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 29ec9c05a7e7d594c64a450fe64e5bb0e0d1b7d0
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72034764"
---
# <a name="how-to-migrate-users-with-individual-licenses-to-groups-for-licensing"></a>Comment migrer des utilisateurs disposant de licences individuelles vers des groupes pour la gestion des licences

Vous pouvez disposer de licences existantes déployées pour les utilisateurs dans les organisations par le biais d’une « attribution directe », autrement dit utiliser des scripts PowerShell ou d’autres outils pour attribuer des licences utilisateur individuelles. Avant de commencer à utiliser une licence basée sur le groupe pour gérer les licences de votre organisation, vous pouvez utiliser ce plan de migration afin de remplacer en toute transparence les solutions existantes par une licence basée le groupe.

La chose la plus importante à retenir est que vous devez éviter toute situation dans laquelle la migration vers une licence basée sur le groupe amène les utilisateurs à perdre de manière temporaire les licences qui leur sont actuellement attribuées. Tous les processus susceptibles d’entraîner le retrait de licences doivent être évités afin d’éliminer le risque pour les utilisateurs de perdre l’accès aux services et à leurs données.

## <a name="recommended-migration-process"></a>Processus de migration recommandé

1. Votre gestion de l’attribution et de la suppression des licences utilisateur est actuellement automatisée (par exemple, avec PowerShell). Conservez ce paramétrage.

1. Créez un groupe de gestion des licences (ou choisissez les groupes existants à utiliser) et vérifiez que tous les utilisateurs requis sont ajoutés en tant que membres.

1. Attribuez les licences nécessaires à ces groupes. Vous devez refléter l’état de licence que votre procédure de gestion automatisée existante (par exemple, avec PowerShell) applique à ces utilisateurs.

1. Vérifiez que les licences ont bien été appliquées à tous les utilisateurs de ces groupes. Pour ce faire, vérifiez l’état de traitement de chaque groupe, ainsi que les journaux d’activité d’audit.

   - Vous pouvez vérifier ponctuellement certains utilisateurs individuels en examinant les détails de leur licence. Vous constaterez qu’ils disposent des mêmes licences attribuées « directement » et « héritées » de groupes.

   - Vous pouvez exécuter un script PowerShell pour [vérifier les méthodes d’attribution des licences aux utilisateurs](licensing-group-advanced.md#use-powershell-to-see-who-has-inherited-and-direct-licenses).

   - Lorsque la même licence produit est attribuée à l’utilisateur à la fois directement et par le biais d’un groupe, une seule licence est employée par l’utilisateur. Par conséquent, aucune licence supplémentaire n’est requise pour la migration.

1. Assurez-vous qu’aucune attribution de licence n’a échoué en vérifiant les utilisateurs en état d’erreur dans chaque groupe. Pour plus d’informations, consultez [Identification et résolution des problèmes de licence pour un groupe](licensing-groups-resolve-problems.md).

Envisagez de supprimer les affectations directes d’origine. Nous vous recommandons de le faire progressivement et de superviser d’abord le résultat sur une partie des utilisateurs. Vous pouvez laisser les affectations directes d’origine aux utilisateurs. Toutefois, si les utilisateurs quittent leurs groupes sous licence, ils conservent les licences directement affectées, ce que vous ne souhaitez peut-être pas.

## <a name="an-example"></a>exemple

Une organisation compte 1 000 utilisateurs. Tous les utilisateurs ont besoin de licences Office 365 Entreprise E3. Pour le moment, l’organisation dispose d’un script PowerShell qui s’exécute en local afin d’ajouter et de supprimer les licences des utilisateurs qui arrivent ou s’en vont. Néanmoins, elle souhaite remplacer ce script par des licences basées sur les groupes afin de pouvoir les gérer automatiquement par Azure AD.

Voici ce à quoi le processus de migration peut ressembler :

1. À l’aide du portail Azure, affectez la licence Office 365 E3 au groupe **Tous les utilisateurs** dans Azure AD.

1. Vérifiez que l’affectation de licence est effectuée pour tous les utilisateurs. Accédez à la page du groupe, sélectionnez **Licences**, puis vérifiez l’état de traitement en haut du panneau **Licences**.

   - Recherchez « Les dernières modifications de licence ont été appliquées à tous les utilisateurs » afin de confirmer la fin de l’opération.

   - Recherchez une notification en haut de la page, indiquant les utilisateurs pour lesquels des licences n’ont peut-être pas été correctement attribuées. Avons-nous manqué de licences pour certains utilisateurs ? Certains utilisateurs présentent-ils des plans de licence en conflit qui les empêchent d’hériter des licences de groupe ?

1. Vérifiez ponctuellement certains utilisateurs afin de veiller à ce qu’à la fois les licences directes et les licences de groupe leur soient bien appliquées. Accédez à la page de profil d’un utilisateur, sélectionnez **Licences**, puis examinez l’état des licences.

   - Voici l’état utilisateur attendu lors de la migration :

      ![l’état utilisateur attendu lors de la migration](./media/licensing-groups-migrate-users/expected-user-state.png)

     Cela confirme que l’utilisateur possède des licences directes et héritées. Nous voyons qu’Office 365 E3 est affecté.

   - Sélectionnez chaque licence pour voir quels services sont activés. Pour vérifier que les licences directes et de groupe activent exactement les mêmes services pour l’utilisateur, sélectionnez **Affectations**.

1. Après avoir confirmé l’équivalence des licences directes et des licences de groupe, vous pouvez commencer à supprimer les licences directes des utilisateurs. Vous pouvez tester cette procédure en supprimant ces licences pour des utilisateurs individuels sur le portail, puis exécuter des scripts d’automatisation afin de les supprimer en bloc. Voici l’exemple d’un même utilisateur dont les licences directes ont été supprimées par le biais du portail. L’état de licence reste inchangé, mais les attributions directes n’apparaissent plus.

   ![confirmer que les licences directes ont été supprimées](./media/licensing-groups-migrate-users/direct-licenses-removed.png)

## <a name="next-steps"></a>Étapes suivantes

Découvrez-en plus sur d’autres scénarios de gestion des licences de groupe :

- [What is group-based licensing in Azure Active Directory? (Présentation des licences basées sur le groupe dans Azure Active Directory)](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
- [Affectation de licences à un groupe dans Azure Active Directory](licensing-groups-assign.md)
- [Identification et résolution des problèmes de licence pour un groupe dans Azure Active Directory](licensing-groups-resolve-problems.md)
- [Guide pratique pour migrer des utilisateurs entre des licences de produit à l’aide de la gestion de licences basée sur des groupes dans Azure Active Directory](licensing-groups-change-licenses.md)
- [Azure Active Directory group-based licensing additional scenarios (Autres scénarios de licence basée sur le groupe Azure Active Directory)](licensing-group-advanced.md)
- [Exemples PowerShell pour les licences basées sur les groupes dans Azure Active Directory](licensing-ps-examples.md)
