---
title: Personnaliser l’interface utilisateur dans Azure Active Directory B2C
description: Découvrez comment personnaliser l’interface utilisateur de vos applications qui utilisent Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 6ebaeedf88bc02aa16e8be07fcb734e44ffa5bb6
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71258166"
---
# <a name="customize-the-user-interface-in-azure-active-directory-b2c"></a>Personnaliser l’interface utilisateur dans Azure Active Directory B2C

La personnalisation de l’interface utilisateur qu’Azure Active Directory B2C (Azure AD B2C) présente à vos clients permet d’offrir une expérience utilisateur fluide au sein de votre application. Ces expériences incluent la modification du profil, l’inscription, la connexion et la réinitialisation du mot de passe. Cet article présente les méthodes de personnalisation de l’interface utilisateur pour les flux utilisateur et les stratégies personnalisées.

## <a name="ui-customization-in-different-scenarios"></a>Personnalisation de l’interface utilisateur dans différents scénarios

Différentes méthodes sont disponibles pour personnaliser l’interface utilisateur des expériences utilisateur dans votre application, chacune étant indiquée pour un scénario donné.

### <a name="user-flows"></a>Flux d’utilisateurs

Si vous utilisez des [flux utilisateur](active-directory-b2c-reference-policies.md), vous pouvez modifier l’apparence de vos pages de flux utilisateur en utilisant des *modèles de mise en page* prédéfinis ou en utilisant vos propres fichiers HTML et CSS. Les deux méthodes sont décrites plus loin dans cet article.

Vous utilisez le [portail Azure](tutorial-customize-ui.md) pour configurer la personnalisation de l’interface utilisateur pour les flux utilisateur.

### <a name="custom-policies"></a>Stratégies personnalisées

Si vous utilisez des [stratégies personnalisées](active-directory-b2c-overview-custom.md) pour fournir des expériences de modification de profil, de réinitialisation du mot de passe, d’inscription ou de connexion dans votre application, utilisez des [fichiers de stratégie pour personnaliser l’interface utilisateur](active-directory-b2c-ui-customization-custom.md).

Si vous avez besoin de fournir du contenu dynamique basé sur la décision d’un client, utilisez des [stratégies personnalisées qui peuvent changer le contenu de la page de manière dynamique](active-directory-b2c-ui-customization-custom-dynamic.md) en fonction d’un paramètre qui est envoyé dans une chaîne de requête. Par exemple, vous pouvez changer l’image d’arrière-plan dans la page de connexion ou d’inscription Azure AD B2C en fonction d’un paramètre que vous transmettez depuis votre application web ou mobile.

### <a name="javascript"></a>JavaScript

Vous pouvez activer le code JavaScript côté client pour les [flux utilisateur](user-flow-javascript-overview.md) et les [stratégies personnalisées](page-layout.md).

### <a name="sign-in-only-ui-customization"></a>Personnalisation de l’interface utilisateur pour la connexion uniquement

Si vous fournissez uniquement une expérience de connexion, la page de réinitialisation du mot de passe associée et des e-mails de vérification, utilisez la même procédure de personnalisation que celle consacrée à une [page de connexion Azure AD](../active-directory/fundamentals/customize-branding.md).

Si les clients tentent de modifier leur profil avant de se connecter, ils sont redirigés vers une page que vous personnalisez à l’aide de la même procédure que celle utilisée pour la personnalisation de la page de connexion Azure AD.

## <a name="page-layout-templates"></a>Modèles de mise en page

Les flux utilisateur fournissent plusieurs modèles intégrés pour vous permettre de donner un aspect professionnel à vos pages d’expérience utilisateur. Ces modèles de mise en page peuvent également servir de point de départ à votre personnalisation.

Sous **Personnaliser** dans le menu de gauche, sélectionnez **Mises en page**, puis **Modèle**.

![Liste déroulante de sélection de modèle dans la page de flux d’utilisateur du portail Azure](media/customize-ui-overview/template-selection.png)

Sélectionnez ensuite un modèle dans la liste. Voici quelques exemples de pages de connexion pour chaque modèle :

| Bleu océan | Gris ardoise | Classique |
|:-:|:-:|:-:|
|![Exemple de modèle Bleu océan sur la page de connexion d’inscription](media/customize-ui-overview/template-ocean-blue.png)|![Exemple de modèle Gris ardoise sur la page de connexion et d’inscription](media/customize-ui-overview/template-slate-gray.png)|![Exemple de modèle Classique sur la page de connexion et d’inscription](media/customize-ui-overview/template-classic.png)|

Lorsque vous choisissez un modèle, la mise en page sélectionnée est appliquée à toutes les pages dans votre flux utilisateur et l’URI de chaque page est visible dans le champ **URI de la page personnalisée**.

## <a name="custom-html-and-css"></a>Fichiers HTML et CSS personnalisés

Azure AD B2C exécute le code dans le navigateur de votre client à l’aide d’une approche appelée [Partage des ressources cross-origin (CORS)](https://www.w3.org/TR/cors/).

Au moment de l’exécution, le contenu est chargé depuis une URL que vous spécifiez dans votre flux utilisateur ou stratégie personnalisée. Chaque page de l’expérience utilisateur charge son contenu à partir de l’URL que vous spécifiez pour cette page. Une fois le contenu chargé à partir de votre URL, il est fusionné avec un fragment HTML inséré par Azure AD B2C, puis la page est présentée à votre client.

Passez en revue les conseils suivants avant d’utiliser vos propres fichiers HTML et CSS pour personnaliser l’interface utilisateur :

- Azure AD B2C **fusionne** le contenu HTML dans vos pages. Ne copiez pas et n’essayez pas de modifier le contenu par défaut fourni par Azure AD B2C. Il est préférable de créer votre contenu HTML à partir de zéro et d’utiliser le contenu par défaut comme référence.
- **JavaScript** peut être inclus dans votre contenu personnalisé pour les [flux utilisateur](user-flow-javascript-overview.md) et les [stratégies personnalisées](javascript-samples.md).
- Les **versions de navigateur** prises en charge sont les suivantes :
  - Internet Explorer 11, 10 et Microsoft Edge
  - Prise en charge limitée pour Internet Explorer 9 et 8
  - Google Chrome 42.0 et ultérieur
  - Mozilla Firefox 38.0 et ultérieur
- N’ajoutez pas de **balises de formulaire** dans votre fichier HTML. Les balises de formulaire interfèrent avec les opérations POST générées par le code HTML injecté par Azure AD B2C.

### <a name="where-do-i-store-ui-content"></a>Où stocker le contenu de l’interface utilisateur ?

Lorsque vous utilisez vos propres fichiers HTML et CSS pour personnaliser l’interface utilisateur, vous pouvez héberger votre contenu d’IU sur n’importe quel point de terminaison HTTPS disponible publiquement qui prend en charge CORS. Par exemple, [Stockage Blob Azure](../storage/blobs/storage-blobs-introduction.md), serveurs web, réseaux de distribution de contenu, AWS S3 ou systèmes de partage de fichiers.

Le point important est que vous hébergez le contenu sur un point de terminaison HTTPS disponible publiquement avec CORS activé. Vous devez utiliser une URL absolue quand vous le spécifiez dans votre contenu.

## <a name="get-started-with-custom-html-and-css"></a>Bien démarrer avec des fichiers HTML et CSS personnalisés

Découvrez comment utiliser vos propres fichiers HTML et CSS dans vos pages d’expérience utilisateur en suivant ces instructions.

- Créez du contenu HTML bien formé avec un élément `<div id="api"></div>` vide situé quelque part dans le `<body>`. Cet élément marque l’endroit où le contenu Azure AD B2C est inséré. L’exemple suivant illustre une page minimale :

    ```html
    <!DOCTYPE html>
    <html>
      <head>
        <title>!Add your title here!</title>
        <link rel="stylesheet" href="https://mystore1.blob.core.windows.net/b2c/style.css">
      </head>
      <body>
        <h1>My B2C Application</h1>
        <div id="api"></div>   <!-- Leave this element empty because Azure AD B2C will insert content here. -->
      </body>
    </html>
    ```

- Utilisez les feuilles de style en cascade pour donner du style aux éléments d’interface utilisateur insérés par Azure AD B2C dans votre page. L’exemple suivant montre un fichier CSS simple qui inclut également des paramètres pour les éléments HTML d’inscription injectés :

    ```css
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center;
    }
    .entry {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center;
    }
    .create {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

- Héberger votre contenu sur un point de terminaison HTTPS (avec CORS activé). Vous devez activer à la fois les méthodes de requête GET et OPTIONS lors de la configuration de CORS.
- Créez ou modifiez un flux utilisateur ou une stratégie personnalisée pour utiliser le contenu que vous avez créé.

### <a name="html-fragments-from-azure-ad-b2c"></a>Fragments HTML à partir d’Azure AD B2C

Le tableau suivant répertorie les fragments HTML qu’Azure AD B2C fusionne dans l’élément `<div id="api"></div>` situé dans votre contenu.

| Page insérée | Description du HTML |
| ------------- | ------------------- |
| Sélection du fournisseur d’identité | Contient une liste de boutons pour les fournisseurs d’identité que l’utilisateur peut choisir durant l’inscription ou la connexion. Ces boutons incluent des fournisseurs d’identité sociale tels que Facebook et Google, ou des comptes locaux (basés sur une adresse e-mail ou un nom d’utilisateur). |
| Inscription avec un compte local | Contient un formulaire d’inscription avec un compte local basé sur une adresse e-mail ou un nom d’utilisateur. Le formulaire peut contenir différentes commandes de saisie telles que la zone de saisie de texte, celle du mot de passe, le bouton radio, les cases du menu déroulant à sélection unique et la sélection de plusieurs cases à cocher. |
| Inscription avec un compte social | Peut s’afficher lors de l’inscription à l’aide d’un compte existant d’un fournisseur d’identité sociale tel que Facebook ou Google. Cette page est utilisée quand des informations supplémentaires doivent être recueillies à partir du client à l’aide d’un formulaire d’inscription. |
| Connexion ou inscription unifiée | Gère l’inscription et la connexion des clients qui peuvent utiliser des fournisseurs d’identité sociale tels que Facebook ou Google ou des comptes locaux. |
| Authentification multifacteur | Les clients peuvent vérifier leur numéro de téléphone (par voie textuelle ou vocale) au cours de l’inscription ou de la connexion. |
| Error | Fournit des informations d’erreur au client. |

## <a name="localize-content"></a>Localiser le contenu

Vous localisez votre contenu HTML en activant la [personnalisation de la langue](active-directory-b2c-reference-language-customization.md) dans votre locataire Azure AD B2C. L’activation de cette fonctionnalité permet à Azure AD B2C de transmettre le paramètre OpenID Connect `ui-locales` à votre point de terminaison. Votre serveur de contenu peut utiliser ce paramètre pour fournir des pages HTML propres à la langue.

Le contenu peut être extrait de différents emplacements en fonction des paramètres régionaux utilisés. Dans votre point de terminaison avec CORS activé, vous configurez une structure de dossiers pour héberger du contenu pour des langues spécifiques. Vous devez appeler celui qui convient si vous utilisez la valeur générique `{Culture:RFC5646}`.

Par exemple, votre URI de page personnalisée peut ressembler à ceci :

```HTTP
https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html
```

Vous pouvez charger la page en français en extrayant le contenu à partir de :

```HTTP
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="examples"></a>Exemples

Différents exemples de fichiers modèles sont disponibles dans le référentiel [B2C-AzureBlobStorage-Client](https://github.com/azureadquickstarts/b2c-azureblobstorage-client) sur GitHub.

Les exemples de fichiers HTML et CSS des modèles se trouvent dans le répertoire [/sample_templates](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates).

## <a name="next-steps"></a>Étapes suivantes

- Si vous utilisez des **flux utilisateur**, vous pouvez commencer à personnaliser votre interface utilisateur avec le tutoriel :

    [Personnaliser l’interface utilisateur de vos applications dans Azure Active Directory B2C](tutorial-customize-ui.md).
- Si vous utilisez des **stratégies personnalisées**, vous pouvez commencer à personnaliser l’interface utilisateur avec l’article :

    [Personnaliser l’interface utilisateur de votre application à l’aide d’une stratégie personnalisée dans Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md).
