---
title: Point de terminaison HTTPS | Place de marché Azure
description: Configurez la gestion des prospects pour un point de terminaison HTTPS.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: evansma
ms.openlocfilehash: 6a34bdcab5a13af682515bbae96e9a1800ccc37f
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69901401"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>Configuration de la gestion des prospects à l’aide d’un point de terminaison HTTPS

Si votre système de gestion de la relation client (CRM) n’est pas explicitement pris en charge dans l’Espace partenaires pour recevoir des prospects de la Place de marché Azure et d’AppSource, vous pouvez utiliser un point de terminaison HTTPS dans Microsoft Flow pour gérer ces prospects. Avec un point de terminaison HTTPS, ces prospects peuvent être envoyés sous forme de notification par e-mail ou être écrits dans un système de gestion de la relation client (CRM) pris en charge par Microsoft Flow. Les instructions de cet article vous guideront tout au long du processus de création d’un nouveau flux avec Microsoft Flow, ce qui générera l’URL HTTP POST que vous allez entrer dans le portail de publication, dans le champ Gestion des prospects > **URL du point de terminaison HTTPS**. En outre, vous trouverez des instructions sur la façon dont vous pouvez tester votre flux avec un outil appelé [Postman](https://www.getpostman.com/downloads/) qui se trouve en ligne.

## <a name="create-a-flow-using-microsoft-flow"></a>Création d’un flux à l’aide de Microsoft Flow

1. Ouvrez la page web [Flow](https://flow.microsoft.com/). Sélectionnez **Se connecter** ou, si vous n’avez pas encore compte, **S’inscrire gratuitement** pour créer un compte Flow gratuit.

2. Connectez-vous et sélectionnez **Mes flux** dans la barre de menus.

3. Sélectionnez **+Automatisé - vierge**.

    ![Mes flux +Automatisé - vierge](./media/commercial-marketplace-lead-management-instructions-https/my-flows-automated.png)

4. Dans la fenêtre *Créer un flux automatisé*, sélectionnez **Ignorer**. 

    ![Créer un flux automatisé - Ignorer](./media/commercial-marketplace-lead-management-instructions-https/build-automated-flow.png)

5. Dans le champ **Rechercher parmi les connecteurs et les déclencheurs**, tapez « requête » pour rechercher le connecteur de requête.
6. Sous *Déclencheurs*, sélectionnez **Lors de la réception d’une requête HTTP**. 

    ![Déclencheur de demande - Déclencheurs](./media/commercial-marketplace-lead-management-instructions-https/request-connector.png)

7. Dans la fenêtre *Quand une requête HTTP arrive*, copiez et collez le schéma JSON ci-dessous dans la zone de texte **Schéma JSON du corps de la requête**. Ce schéma est utilisé par Microsoft pour vos données de prospect.

    ![Déclencheur de demande - Déclencheurs](./media/commercial-marketplace-lead-management-instructions-https/https-request-received.png)

    **Schéma JSON**

    ```JSON
    {
      "$schema": "https://json-schema.org/draft-04/schema#",
      "definitions": {},
      "id": "http://example.com/example.json",
      "properties": {
        "ActionCode": {
          "id": "/properties/ActionCode",
          "type": "string"
        },
        "OfferTitle": {
          "id": "/properties/OfferTitle",
          "type": "string"
        },
        "LeadSource": {
          "id": "/properties/LeadSource",
          "type": "string"
        },
        "UserDetails": {
          "id": "/properties/UserDetails",
          "properties": {
            "Company": {
              "id": "/properties/UserDetails/properties/Company",
              "type": "string"
            },
            "Country": {
              "id": "/properties/UserDetails/properties/Country",
              "type": "string"
            },
            "Email": {
              "id": "/properties/UserDetails/properties/Email",
              "type": "string"
            },
            "FirstName": {
              "id": "/properties/UserDetails/properties/FirstName",
              "type": "string"
            },
            "LastName": {
              "id": "/properties/UserDetails/properties/LastName",
              "type": "string"
            },
            "Phone": {
              "id": "/properties/UserDetails/properties/Phone",
              "type": "string"
            },
            "Title": {
              "id": "/properties/UserDetails/properties/Title",
              "type": "string"
            }
          },
          "type": "object"
        }
      },
      "type": "object"
    }
    ```

>[!Note]
>À ce stade de la configuration, vous pouvez choisir de vous connecter à un système CRM ou de configurer une notification par e-mail. Suivez les instructions restantes en fonction de votre choix.

### <a name="to-connect-to-a-crm-system"></a>Pour vous connecter à un système CRM

1. Sélectionnez **+ Nouvelle étape**.
2. Choisissez le système CRM de votre choix en le recherchant là où il est mentionné *Rechercher parmi les actions et les connecteurs*, puis sélectionnez-le dans la section *Actions* avec l’action de création d’un enregistrement. La capture d’écran suivante montre **Dynamics 365 - Créer un enregistrement** comme exemple.

    ![Créer un enregistrement](./media/commercial-marketplace-lead-management-instructions-https/create-new-record.png)

3. Indiquez le **nom de l’organisation** associé au système CRM. Sélectionnez **Prospects** dans la liste déroulante **Nom de l’entité**.

    ![Sélection de prospects](./media/commercial-marketplace-lead-management-instructions-https/select-leads.png)

4. Flow affiche un formulaire dans lequel fournir des informations sur les prospects. Vous pouvez mapper des éléments de la demande d’entrée en choisissant d’ajouter du contenu dynamique. La capture d’écran suivante présente **OfferTitle** comme exemple.

    ![Ajout de contenu dynamique](./media/commercial-marketplace-lead-management-instructions-https/add-dynamic-content.png)

5. Mappez les champs de votre choix, puis sélectionnez **Enregistrer** pour enregistrer votre flux. Une URL HTTP POST est créée et elle est accessible dans la fenêtre *Quand une requête HTTP arrive*. Copiez cette URL en utilisant le contrôle de copie qui se trouve à droite de l’URL HTTP POST. Cette opération est importante, car vous n’oublierez pas par erreur une partie de l’URL. Enregistrez cette URL, car vous en aurez besoin lors de la configuration de la gestion des prospects dans le portail de publication.

    ![Quand une requête HTTP arrive.](./media/commercial-marketplace-lead-management-instructions-https/when-http-request-received.png)

### <a name="to-set-up-email-notification"></a>Pour configurer une notification par e-mail

1. Maintenant que vous avez terminé le schéma JSON, sélectionnez **+Nouvelle étape**.
2. Sous **Choisir une action**, sélectionnez **Actions**.
3. Sous **Actions**, sélectionnez **Envoyer un e-mail (Office 365 Outlook)** .

    >[!Note]
    >Si vous souhaitez utiliser un autre fournisseur de messagerie, sélectionnez *Envoyer une notification par e-mail* comme action à la place.

    ![Ajouter une action de messagerie](./media/commercial-marketplace-lead-management-instructions-https/https-request-received-send-email.png)

4. Dans la fenêtre **Envoyer un e-mail**, configurez les champs obligatoires suivants :

   - **À** : entrez au moins une adresse valide à laquelle les prospects sont envoyés.
   - **Objet** : Flow vous donne la possibilité d’ajouter du contenu dynamique, par exemple **LeadSource**, dans la capture d’écran suivante. Commencez par taper un nom de champ, puis cliquez sur la liste déroulante du contenu dynamique dans la fenêtre contextuelle. 

        >[!Note] 
        > Lorsque vous ajoutez des noms de champs, vous pouvez les faire suivre d’un « : », puis appuyer sur Entrée pour créer une nouvelle ligne. Une fois que vous avez ajouté vos noms de champs, vous pouvez ajouter chaque paramètre associé à partir de la liste déroulante dynamique.

        ![Ajouter une action de messagerie à l’aide de contenu dynamique](./media/commercial-marketplace-lead-management-instructions-https/add-email-using-dynamic-content.png)

   - **Corps** : dans la liste déroulante de contenu dynamique, ajoutez les informations souhaitées dans le corps de l’e-mail. Par exemple, LastName, FirstName, Email et Company. <br> <br> Quand vous avez terminé de configurer la notification par e-mail, elle se présente comme l’exemple affiché dans la capture d’écran suivante.


       ![Ajouter une action de messagerie](./media/commercial-marketplace-lead-management-instructions-https/send-an-email.png)

5. Sélectionnez **Enregistrer** pour terminer votre flux. Une URL HTTP POST est créée et accessible dans la fenêtre *Quand une requête HTTP arrive*. Copiez cette URL en utilisant le contrôle de copie qui se trouve à droite de l’URL HTTP POST. Cette opération est importante, car vous n’oublierez pas par erreur une partie de l’URL. Enregistrez cette URL, car vous en aurez besoin lors de la configuration de la gestion des prospects dans le portail de publication.

   ![URL HTTP POST ](./media/commercial-marketplace-lead-management-instructions-https/http-post-url.png)

### <a name="testing"></a>Test

Vous pouvez vérifier que tout fonctionne comme prévu en utilisant les étapes suivantes à l’aide d’un outil appelé [Postman](https://app.getpostman.com/app/download/win64), qui peut être téléchargé en ligne. Il est disponible pour Windows. 

1. Lancez Postman et sélectionnez **Nouvelle** > **requête** pour configurer votre outil de test. 

   ![Demande de configuration de votre outil de test](./media/commercial-marketplace-lead-management-instructions-https/postman-request.png)

2. Renseignez le formulaire *Enregistrer la requête*, sélectionnez **Enregistrer** pour l’enregistrer dans le dossier que vous avez créé.

   ![Enregistrer la requête](./media/commercial-marketplace-lead-management-instructions-https/postman-save-to-test.png)

3. Sélectionnez **POST** dans la liste déroulante. 

   ![Tester mon flux](./media/commercial-marketplace-lead-management-instructions-https/test-my-flow.png)

4. Collez l’URL HTTP POST du flux que vous avez créé dans Microsoft Flow, là où il est mentionné *Entrer l’URL de la demande*.

   ![Coller l’URL HTTP POST](./media/commercial-marketplace-lead-management-instructions-https/paste-http-post-url.png)

5. Revenez au [flux](https://flow.microsoft.com/) et recherchez le flux que vous avez créé pour envoyer des prospects, en accédant à **Mes flux** dans la barre de menus Flux.  Sélectionnez les 3 points en regard du nom du flux et sélectionnez **Modifier**.

   ![Mes flux - Modifier](./media/commercial-marketplace-lead-management-instructions-https/my-flows-edit.png)

6. Sélectionnez **Tester** en haut à droite, sélectionnez « Je vais exécuter l’action de déclenchement », puis sélectionnez **Tester**. Vous verrez une indication dans le haut de l’écran indiquant que le test a démarré.

   ![Flux de test - Déclencheur](./media/commercial-marketplace-lead-management-instructions-https/test-flow-trigger-action.png)

7. Revenez à votre application Postman et sélectionnez **Envoyer** en regard de l’emplacement où vous avez collé l’URL HTTPS.

   ![Tester mon flux - Envoyer](./media/commercial-marketplace-lead-management-instructions-https/postman-send.png)

8. Revenez à votre flux et vérifiez le résultat. Si tout fonctionne comme prévu, vous verrez un message indiquant qu’il a réussi.

   ![Flux - Vérification des résultats](./media/commercial-marketplace-lead-management-instructions-https/my-flow-check-results.png)

9. Vous devez avoir également reçu un e-mail. Consultez votre messagerie. 

    >[!Note] 
    >Si vous ne voyez pas de message de test, vérifiez vos dossiers de courrier indésirable. Ci-dessous, vous remarquerez les étiquettes de champ que vous avez ajoutées lors de la configuration de la notification par e-mail. S’il s’agissait d’un prospect réel généré à partir de votre offre, vous pourriez aussi voir les coordonnées réelles du prospect dans le corps et dans la ligne d’objet.

   ![E-mail reçu](./media/commercial-marketplace-lead-management-instructions-https/email-received.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Configuration de votre offre pour l’envoi des prospects au point de terminaison HTTPS

Lorsque vous êtes prêt à configurer les informations de gestion des prospects pour votre offre dans le portail de publication, procédez comme suit :

1. Accédez à la page **Configuration de l’offre** de votre offre.
2. Sélectionnez **Connecter** dans la section de gestion des prospects.
3. Dans la fenêtre contextuelle des détails de la connexion, sélectionnez **Point de terminaison HTTPS** pour **Destination du prospect** et collez l’URL HTTP POST dans le flux que vous avez créé en suivant les étapes précédentes dans le champ **URL du point de terminaison HTTPS**.
4. Sélectionnez **Enregistrer**. 

>[!Note] 
>Vous devez terminer la configuration du reste de l’offre et la publier avant de pouvoir recevoir des prospects pour cette offre.

Quand des prospects sont générés, Microsoft les envoie au service Flow, qui les achemine vers le système CRM ou l’adresse e-mail que vous avez configuré.

![Gestion des prospects - Connecter](./media/commercial-marketplace-lead-management-instructions-https/lead-management-connect.png)

![Informations de connexion](./media/commercial-marketplace-lead-management-instructions-https/connection-details.png)

![Informations de connexion](./media/commercial-marketplace-lead-management-instructions-https/connection-details-1.png)

