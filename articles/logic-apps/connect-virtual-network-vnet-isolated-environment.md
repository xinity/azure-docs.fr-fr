---
title: Se connecter à des réseaux virtuels Azure avec un environnement de service d’intégration (ISE) - Azure Logic Apps
description: Créez un environnement de service d’intégration (ISE) pouvant accéder aux réseaux virtuels Azure à partir d’Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: 4c4eb5a6cb7527bcb3eb21beebb8063b0bd021d3
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72680476"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Connexion à des réseaux virtuels Azure à partir d’Azure Logic Apps à l'aide d'un environnement de service d’intégration (ISE)

Pour les scénarios où vos applications logiques et vos comptes d’intégration ont besoin d’accéder à un [réseau virtuel Azure](../virtual-network/virtual-networks-overview.md), créez un [*environnement de service d’intégration* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). Un environnement de service d’intégration est un environnement privé et isolé, qui utilise un stockage dédié et d’autres ressources conservées séparément du service Logic Apps public ou « mondial ». Cette séparation réduit également l’impact que d’autres locataires Azure peuvent avoir sur les performances de vos applications.

Quand vous créez un environnement ISE, Azure *l’injecte* dans votre réseau virtuel Azure, qui déploie ensuite le service Logic Apps dans votre réseau virtuel. Quand vous créez une application logique ou un compte d’intégration, sélectionnez votre environnement ISE comme emplacement. Votre application logique ou votre compte d’intégration peut ensuite accéder directement à des ressources, comme des machines virtuelles, des serveurs, des systèmes et des services, dans votre réseau virtuel.

![Sélection d’un environnement de service d’intégration](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

> [!IMPORTANT]
> Pour que les applications logiques et les comptes d’intégration fonctionnent ensemble dans un environnement ISE, il faut que tous deux utilisent le *même ISE* que leur emplacement.

Un ISE dispose de limites accrues quant à la durée d’exécution, la conservation du stockage, le débit, le délai d’attente des requêtes et réponses HTTP, la taille des messages et les requêtes de connecteur personnalisé. Pour plus d’informations, consultez [Limites et configuration pour Azure Logic Apps](logic-apps-limits-and-config.md). Pour en savoir plus sur les ISE, consultez [Accéder aux ressources Réseau virtuel Microsoft Azure à partir d’Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

Cet article vous explique comment effectuer ces tâches :

* Vérifiez que tous les ports nécessaires sur votre réseau virtuel sont ouverts afin que le trafic puisse transiter à travers votre environnement de service d’intégration (ISE), entre les sous-réseaux dans ce réseau virtuel.

* Créez votre ISE.

* Ajoutez de la capacité supplémentaire à votre ISE.

> [!IMPORTANT]
> Les applications logiques, les déclencheurs et actions intégrés et les connecteurs qui s’exécutent dans votre ISE utilisent un autre plan de tarification que celui basé sur la consommation. Pour plus d’informations sur la tarification et la facturation des environnements de service d’intégration, consultez le [modèle de tarif pour Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing). Pour connaître la tarification, consultez [Modèle de tarif pour Azure Logic Apps](../logic-apps/logic-apps-pricing.md).

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, [inscrivez-vous pour bénéficier d’un compte Azure gratuit](https://azure.microsoft.com/free/).

* Un [réseau virtuel Azure](../virtual-network/virtual-networks-overview.md). Si vous n’avez pas de réseau virtuel, découvrez comment [créer un réseau virtuel Azure](../virtual-network/quick-create-portal.md). 

  * Votre réseau virtuel doit comporter quatre sous-réseaux *vides* pour la création et le déploiement de ressources dans votre ISE. Vous pouvez créer ces sous-réseaux à l’avance, ou attendre de créer votre ISE où vous pouvez créer vos sous-réseaux simultanément. En savoir plus sur [exigences des sous-réseaux](#create-subnet).

  * Les noms des sous-réseaux doivent commencer par un caractère alphabétique ou un trait de soulignement, et les caractères suivants sont interdits : `<`, `>`, `%`, `&`, `\\`, `?`, `/`. 
  
  * Si vous souhaitez déployer l’environnement ISE par le biais d’un modèle Azure Resource Manager, assurez-vous d’abord de déléguer un sous-réseau vide à Microsoft.Logic/integrationServiceEnvironment. Vous n’avez pas besoin de procéder à cette délégation quand vous effectuez le déploiement par le biais du portail Azure.

  * Assurez-vous que votre réseau virtuel [mette à disposition ces ports](#ports) pour permettre le bon fonctionnement et l'accessibilité de votre ISE.

  * Si vous utilisez [ExpressRoute](../expressroute/expressroute-introduction.md), qui fournit une connexion privée aux services de cloud computing Microsoft, vous devez [créer une table de route](../virtual-network/manage-route-table.md) comportant l'itinéraire suivant, et lier cette table à chaque sous-réseau utilisé par votre ISE :

    **Nom** : <*nom d’itinéraire*><br>
    **Préfixe de l’adresse** : 0.0.0.0/0<br>
    **Tronçon suivant** : Internet

* Si vous souhaitez utiliser des serveurs DNS personnalisés pour votre réseau virtuel Azure, [configurez ces serveurs en suivant ces étapes](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) avant de déployer votre ISE sur votre réseau virtuel. Sinon, chaque fois que vous changez de serveur DNS, vous devez également redémarrer votre ISE.

  > [!IMPORTANT]
  > Si vous modifiez les paramètres de votre serveur DNS après avoir créé un ISE, veillez à redémarrer ce dernier. Pour plus d'informations sur la gestion des paramètres d'un serveur DNS, consultez [Créer, modifier ou supprimer un réseau virtuel](../virtual-network/manage-virtual-network.md#change-dns-servers).

<a name="ports"></a>

## <a name="check-network-ports"></a>Vérifier les ports réseau

Lorsque vous utilisez un ISE avec un réseau virtuel Azure, il arrive souvent qu'un ou plusieurs ports soient bloqués. Les connecteurs que vous utilisez pour créer les connexions entre votre ISE et le système de destination peuvent également avoir leurs propres exigences en matière de port. Par exemple, si vous communiquez avec un système FTP en utilisant le connecteur FTP, assurez-vous que le port que vous utilisez sur votre système FTP, comme le port 21 pour l'envoi de commandes, est disponible. Pour veiller à ce que votre ISE reste accessible et opérationnel, ouvrez les ports spécifiés dans le tableau ci-dessous. Sinon, si certains des ports requis ne sont pas disponibles, votre ISE cesse de fonctionner.

> [!IMPORTANT]
> Les ports source étant éphémères, affectez-leur la valeur `*` pour toutes les règles.
> Pour la communication au sein de vos sous-réseaux, votre ISE exige que vous ouvriez tous les ports de ces sous-réseaux.

* Si vous avez créé un réseau virtuel et des sous-réseaux sans contraintes, vous n'avez pas besoin de configurer des [groupes de sécurité réseau (NSG)](../virtual-network/security-overview.md#network-security-groups) dans votre réseau virtuel pour contrôler le trafic sur les sous-réseaux.

* Sur un réseau virtuel existant, vous pouvez *facultativement* configurer des groupes de sécurité réseau en [filtrant le trafic réseau sur les sous-réseaux](../virtual-network/tutorial-filter-network-traffic.md). Si vous choisissez cet itinéraire, veillez à ouvrir les ports spécifiés dans le tableau ci-dessous sur le réseau virtuel sur lequel vous souhaitez configurer les groupes de sécurité réseau. Si vous utilisez des [règles de sécurité NSG](../virtual-network/security-overview.md#security-rules), les protocoles TCP et UDP sont tous deux nécessaires.

* Si votre réseau virtuel comporte déjà des groupes de sécurité réseau ou des pare-feu, veillez à ouvrir les ports spécifiés dans le tableau ci-dessous. Si vous utilisez des [règles de sécurité NSG](../virtual-network/security-overview.md#security-rules), les protocoles TCP et UDP sont tous deux nécessaires.

Le tableau suivant désigne les ports de votre réseau virtuel utilisés par votre ISE et les emplacements où ces ports sont utilisés. Les [balises de service de Resource Manager](../virtual-network/security-overview.md#service-tags) représentent un groupe de préfixes d’adresses IP qui permet de simplifier la création de règles de sécurité.

| Objectif | Direction | Ports de destination | Balise du service source | Identification de destination | Notes |
|---------|-----------|-------------------|--------------------|-------------------------|-------|
| Communication depuis Azure Logic Apps | Règle de trafic sortant | 80, 443 | VirtualNetwork | Internet | Le port dépend du service externe avec lequel le service Logic Apps communique |
| Azure Active Directory | Règle de trafic sortant | 80, 443 | VirtualNetwork | AzureActiveDirectory | |
| Dépendance du Stockage Azure | Règle de trafic sortant | 80, 443 | VirtualNetwork | Stockage | |
| Communication interne aux sous-réseaux | Trafic entrant et sortant | 80, 443 | VirtualNetwork | VirtualNetwork | Pour les communications entre sous-réseaux |
| Communication vers Azure Logic Apps | Trafic entrant | 443 | Points de terminaison d’accès interne : <br>VirtualNetwork <p><p>Points de terminaison d’accès externe : <br>Internet <p><p>**Remarque**: Ces points de terminaison font référence au paramètre du point de terminaison [sélectionné lors de la création de votre ISE](#create-environment). Pour plus d’informations, consultez l’article [Accès au point de terminaison](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). | VirtualNetwork | L’adresse IP de l’ordinateur ou du service qui appelle n’importe quel déclencheur de requête ou webhook qui existe dans votre application logique. Fermer ou bloquer ce port empêche les appels HTTP vers Logic Apps avec les déclencheurs de requête. |
| Historique des exécutions d’une application logique | Trafic entrant | 443 | Points de terminaison d’accès interne : <br>VirtualNetwork <p><p>Points de terminaison d’accès externe : <br>Internet <p><p>**Remarque**: Ces points de terminaison font référence au paramètre du point de terminaison [sélectionné lors de la création de votre ISE](#create-environment). Pour plus d’informations, consultez l’article [Accès au point de terminaison](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). | VirtualNetwork | L’adresse IP de l’ordinateur à partir duquel vous affichez l’historique des exécutions de l’application logique. Bien que la fermeture ou le blocage de ce port ne vous empêche pas d’afficher l’historique des exécutions, vous ne pouvez pas afficher les entrées et sorties pour chaque étape dans cet historique des exécutions. |
| Gestion des connexions | Règle de trafic sortant | 443 | VirtualNetwork  | AppService | |
| Publier des journaux de diagnostic et métriques | Règle de trafic sortant | 443 | VirtualNetwork  | AzureMonitor | |
| Communication à partir d’Azure Traffic Manager | Trafic entrant | 443 | AzureTrafficManager | VirtualNetwork | |
| Concepteur Logic Apps - Propriétés dynamiques | Trafic entrant | 454 | Internet | VirtualNetwork | Les demandes proviennent des [adresses IP de point de terminaison d’accès entrantes de Logic Apps cette région](../logic-apps/logic-apps-limits-and-config.md#inbound). |
| Dépendance de gestion App Service | Trafic entrant | 454, 455 | AppServiceManagement | VirtualNetwork | |
| Déploiement du connecteur | Trafic entrant | 454 | AzureConnectors | VirtualNetwork | Nécessaire pour le déploiement et la mise à jour des connecteurs. La fermeture ou le blocage de ce port entraîne l’échec des déploiements de l’ISE et empêche les correctifs ou mises à jour du connecteur. |
| Déploiement de la stratégie de connecteur | Trafic entrant | 3443 | AppService | VirtualNetwork | Nécessaire pour le déploiement et la mise à jour des connecteurs. La fermeture ou le blocage de ce port entraîne l’échec des déploiements de l’ISE et empêche les correctifs ou mises à jour du connecteur. |
| Dépendance Azure SQL | Règle de trafic sortant | 1433 | VirtualNetwork | SQL | |
| Azure Resource Health | Règle de trafic sortant | 1886 | VirtualNetwork | AzureMonitor | Pour publier l’état d’intégrité sur Resource Health |
| Gestion des API - Point de terminaison de gestion | Trafic entrant | 3443 | APIManagement | VirtualNetwork | |
| Dépendance du journal pour la stratégie Event Hub et l’agent de surveillance | Règle de trafic sortant | 5672 | VirtualNetwork | Event Hub | |
| Accès aux instances du Cache Azure pour Redis entre instances de rôle | Trafic entrant <br>Règle de trafic sortant | 6379-6383 | VirtualNetwork | VirtualNetwork | En outre, pour qu’ISE fonctionne avec le cache Azure pour Redis, vous devez ouvrir les [ports entrants et sortants décrits dans la FAQ sur le cache Azure pour Redis](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements). |
| Azure Load Balancer | Trafic entrant | * | AzureLoadBalancer | VirtualNetwork | |
||||||

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Créer votre environnement de service d’intégration

Pour créer votre environnement de service d’intégration, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com), dans le menu principal d’Azure, sélectionnez **Créer une ressource**.
Dans la zone de recherche, entrez « environnement de service d’intégration » comme filtre.

   ![Créer une ressource](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. Dans le volet de la création de l’environnement de service d'intégration, choisissez **Créer**.

   ![Choisissez « Créer ».](./media/connect-virtual-network-vnet-isolated-environment/create-integration-service-environment.png)

1. Spécifiez ces informations pour votre environnement, puis choisissez **Vérifier + créer**, par exemple :

   ![Spécifier les informations pour l’environnement](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Propriété | Obligatoire | Value | Description |
   |----------|----------|-------|-------------|
   | **Abonnement** | OUI | <*Azure-subscription-name*> | Abonnement Azure à utiliser pour votre environnement |
   | **Groupe de ressources** | OUI | <*nom-groupe-de-ressources-Azure*> | Groupe de ressources Azure où vous voulez créer votre environnement |
   | **Nom de l’environnement de service d’intégration** | OUI | <*nom-environnement*> | Votre nom ISE, qui peut contenir uniquement des lettres, des chiffres, des traits d’union (`-`), des traits de soulignement (`_`) et des points (`.`). |
   | **Lieu** | OUI | <*région-centre de données-Azure*> | Région du centre de données Azure où déployer votre environnement |
   | **Référence (SKU)** | OUI | **Premium** ou **Développeur (aucun contrat SLA)** | Référence SKU d’ISE à créer et à utiliser. Pour connaître les différences entre ces références SKU, consultez [Références SKU d’ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level). <p><p>**Important !** Cette option est disponible uniquement lors de la création de votre ISE et ne peut pas être modifiée ultérieurement. |
   | **Capacité supplémentaire** | Premium : <br>OUI <p><p>Développeur : <br>Non applicable | Premium : <br>0 à 10 <p><p>Développeur : <br>Non applicable | Le nombre d’unités de traitement supplémentaires à utiliser pour cette ressource ISE. Pour ajouter de la capacité après création, consultez [Ajouter de la capacité à l’ISE](#add-capacity). |
   | **Point de terminaison de l'accès** | OUI | **Interne** ou **externe** | Le type de points de terminaison d’accès à utiliser avec votre ISE, qui déterminent si les déclencheurs de demande ou de webhook sur les applications logiques dans votre ISE peuvent recevoir des appels en dehors de votre réseau virtuel. Le type de point de terminaison affecte également l’accès aux entrées et aux sorties dans l’historique des exécutions de votre application logique. Pour plus d’informations, consultez l’article [Accès au point de terminaison](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). <p><p>**Important !** Cette option est disponible uniquement lors de la création de votre ISE et ne peut pas être modifiée ultérieurement. |
   | **Réseau virtuel** | OUI | <*Azure-virtual-network-name*> | Réseau virtuel Azure où vous voulez injecter votre environnement, pour que les applications logiques de cet environnement puissent accéder à votre réseau virtuel. Si vous n’avez pas de réseau, [créez d’abord un réseau virtuel Azure](../virtual-network/quick-create-portal.md). <p>**Important !** Vous pouvez effectuer cette injection *seulement*  quand vous créez votre ISE. |
   | **Sous-réseaux** | OUI | <*subnet-resource-list*> | Un environnement ISE nécessite quatre sous-réseaux *vides* pour la création et le déploiement des ressources dans votre environnement. Pour créer chaque sous-réseau, [suivez les étapes décrites dans ce tableau](#create-subnet). |
   |||||

   <a name="create-subnet"></a>

   **Créer un sous-réseau**

   Pour créer et déployer des ressources dans votre environnement, votre ISE a besoin de quatre sous-réseaux *vides* qui ne sont délégués à aucun service. Vous *ne pouvez pas changer* ces adresses de sous-réseaux après avoir créé votre environnement.
   
   > [!IMPORTANT]
   > 
   > Les noms des sous-réseaux doivent commencer par un caractère alphabétique ou un trait de soulignement (pas de chiffres), et les caractères suivants sont interdits : `<`, `>`, `%`, `&`, `\\`, `?`, `/`.
   
   En outre, chaque sous-réseau doit être conforme aux exigences suivantes :

   * Il utilise le format [CIDR (Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) et un espace d’adressage de Classe B.

   * Utilise au moins un `/27` dans l’espace d’adressage, car chaque sous-réseau doit avoir *au moins* 32 *adresses*. Par exemple :

     * `10.0.0.0/27` a 32 adresses car 2<sup>(32-27)</sup> est égal à 2<sup>5</sup> soit 32.

     * `10.0.0.0/24` a 256 adresses car 2<sup>(32-24)</sup> est égal à 2<sup>8</sup> soit 256.

     * `10.0.0.0/28` a seulement 16 adresses et est trop petit, car 2<sup>(32-28)</sup> représente 2<sup>4</sup>, ou 16.

     Pour en savoir plus sur le calcul des adresses, consultez [Blocs CIDR IPv4](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks).

   * Si vous utilisez [ExpressRoute](../expressroute/expressroute-introduction.md), vous devez [créer une table de route](../virtual-network/manage-route-table.md) comportant l'itinéraire suivant, et lier cette table à chaque sous-réseau utilisé par votre ISE :

     **Nom** : <*nom d’itinéraire*><br>
     **Préfixe de l’adresse** : 0.0.0.0/0<br>
     **Tronçon suivant** : Internet

   1. Sous la liste **Sous-réseaux**, choisissez **Gérer la configuration du sous-réseau**.

      ![Gérer la configuration du sous-réseau](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet.png)

   1. Dans le volet **Sous-réseaux**, choisissez **Sous-réseau**.

      ![Ajouter un sous-réseau](./media/connect-virtual-network-vnet-isolated-environment/add-subnet.png)

   1. Dans le volet **Ajouter un sous-réseau**, spécifiez ces informations.

      * **Nom** : Nom de votre sous-réseau
      * **Plage d’adresses (bloc CIDR)** : Plage de votre sous-réseau dans votre réseau virtuel, au format CIDR

      ![Détails de l’ajout d’un sous-réseau](./media/connect-virtual-network-vnet-isolated-environment/subnet-details.png)

   1. Une fois que vous avez terminé, sélectionnez **OK**.

   1. Répétez ces étapes pour trois autres sous-réseaux.

      > [!NOTE]
      > Si les sous-réseaux que vous tentez de créer ne sont pas valides, le portail Azure affiche un message mais ne bloque pas votre progression.

   Pour plus d’informations sur la création de sous-réseaux, consultez [Ajouter un sous-réseau à un réseau virtuel](../virtual-network/virtual-network-manage-subnet.md).

1. Une fois qu’Azure a validé les informations de votre ISE, choisissez **Créer**, par exemple :

   ![Après la validation, choisissez « Créer »](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure commence le déploiement de votre environnement, mais ce processus *peut prendre* jusqu’à deux heures. Pour vérifier l’état du déploiement, dans votre barre d’outils Azure, choisissez l’icône Notifications, qui ouvre le volet Notifications.

   ![Vérifier l’état du déploiement](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Un fois le déploiement terminé, Azure montre cette notification :

   ![Déploiement réussi](./media/connect-virtual-network-vnet-isolated-environment/deployment-success.png)

   Sinon, suivez les instructions du portail Azure pour la résolution des problèmes de déploiement.

   > [!NOTE]
   > Si le déploiement échoue ou si vous supprimez votre ISE, Azure peut prendre jusqu’à une heure avant de libérer vos sous-réseaux. Ce délai signifie que vous devrez peut-être attendre avant de réutiliser ces sous-réseaux dans un autre ISE.
   >
   > Si vous supprimez votre réseau virtuel, Azure a généralement besoin de jusqu'à deux heures pour libérer vos sous-réseaux, mais cette opération peut prendre plus longtemps. 
   > Lors de la suppression de réseaux virtuels, assurez-vous qu’aucune ressource n’est restée connectée. 
   > Consultez [Supprimer un réseau virtuel](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

1. Pour voir votre environnement, choisissez **Accéder à la ressource** si Azure n’accède pas automatiquement à votre environnement une fois le déploiement terminé.

1. Pour vérifier l’intégrité du réseau de votre ISE, consultez [Gérer votre environnement de service d’intégration](../logic-apps/ise-manage-integration-service-environment.md#check-network-health).

1. Pour commencer à créer des applications logiques et d’autres artefacts dans votre ISE, consultez [Ajouter des artefacts à des environnements de service d’intégration](../logic-apps/add-artifacts-integration-service-environment-ise.md).

<a name="add-capacity"></a>

## <a name="add-ise-capacity"></a>Ajouter de la capacité à l’ISE

L’unité de base d’ISE Premium dispose d’une capacité fixe ; si vous avez besoin de davantage de débit, vous pouvez ajouter des unités d’échelle, pendant la création ou après. Vous pouvez mise à l’échelle automatiquement en fonction des mesures de performances ou d’un nombre d’unités de traitement supplémentaires. Si vous choisissez la mise à l’échelle automatique en fonction des mesures, vous pouvez choisir à partir de différents critères et spécifier les conditions de seuil pour répondre à ces critères. La référence SKU Développeur n’inclut pas la capacité à ajouter des unités d’échelle.

1. Dans le portail Azure, recherchez votre ISE.

1. Pour consulter les mesures de performances et d’utilisation pour votre ISE, sélectionnez **Vue d’ensemble** dans le menu principal de votre ISE.

   ![Afficher l’utilisation pour l’ISE](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-usage.png)

1. Pour configurer la mise à l’échelle automatique, sous **Paramètres**, sélectionnez **Monter en charge**. Sous l’onglet **Configurer**, choisissez **Activer la mise à l’échelle automatique**.

   ![Activer la mise à l’échelle automatique](./media/connect-virtual-network-vnet-isolated-environment/scale-out.png)

1. Pour **Nom du paramètre de mise à l’échelle automatique**, fournissez un nom pour votre paramètre.

1. Dans la section **Par défaut**, choisissez soit **Mettre à l’échelle selon une métrique** soit **Mettre à l’échelle d’un nombre d’instances spécifique**.

   * Si vous choisissez la mise à l’échelle basée sur les instances, entrez un nombre d’unités de traitement entre 0 et 10 (inclus).

   * Si vous choisissez la méthode basée sur les mesures, procédez comme suit :

     1. Dans la section **Règles**, choisissez **+ Ajouter une règle**.

     1. Dans le volet **Règle de mise à l’échelle**, configurez vos critères et l’action à effectuer lorsque la règle se déclenche.

     1. Une fois que vous avez terminé, sélectionnez **Ajouter**.

1. Lorsque vous avez terminé de configurer vos paramètres de mise à l’échelle, enregistrez vos modifications.

## <a name="next-steps"></a>Étapes suivantes

* [Ajouter des artefacts à des environnements de service d’intégration](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Vérifier l’intégrité du réseau pour les environnements de service d’intégration](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
* En apprendre davantage sur le [Réseau virtuel Azure](../virtual-network/virtual-networks-overview.md)
* Découvrir l’[Intégration d’un réseau virtuel pour les services Azure](../virtual-network/virtual-network-for-azure-services.md)
