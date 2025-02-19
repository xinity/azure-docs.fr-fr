---
title: Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur pour les applications SaaS | Documents Microsoft
description: Découvrez comment vérifier l’état des tâches d’approvisionnement automatique de comptes d’utilisateur et comment résoudre les problèmes d’approvisionnement d’utilisateurs individuels.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.date: 09/09/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2e5ef4067f22d0e9e015e4d9a646f8b92309010a
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71033524"
---
# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>Didacticiel : Créer des rapports sur le provisionnement automatique de comptes d’utilisateur

Azure Active Directory (Azure AD) comprend un [service d’approvisionnement de comptes d’utilisateur](user-provisioning.md) qui permet d’automatiser l’approvisionnement ou la suppression de comptes d’utilisateur dans des applications SaaS et d’autres systèmes, pour gérer le cycle de vie des identités de bout en bout. Azure AD prend en charge des connecteurs préintégrés d’approvisionnement d’utilisateur pour l’ensemble des applications et systèmes dans la section « Suggestions » de la [Galerie d’applications Azure AD](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1&subcategories=featured).

Cet article décrit comment vérifier l’état de l’approvisionnement des travaux après qu’ils ont été configurés, et résoudre les problèmes d’approvisionnement d’utilisateurs individuels et de groupes.

## <a name="overview"></a>Vue d'ensemble

Les connecteurs de provisionnement peuvent être configurés via le [portail Azure](https://portal.azure.com), en suivant la [documentation fournie](../saas-apps/tutorial-list.md) pour l’application prise en charge. Une fois configurés et opérationnels, les travaux de provisionnement peuvent faire l’objet de rapports créés à l’aide de l’une des deux méthodes suivantes :

* **Portail Azure** : Cet article aborde principalement la récupération des informations d’un rapport à partir du [portail Azure](https://portal.azure.com), qui fournit un rapport de synthèse, ainsi que des journaux d’audit détaillés sur le provisionnement d’une application donnée.
* **API d’audit** : Azure Active Directory fournit également une API d’audit qui permet la récupération par programmation des journaux d’audit détaillés sur l’approvisionnement. Pour une documentation spécifique sur l’utilisation de cette API, voir [Référence d’API d’audit Azure Active Directory](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit). Bien que cet article ne traite pas spécifiquement de l’utilisation de l’API, il détaille les types d’événements d’approvisionnement qui sont enregistrés dans le journal d’audit.

### <a name="definitions"></a>Définitions

Cet article utilise les termes suivants, définis ci-dessous :

* **Système source** : référentiel d’utilisateurs à partir duquel le service d’approvisionnement Azure AD se synchronise. Azure Active Directory est le système source pour la majorité des connecteurs de provisionnement pré-intégrés, mais il existe des exceptions (par exemple, la synchronisation entrante Workday).
* **Système cible** : référentiel des utilisateurs avec lequel le service d’approvisionnement Azure AD se synchronise. Il s’agit généralement d’une application SaaS (exemples : Salesforce, ServiceNow, G Suite, Dropbox for Business), mais dans certains cas, cela peut être un système local comme Active Directory (par exemple, la synchronisation entrante Workday dans Active Directory).

## <a name="getting-provisioning-reports-from-the-azure-portal"></a>Obtention de rapports d’approvisionnement du portail Azure

Pour obtenir des informations de rapport de provisionnement pour une application donnée, ouvrez le [portail Azure](https://portal.azure.com), puis **Azure Active Directory** &gt; **Applications d’entreprise** &gt; **Journaux de provisionnement (préversion)** dans la section **Activité**. Vous pouvez également accéder à l’application d’entreprise pour laquelle le provisionnement est configuré. Par exemple, si vous approvisionnez des utilisateurs pour LinkedIn Elevate, le chemin de navigation pour accéder aux détails de l’application est le suivant :

**Azure Active Directory &gt; Applications d’entreprise &gt; Toutes les applications &gt; LinkedIn Elevate**

À ce stade, vous pouvez accéder tant à la barre de progression de provisionnement qu’aux journaux de provisionnement, ces deux options étant décrites ci-dessous.

## <a name="provisioning-progress-bar"></a>Barre de progression de provisionnement

La [barre de progression](application-provisioning-when-will-provisioning-finish-specific-user.md#view-the-provisioning-progress-bar) de provisionnement est visible sous l’onglet **Provisionnement** pour une application donnée. Elle se trouve dans la section **État actuel**, sous **Paramètres**, et affiche l’état actuel du cycle initial ou incrémentiel. Cette section présente également les éléments suivants :

* Le nombre total d’utilisateurs et de groupes qui ont été synchronisés et figurent actuellement dans l’étendue pour l’approvisionnement entre le système source et le système cible.
* La dernière exécution de la synchronisation. Les synchronisations se produisent généralement toutes les 20 à 40 minutes après le [cycle initial](user-provisioning.md#what-happens-during-provisioning).
* Indique si un [cycle initial](user-provisioning.md#what-happens-during-provisioning) a été effectué.
* Indique si le processus de provisionnement a été mis en quarantaine, et la raison de la mise en quarantaine (par exemple, un échec de communication avec le système cible en raison d’informations d’identification d’administrateur non valides).

L’**État actuel** doit être le premier emplacement que les administrateurs consultent pour vérifier l’intégrité opérationnelle du travail de provisionnement.

 ![Rapport de synthèse](./media/check-status-user-account-provisioning/provisioning-progress-bar-section.png)

## <a name="provisioning-logs-preview"></a>Journaux de provisionnement (préversion)

Toutes les activités effectuées par le service de provisionnement sont enregistrées dans les [journaux de provisionnement](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) Azure AD. Vous pouvez accéder aux journaux de provisionnement dans le portail Azure en sélectionnant **Azure Active Directory** &gt; **Applications d’entreprise** &gt; **Journaux de provisionnement (préversion)** dans la section **Activité**. Vous pouvez rechercher les données de provisionnement en fonction du nom de l’utilisateur ou de l’identificateur dans le système source ou le système cible. Pour plus d’informations, consultez [Journaux de provisionnement (préversion)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). Les types d’événements d’activité journalisés sont les suivants :

## <a name="troubleshooting"></a>Résolution de problèmes

Le rapport de synthèse et les journaux de provisionnement jouent un rôle clé pour aider les administrateurs à résoudre différents problèmes de provisionnement de comptes utilisateur.

Pour obtenir des instructions basées sur un scénario concernant la manière de résoudre des problèmes d’approvisionnement automatique d’utilisateurs, voir [Problèmes lors de la configuration et de l’approvisionnement des utilisateurs pour une application](application-provisioning-config-problem.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](what-is-single-sign-on.md)
