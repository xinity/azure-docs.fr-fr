---
title: Fichier Include
description: Fichier Include
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 09/24/2019
ms.openlocfilehash: 7e67b9e1c611d14b78db53f8662fb4e1f493a8df
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990201"
---
> [!IMPORTANT]
> * Le nouveau panneau **Azure Active Directory** > **Inscriptions d’applications** remplace l’ancien panneau **Azure Active Directory** > **Inscriptions d’applications (héritées)** depuis mai 2019.
> * Les inscriptions créées ou affichées dans le panneau hérité apparaissent automatiquement dans le nouveau panneau.
> * Pour obtenir des informations complètes sur la migration vers la nouvelle expérience d’inscription d’applications Azure, lisez le [Guide d’entraînement pour les inscriptions d’applications Azure](https://docs.microsoft.com/azure/active-directory/develop/app-registrations-training-guide) et le [Guide de démarrage rapide d’Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app).

1. Dans le [portail Azure](https://ms.portal.azure.com/), sélectionnez **Azure Active Directory** > **Inscriptions des applications** > **Nouvelle inscription**.

   [![Nouvelle inscription d’application dans Azure Active Directory](media/time-series-insights-aad-registration/active-directory-new-application-registration.png)](media/time-series-insights-aad-registration/active-directory-new-application-registration.png#lightbox)

   > [!TIP]
   > Le nouveau panneau d’inscription d’applications Azure Active Directory vous permet de filtrer les applications affichées en sélectionnant **Applications possédées**.

    Votre application est répertoriée ici une fois que vous l’enregistrez.

1. Donnez à l’application un nom et sélectionnez **Comptes dans cet annuaire organisationnel uniquement** pour spécifier les **Types de comptes pris en charge** qui peuvent accéder à l’API. Choisissez une URI valide pour rediriger les utilisateurs après l’authentification, puis **Inscrire**.

   [![Créer l’application dans Azure Active Directory](media/time-series-insights-aad-registration/active-directory-registration.png)](media/time-series-insights-aad-registration/active-directory-registration.png#lightbox)

1. Des informations importantes sur l’application Azure Active Directory s’affichent dans le panneau **Vue d’ensemble** de votre application répertoriée. Sélectionnez votre application sous **Applications possédées**, puis **Vue d’ensemble**.

   [![Copier l’ID de l’application](media/time-series-insights-aad-registration/active-directory-copy-application-id.png)](media/time-series-insights-aad-registration/active-directory-copy-application-id.png#lightbox)

   Copie votre **ID d’application (client)** à utiliser dans votre application cliente.

1. Le panneau **Authentification** spécifie les paramètres importants de configuration d’authentification. 

    1. **L’URI de redirection** doit correspondre à l’adresse fournie par la requête d’authentification :

        * Pour les applications hébergées dans un environnement de développement local, sélectionnez **Client public (mobile et bureau)** . Veillez à définir **Type de client par défaut** sur Oui.
        * Pour les applications à page unique hébergées sur Azure App Service, sélectionnez **Web**.

    1. Activez le flux d’octroi implicite en cochant les **jetons d’ID**.

   [![Créer une nouvelle clé secrète client](media/time-series-insights-aad-registration/active-directory-auth-blade.png)](media/time-series-insights-aad-registration/active-directory-auth-blade.png#lightbox)

   Cliquez sur **Enregistrer**.

1. Sélectionnez **Certificats et secrets**, puis **Nouveau secret client** pour créer un mot de passe d’application que le client peut utiliser pour prouver son identité.

   [![Créer une nouvelle clé secrète client](media/time-series-insights-aad-registration/active-directory-application-keys-save.png)](media/time-series-insights-aad-registration/active-directory-application-keys-save.png#lightbox)

   Le mot de passe de votre secret client est alors affiché. Copiez la clé dans votre éditeur de texte.

   > [!NOTE]
   > Vous avez la possibilité d’importer un certificat à la place. Pour une sécurité renforcée, un certificat est recommandé. Pour utiliser un certificat, sélectionnez **Charger un certificat**.

1. Associez votre application Azure Active Directory à Azure Time Series Insights. Sélectionnez **Autorisations de l’API** > **Ajouter une autorisation** > **API que mon organisation utilise**. 

    [![Associer une API à votre application Azure Active Directory](media/time-series-insights-aad-registration/active-directory-app-api-permission.png)](media/time-series-insights-aad-registration/active-directory-app-api-permission.png#lightbox)

   Saisissez `Azure Time Series Insights` dans la barre de recherche, puis sélectionnez `Azure Time Series Insights`.

1. Ensuite, spécifiez le genre d’autorisation d’API exigé par votre application. Par défaut, **Autorisations déléguées** est en surbrillance. Choisissez un type d’autorisation puis, sélectionnez **Ajouter les autorisations**.

    [![Spécifier le type d’autorisation d’API que votre application exige](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png)](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png#lightbox)