---
title: Surveiller l’utilisation et les dépenses avec des alertes de coût | Microsoft Docs
description: Cet article explique comment les alertes de coût vous aident à surveiller l’utilisation et les dépenses dans Azure Cost Management.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management
manager: alavital
ms.custom: ''
ms.openlocfilehash: f1bf62596b6edcc6fff6572e431f3a777be93f05
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66002088"
---
# <a name="use-cost-alerts-to-monitor-usage-and-spending"></a>Utiliser les alertes de coût pour surveiller l’utilisation et les dépenses

Cet article vous aide à comprendre et à utiliser des alertes de gestion des coûts afin de surveiller votre utilisation et vos dépenses Azure. Les alertes de coût sont générées automatiquement en fonction du moment où les ressources Azure sont consommées. Les alertes affichent toutes les alertes actives de gestion des coûts et de facturation au même endroit. Lorsque votre consommation atteint un seuil donné, les alertes sont générées par Azure Cost Management. Il existe trois types d’alertes de coût : alertes budgétaires, alertes de crédit et alertes de quota de dépenses de département.

## <a name="budget-alerts"></a>Alertes budgétaires

Les alertes budgétaires vous avertissent quand les dépenses, selon l’utilisation ou le coût, atteignent ou dépassent la quantité définie dans la [condition d’alerte du budget](tutorial-acm-create-budgets.md). Les budgets de gestion des coûts sont créés à l’aide du Portail Microsoft Azure ou de l’API [Azure Consumption](https://docs.microsoft.com/rest/api/consumption).

Dans le Portail Microsoft Azure, les budgets sont définis par coût. À l’aide de l’API Azure Consumption, les budgets sont définis par le coût ou par l’utilisation de la consommation. Les alertes de budget prennent en charge à la fois les budgets basés sur le coût et ceux basés sur l’utilisation. Les alertes de budget sont générées automatiquement chaque fois que les conditions d’alertes budgétaires sont remplies. Vous pouvez afficher toutes les alertes de coût dans le Portail Microsoft Azure. Chaque fois qu’une alerte est générée, elle est affichée dans les alertes de coût. Un e-mail d’alerte est également envoyé aux personnes de la liste de destinataires d’alertes du budget.

## <a name="credit-alerts"></a>Alertes de crédit

Les alertes de crédit vous préviennent lorsque les engagements monétaires de crédit Azure sont consommés. Les engagements monétaires sont destinés aux organisations avec des Contrats Entreprise. Les alertes de crédit sont générées automatiquement à 90 % et à 100 % de votre solde de crédit Azure. Chaque fois qu’une alerte est générée, elle est reflétée dans les alertes de coût et dans l’e-mail envoyé aux propriétaires de compte.

## <a name="department-spending-quota-alerts"></a>Alertes de quota de dépenses du service

Les alertes de quota de dépense du service vous avertissent lorsque les dépenses du service atteignent un seuil fixe du quota. Les quotas de dépenses sont configurés dans le portail EA. Chaque fois qu’un seuil est atteint, un e-mail est envoyé aux propriétaires de service et cela apparaît dans les alertes de coût. Par exemple, 50 % ou 75 % du quota.

## <a name="supported-alert-features-by-offer-categories"></a>Fonctionnalités d’alerte prises en charge par catégories d’offres

La prise en charge des types d’alerte varie en fonction de votre type de compte Azure (offre Microsoft). Le tableau suivant présente les fonctionnalités d’alerte qui sont prises en charge par les différentes offres Microsoft. Vous pouvez consulter la liste complète des offres Microsoft dans l’article [Comprendre les données Cost Management](understand-cost-mgt-data.md).

| Type d’alerte | Contrat Entreprise | Contrat client Microsoft | Direct web/Paiement à l’utilisation |
|---|---|---|---|
| Budget | ✔ | ✔ | ✔ |
| Crédit | ✔ |✘ | ✘ |
| Quota de dépenses des services | ✔ | ✘ | ✘ |



## <a name="view-cost-alerts"></a>Afficher les alertes de coût

Pour afficher les alertes de coût, ouvrez l’étendue souhaitée dans le portail Azure et sélectionnez **Budgets** dans le menu. Utilisez le paramètre **Étendue** pour passer à une autre étendue. Sélectionnez **Alertes de coût** dans le menu. Pour plus d’informations sur les étendues, consultez [Comprendre et utiliser les étendues](understand-work-scopes.md).

![Exemple d’images d’alertes affichées dans Azure Cost Management](./media/cost-mgt-alerts-monitor-usage-spending/budget-alerts-fullscreen.png)

Le nombre total d’alertes actives et ignorées s’affiche sur la page des alertes de coût.

Toutes les alertes indiquent le type d’alerte. Une alerte budgétaire indique la raison pour laquelle elle a été générée et le nom du budget auquel elle s’applique. Chaque alerte affiche la date à laquelle elle a été générée, son état et l’étendue (abonnement ou gestion de groupe) à laquelle elle s’applique.

États possibles : **actif** et **ignoré**. L’état actif indique que l’alerte est toujours pertinente. L’état ignoré indique que quelqu’un a marqué l’alerte pour la définir en tant que non pertinente.

Sélectionnez une alerte dans la liste pour afficher les détails la concernant. Les détails de l’alerte affichent des informations supplémentaires sur celle-ci. Les alertes de budget incluent un lien vers le budget. Si une suggestion est disponible pour une alerte de budget, un lien vers la recommandation apparaît également. Les alertes de quota de dépenses de budget, de crédit et de service offrent un lien permettant d’effectuer une analyse dans l’analyse des coûts où vous pouvez explorer les coûts pour l’étendue de l’alerte. L’exemple suivant montre les dépenses d’un service avec les détails de l’alerte.

![Image illustrant les dépenses d’un service avec les détails de l’alerte](./media/cost-mgt-alerts-monitor-usage-spending/dept-spending-selected-with-credits.png)

Lorsque vous affichez les détails d’une alerte ignorée, vous pouvez la réactiver si une action manuelle est nécessaire. L’image suivante en montre un exemple.

![Image illustrant les options permettant d’ignorer et de réactiver](./media/cost-mgt-alerts-monitor-usage-spending/Dismiss-reactivate-options.png)

## <a name="see-also"></a>Voir aussi

- Si vous n’avez encore créé de budget ou défini des conditions d’alerte pour un budget, effectuez le didacticiel [Créer et gérer des budgets](tutorial-acm-create-budgets.md).
