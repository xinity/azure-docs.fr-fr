---
title: Forum aux questions sur Azure Kubernetes Service (AKS)
description: Recherchez des réponses à certaines des questions les plus fréquemment posées sur Azure Kubernetes Service (AKS).
services: container-service
author: mlearned
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 10/02/2019
ms.author: mlearned
ms.openlocfilehash: 4d736556147797bcd007bdab1b5328deeadea712
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827348"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Forum aux questions sur Azure Kubernetes Service (AKS)

Cet article aborde les questions fréquemment posées sur Azure Kubernetes Service (AKS).

## <a name="which-azure-regions-currently-provide-aks"></a>Quelles régions Azure proposent actuellement AKS ?

Pour obtenir la liste complète des régions disponibles, consultez [Régions AKS et disponibilité][aks-regions].

## <a name="does-aks-support-node-autoscaling"></a>AKS prend-il en charge la mise à l’échelle automatique des nœuds ?

Oui, la mise à l’échelle horizontale automatique des nœuds d’agent dans AKS est actuellement disponible en préversion. Pour obtenir des instructions, consultez [Mettre automatiquement à l'échelle un cluster pour répondre aux demandes applicatives dans AKS][aks-cluster-autoscaler]. La mise à l’échelle automatique d’AKS est basée sur l’[autoscaler Kubernetes][auto-scaler].

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Puis-je déployer AKS dans mon réseau virtuel existant ?

Oui, vous pouvez déployer un cluster AKS dans un réseau virtuel existant en utilisant la [fonctionnalité de mise en réseau avancée][aks-advanced-networking].

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>Puis-je limiter qui a accès au serveur d’API Kubernetes ?

Oui, vous pouvez limiter l’accès au serveur d’API Kubernetes en utilisant les [plages d’adresses IP autorisées du serveur d’API][api-server-authorized-ip-ranges], qui sont actuellement en préversion.

## <a name="can-i-make-the-kubernetes-api-server-accessible-only-within-my-virtual-network"></a>Puis-je rendre le serveur d’API Kubernetes accessible uniquement dans mon réseau virtuel ?

Pas pour l’instant, mais cela est prévu. Vous pouvez suivre l’avancement de cette option dans le [dépôt GitHub AKS][private-clusters-github-issue].

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>Puis-je avoir des machines virtuelles de différentes tailles dans un même cluster ?

Oui, vous pouvez utiliser différentes tailles de machines virtuelles dans votre cluster AKS en créant [plusieurs pools de nœuds][multi-node-pools], ce qui est actuellement en préversion.

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Des mises à jour sécurisées sont-elles appliquées aux nœuds de l’agent AKS ?

Azure applique automatiquement les correctifs de sécurité pour les nœuds Linux de votre cluster selon une planification nocturne. Toutefois, vous êtes chargé de vous assurer que ces nœuds Linux sont redémarrés selon les besoins. Vous avez plusieurs options pour redémarrer les nœuds :

- Manuellement, via le portail Azure ou l’interface de ligne de commande Azure.
- En mettant à niveau votre cluster AKS. Le cluster met automatiquement à niveau des [nœuds drain et cordon][cordon-drain], avant de mettre en ligne un nouveau nœud avec la dernière image Ubuntu et une nouvelle version du correctif ou une version Kubernetes mineure. Pour plus d’informations, consultez [Mettre à niveau un cluster AKS][aks-upgrade].
- En utilisant [Kured](https://github.com/weaveworks/kured), un démon de redémarrage open source pour Kubernetes. Kured s’exécute en tant que [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) et analyse chaque nœud à la recherche d’un fichier indiquant qu’un redémarrage est nécessaire. Au sein du cluster, les redémarrages du système d’exploitation sont gérés par le même [processus d’isolation et de drainage][cordon-drain] que celui appliqué pour une mise à niveau de cluster.

Pour plus d’informations sur l’utilisation de kured, consultez [Appliquer les mises à jour de sécurité et de noyau aux nœuds dans AKS][node-updates-kured].

### <a name="windows-server-nodes"></a>Nœuds Windows Server

Pour les nœuds Windows Server (actuellement en préversion dans AKS), Windows Update ne s’exécute pas et n’applique pas automatiquement les dernières mises à jour. Suivez une planification régulière basée sur le cycle de mise à jour de Windows et votre propre processus de validation pour effectuer une mise à niveau sur le cluster ou le ou les pools de nœuds Windows Server dans votre cluster AKS. Ce processus de mise à niveau crée des nœuds qui exécutent la dernière image et les derniers correctifs de Windows Server, puis supprime les anciens nœuds. Pour plus d’informations sur ce processus, consultez [Mettre à niveau un pool de nœuds dans AKS][nodepool-upgrade].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Pourquoi deux groupes de ressources sont-ils créés avec AKS ?

AKS s’appuie sur différentes ressources de l'infrastructure Azure, notamment les groupes de machines virtuelles identiques, réseaux virtuels et disques managés. Cela vous permet de tirer parti des nombreuses fonctionnalités essentielles de la plateforme Azure dans l’environnement Kubernetes managé fourni par AKS. Par exemple, la plupart des machines virtuelles Azure peuvent être directement utilisées avec AKS. En outre, les Réservations Azure permettent de bénéficier automatiquement de remises sur ces ressources.

Pour permettre cette architecture, chaque déploiement AKS s’étend sur deux groupes de ressources :

1. Vous créez le premier groupe de ressources. Ce groupe contient uniquement la ressource de service Kubernetes. Le fournisseur de ressources AKS crée automatiquement le second groupe de ressources au cours du déploiement. Un exemple du second groupe de ressources est *MC_myResourceGroup_myAKSCluster_eastus*. Pour obtenir des informations sur la façon de spécifier le nom de ce second groupe de ressources, consultez la section suivante.
1. Le second groupe de ressources, nommé *groupe de ressources de nœud*, contient toutes les ressources d’infrastructure associées au cluster. Ces ressources incluent les machines virtuelles de nœud Kubernetes, la mise en réseau et le stockage. Par défaut, le nom du groupe de ressources de nœud ressemble à ceci : *MC_myResourceGroup_myAKSCluster_eastus*. AKS supprime automatiquement la ressource de nœud à chaque fois que le cluster est supprimé. Cette ressource doit donc être utilisée uniquement pour les ressources qui partagent le cycle de vie du cluster.

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>Puis-je nommer mon groupe de ressources d’infrastructure AKS comme je le veux ?

Oui. Par défaut, AKS nomme le groupe de ressources de nœud *MC_resourcegroupname_clustername_location*, mais vous pouvez également entrer votre propre nom.

Pour spécifier votre propre nom de groupe de ressources, installez la version *0.3.2* ou une version ultérieure de l’extension Azure CLI [aks-preview][aks-preview-cli]. Lorsque vous créez un cluster AKS à l’aide de la commande [az aks create][az-aks-create], utilisez le paramètre *--node-resource-group* et spécifiez un nom pour le groupe de ressources. Si vous [utilisez un modèle Azure Resource Manager][aks-rm-template] pour déployer un cluster AKS, vous pouvez définir le nom du groupe de ressources à l’aide de la propriété *nodeResourceGroup*.

* Le groupe de ressources secondaire est automatiquement créé par le fournisseur de ressources Azure dans votre propre abonnement.
* Vous pouvez spécifier un nom de groupe de ressources personnalisé uniquement lorsque vous créez le cluster.

Lorsque vous travaillez avec le groupe de ressources de nœud, n’oubliez pas que vous ne pouvez pas :

* Spécifier un groupe de ressources existant pour le groupe de ressources de nœud.
* Spécifier un autre abonnement pour le groupe de ressources de nœud.
* Modifier le nom du groupe de ressources de nœud une fois que le cluster a été créé.
* Spécifier des noms pour les ressources managées au sein du groupe de ressources de nœud.
* Modifier ou supprimer les balises des ressources managées au sein du groupe de ressources de nœud. (Consultez des informations supplémentaires dans la section suivante.)

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>Puis-je modifier les balises et d’autres propriétés des ressources AKS dans le groupe de ressources de nœud ?

Si vous modifiez ou supprimez des balises créées par Azure et d’autres propriétés de ressources dans le groupe de ressources de nœud, vous pouvez obtenir des résultats inattendus tels que des erreurs de mise à l’échelle et de mise à niveau. AKS vous permet de créer et modifier des balises personnalisées. Vous souhaiterez peut-être créer ou modifier des balises personnalisées, par exemple, pour affecter une unité commerciale ou un centre de coûts. En modifiant les ressources du groupe de ressources de nœud dans le cluster AKS, vous empêchez d’atteindre l’objectif de niveau de service (SLO). Pour plus d'informations, consultez [AKS offre-t-il un contrat de niveau de service ?](#does-aks-offer-a-service-level-agreement)

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>Quels sont les contrôleurs d’admission Kubernetes qui sont pris en charge par AKS ? Les contrôleurs d’admission peuvent-ils être ajoutés ou supprimés ?

AKS prend en charge les [contrôleurs d’admission][admission-controllers] suivants :

- *NamespaceLifecycle*
- *LimitRanger*
- *ServiceAccount*
- *DefaultStorageClass*
- *DefaultTolerationSeconds*
- *MutatingAdmissionWebhook*
- *ValidatingAdmissionWebhook*
- *ResourceQuota*
- *DenyEscalatingExec*
- *AlwaysPullImages*

Actuellement, vous ne pouvez pas modifier la liste des contrôleurs d’admission dans AKS.

## <a name="is-azure-key-vault-integrated-with-aks"></a>Azure Key Vault est-il intégré à AKS ?

Pour l’instant, AKS n’est pas intégré nativement à Azure Key Vault. Toutefois, le [projet FlexVolume d’Azure Key Vault pour Kubernetes][keyvault-flexvolume] permet une intégration directe des pods Kubernetes aux secrets Key Vault.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Puis-je exécuter des conteneurs Windows Server sur AKS ?

Oui, les conteneurs Windows Server sont disponibles en préversion. Pour exécuter des conteneurs Windows Server dans AKS, vous créez un pool de nœuds qui exécute Windows Server en tant que système d’exploitation invité. Les conteneurs Windows Server peuvent utiliser uniquement Windows Server 2019. Pour commencer, consultez [Créer un cluster AKS avec un pool de nœuds Windows Server][aks-windows-cli].

La prise en charge des pools de nœuds dans Windows Server comprend certaines limitations qui font partie du projet amont Windows Server dans Kubernetes. Pour plus d’informations sur ces limitations, consultez [Limitations des conteneurs Windows Server dans AKS][aks-windows-limitations].

## <a name="does-aks-offer-a-service-level-agreement"></a>AKS offre-t-il un contrat de niveau de service ?

Dans un contrat de niveau de service (SLA), le fournisseur accepte de rembourser le coût du service au client si le niveau de service publié n’est pas respecté. AKS étant gratuit, aucun coût ne peut être remboursé, et dès lors, AKS ne dispose d'aucun contrat SLA formel. Toutefois, AKS cherche à maintenir une disponibilité d’au moins 99,5 % pour le serveur d’API Kubernetes.

Il est important de bien distinguer la disponibilité du service AKS, qui fait référence au temps de fonctionnement du plan de contrôle Kubernetes, et la disponibilité de votre charge de travail spécifique, qui s’exécute sur les machines virtuelles Azure. Même si le plan de contrôle n'est pas prêt et donc indisponible, vos charges de travail de cluster s’exécutant sur des machines virtuelles Azure peuvent continuer de fonctionner. Les machines virtuelles Azure sont des ressources payantes qui s'appuient sur un contrat SLA financier. Consultez [ceci pour plus d'informations](https://azure.microsoft.com/en-us/support/legal/sla/virtual-machines/v1_8/) sur le contrat SLA de machine virtuelle Azure et la manière d'améliorer cette disponibilité avec des fonctionnalités telles que les [Zones de disponibilité][availability-zones].

## <a name="why-cant-i-set-maxpods-below-30"></a>Pourquoi ne puis-je pas définir une valeur maxPods inférieure à 30 ?

Dans AKS, vous pouvez définir la valeur `maxPods` lorsque vous créez le cluster en utilisant les modèles Azure CLI et Azure Resource Manager. Toutefois, Kubenet et Azure CNI requièrent une *valeur minimale* (validée au moment de la création) :

| Mise en réseau | Minimale | Maximale |
| -- | :--: | :--: |
| Azure CNI | 30 | 250 |
| Kubenet | 30 | 110 |

Comme AKS est un service géré, nous déployons et gérons les modules complémentaires et les pods dans le cadre du cluster. Dans le passé, les utilisateurs pouvaient définir une valeur `maxPods` inférieure à la valeur que les pods managés nécessitaient pour s’exécuter (par exemple, 30). AKS calcule désormais le nombre minimal de pods à l’aide de cette formule : ((maxPods ou (maxPods * vm_count)) > minimum de pods de modules complémentaires managés.

Les utilisateurs ne peuvent pas remplacer la validation de la valeur minimale `maxPods`.

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>Puis-je appliquer des remises de réservation Azure sur mes nœuds d’agent AKS ?

Les nœuds d’agent AKS sont facturés en tant que machines virtuelles Azure standard. Par conséquent, si vous avez acheté des [réservations Azure][reservation-discounts] pour la taille de machine virtuelle que vous utilisez dans AKS, ces remises sont automatiquement appliquées.

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>Puis-je déplacer/migrer mon cluster entre des locataires Azure ?

Vous pouvez utiliser la commande `az aks update-credentials` pour déplacer un cluster AKS entre des locataires Azure. Suivez les instructions de la page [Choisir de mettre à jour ou de créer un principal du service](https://docs.microsoft.com/azure/aks/update-credentials), puis de la section [Mettre à jour le cluster AKS avec les nouvelles informations d’identification](https://docs.microsoft.com/azure/aks/update-credentials#update-aks-cluster-with-new-credentials).

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>Puis-je déplacer/migrer mon cluster entre des abonnements ?

Désolé... Le déplacement des clusters entre des abonnements n’est pas pris en charge pour le moment.

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>Puis-je déplacer mes clusters AKS de l'abonnement Azure actuel vers un autre abonnement ? 

Le déplacement de votre cluster AKS et de ses ressources associées entre des abonnements Azure n'est pas pris en charge.

## <a name="why-is-my-cluster-delete-taking-so-long"></a>Pourquoi la suppression de mon cluster est-elle si longue ? 

La plupart des clusters sont supprimés à la demande de l'utilisateur ; dans certains cas, notamment lorsque des clients apportent leur propre groupe de ressources ou effectuent des suppressions de tâches entre des groupes de ressources, l’opération peut prendre plus de temps ou échouer. Si vous rencontrez un problème avec les suppressions, vérifiez que le groupe de ressources ne contient aucun verrou, que toutes les ressources en dehors du groupe sont dissociées, etc.

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>Si j'ai des pods/déploiements à l'état 'NodeLost' ou 'Unknown', puis-je quand même mettre à niveau mon cluster ?

Vous pouvez, mais AKS ne le recommande pas. Idéalement, les mises à niveau devraient être effectuées lorsque l'état du cluster est connu et sain.

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>Si j'ai un cluster avec un ou plusieurs nœuds à l’état Unhealthy (non sain) ou shut down (arrêté), puis-je effectuer une mise à niveau ?

Non, veuillez supprimer tout nœud à l’état failed (échec) ou qui a été supprimé du cluster avant la mise à niveau.

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>J'ai lancé une suppression de cluster, mais l'erreur `[Errno 11001] getaddrinfo failed` s’affiche 

Le plus souvent, cela est dû au fait qu'un ou plusieurs groupes de sécurité réseau (NSG) sont encore utilisés et associés au cluster.  Veuillez les supprimer puis relancer la suppression.

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>J'ai effectué une mise à niveau, mais maintenant mes pods se retrouvent dans des boucles de plantage, et les readiness probes échouent.

Veuillez vérifier que votre principal de service n'a pas expiré.  Consultez : [Principal du service AKS](https://docs.microsoft.com/azure/aks/kubernetes-service-principal) et [Informations d'identification pour la mise à jour d’AKS](https://docs.microsoft.com/azure/aks/update-credentials).

## <a name="my-cluster-was-working-but-suddenly-can-not-provision-loadbalancers-mount-pvcs-etc"></a>Mon cluster fonctionnait, mais tout à coup il ne peut plus approvisionner LoadBalancers, monter des revendications de volume persistant (PVC), etc. 

Veuillez vérifier que votre principal de service n'a pas expiré.  Consultez : [Principal du service AKS](https://docs.microsoft.com/azure/aks/kubernetes-service-principal) et [Informations d'identification pour la mise à jour d’AKS](https://docs.microsoft.com/azure/aks/update-credentials).

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>Puis-je utiliser les API du groupe de machines virtuelles identiques pour effectuer une mise à l'échelle manuelle ?

Non, les opérations de mise à l'échelle à l'aide des API du groupe de machines virtuelles identiques ne sont pas prises en charge. Utilisez les API AKS (`az aks scale`).

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-0-nodes"></a>Puis-je utiliser les groupes de machines virtuelles identiques pour mettre à l'échelle manuellement des nœuds 0 ?

Non, les opérations de mise à l'échelle à l'aide des API du groupe de machines virtuelles identiques ne sont pas prises en charge.

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>Puis-je arrêter ou désallouer toutes mes machines virtuelles ?

Bien qu'AKS dispose de mécanismes de résilience capable de gérer une telle configuration et de récupérer à partir de celle-ci, c’est configuration n’est pas recommandée.

## <a name="can-i-use-custom-vm-extensions"></a>Puis-je utiliser des extensions de machine virtuelle personnalisées ?

Non, AKS est un service géré et la manipulation des ressources IaaS n'est pas prise en charge. Pour installer des composants personnalisés, etc. veuillez utiliser les API et les mécanismes Kubernetes. Par exemple, utilisez DaemonSets pour installer les composants nécessaires.

<!-- LINKS - internal -->

[aks-regions]: ./quotas-skus-regions.md#region-availability
[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./autoscaler.md
[virtual-kubelet]: virtual-kubelet.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration.md
[node-updates-kured]: node-updates-kured.md
[aks-preview-cli]: /cli/azure/ext/aks-preview/aks
[az-aks-create]: /cli/azure/aks#az-aks-create
[aks-rm-template]: /azure/templates/microsoft.containerservice/2019-06-01/managedclusters
[aks-cluster-autoscaler]: cluster-autoscaler.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[aks-windows-cli]: windows-container-cli.md
[aks-windows-limitations]: windows-node-limitations.md
[reservation-discounts]: ../billing/billing-save-compute-costs-reservations.md
[api-server-authorized-ip-ranges]: ./api-server-authorized-ip-ranges.md
[multi-node-pools]: ./use-multiple-node-pools.md
[availability-zones]: ./availability-zones.md

<!-- LINKS - external -->

[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol
[private-clusters-github-issue]: https://github.com/Azure/AKS/issues/948
