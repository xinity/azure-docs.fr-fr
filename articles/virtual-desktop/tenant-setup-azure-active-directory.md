---
title: Créer un locataire dans Windows Virtual Desktop - Azure
description: Explique comment configurer des locataires Windows Virtual Desktop dans Azure Active Directory.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 09/06/2019
ms.author: helohr
ms.openlocfilehash: 04a65442e86168239f08fb71303b8d9e9e152e72
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "71679468"
---
# <a name="tutorial-create-a-tenant-in-windows-virtual-desktop"></a>Didacticiel : Créer un locataire dans Windows Virtual Desktop

La création d’un locataire dans Windows Virtual Desktop constitue la première étape de la création de votre solution de virtualisation de bureau. Un locataire est un groupe d’un ou de plusieurs pools d’hôtes. Chaque pool d’hôtes se compose de plusieurs hôtes de session s’exécutant en tant que machines virtuelles dans Azure et inscrits auprès du service Windows Virtual Desktop. Chaque pool d’hôtes comprend également un ou plusieurs groupes d’applications qui sont utilisés pour publier des ressources du Bureau à distance et d’application à distance pour les utilisateurs. Avec un locataire, vous pouvez générer des pools d’hôtes, créer des groupes d’applications, affecter des utilisateurs et établir des connexions par l’intermédiaire du service.

Dans ce tutoriel, vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Accorder des autorisations Azure Active Directory au service Windows Virtual Desktop.
> * Attribuer le rôle d’application TenantCreator à un utilisateur dans votre locataire Azure Active Directory.
> * Créer un locataire Windows Virtual Desktop.

Voici ce dont vous avez besoin pour configurer votre locataire Windows Virtual Desktop :

* L’ID de locataire [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) pour les utilisateurs Windows Virtual Desktop.
* Un compte d’administrateur général dans le locataire Azure Active Directory.
   * Cela s’applique également aux organisations de fournisseurs de solutions cloud (CSP) qui créent un locataire Windows Virtual Desktop pour leurs clients. Si vous êtes une organisation CSP, vous devez être en mesure de vous connecter en tant qu’administrateur général de l’instance Azure Active Directory du client.
   * Le compte d’administrateur doit provenir du locataire Azure Active Directory dans lequel vous tentez de créer le locataire Windows Virtual Desktop. Ce processus ne prend pas en charge les comptes (invités) Azure Active Directory B2B.
   * Le compte d’administrateur doit être un compte professionnel ou scolaire.
* Un abonnement Azure.

## <a name="grant-permissions-to-windows-virtual-desktop"></a>Octroyer des autorisations à Windows Virtual Desktop

Si vous avez déjà accordé des autorisations à Windows Virtual Desktop pour cette instance Azure Active Directory, ignorez cette section.

L’octroi d’autorisations au service Windows Virtual Desktop permet à celui-ci d’interroger l’instance Azure Active Directory concernant les tâches d’administration et les tâches d’utilisateur final.

Pour accorder des autorisations au service :

1. Ouvrez un navigateur et démarrez le flux de consentement administrateur vers l’[application serveur Windows Virtual Desktop](https://login.microsoftonline.com/common/adminconsent?client_id=5a0aa725-4958-4b0c-80a9-34562e23f3b7&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback).
   > [!NOTE]
   > Si vous gérez un client et devez accorder le consentement administrateur pour l’annuaire du client, entrez l’URL suivante dans le navigateur et remplacez {tenant} par le nom de domaine Azure AD du client. Par exemple, si l’organisation du client a inscrit le nom de domaine Azure AD contoso.onmicrosoft.com, remplacez {tenant} par contoso.onmicrosoft.com.
   >```
   >https://login.microsoftonline.com/{tenant}/adminconsent?client_id=5a0aa725-4958-4b0c-80a9-34562e23f3b7&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback
   >```

2. Connectez-vous à la page de consentement Windows Virtual Desktop avec un compte d’administrateur général. Par exemple, si vous faites partie de l’organisation Contoso, votre compte peut être admin@contoso.com ou admin@contoso.onmicrosoft.com.
3. Sélectionnez **Accepter**.
4. Attendez une minute pour qu’Azure AD puisse enregistrer le consentement.
5. Ouvrez un navigateur et démarrez le flux de consentement administrateur vers l’[application cliente Windows Virtual Desktop](https://login.microsoftonline.com/common/adminconsent?client_id=fa4345a4-a730-4230-84a8-7d9651b86739&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback).
   >[!NOTE]
   > Si vous gérez un client et devez accorder le consentement administrateur pour l’annuaire du client, entrez l’URL suivante dans le navigateur et remplacez {tenant} par le nom de domaine Azure AD du client. Par exemple, si l’organisation du client a inscrit le nom de domaine Azure AD contoso.onmicrosoft.com, remplacez {tenant} par contoso.onmicrosoft.com.
   >```
   > https://login.microsoftonline.com/{tenant}/adminconsent?client_id=fa4345a4-a730-4230-84a8-7d9651b86739&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback
   >```

6. Connectez-vous à la page de consentement Windows Virtual Desktop en tant qu’administrateur général comme vous l’avez fait à l’étape 2.
7. Sélectionnez **Accepter**.

## <a name="assign-the-tenantcreator-application-role"></a>Attribuer le rôle d’application TenantCreator

L’attribution du rôle d’application TenantCreator à un utilisateur Azure Active Directory permet à ce dernier de créer un locataire Windows Virtual Desktop associé à l’instance Azure Active Directory. Vous devez utiliser votre compte d’administrateur général pour attribuer le rôle TenantCreator.

Pour attribuer le rôle d’application TenantCreator :

1. Ouvrez un navigateur et connectez-vous au [Portail Azure](https://portal.azure.com) avec votre compte d’administrateur général.
   
   Dans le cas où il existe plusieurs locataires Azure Active Directory, il est recommandé d’ouvrir une session de navigation privée, puis de copier-coller les URL dans la barre d’adresse.
2. Dans la barre de recherche du Portail Azure, recherchez **Applications d’entreprise** et sélectionnez l’entrée qui s’affiche sous la catégorie **Services**.
3. Dans **Applications d’entreprise**, recherchez **Windows Virtual Desktop**. Les deux applications pour lesquelles vous avez donné votre consentement dans la section précédente s’affichent. Entre ces deux applications, sélectionnez **Windows Virtual Desktop**.
   ![Capture d’écran des résultats de la recherche « Windows Virtual Desktop » dans « Applications d’entreprise ». application nommée « Windows Virtual Desktop » en surbrillance](media/tenant-enterprise-app.png)
4. Sélectionnez **Utilisateurs et groupes**. Comme vous pouvez le constater, l’administrateur qui a donné son consentement à l’application apparaît déjà avec le rôle **Accès par défaut**. Ce n’est pas suffisant pour créer un locataire Windows Virtual Desktop. Continuez à suivre ces instructions pour ajouter le rôle **TenantCreator** à un utilisateur.
   ![Capture d’écran des utilisateurs et des groupes affectés à la gestion de l’application d’entreprise « Windows Virtual Desktop » : une seule attribution, pour « Accès par défaut ».](media/tenant-default-access.png)
5. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans le panneau **Ajouter une attribution**.
6. Recherchez un compte d’utilisateur qui va créer votre locataire Windows Virtual Desktop. Pou rester simple, ce peut être le compte d’administrateur général.
   - Si vous utilisez un fournisseur d’identité Microsoft comme contosoadmin@live.com ou contosoadmin@outlook.com, vous ne pourrez peut-être pas vous connecter à Windows Virtual Desktop. Nous vous recommandons plutôt d’utiliser un compte propre au domaine comme admin@contoso.com ou admin@contoso.onmicrosoft.com.

   ![Capture d’écran de la sélection d’un utilisateur à ajouter comme « TenantCreator ».](media/tenant-assign-user.png)

   > [!NOTE]
   > L’utilisateur (ou le groupe contenant un utilisateur) sélectionné doit provenir de cette instance Azure Active Directory. Vous ne pouvez pas choisir un utilisateur invité (B2B) ou un principal de service.

7. Sélectionnez le compte d’utilisateur, choisissez le bouton **Sélectionner**, puis sélectionnez **Attribuer**.
8. Sur la page **Windows Virtual Desktop – Utilisateurs et groupes**, vérifiez qu’il existe une nouvelle entrée pour le rôle **TenantCreator** attribué à l’utilisateur qui devra créer le locataire Windows Virtual Desktop.
   ![Capture d’écran des utilisateurs et des groupes affectés à la gestion de l’application d’entreprise « Windows Virtual Desktop » : Capture d’écran comportant une deuxième entrée, qui correspond à un utilisateur affecté au rôle « TenantCreator ».](media/tenant-tenant-creator-added.png)

Pour pouvoir créer votre locataire Windows Virtual Desktop, il vous faut deux informations :
- Votre ID de locataire Azure Active Directory (ou **ID répertoire**)
- L'identifiant de votre abonnement Azure.

Pour trouver votre ID de locataire Azure Active Directory (ou **ID répertoire**) :
1. Dans la même session du Portail Azure, recherchez **Azure Active Directory** dans la barre de recherche et sélectionnez l’entrée qui s’affiche sous la catégorie **Services**.
   ![Capture d’écran des résultats de la recherche « Azure Active Directory » sur le Portail Azure : résultat de la recherche sous « Services » mis en surbrillance](media/tenant-search-azure-active-directory.png)
2. Faites défiler la page, puis sélectionnez **Propriétés**.
3. Recherchez **ID répertoire**, puis sélectionnez l’icône de Presse-papiers. Collez l’ID dans un emplacement pratique pour pouvoir l’utiliser plus tard comme valeur de **AadTenantId**.
   ![Capture d’écran des propriétés d’Azure Active Directory : souris qui pointe sur l’icône du Presse-papiers « ID répertoire » à copier-coller.](media/tenant-directory-id.png)

Pour trouver votre ID d’abonnement Azure :
1. Dans la même session du Portail Azure, recherchez **Abonnements** dans la barre de recherche et sélectionnez l’entrée qui s’affiche sous la catégorie **Services**.
   ![Capture d’écran des résultats de la recherche « Azure Active Directory » sur le Portail Azure : résultat de la recherche sous « Services » mis en surbrillance](media/tenant-search-subscription.png)
2. Sélectionnez l’abonnement Azure que vous souhaitez utiliser pour recevoir les notifications de service Windows Virtual Desktop.
3. Recherchez **ID d’abonnement**, puis pointez sur la valeur jusqu’à ce qu’une icône de Presse-papiers s’affiche. Sélectionnez l’icône de Presse-papiers et collez l’ID dans un emplacement pratique pour pouvoir l’utiliser plus tard comme valeur de **AzureSubscriptionId**.
   ![Capture d’écran des propriétés de l’abonnement Azure : souris qui pointe sur l’icône du Presse-papiers « ID d’abonnement » à copier-coller.](media/tenant-subscription-id.png)

## <a name="create-a-windows-virtual-desktop-tenant"></a>Créer un locataire Windows Virtual Desktop

Maintenant que vous avez accordé des autorisations pour interroger l’instance Azure Active Directory au service Windows Virtual Desktop et attribué le rôle TenantCreator à un compte d’utilisateur, vous pouvez créer un locataire Windows Virtual Desktop.

Tout d’abord, si vous ne l’avez pas déjà fait, [téléchargez et importez le module Windows Virtual Desktop](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) à utiliser dans votre session PowerShell.

Connectez-vous à Windows Virtual Desktop à l’aide du compte d’utilisateur TenantCreator avec cette applet de commande :

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Après cela, créez un locataire Windows Virtual Desktop associé au locataire Azure Active Directory :

```powershell
New-RdsTenant -Name <TenantName> -AadTenantId <DirectoryID> -AzureSubscriptionId <SubscriptionID>
```

Remplacez les valeurs entre crochets par des valeurs appropriées à votre organisation et votre locataire. Le nom que vous choisissez pour votre nouveau locataire Windows Virtual Desktop doit être un nom global unique. Par exemple, supposons que vous êtes l’utilisateur TenantCreator Windows Virtual Desktop de l’organisation Contoso. L’applet de commande que vous devez exécuter peut ressembler à ceci :

```powershell
New-RdsTenant -Name Contoso -AadTenantId 00000000-1111-2222-3333-444444444444 -AzureSubscriptionId 55555555-6666-7777-8888-999999999999
```

## <a name="next-steps"></a>Étapes suivantes

Après avoir créé votre locataire, il vous faudra créer un principal de service dans Azure Active Directory et lui attribuer un rôle dans Windows Virtual Desktop. Le principal de service vous permettra de déployer l’offre Place de marché Azure Windows Virtual Desktop pour créer un pool d’hôtes. Pour en savoir plus sur les pools d’hôtes, passez au tutoriel concernant la création d’un pool d’hôtes dans Windows Virtual Desktop.

> [!div class="nextstepaction"]
> [Créer des principaux de service et des attributions de rôles avec PowerShell](./create-service-principal-role-powershell.md)
