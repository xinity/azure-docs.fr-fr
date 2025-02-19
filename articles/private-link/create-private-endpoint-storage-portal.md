---
title: Connexion privée à un compte de stockage à l’aide d’Azure Private Endpoint
description: Découvrez comment vous connecter en privé à un compte de stockage dans Azure à l’aide de Private Endpoint.
services: private-link
author: KumudD
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 8a72f70fbc1ab6052587beb1d949dd73b1ad3559
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376144"
---
# <a name="connect-privately-to-a-storage-account-using-azure-private-endpoint"></a>Connexion privée à un compte de stockage à l’aide d’Azure Private Endpoint
Azure Private Endpoint est le composant fondamental de Private Link dans Azure. Il permet à des ressources Azure, comme des machines virtuelles, de communiquer en privé avec des ressources Private Link.

Dans ce guide de démarrage rapide, vous allez apprendre à créer une machine virtuelle sur un réseau virtuel Azure, un compte de stockage avec Private Endpoint à l’aide du portail Azure. Ensuite, vous pouvez accéder en toute sécurité au compte de stockage à partir de la machine virtuelle.


## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur https://portal.azure.com.

## <a name="create-a-vm"></a>Créer une machine virtuelle
Dans cette section, vous allez créer un réseau virtuel et le sous-réseau pour héberger la machine virtuelle qui est utilisée pour accéder à votre ressource Private Link (un compte de stockage pour cet exemple).

### <a name="create-the-virtual-network"></a>Créer un réseau virtuel

Dans cette section, vous allez créer un réseau virtuel et le sous-réseau pour héberger la machine virtuelle qui est utilisée pour accéder à votre ressource Private Link.

1. Dans le coin supérieur gauche de l’écran, sélectionnez **Créer une ressource** > **Mise en réseau** > **Réseau virtuel**.
1. Dans **Créer un réseau virtuel**, entrez ou sélectionnez ces informations :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom | Entrez *MyVirtualNetwork*. |
    | Espace d’adressage | Entrez *10.1.0.0/16*. |
    | Subscription | Sélectionnez votre abonnement.|
    | Resource group | Sélectionnez **Créer nouveau**, entrez *myResourceGroup* et sélectionnez **OK**. |
    | Location | Sélectionnez **WestCentralUS**.|
    | Sous-réseau - Nom | Entrez *mySubnet*. |
    | Plage d’adresses du sous-réseau | Entrez *10.1.0.0/24*. |
    |||
1. Conservez les autres valeurs par défaut, puis sélectionnez **Créer**.


### <a name="create-virtual-machine"></a>Créer une machine virtuelle

1. En haut à gauche de l’écran du portail Azure, sélectionnez **Créer une ressource** > **Calcul** > **Machine virtuelle**.

1. Dans **Créer une machine virtuelle - Notions de base**, entrez ou sélectionnez ces informations :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **DÉTAILS DU PROJET** | |
    | Subscription | Sélectionnez votre abonnement. |
    | Resource group | Sélectionnez **myResourceGroup**. Vous avez créé cela dans la section précédente.  |
    | **DÉTAILS DE L’INSTANCE** |  |
    | Nom de la machine virtuelle | Entrez *myVm*. |
    | Région | Sélectionnez **WestCentralUS**. |
    | Options de disponibilité | Conservez la valeur par défaut **Aucune redondance d’infrastructure nécessaire**. |
    | Image | Sélectionnez **Windows Server 2019 Datacenter**. |
    | Size | Conservez la valeur par défaut **Standard DS1 v2**. |
    | **COMPTE ADMINISTRATEUR** |  |
    | Nom d’utilisateur | Entrez un nom d’utilisateur de votre choix. |
    | Mot de passe | Entrez un mot de passe de votre choix. Le mot de passe doit contenir au moins 12 caractères et satisfaire aux [exigences de complexité définies](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirmer le mot de passe | Retapez le mot de passe. |
    | **RÈGLES DES PORTS D’ENTRÉE** |  |
    | Aucun port d’entrée public | Conservez la valeur par défaut **Aucun**. |
    | **ÉCONOMISEZ DE L’ARGENT** |  |
    | Vous disposez déjà d’une licence Windows ? | Conservez la valeur par défaut **Non**. |
    |||

1. Sélectionnez **Suivant : Disques**.

1. Dans **Créer une machine virtuelle - Disks**, conservez les valeurs par défaut et sélectionnez **Suivant : Mise en réseau**.

1. Dans **Créer une machine virtuelle - Mise en réseau**, entrez ou sélectionnez ces informations :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Réseau virtuel | Conservez la valeur par défaut, **MyVirtualNetwork**.  |
    | Espace d’adressage | Conservez la valeur par défaut, **10.1.0.0/24**.|
    | Subnet | Conservez la valeur par défaut, **mySubnet (10.1.0.0/24)** .|
    | Adresse IP publique | Conservez la valeur par défaut **(new) myVm-ip**. |
    | Aucun port d’entrée public | Sélectionnez **Autoriser les ports sélectionnés**. |
    | Sélectionner des ports d’entrée | Sélectionnez **HTTP** et **RDP**.|
    ||

1. Sélectionnez **Revoir + créer**. Vous êtes redirigé vers la page **Vérifier + créer** où Azure valide votre configuration.

1. Lorsque le message **Validation passed** (Validation réussie) apparaît, sélectionnez **Créer**.

## <a name="create-your-private-endpoint"></a>Créer votre instance Private Endpoint
Dans cette section, vous allez créer un compte de stockage privé à l’aide de Private Endpoint. 

1. Dans le coin supérieur gauche de l’écran dans le portail Azure, sélectionnez **Créer une ressource** > **Stockage** > **Compte de stockage**.

1. Dans **Créer un compte de stockage - De base**, entrez ou sélectionnez ces informations :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **DÉTAILS DU PROJET** | |
    | Subscription | Sélectionnez votre abonnement. |
    | Resource group | Sélectionnez **myResourceGroup**. Vous avez créé cela dans la section précédente.|
    | **DÉTAILS DE L’INSTANCE** |  |
    | Nom du compte de stockage  | Entrez *mystorageaccount*. Si ce nom est utilisé, créez un nom unique. |
    | Région | Sélectionnez **WestCentralUS**. |
    | Performances| Conservez la valeur par défaut **Standard**. |
    | Type de compte | Conservez la valeur par défaut **Stockage (usage général v2)** . |
    | Réplication | Sélectionnez **Stockage géo-redondant avec accès en lecture (RA-GRS)** . |
    |||
  
3. Sélectionnez **Suivant : Mise en réseau**.
4. Dans **Créer un compte de stockage - Mise en réseau**, pour la méthode de connectivité, sélectionnez **Point de terminaison privé**.
5. Dans **Créer un compte de stockage - Mise en réseau**, sélectionnez **Ajouter un point de terminaison privé**. 
6. Dans **Créer un point de terminaison privé**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **DÉTAILS DU PROJET** | |
    | Subscription | Sélectionnez votre abonnement. |
    | Resource group | Sélectionnez **myResourceGroup**. Vous avez créé cela dans la section précédente.|
    |Location|Sélectionnez **WestCentralUS**.|
    |Nom|Entrez *myPrivateEndpoint*.  |
    |Sous-ressource de stockage|Conservez l’objet **Blob** par défaut. |
    | **MISE EN RÉSEAU** |  |
    | Réseau virtuel  | Sélectionnez  *MyVirtualNetwork* dans le groupe de ressources *myResourceGroup*. |
    | Subnet | Sélectionnez *mySubnet*. |
    | **INTÉGRATION À DNS PRIVÉ**|  |
    | Intégrer à une zone DNS privée  | Conservez la valeur par défaut **Oui**. |
    | Zone DNS privée  | Conservez la valeur par défaut ** (New) privatelink.blob.core.windows.net**. |
    |||
7. Sélectionnez **OK**. 
8. Sélectionnez **Revoir + créer**. Vous êtes redirigé vers la page **Vérifier + créer** où Azure valide votre configuration. 
9. Lorsque le message **Validation passed** (Validation réussie) apparaît, sélectionnez **Créer**. 
10. Accédez à la ressource de compte de stockage que vous venez de créer.
11. Sélectionnez **Clés d’accès** dans le menu de contenu de gauche.
12. Sélectionnez **Copier** sur la chaîne de connexion pour key1.
 
## <a name="connect-to-a-vm-from-the-internet"></a>Se connecter à une machine virtuelle à partir d’Internet

Connectez-vous à la machine virtuelle *myVm* à partir d’Internet comme suit :

1. Dans la barre de recherche du portail, entrez *myVm*.

1. Sélectionnez le bouton **Connexion**. Après avoir sélectionné le bouton **Connecter**, **Se connecter à la machine virtuelle** s’ouvre.

1. Sélectionnez **Télécharger le fichier RDP**. Azure crée un fichier de protocole RDP (Remote Desktop Protocol) ( *.rdp*) et le télécharge sur votre ordinateur.

1. Ouvrez le fichier .rdp* téléchargé.

    1. Si vous y êtes invité, sélectionnez **Connexion**.

    1. Entrez le nom d’utilisateur et le mot de passe spécifiés lors de la création de la machine virtuelle.

        > [!NOTE]
        > Vous devrez peut-être sélectionner **Plus de choix** > **Utiliser un autre compte**, pour spécifier les informations d’identification que vous avez entrées lorsque vous avez créé la machine virtuelle.

1. Sélectionnez **OK**.

1. Un avertissement de certificat peut s’afficher pendant le processus de connexion. Si vous recevez un avertissement de certificat, sélectionnez **Oui** ou **Continuer**.

1. Une fois que le bureau de la machine virtuelle s’affiche, réduisez-le pour revenir à votre poste de travail local.  

## <a name="access-storage-account-privately-from-the-vm"></a>Accéder au compte de stockage en privé à partir de la machine virtuelle

Dans cette section, vous allez vous connecter en privé au compte de stockage à l’aide de Private Endpoint.

> [!IMPORTANT]
> La configuration DNS pour le stockage nécessite une modification manuelle du fichier hosts pour inclure le nom de domaine complet du compte spécifique. Modifiez le fichier suivant en utilisant les autorisations d’administrateur sur Windows : c:\Windows\System32\Drivers\etc\hosts ou Linux /etc/hosts. Incluez les informations DNS pour le compte de l’étape précédente au format [Adresse IP privée] myaccount.blob.core.windows.net

1. Dans le Bureau à distance de  *myVM*, ouvrez PowerShell.
2. Entrez `nslookup mystorageaccount.blob.core.windows.net` Vous recevez un message similaire à celui-ci :
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mystorageaccount123123.privatelink.blob.core.windows.net
    Address:  10.0.0.5
    Aliases:  mystorageaccount.blob.core.windows.net
    ```
3. Installez [l’Explorateur Stockage Microsoft Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).
4. Sélectionnez **Comptes de stockage** en cliquant avec le bouton droit.
5. Sélectionnez **Se connecter à un stockage Azure**.
6. Sélectionnez **Utiliser une chaîne de connexion**.
7. Sélectionnez **Suivant**.
8. Entrez la chaîne de connexion en collant les informations précédemment copiées.
9. Sélectionnez **Suivant**.
10. Sélectionnez **Connecter**.
11. Parcourir les conteneurs d’objets blob à partir de mystorageaccount 
12. (Facultatif) Créez des dossiers et/ou téléchargez des fichiers vers *mystorageaccount* . 
13. Fermez la connexion Bureau à distance avec  *myVM*. 

Options supplémentaires pour accéder au compte de stockage :
- L’Explorateur Stockage Microsoft Azure est une application gratuite autonome de Microsoft qui vous permet d’exploiter visuellement les données de stockage Azure sur Windows, macOS et Linux. Vous pouvez installer l’application pour parcourir en privé le contenu du compte de stockage. 
 
- L’utilitaire AzCopy est une autre option hautes performances pour transférer des données par script avec le stockage Azure. Utilisez AzCopy pour transférer des données à destination et à partir du stockage Blob, Table et Fichier. 


## <a name="clean-up-resources"></a>Supprimer des ressources 
Lorsque vous avez fini d’utiliser le point de terminaison privé, le compte de stockage et la machine virtuelle, supprimez le groupe de ressources et toutes les ressources qu’il contient : 
1. Entrez *myResourceGroup* dans la zone **Rechercher** en haut du portail, puis sélectionnez *myResourceGroup* dans les résultats de la recherche. 
2. Sélectionnez **Supprimer le groupe de ressources**. 
3. Entrez *myResourceGroup* pour **TAPEZ LE NOM DU GROUPE DE RESSOURCES**, puis sélectionnez **Supprimer**. 

## <a name="next-steps"></a>Étapes suivantes
Dans ce guide de démarrage rapide, vous avez créé une machine virtuelle sur un réseau virtuel, ainsi qu’un compte de stockage et un point de terminaison privé. Vous vous êtes connecté à une machine virtuelle à partir d’Internet et avez communiqué de façon sécurisée avec le compte de stockage via une liaison privée. Pour en savoir plus sur le point de terminaison privé, consultez [Qu’est-ce qu’Azure Private Endpoint ?](private-endpoint-overview.md).
