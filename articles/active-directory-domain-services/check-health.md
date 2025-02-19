---
title: Vérifier l’intégrité des services de domaine Azure Active Directory | Microsoft Docs
description: Découvrez comment vérifier l’intégrité d’un domaine managé Azure Active Directory Domain Services (Azure AD DS) et comprendre les messages d’état à l’aide du portail Azure.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: iainfou
ms.openlocfilehash: 50b142acb457d16abeb24f22d56b653a38aca76d
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70898240"
---
# <a name="check-the-health-of-an-azure-active-directory-domain-services-managed-domain"></a>Vérifier l’intégrité d’un domaine géré par les services de domaine Azure Active Directory

Azure Active Directory Domain Services (Azure AD DS) exécute des tâches en arrière-plan pour assurer que le domaine géré est intègre et à jour. Ces tâches incluent la réalisation de sauvegardes, l’application de mises à jour de sécurité et la synchronisation des données à partir d’Azure AD. En cas de problème avec le domaine managé Azure AD DS, ces tâches peuvent ne pas s’exécuter correctement. Pour examiner et résoudre les problèmes éventuels, vous pouvez vérifier l’état d’intégrité d’un domaine managé Azure AD DS à l’aide du portail Azure.

Cet article explique comment afficher l’état d’intégrité d’Azure AD DS et comprendre les informations ou les alertes affichées.

## <a name="view-the-health-status"></a>Afficher l’état d’intégrité

L’état d’intégrité d’un domaine managé Azure AD DS peut être vérifié à l’aide du portail Azure. Vous pouvez consulter les informations relatives à l’heure de la dernière sauvegarde et synchronisation avec Azure AD, ainsi que toutes les alertes qui indiquent un problème avec l’intégrité du domaine managé. Pour afficher l’état d’intégrité d’un domaine managé Azure AD DS, procédez comme suit :

1. Dans le portail Azure, recherchez et sélectionnez **Azure AD Domain Services**.
1. Sélectionnez votre domaine managé Azure AD DS, par exemple *contoso.com*.
1. Sur le côté gauche de la fenêtre de ressources Azure AD DS, sélectionnez **Intégrité**. L’exemple de capture d’écran suivant montre un domaine managé Azure AD DS intègre et l’état de la dernière sauvegarde et synchronisation d’Azure AD :

    ![Vue d’ensemble de la page d’intégrité dans le portail Azure présentant l’état d’Azure Active Directory Domain Services](./media/check-health/health-page.png)

Le timestamp *Dernière évaluation* de la page d’intégrité indique la date de la dernière vérification du domaine managé Azure AD DS. L’intégrité d’un domaine managé Azure AD DS est évaluée toutes les heures. Si vous apportez des modifications à un domaine managé Azure AD DS, attendez le prochain cycle d’évaluation pour voir l’état d’intégrité à jour.

L’état affiché dans le coin supérieur droit indique l’intégrité globale du domaine managé Azure AD DS. L’état prend en compte toutes les alertes existantes de votre domaine. Le tableau suivant détaille les indicateurs d’état disponibles :

| Statut | Icône | Explication |
| --- | :----: | --- |
| Exécution | <img src= "./media/active-directory-domain-services-alerts/running-icon.png" width = "15" alt="Green check mark for running"> | Le domaine managé Azure AD DS fonctionne normalement et n’a pas d’alertes critiques ou d’avertissement. Le domaine peut avoir des alertes d’information. |
| Doit être surveillé (avertissement) | <img src= "./media/active-directory-domain-services-alerts/warning-icon.png" width = "15" alt="Yellow exclamation mark for warning"> | Il n’existe aucune alerte critique sur le domaine managé Azure AD DS, mais il existe une ou plusieurs alertes d’avertissement qui doivent être traitées. |
| Doit être surveillé (critique) | <img src= "./media/active-directory-domain-services-alerts/critical-icon.png" width = "15" alt="Red exclamation mark for critical"> | Il y a une ou plusieurs alertes critiques sur le domaine managé Azure AD DS qui doivent être traitées. Vous avez peut-être également des alertes d’avertissement et/ou d’information. |
| Déploiement en cours | <img src= "./media/active-directory-domain-services-alerts/deploying-icon.png" width = "15" alt="Blue circular arrows for deploying"> | Le domaine Azure AD DS est en cours de déploiement. |

## <a name="understand-monitors-and-alerts"></a>Comprendre les analyses et les alertes

L’état d’intégrité d’un domaine managé Azure AD DS présente deux types d’informations : les analyses et les alertes. Les analyses indiquent l’heure à laquelle les tâches principales en arrière-plan se sont terminées. Les alertes fournissent des informations ou des suggestions pour améliorer la stabilité du domaine géré.

### <a name="monitors"></a>Analyses

Les analyses sont des zones d’un domaine managé Azure AD DS qui sont vérifiées régulièrement. S’il existe des alertes actives pour le domaine managé Azure AD DS, cela peut entraîner le signalement d’un problème par une des analyses. Azure AD Domain Services surveille actuellement les zones suivantes :

* Sauvegarde
* Synchronisation avec Azure AD

#### <a name="backup-monitor"></a>Analyse de sauvegarde

L’analyse de sauvegarde vérifie que les sauvegardes régulières automatisées du domaine managé Azure AD DS s’exécutent correctement. Le tableau suivant détaille l’état de l’analyse de sauvegarde disponible :

| Valeur de détail | Explication |
| --- | --- |
| Jamais sauvegardé | Cet état est normal pour les nouveaux domaines managés Azure AD DS. La première sauvegarde doit être créée 24 heures après le déploiement du domaine managé Azure AD DS. Si cet état persiste, [ouvrez une demande de support Azure][azure-support]. |
| La dernière sauvegarde a été effectuée entre 1 et 14 jours auparavant. | Cet intervalle de temps correspond à l’état attendu pour l’analyse de sauvegarde. Les sauvegardes régulières automatisées doivent avoir lieu au cours de cette période. |
| La dernière sauvegarde a été effectuée il y a plus de 14 jours. | Un intervalle de temps supérieur à deux semaines indique un problème avec les sauvegardes régulières automatisées. Les alertes critiques actives peuvent empêcher le domaine managé Azure AD DS d’être sauvegardé. Résolvez les alertes actives pour le domaine managé Azure AD DS. Si l’analyse de sauvegarde ne met pas à jour l’état pour signaler une sauvegarde récente, [ouvrez une demande de support Azure][azure-support]. |

#### <a name="synchronization-with-azure-ad-monitor"></a>Synchronisation avec Azure AD Monitor

Un domaine managé Azure AD DS se synchronise régulièrement avec Azure Active Directory. Le nombre d’utilisateurs et d’objets de groupe et le nombre de modifications effectuées dans l’annuaire Azure AD depuis la dernière synchronisation, affectent le temps nécessaire à la synchronisation. Si le domaine managé Azure AD DS a été synchronisé pour la dernière fois il y a plus de trois jours, recherchez et résolvez les alertes actives. Si l’analyse de synchronisation ne met pas à jour l’état pour afficher une synchronisation récente, [ouvrez une demande de support Azure][azure-support].

### <a name="alerts"></a>Alertes

Des alertes sont générées pour les problèmes d’un domaine managé Azure AD DS qui doivent être adressés pour que le service s’exécute correctement. Chaque alerte explique le problème et fournit une URL qui décrit les étapes spécifiques pour résoudre le problème. Pour plus d’informations sur les alertes possibles et leurs solutions, consultez [Dépannage des alertes](troubleshoot-alerts.md).

Les alertes d’état d’intégrité sont classées selon les niveaux de gravité suivants :

 * Les **alertes critiques** sont des problèmes ayant un impact sérieux sur le domaine managé Azure AD DS. Ces alertes doivent être traitées immédiatement. La plateforme Azure ne peut pas surveiller, gérer, mettre à jour et synchroniser le domaine managé tant que les problèmes ne sont pas résolus.
 * Les **alertes d’avertissement** vous informent des problèmes qui peuvent avoir un impact sur les opérations du domaine managé Azure AD DS si le problème persiste. Ces alertes fournissent aussi des recommandations pour sécuriser le domaine managé.
 * Les **alertes d’information** sont des notifications qui n’ont pas d’impact négatif sur le domaine managé Azure AD DS. Les alertes d’information fournissent des informations sur ce qui se passe dans le domaine managé.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les alertes affichées dans la page d’état d’intégrité, consultez [Résoudre les alertes sur votre domaine managé][troubleshoot-alerts]

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[troubleshoot-alerts]: troubleshoot-alerts.md
