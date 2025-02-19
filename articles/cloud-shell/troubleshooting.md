---
title: Résolution des problèmes Azure Cloud Shell | Microsoft Docs
description: Dépannage d’Azure Cloud Shell
services: azure
documentationcenter: ''
author: maertendMSFT
manager: hemantm
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: damaerte
ms.openlocfilehash: 91dc87cd6bda93663fb4b4eae3d498ae56ba4b3e
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169594"
---
# <a name="troubleshooting--limitations-of-azure-cloud-shell"></a>Résolution des problèmes et limitations d’Azure Cloud Shell

Les solutions connues pour la résolution des problèmes d’Azure Cloud Shell sont les suivantes :

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-troubleshooting"></a>Résolution générale des problèmes

### <a name="early-timeouts-in-firefox"></a>Délais d’expiration anticipés dans Firefox

- **Détails** : Cloud Shell utilise un websocket ouvert pour passer les entrées/sorties à votre navigateur. Firefox a des stratégies prédéfinies qui peuvent fermer le websocket prématurément et causer des délais d’expiration anticipés dans Cloud Shell.
- **Résolution** : ouvrez Firefox et accédez à « about:config » dans la barre d’URL. Recherchez « network.websocket.timeout.ping.request » et remplacez la valeur 0 par 10.

### <a name="disabling-cloud-shell-in-a-locked-down-network-environment"></a>Désactiver Cloud Shell dans un environnement réseau verrouillé

- **Détails** : les administrateurs peuvent être amenés à désactiver l’accès à Cloud Shell pour les utilisateurs. Cloud Shell utilise l’accès au domaine `ux.console.azure.com`, qui peut être refusé, interrompant tout accès aux points d’entrée de Cloud Shell, y compris portal.azure.com, shell.azure.com, l’extension de compte Azure Visual Studio Code et docs.microsoft.com.
- **Résolution** : limitez l’accès de `ux.console.azure.com` à votre environnement par le biais des paramètres réseau. L’icône Cloud Shell reste présente sur portal.azure.com, mais ne permettra pas la connexion au service.

### <a name="storage-dialog---error-403-requestdisallowedbypolicy"></a>Boîte de dialogue Stockage – Erreur : 403 RequestDisallowedByPolicy

- **Détails** : lorsque vous créez un compte de stockage par le biais de Cloud Shell, l’opération échoue en raison d’une stratégie Azure appliquée par votre administrateur. Le message d’erreur inclut le texte suivant :`The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **Résolution** : contactez votre administrateur Azure pour supprimer ou mettre à jour la stratégie Azure refusant la création du stockage.

### <a name="storage-dialog---error-400-disallowedoperation"></a>Boîte de dialogue Stockage – Erreur : 400 DisallowedOperation

- **Détails** : lorsque vous utilisez un abonnement Azure Active Directory, vous ne pouvez pas créer de stockage.
- **Résolution** : pour résoudre ce problème, utilisez un abonnement Azure habilité à créer des ressources de stockage. Les abonnements Azure AD ne peuvent pas créer de ressources Azure.

### <a name="terminal-output---error-failed-to-connect-terminal-websocket-cannot-be-established-press-enter-to-reconnect"></a>Sortie du terminal – Erreur : Failed to connect terminal: websocket cannot be established. Press `Enter` to reconnect. (Échec de la connexion du terminal : impossible d’établir la connexion WebSocket. Appuyez sur Entrée pour rétablir la connexion.)
- **Détails** : Cloud Shell doit pouvoir établir une connexion websocket à l’infrastructure Cloud Shell.
- **Résolution** : vérifiez vous avez configuré vos paramètres réseau pour activer l’envoi de demandes https et websocket aux domaines sur *. console.azure.com.

### <a name="set-your-cloud-shell-connection-to-support-using-tls-12"></a>Définir votre connexion Cloud Shell pour prendre en charge l’utilisation de TLS 1.2
 - **Détails** : pour définir la version de TLS pour votre connexion à Cloud Shell, vous devez définir des paramètres spécifiques au navigateur.
 - **Résolution** : accédez aux paramètres de sécurité de votre navigateur et cochez la case en regard de l’option d’utilisation de TLS 1.2.

## <a name="bash-troubleshooting"></a>Résolution des problèmes de Bash

### <a name="cannot-run-the-docker-daemon"></a>Impossible d’exécuter le démon Docker

- **Détails** : Cloud Shell utilisant un conteneur pour héberger votre environnement d’interpréteur de commandes, l’exécution du démon est interdite.
- **Résolution** : utilisez l’outil [docker-machine](https://docs.docker.com/machine/overview/), qui est installé par défaut, pour gérer les conteneurs Docker d’un hôte Docker distant.

## <a name="powershell-troubleshooting"></a>Résolution des problèmes de PowerShell

### <a name="gui-applications-are-not-supported"></a>Les applications de l’interface graphique utilisateur ne sont pas prises en charge.

- **Détails** : si un utilisateur lance une application d’interface graphique utilisateur, l’invite ne renvoie rien. Par exemple, lorsqu’un utilisateur clone un dépôt GitHub privé pour lequel l’authentification à deux facteurs est activée, une boîte de dialogue s’affiche, permettant de procéder à l’authentification à deux facteurs.
- **Résolution** : fermez et rouvrez l’interpréteur de commandes.

### <a name="troubleshooting-remote-management-of-azure-vms"></a>Résolution des problèmes de gestion à distance des machines virtuelles Azure
> [!NOTE]
> Les machines virtuelles Azure doivent avoir une adresse IP publique.

- **Détails** : en raison des paramètres de pare-feu Windows par défaut pour WinRM, l’erreur suivante peut s’afficher :`Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
- **Résolution** :  exécutez `Enable-AzVMPSRemoting` pour activer tous les aspects de la communication à distance PowerShell sur l’ordinateur cible.

### <a name="dir-does-not-update-the-result-in-azure-drive"></a>`dir` ne met pas à jour le résultat dans le lecteur Azure.

- **Détails** : par défaut, afin d’optimiser l’expérience utilisateur, les résultats de `dir` sont mis en cache dans le lecteur Azure.
- **Résolution** : après avoir créé, mis à jour ou supprimé une ressource Azure, exécutez `dir -force` pour mettre à jour les résultats dans le lecteur Azure.

## <a name="general-limitations"></a>Limitations générales

Azure Cloud Shell a les limitations connues suivantes :

### <a name="system-state-and-persistence"></a>État du système et persistance

La machine qui fournit votre session Cloud Shell est temporaire. En effet, elle est recyclée lorsque votre session reste inactive pendant 20 minutes. Cloud Shell requiert qu’un partage de fichiers Azure soit monté. Par conséquent, votre abonnement doit être en mesure de configurer des ressources de stockage pour accéder à Cloud Shell. Autres éléments à prendre en compte :

- Avec le stockage monté, seules les modifications apportées à votre répertoire `clouddrive` sont conservées. Dans Bash, votre répertoire `$HOME` est également conservé.
- Les partages de fichiers Azure peuvent être montés uniquement depuis votre [région affectée](persisting-shell-storage.md#mount-a-new-clouddrive).
  - Dans Bash, exécutez `env` pour rechercher votre région définie en tant que `ACC_LOCATION`.
- Azure Files prend uniquement en charge les comptes de stockage localement redondant et les comptes de stockage géoredondant.

### <a name="browser-support"></a>Prise en charge des navigateurs

Cloud Shell prend en charge les versions les plus récentes des navigateurs suivants :

- Microsoft Edge
- Microsoft Internet Explorer
- Google Chrome
- Mozilla Firefox
- Apple Safari
  - Safari en mode privé n’est pas pris en charge.

### <a name="copy-and-paste"></a>Copier et coller

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="usage-limits"></a>Limites d’utilisation

Cloud Shell est destiné aux cas d’usage interactif. De fait, les sessions non interactives longues se terminent sans avertissement.

### <a name="user-permissions"></a>Autorisations utilisateur

Les autorisations sont définies en tant qu’utilisateurs standards sans accès sudo. Les installations en dehors du répertoire `$Home` ne sont pas conservées.

## <a name="bash-limitations"></a>Limites de Bash

### <a name="editing-bashrc"></a>Modifier .bashrc

Faites attention lorsque vous modifiez .bashrc : cela peut entraîner des erreurs inattendues dans Cloud Shell.

## <a name="powershell-limitations"></a>Limites de PowerShell

### <a name="preview-version-of-azuread-module"></a>Préversion du module AzureAD

`AzureAD.Standard.Preview`, une préversion du module basé sur .NET Standard, est actuellement disponible. Ce module fournit les mêmes fonctionnalités que `AzureAD`.

### <a name="sqlserver-module-functionality"></a>Fonctionnalités du module `SqlServer`

Le module `SqlServer` inclus dans Cloud Shell n’offre qu’une prise en charge préliminaire pour PowerShell Core. En particulier, `Invoke-SqlCmd` n’est pas encore disponible.

### <a name="default-file-location-when-created-from-azure-drive"></a>Emplacement du fichier par défaut lors de sa création à partir du lecteur Azure

Les utilisateurs ne peuvent pas créer de fichiers sous le lecteur Azure à l’aide des cmdlets PowerShell. Si les utilisateurs créent des fichiers à l’aide d’autres outils, tels que vim ou nano, les fichiers sont enregistrés dans le dossier `$HOME` par défaut.

### <a name="tab-completion-can-throw-psreadline-exception"></a>La saisie semi-automatique via la touche Tab peut lever une exception PSReadline.

Si le mode EditMode de l’utilisateur dans PSReadline est défini sur Emacs, que l’utilisateur tente d’afficher toutes les possibilités par le biais de la saisie semi-automatique via la touche Tab et que la taille de la fenêtre est trop petite pour afficher toutes les possibilités, PSReadline lève une exception non prise en charge.

### <a name="large-gap-after-displaying-progress-bar"></a>Écart important après avoir affiché la barre de progression

Si une commande ou une action de l’utilisateur affiche une barre de progression (par exemple, le renseignement d’un onglet sur le lecteur `Azure:`), il est possible que le curseur ne soit pas défini correctement et qu’un écart apparaisse là où la barre de progression se situait précédemment.

### <a name="random-characters-appear-inline"></a>Des caractères aléatoires apparaissent en ligne

Les codes de la séquence de position du curseur, par exemple `5;13R`, peuvent apparaître dans l’entrée utilisateur. Les caractères peuvent être supprimés manuellement.

## <a name="personal-data-in-cloud-shell"></a>Données personnelles dans Cloud Shell

Azure Cloud Shell prend très au sérieux vos données personnelles ; les données capturées et stockées par le service Azure Cloud Shell sont utilisées pour vous fournir un environnement par défaut, tel que l’interpréteur de commandes le plus récemment utilisé, les taille et type de police par défaut, ainsi que les détails des partages de fichiers pour le lecteur cloud. Si vous souhaitez exporter ou supprimer ces données, reportez-vous aux instructions suivantes.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

### <a name="export"></a>Exportation
Pour **exporter** les paramètres utilisateur que Cloud Shell enregistre pour vous, tels que l’interpréteur de commandes, la taille de police et le type de police par défaut, exécutez les commandes suivantes.

1. [![](https://shell.azure.com/images/launchcloudshell.png "Lancer Azure Cloud Shell")](https://shell.azure.com)
2. Exécutez les commandes suivantes dans Bash ou PowerShell :

Bash :

  ```
  token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
  curl https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token" -s | jq
  ```

PowerShell :

  ```powershell
  $token= ((Invoke-WebRequest -Uri "$env:MSI_ENDPOINT`?resource=https://management.core.windows.net/" -Headers @{Metadata='true'}).content |  ConvertFrom-Json).access_token
  ((Invoke-WebRequest -Uri https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -Headers @{Authorization = "Bearer $token"}).Content | ConvertFrom-Json).properties | Format-List
```

### <a name="delete"></a>Supprimer
Pour **supprimer** vos paramètres utilisateur que Cloud Shell enregistre pour vous, tels que l’interpréteur de commandes, la taille de police et le type de police par défaut, exécutez les commandes suivantes. Au prochain démarrage de Cloud Shell, vous serez invité à réintégrer un partage de fichiers. 

>[!Note]
> Si vous supprimez vos paramètres utilisateur, le partage Azure Files proprement dit n’est pas supprimé. Accédez à Azure Files pour effectuer cette action.

1. [![](https://shell.azure.com/images/launchcloudshell.png "Lancer Azure Cloud Shell")](https://shell.azure.com)
2. Exécutez les commandes suivantes dans Bash ou PowerShell :

Bash :

  ```
  token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
  curl -X DELETE https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token"
  ```

PowerShell :

  ```powershell
  $token= ((Invoke-WebRequest -Uri "$env:MSI_ENDPOINT`?resource=https://management.core.windows.net/" -Headers @{Metadata='true'}).content |  ConvertFrom-Json).access_token
  Invoke-WebRequest -Method Delete -Uri https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -Headers @{Authorization = "Bearer $token"}
  ```
## <a name="azure-government-limitations"></a>Limitations d’Azure Government
Azure Cloud Shell dans Azure Government est accessible uniquement par le biais du Portail Azure.
