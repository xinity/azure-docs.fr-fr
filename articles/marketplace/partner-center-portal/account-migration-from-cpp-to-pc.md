---
title: Migration de compte du Portail Cloud Partner vers l’Espace partenaires - Place de marché commerciale pour Azure
description: Découvrez comment migrer votre compte du Portail Cloud Partner vers l'Espace partenaires. - Place de marché commerciale pour Azure
author: ChJenk
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: 6887430cad06ad33434f0f01e741979014b4558b
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219575"
---
# <a name="account-migration-from-cloud-partner-portal-to-partner-center"></a>Migration de compte du Portail Cloud Partner vers l’Espace partenaires

Si vous avez déjà un compte du portail Cloud Partner, les paramètres de votre compte doivent être migrés vers l’Espace partenaires.

## <a name="account-migration-process"></a>Processus de migration de compte

Si vous disposez du rôle Propriétaire dans le portail Cloud Partner pour un compte donné, une bannière jaune s’affiche dans votre page Profil d’éditeur. Vous pouvez vous retrouver dans un des deux cas suivants :

- Votre compte a déjà été migré et vous êtes prêt à gérer les paramètres de votre compte dans l’Espace partenaires.
- Votre compte n’a pas été migré vers l’Espace partenaires et vous devez prendre des mesures supplémentaires.

### <a name="your-account-has-been-migrated-to-partner-center"></a>Votre compte a été migré vers l’Espace partenaires

Pour tous les comptes qui ont effectué la migration de CPP vers l’Espace partenaires, la gestion des comptes se produit dans l’Espace partenaires. Les modifications telles que l’ajout/la suppression d’utilisateurs seront synchronisées avec CPP.

### <a name="you-have-not-yet-migrated-your-account-to-partner-center"></a>Vous n’avez pas encore migré votre compte vers l’Espace partenaires

Cliquez sur la bannière pour démarrer le processus de migration de votre compte. Vous devez entrer les éléments suivants :

1. Adresse e-mail professionnelle : <br> <br> Dans la plupart des cas, vous vous connectez avec l’adresse e-mail que vous utilisez pour vous connecter au portail Cloud Partner. Dans certains cas, vous devez utiliser une autre adresse e-mail :

    * Compte Microsoft : Si le compte du portail Cloud Partner est un compte Microsoft, entrez une adresse e-mail professionnelle valide associée au locataire pour lequel l’ID Microsoft Partner Network (MPN) est inscrit. Pour plus d’informations, consultez [S’inscrire au programme Microsoft Partner Network](#sign-up-for-microsoft-partner-network-program).

    * Incompatibilité du locataire : Si votre adresse e-mail professionnelle n’appartient pas au locataire associé à l’ID Microsoft Partner Network présent sur votre compte du portail Cloud Partner, une erreur s’affiche. Pour y remédier, entrez une adresse e-mail associée au locataire. Un message d’erreur indiquera le nom du locataire.

2. S'inscrire au programme Microsoft Partner Network

    Si votre compte du portail Cloud Partner n’a pas d’ID Microsoft Partner Network ou si celui-ci n’est pas valide, vous devrez vous inscrire au programme Microsoft Partner Network dans le cadre du processus d’activation.

## <a name="sign-up-for-microsoft-partner-network-program"></a>S'inscrire au programme Microsoft Partner Network

Les entreprises qui souhaitent s’associer à Microsoft doivent rejoindre le Microsoft Partner Network (MPN) et obtenir un ID MPN. Si vous êtes déjà membre du Microsoft Partner Network et que vous avez un ID MPN, conservez les informations à portée de main car vous en aurez besoin lors du processus d’activation du compte.  

Si vous n’êtes pas membre du Microsoft Partner Network, vous pouvez [vous inscrire ici](https://signup.microsoft.com/signup?sku=StoreForBusinessIW&origin=partnerdashboard&culture=en-us&ru=https://partner.microsoft.com/dashboard/account/v3/xpu/onboard?ru=/en-us/dashboard/account/v3/enrollment/companyprofile/basicpartnernetwork/new) pour obtenir un ID MPN. Prenez note de votre ID MPN, car vous devrez l’entrer pendant le processus d’activation du compte.

Pour en savoir plus sur le Microsoft Partner Network, consultez [Rejoindre le Microsoft Partner Network](https://partner.microsoft.com/en-US/membership) sur le site web des partenaires. Pour en savoir plus sur les avantages dont bénéficient les éditeurs de logiciels indépendants membres du Microsoft Partner Network, consultez le [hub de ressources pour éditeurs de logiciels indépendants](https://partner.microsoft.com/isv-resource-hub).  

## <a name="move-dynamics-365-and-powerapps-offers-to-partner-center"></a>Déplacer des offres Dynamics 365 et PowerApps vers l’Espace partenaires

Pour simplifier la gestion des comptes et des offres pour Dynamics 365 Customer Engagement, PowerApps et Dynamics 365 Operations, les offres ont été déplacées vers l’[Espace partenaires](https://partner.microsoft.com/). Ce déplacement garantit que le même contenu est disponible pour les catalogues publics et de vendeurs.

Pour obtenir des informations spécifiques sur ce qui doit être fait d’ici le **15 octobre 2019** pour vos offres Dynamics 365 Customer Engagement, PowerApps et Dynamics 365 Operations, suivez les instructions ci-dessous.

> [!NOTE]
> Cela ne s’applique pas aux offres Dynamics 365 Business Central.  

1. Si votre compte d’appartenance MPN a été initialement créé dans Partner Membership Center (PMC), connectez-vous à l’[Espace partenaires](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) pour vérifier que ce compte a été migré. Si vous voyez un écran de profil avec votre ID MPN, vous pouvez continuer. Sinon, vous devez démarrer la migration de votre compte en suivant les invites affichées dans [Partner Membership Center](https://partners.microsoft.com/partnerprogram/Welcome.aspx). Si vous avez besoin d’aide, consultez la page de [support](https://partner.microsoft.com/support?issueid=100-0077).
2. Accédez à la [page Vue d’ensemble de la Place de marché commerciale dans l’Espace partenaires](https://partner.microsoft.com/dashboard/commercial-marketplace/overview). Si vous voyez la mention « Place de marché commerciale » dans le volet de navigation de gauche, vous êtes inscrit et vous pouvez passer à l’étape suivante. Sinon, [inscrivez-vous dès maintenant sur la Place de marché commerciale](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/azureisv).
3. Vérifiez que vos offres sont proposées dans AppSource [en recherchant vos offres](https://appsource.microsoft.com/). Si vos offres se trouvent déjà dans AppSource, passez à l’étape suivante. Si l’une de vos offres n’est pas dans AppSource, créez une [offre Dynamics 365 Customer Engagement](create-new-customer-engagement-offer.md) ou une [offre Dynamics 365 Operations](create-new-operations-offer.md).
4. Dans la [page Contrats](https://partner.microsoft.com/dashboard/account/agreements) de l’Espace partenaires, assurez-vous d’avoir consulté et accepté l’**addenda du Programme Business Applications ISV**.
5. Dans les [Paramètres du compte](https://partner.microsoft.com/dashboard/account/v3/accountsettings/billingprofile) de l’Espace partenaires, vérifiez que vos informations de facturation sont complètes.
6. Soumettez toutes vos offres nouvelles ou existantes pour certification et publication, même si elles ont déjà été certifiées.
    * Complétez les écrans d’informations, notamment en fournissant votre application à des fins de certification, ainsi que des informations de marketing. Sélectionnez **Soumettre** (dans le coin supérieur droit de l’écran) au plus tard le **15 octobre 2019**. Ces étapes doivent être effectuées afin que la disponibilité de l’offre ne soit pas affectée.
    * Si vous êtes éligible, vous pouvez demander à participer au niveau Premium au cours de ce processus.
    * La certification ou la recertification nécessite que votre application prenne en charge la version la plus récente de notre plateforme Business Applications.
    * Une fois votre application approuvée, vous recevrez un e-mail pour revenir à l’offre et sélectionner « Démarrer » afin que l’offre soit mise en ligne sur Microsoft AppSource.

## <a name="additional-resources"></a>Ressources supplémentaires

Participez aux [appels hebdomadaires de la communauté des éditeurs de logiciels indépendants Dynamics](https://aka.ms/DynamicsISV-CommunityCall) pour obtenir de l’aide et des mises à jour.

Si vous avez besoin d’aide pour la publication, la certification ou la gestion de vos offres de la Place de marché, [soumettez un ticket de support](https://aka.ms/MarketplacePublisherSupport).

## <a name="next-steps"></a>Étapes suivantes

- [Gérer votre compte Place de marché commerciale dans l’Espace partenaires](./manage-account.md)
