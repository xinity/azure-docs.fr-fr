---
title: Qu’est-ce que la fédération avec Azure AD ? | Microsoft Docs
description: Décrit la fédération avec Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 52f9832a4307ea9519df448f192eeca70043a6d6
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035168"
---
# <a name="what-is-federation-with-azure-ad"></a>Qu’est-ce que la fédération avec Azure AD ?

La fédération est une collection de domaines pour lesquels une confiance est établie. Le niveau de confiance peut varier, mais il inclut généralement l’authentification et presque toujours l’autorisation. Une fédération classique peut inclure plusieurs organisations qui ont établi une confiance pour un accès partagé à un ensemble de ressources.

Vous pouvez fédérer votre environnement local avec Azure AD et utiliser cette fédération pour l’authentification et l’autorisation.  Cette méthode de connexion garantit que toutes les opérations d’authentification de l’utilisateur se produisent localement.  Cette méthode permet aux administrateurs d’implémenter des niveaux de contrôle d’accès plus stricts. La fédération est disponible avec AD FS et PingFederate.

![Identité fédérée](./media/whatis-hybrid-identity/federated-identity.png)


> [!TIP]
> Si vous choisissez d’utiliser la Fédération avec Active Directory Federation Services (AD FS), vous avez la possibilité de configurer la synchronisation de hachage de mot de passe en tant que dispositif de secours en cas de défaillance de votre infrastructure AD FS.


## <a name="next-steps"></a>Étapes suivantes

- [Qu’est-ce que l’identité hybride ?](https://docs.microsoft.com/en-us/azure/active-directory/hybrid/whatis-hybrid-identity)
- [Présentation d’Azure AD Connect et Connect Health](whatis-azure-ad-connect.md)
- [Qu’est-ce que la synchronisation de hachage de mot de passe ?](whatis-phs.md)
- [Qu’est-ce que la fédération ?](whatis-fed.md)
- [Qu’est-ce que l’authentification unique ?](how-to-connect-sso.md)
- [Fonctionnement de la fédération](how-to-connect-fed-whatis.md)
- [Fédération avec PingFederate](how-to-connect-install-custom.md#configuring-federation-with-pingfederate)
