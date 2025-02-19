---
title: Tutoriel - Configurer LDAPS pour Azure Active Directory Domain Services | Microsoft Docs
description: Dans ce tutoriel, vous découvrez comment configurer le protocole LDAP sécurisé pour un domaine managé Azure Active Directory Domain Services.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 08/14/2019
ms.author: iainfou
ms.openlocfilehash: 2eaae9093614f1512dcd75d23c98bca871bf2850
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70193332"
---
# <a name="tutorial-configure-secure-ldap-for-an-azure-active-directory-domain-services-managed-domain"></a>Didacticiel : Configurer le protocole LDAP sécurisé pour un domaine managé Azure Active Directory Domain Services

Pour communiquer avec votre domaine managé Azure Active Directory Domain Services (Azure AD DS), le protocole LDAP (Lightweight Directory Access Protocol) est utilisé. Par défaut, le trafic LDAP n’est pas chiffré, ce qui constitue un problème de sécurité pour de nombreux environnements. Avec Azure AD DS, vous pouvez configurer le domaine managé pour qu’utilise le protocole LDAPS (Lightweight Directory Access Protocol sécurisé). Quand vous utilisez le protocole LDAP sécurisé, le trafic est chiffré. Le protocole LDAP sécurisé est également appelé LDAP over SSL (Secure Sockets Layer) / TLS (Transport Layer Security).

Ce tutoriel vous montre comment configurer LDAPS pour un domaine managé Azure AD DS.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un certificat numérique pour une utilisation avec Azure AD DS
> * Activer le protocole LDAP sécurisé pour Azure AD DS
> * Configurer le protocole LDAP sécurisé pour une utilisation sur l’internet public
> * Lier et tester le protocole LDAP sécurisé pour un domaine managé Azure AD DS

Si vous n’avez pas d’abonnement Azure, [créez un compte](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce tutoriel, vous avez besoin des ressources et des privilèges suivants :

* Un abonnement Azure actif.
    * Si vous n’avez pas d’abonnement Azure, [créez un compte](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un locataire Azure Active Directory associé à votre abonnement, synchronisé avec un annuaire local ou un annuaire cloud uniquement.
    * Si nécessaire, [créez un locataire Azure Active Directory][create-azure-ad-tenant] ou [associez un abonnement Azure à votre compte][associate-azure-ad-tenant].
* Un domaine managé Azure Active Directory Domain Services activé et configuré dans votre locataire Azure AD.
    * Si nécessaire, [créez et configurez une instance Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* L’outil *LDP.exe* installé sur votre ordinateur.
    * Si nécessaire, [installez les outils d’administration de serveur distant (RSAT)][rsat] pour *Active Directory Domain Services et LDAP*.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Dans ce tutoriel, vous configurez le protocole LDAP sécurisé pour le domaine managé Azure AD DS en utilisant le portail Azure. Pour commencer, connectez-vous au [portail Azure](https://portal.azure.com).

## <a name="create-a-certificate-for-secure-ldap"></a>Créer un certificat pour le protocole LDAP sécurisé

Pour utiliser le protocole LDAP sécurisé, un certificat numérique est utilisé pour chiffrer la communication. Ce certificat numérique est appliqué à votre domaine managé Azure AD DS et permet à des outils comme *LDP.exe* d’utiliser une communication chiffrée sécurisée lors de l’interrogation des données. Il existe deux manières de créer un certificat pour un accès LDAP sécurisé au domaine managé :

* Un certificat auprès d’une autorité de certification publique ou d’une autorité de certification d’entreprise.
    * Si votre organisation obtient ses certificats auprès d’une autorité de certification publique, obtenez le certificat LDAP sécurisé auprès de cette dernière. Si vous utilisez dans votre organisation une autorité de certification d’entreprise, obtenez le certificat LDAP sécurisé auprès de cette dernière.
    * Une autorité de certification publique fonctionne seulement quand vous utilisez un nom DNS personnalisé avec votre domaine managé Azure AD DS. Si le nom de domaine DNS de votre domaine managé se termine par *.onmicrosoft.com*, vous ne pouvez pas créer de certificat numérique pour sécuriser la connexion avec ce domaine par défaut. Microsoft détient le domaine *.onmicrosoft.com* : une autorité de certification publique n’émettra donc pas de certificat. Dans ce cas de figure, créez un certificat auto-signé et utilisez-le pour configurer le protocole LDAP sécurisé.
* Un certificat auto-signé que vous créez vous-même.
    * Cette approche est adaptée à des fins de test et c’est ce que montre ce tutoriel.

Le certificat que vous demandez ou que vous créez doit répondre aux exigences suivantes. Votre domaine managé rencontre des problèmes si vous activez le protocole LDAP sécurisé avec un certificat non valide :

* **Émetteur approuvé** : le certificat doit être émis par une autorité approuvée par les ordinateurs qui se connectent au domaine managé à l’aide du protocole LDAP sécurisé. Cette autorité peut être une autorité de certification publique ou une autorité de certification d’entreprise approuvée par ces ordinateurs.
* **Durée de vie** : le certificat doit être valide pour les 3 à 6 mois à venir. L’accès du protocole LDAP sécurisé à votre domaine géré est interrompu lorsque le certificat expire.
* **Nom du sujet** : le nom du sujet du certificat doit correspondre à votre domaine managé. Par exemple, si le nom de votre domaine est *contoso.com*, le nom du sujet du certificat doit être * *.contoso.com*.
    * Le nom DNS ou le nom alternatif du sujet du certificat doit être un certificat générique pour garantir le bon fonctionnement du protocole LDAP sécurisé avec Azure AD Domain Services. Les contrôleurs de domaine utilisent des noms aléatoires, et peuvent être supprimés ou ajoutés pour garantir que le service reste disponible.
* **Utilisation de la clé** : Le certificat doit être configuré pour les *signatures numériques* et le *chiffrage des clés*.
* **Rôle du certificat** : le certificat doit être valide pour l’authentification de serveur SSL.

Dans ce tutoriel, créons un certificat auto-signé pour le protocole LDAP sécurisé en utilisant PowerShell. Ouvrez une fenêtre PowerShell en tant qu’**Administrateur**, puis exécutez les commandes suivantes. Remplacez la variable *$dnsName* par le nom DNS utilisé par votre propre domaine managé, par exemple *contoso.com* :

```powershell
# Define your own DNS name used by your Azure AD DS managed domain
$dnsName="contoso.com"

# Get the current date to set a one-year expiration
$lifetime=Get-Date

# Create a self-signed certificate for use with Azure AD DS
New-SelfSignedCertificate -Subject *.$dnsName `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.$dnsName, $dnsName
```

L’exemple de sortie suivant montre que le certificat a été généré avec succès et qu’il est stocké dans le magasin de certificats local (*LocalMachine\MY*) :

```output
PS C:\WINDOWS\system32> New-SelfSignedCertificate -Subject *.$dnsName `
>>   -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
>>   -Type SSLServerAuthentication -DnsName *.$dnsName, $dnsName.com

   PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\MY

Thumbprint                                Subject
----------                                -------
959BD1531A1E674EB09E13BD8534B2C76A45B3E6  CN=contoso.com
```

## <a name="understand-and-export-required-certificates"></a>Comprendre et exporter les certificats nécessaires

Pour utiliser le protocole LDAP sécurisé, le trafic réseau est chiffré avec une infrastructure à clé publique (PKI).

* Une clé **privée** est appliquée au domaine managé Azure AD DS.
    * Cette clé privée est utilisée pour *déchiffrer* le trafic LDAP sécurisé. La clé privée doit être appliquée seulement au domaine managé Azure AD DS et ne pas être distribuée à grande échelle à des ordinateurs clients.
    * Un certificat qui inclut la clé privée utilise le format de fichier *.PFX*.
* Une clé **publique** est appliquée aux ordinateurs clients.
    * Cette clé publique est utilisée pour *chiffrer* le trafic LDAP sécurisé. La clé publique peut être distribuée aux ordinateurs clients.
    * Les certificats sans clé privée utilisent le format de fichier *.CER*.

Ces deux clés, les clés *privées* et *publiques*, permettent de garantir que seuls les ordinateurs appropriés peuvent communiquer entre eux. Si vous utilisez une autorité de certification publique ou une autorité de certification d’entreprise, vous recevez un certificat qui inclut la clé privée et qui peut être appliqué à un domaine managé Azure AD DS. La clé publique doit déjà être connue et approuvée par les ordinateurs clients. Dans ce tutoriel, vous avez créé un certificat auto-signé avec la clé privée : vous devez donc exporter les composants privés et publics appropriés.

### <a name="export-a-certificate-for-azure-ad-ds"></a>Exporter un certificat pour Azure AD DS

Avant de pouvoir utiliser le certificat numérique créé à l’étape précédente avec votre domaine managé Azure AD DS, exportez le certificat vers un fichier de certificat *.PFX* qui contient la clé privée.

1. Pour ouvrir la boîte de dialogue *Exécuter*, sélectionnez les touches **Windows** et **R**.
1. Ouvrez la console MMC (Microsoft Management Console) en entrant **mmc** dans la boîte de dialogue *Exécuter*, puis sélectionnez **OK**.
1. Sur l’invite **Contrôle de compte d’utilisateur**, cliquez sur **Oui** pour démarrer la console MMC en tant qu’administrateur.
1. Dans le menu **Fichier**, cliquez sur **Ajouter/Supprimer un composant logiciel enfichable...** .
1. Dans l’Assistant **Composant logiciel enfichable Certificats**, choisissez **Compte d’ordinateur**, puis cliquez sur **Suivant**.
1. Dans la page **Sélectionner un ordinateur**, sélectionnez **Ordinateur local (celui sur lequel s’exécute cette console)** , puis sélectionnez **Terminer**.
1. Dans la boîte de dialogue **Ajouter ou supprimer des composants logiciels enfichables**, cliquez sur **OK** et ajoutez le composant logiciel enfichable Certificats dans la console MMC.
1. Dans la fenêtre MMC, développez **Racine de la console**. Sélectionnez **Certificats (ordinateur local)** , puis développez le nœud **Personnel**, puis le nœud **Certificats**.

    ![Ouvrir le magasin de certificats personnels dans la console MMC](./media/tutorial-configure-ldaps/open-personal-store.png)

1. Le certificat auto-signé créé à l’étape précédente est affiché, par exemple *contoso.com*. Cliquez avec le bouton droit sur ce certificat, puis choisissez **Toutes les tâches > Exporter...**

    ![Exporter un certificat dans la console MMC](./media/tutorial-configure-ldaps/export-cert.png)

1. Dans l’**Assistant Exportation de certificat**, sélectionnez **Suivant**.
1. La clé privée du certificat doit être exportée. Si la clé privée n’est pas incluse dans le certificat exporté, l’action d’activation du protocole LDAP sécurisé pour votre domaine managé échoue.

    Dans la page **Exporter la clé privée**, cliquez sur **Oui, exporter la clé privée**, puis sélectionnez **Suivant**.
1. Les domaines managés par Azure AD DS prennent en charge seulement le format de fichier de certificat *.PFX* qui inclut la clé privée. N’exportez pas le certificat au format de fichier de certificat *.CER* sans la clé privée.

    Sur la page **Format de fichier d’exportation**, sélectionnez le format de fichier **Échange d’informations personnelles - PKCS #12 (.PFX)** pour le certificat exporté. Cochez la case pour *Inclure tous les certificats dans le chemin d’accès de certification si possible* :

    ![Choisir l’option permettant d’exporter le certificat au format de fichier PKCS 12 (.PFX)](./media/tutorial-configure-ldaps/export-cert-to-pfx.png)

1. Comme ce certificat est utilisé pour déchiffrer des données, vous devez en contrôler l’accès avec soin. Vous pouvez utiliser un mot de passe pour protéger le certificat. Sans le mot de passe correct, le certificat ne peut pas être appliqué à un service.

    Dans la page **Sécurité**, choisissez l’option **Mot de passe** pour protéger le fichier de certificat *.PFX*. Entrez et confirmez un mot de passe, puis sélectionnez **Suivant**. Ce mot de passe est utilisé dans la section suivante pour activer le protocole LDAP sécurisé pour votre domaine managé Azure AD DS.
1. Dans la page **Fichier à exporter**, spécifiez le nom du fichier et l’emplacement où vous voulez exporter le certificat, par exemple *C:\Users\accountname\azure-ad-ds.pfx*.
1. Dans la page de vérification, sélectionnez **Terminer** pour exporter le certificat vers un fichier de certificat *.PFX*. Une boîte de dialogue de confirmation s’affiche quand le certificat a été exporté avec succès.
1. Laissez la console MMC ouverte pour l’utiliser dans la section suivante.

### <a name="export-a-certificate-for-client-computers"></a>Exporter un certificat pour les ordinateurs clients

Les ordinateurs clients doivent approuver l’émetteur du certificat LDAP sécurisé afin d’être en mesure de se connecter au domaine managé avec LDAPS. Les ordinateurs clients ont besoin d’un certificat pour chiffrer correctement les données qui sont déchiffrées par Azure AD DS. Si vous utilisez une autorité de certification publique, l’ordinateur doit approuver automatiquement ces émetteurs de certificats et disposer d’un certificat correspondant. Dans ce tutoriel, vous utilisez un certificat auto-signé et vous générez un certificat incluant la clé privée de l’étape précédente. À présent, exportons puis installons le certificat auto-signé dans le magasin de certificats de confiance sur l’ordinateur client :

1. Revenez à la console MMC pour le magasin *Certificats (ordinateur local) > Personnel > Certificats*. Le certificat auto-signé créé à une étape précédente est affiché, par exemple *contoso.com*. Cliquez avec le bouton droit sur ce certificat, puis choisissez **Toutes les tâches > Exporter...**
1. Dans l’**Assistant Exportation de certificat**, sélectionnez **Suivant**.
1. Comme vous n’avez pas besoin de la clé privée pour les clients, dans la page **Exporter la clé privée**, sélectionnez **Non, ne pas exporter la clé privée**, puis sélectionnez **Suivant**.
1. Dans la page **Format de fichier d’exportation**, sélectionnez le format de fichier **X.509 encodé en base 64 (.cer)** pour le certificat exporté :

    ![Choisir l’option d’exporter le certificat dans le format de fichier « X.509 encodé en base 64 (*.cer) »](./media/tutorial-configure-ldaps/export-cert-to-cer-file.png)

1. Dans la page **Fichier à exporter**, spécifiez le nom du fichier et l’emplacement où vous voulez exporter le certificat, par exemple *C:\Users\accountname\azure-ad-ds-client.cer*.
1. Dans la page de vérification, sélectionnez **Terminer** pour exporter le certificat vers un fichier de certificat *.CER*. Une boîte de dialogue de confirmation s’affiche quand le certificat a été exporté avec succès.

Le fichier de certificat *.CER* peut désormais être distribué aux ordinateurs clients qui doivent approuver la connexion LDAP sécurisée au domaine managé Azure AD DS. Installons le certificat sur l’ordinateur local.

1. Ouvrez l’Explorateur de fichiers et accédez à l’emplacement où vous avez enregistré le fichier de certificat *.CER*, par exemple *C:\Users\accountname\azure-ad-ds-client.cer*.
1. Cliquez avec le bouton droit sur le fichier de certificat *.CER*, puis choisissez **Installer le certificat**.
1. Dans l’**Assistant Importation de certificat**, choisissez de stocker le certificat dans la *Machine locale*, puis sélectionnez **Suivant** :

    ![Choisir l’option d’importer le certificat dans le magasin de la machine locale](./media/tutorial-configure-ldaps/import-cer-file.png)

1. Quand vous y êtes invité, choisissez **Oui** pour permettre à l’ordinateur d’apporter des modifications.
1. Choisissez de **Sélectionner automatiquement le magasin de certificats en fonction du type de certificat**, puis sélectionnez **Suivant**.
1. Dans la page de vérification, sélectionnez **Terminer** pour importer le certificat *.CER*. Une boîte de dialogue de confirmation s’affiche quand le certificat a été importé avec succès.

## <a name="enable-secure-ldap-for-azure-ad-ds"></a>Activer le protocole LDAP sécurisé pour Azure AD DS

Avec un certificat numérique créé et exporté incluant la clé privée, et l’ordinateur client défini pour approuver la connexion, activez maintenant le protocole LDAP sécurisé sur votre domaine managé Azure AD DS. Pour activer le protocole LDAP sécurisé sur un domaine managé Azure AD DS, effectuez les étapes de configuration suivantes :

1. Dans le [portail Azure](https://portal.azure.com), recherchez *domain services* (services de domaine) dans la zone **Rechercher des ressources**. Sélectionnez **Azure AD Domain Services** dans les résultats de la recherche.

    ![Rechercher et sélectionner votre domaine managé Azure AD DS dans le portail Azure](./media/tutorial-configure-ldaps/search-for-domain-services.png)

1. Choisissez votre domaine managé, par exemple *contoso.com*.
1. Sur le côté gauche de la fenêtre Azure AD DS, choisissez **LDAP sécurisé**.
1. Par défaut, l’accès LDAP sécurisé à votre domaine managé est désactivé. Basculez **LDAP sécurisé** sur **Activer**.
1. L’accès LDAP sécurisé à votre domaine managé via Internet est désactivé par défaut. Quand vous activez l’accès LDAP sécurisé public, votre domaine est vulnérable aux attaques par force brute via Internet. À l’étape suivante, un groupe de sécurité réseau est configuré pour verrouiller l’accès seulement aux plages d’adresses IP sources nécessaires.

    Basculez **Autorisez l’accès LDAP sécurisé sur Internet** sur **Activer**.

1. Sélectionnez l’icône de dossier en regard de **Fichier .PFX avec certificat LDAP sécurisé**. Accédez au chemin du fichier *.PFX*, puis sélectionnez le certificat créé à l’étape précédente qui inclut la clé privée.

    Comme indiqué dans la section précédente concernant les exigences en matière de certificats, vous ne pouvez pas utiliser un certificat d’une autorité de certification publique avec le domaine *.onmicrosoft.com* par défaut. Microsoft détient le domaine *.onmicrosoft.com* : une autorité de certification publique n’émettra donc pas de certificat. Vérifiez que votre certificat est au format approprié. Si ce n’est pas le cas, la plateforme Azure génère des erreurs de validation de certificat quand vous activez le protocole LDAP sécurisé.

1. Entrez le **Mot de passe pour déchiffrer le fichier .PFX** défini dans une étape précédente quand le certificat a été exporté vers un fichier *.PFX*.
1. Sélectionnez **Enregistrer** pour activer le protocole LDAP sécurisé.

    ![Activer le protocole LDAP sécurisé pour un domaine managé Azure AD DS dans le portail Azure](./media/tutorial-configure-ldaps/enable-ldaps.png)

Une notification vous informe que le protocole LDAP sécurisé est en cours de configuration pour le domaine managé. Vous ne pouvez pas modifier d’autres paramètres pour le domaine managé tant que cette opération n’est pas terminée.

L’activation du protocole LDAP sécurisé pour votre domaine managé prend quelques minutes. Si le certificat LDAP sécurisé que vous fournissez ne correspond pas aux critères demandés, l’action d’activation du protocole LDAP sécurisé pour le domaine managé échoue. Voici quelques raisons d’échec courantes : le nom de domaine est incorrect, le certificat expire bientôt ou il a déjà expiré. Vous pouvez recréer le certificat avec des paramètres valides, puis activer le protocole LDAP sécurisé en utilisant ce certificat mis à jour.

## <a name="lock-down-secure-ldap-access-over-the-internet"></a>Verrouiller l’accès LDAP sécurisé via Internet

Quand vous activez l’accès LDAP sécurisé via Internet à votre domaine managé Azure AD DS, cela crée une menace de sécurité. Le domaine managé est accessible depuis Internet sur le port TCP 636. Il est recommandé de restreindre l’accès au domaine managé à des adresses IP connues spécifiques pour votre environnement. Une règle de groupe de sécurité réseau Azure peut être utilisée pour limiter l’accès au protocole LDAP sécurisé.

Créons une règle pour autoriser l’accès LDAP sécurisé entrant sur le port TCP 636 à partir d’un ensemble spécifique d’adresses IP. Une règle *DenyAll* par défaut avec une priorité inférieure s’applique à tous les autres trafics entrants provenant d’Internet, de sorte que seules les adresses spécifiées peuvent atteindre votre domaine managé Azure AD DS en utilisant le protocole LDAP sécurisé.

1. Dans le portail Azure, sélectionnez *Groupes de ressources* sur le côté gauche.
1. Choisissez votre groupe de ressources, par exemple *myResourceGroup*, puis sélectionnez votre groupe de sécurité réseau, par exemple *AADDS-contoso.com-NSG*.
1. La liste des règles de sécurité de trafic entrant et sortant existantes s’affiche. Sur le côté gauche de la fenêtre du groupe de sécurité réseau, choisissez **Sécurité > Règles de sécurité de trafic entrant**.
1. Sélectionnez **Ajouter**, puis créez une règle pour autoriser le port *TCP* *636*. Pour améliorer la sécurité, choisissez la source *Adresses IP*, puis spécifiez votre propre adresse ou plage d’adresses IP pour votre organisation.

    | Paramètre                           | Valeur        |
    |-----------------------------------|--------------|
    | Source                            | Adresses IP |
    | Adresses IP sources / Plages CIDR | Adresse ou plage d’adresses IP valide pour votre environnement |
    | Plages de ports source                | *            |
    | Destination                       | Quelconque          |
    | Plages de ports de destination           | 636          |
    | Protocole                          | TCP          |
    | Action                            | AUTORISER        |
    | Priorité                          | 401          |
    | Nom                              | AllowLDAPS   |

1. Quand vous êtes prêt, sélectionnez **Ajouter** pour enregistrer et appliquer la règle.

    ![Créer une règle de groupe de sécurité réseau pour sécuriser l’accès LDAP sécurisé via Internet](./media/tutorial-configure-ldaps/create-inbound-nsg-rule-for-ldaps.png)

## <a name="configure-dns-zone-for-external-access"></a>Configurer une zone DNS pour l’accès externe

Avec l’accès LDAP sécurisé activé via Internet, mettez à jour la zone DNS afin que les ordinateurs clients puissent détecter ce domaine managé. L’*Adresse IP externe de LDAP sécurisé* figure sous l’onglet **Propriétés** pour votre domaine managé Azure AD DS :

![Afficher l’adresse IP externe de LDAP sécurisé pour votre domaine managé Azure AD DS dans le portail Azure](./media/tutorial-configure-ldaps/ldaps-external-ip-address.png)

Configurez votre fournisseur DNS externe pour créer un enregistrement d’hôte, par exemple *ldaps*, qui doit être résolu en cette adresse IP externe. Pour tester localement d’abord sur votre ordinateur, vous pouvez ’abord créer une entrée dans le fichier hosts de Windows. Pour modifier le fichier hosts sur votre ordinateur local, ouvrez le *Bloc-notes* en tant qu’administrateur , puis ouvrez le fichier *C:\Windows\System32\drivers\etc*

L’exemple d’entrée DNS suivant, avec votre fournisseur DNS externe ou dans le fichier hosts local, résout le trafic pour *ldaps.contoso.com* en adresse IP externe *40.121.19.239* :

```
40.121.19.239    ldaps.contoso.com
```

## <a name="test-queries-to-the-managed-domain"></a>Tester les requêtes sur le domaine managé

Pour vous connecter et vous lier à votre domaine managé Azure AD DS, et effectuer une recherche sur LDAP, vous utilisez également *LDP.exe*. Cet outil est inclus dans le package Outils d’administration de serveur distant (RSAT). Pour plus d’informations, consultez [Installer les outils d’administration de serveur distant][rsat].

1. Ouvrez *LDP.exe*, puis connectez-vous au domaine managé. Sélectionnez **Connexion**, puis choisissez **Se connecter...** .
1. Entrez le nom du domaine DNS LDAP sécurisé de votre domaine managé créé à l’étape précédente, par exemple *ldaps.contoso.com*. Pour utiliser le protocole LDAP sécurisé, définissez **Port** sur *636*, puis cochez la case pour **SSL**.
1. Sélectionnez **OK** pour vous connecter au domaine managé.

Ensuite, établissez une liaison à votre domaine managé Azure AD DS. Les utilisateurs (et les comptes de service) ne peuvent pas établir des liaisons simples LDAP si vous désactivez la synchronisation de hachage des mots de passe NTLM sur votre instance Azure AD DS. Pour plus d’informations sur la désactivation de la synchronisation de hachage de mot de passe NTLM, consultez [Sécuriser votre domaine managé Azure AD DS][secure-domain].

1. Sélectionnez l’option de menu **Connexion**, puis choisissez **Lier...** .
1. Fournissez les informations d’identification d’un compte d’utilisateur appartenant au groupe *Administrateurs AAD DC*, par exemple *contosoadmin*. Entrez le mot de passe du compte d’utilisateur, puis entrez votre domaine, par exemple *contoso.com*.
1. Pour **Type de liaison**, choisissez l’option pour *Liaison avec informations d’identification*.
1. Sélectionnez **OK** pour établir une liaison à votre domaine managé Azure AD DS.

Pour voir les objets stockés dans votre domaine managé Azure AD DS :

1. Sélectionnez l’option de menu **Afficher**, puis choisissez **Arborescence**.
1. Laissez le champ *BaseDN* vide, puis sélectionnez **OK**.
1. Choisissez un conteneur, par exemple *Utilisateurs AADDC*, puis cliquez avec le bouton droit sur le conteneur et choisissez **Rechercher**.
1. Laissez tels quels les champs préremplis, puis sélectionnez **Exécuter**. Les résultats de la requête sont affichés dans la fenêtre de droite.

    ![Rechercher des objets dans votre domaine managé Azure AD DS en utilisant LDP.exe](./media/tutorial-configure-ldaps/ldp-query.png)

Pour interroger directement un conteneur spécifique, dans le menu **Afficher > Arborescence**, vous pouvez spécifier un **BaseDN**, comme *OU=AADDC Users,DC=CONTOSO,DC=COM* ou *OU=AADDC Computers,DC=CONTOSO,DC=COM*. Pour plus d’informations sur la façon de mettre en forme et de créer des requêtes, consultez [Principes de base des requêtes LDAP][ldap-query-basics].

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous avez ajouté une entrée DNS au fichier hosts local de votre ordinateur pour tester la connectivité dans le cadre de ce tutoriel, supprimez cette entrée et ajoutez un enregistrement formel dans votre zone DNS. Pour supprimer l’entrée du fichier hosts local, effectuez les étapes suivantes :

1. Sur votre machine locale, ouvrez le *Bloc-notes* en tant qu’administrateur.
1. Recherchez et ouvrez le fichier *C:\Windows\System32\drivers\etc*
1. Supprimez la ligne de l’enregistrement que vous avez ajouté, par exemple `40.121.19.239    ldaps.contoso.com`

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Créer un certificat numérique pour une utilisation avec Azure AD DS
> * Activer le protocole LDAP sécurisé pour Azure AD DS
> * Configurer le protocole LDAP sécurisé pour une utilisation sur l’internet public
> * Lier et tester le protocole LDAP sécurisé pour un domaine managé Azure AD DS

> [!div class="nextstepaction"]
> [Configurer la synchronisation de hachage du mot de passe pour un environnement Azure AD hybride](tutorial-configure-password-hash-sync.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[secure-domain]: secure-your-domain.md

<!-- EXTERNAL LINKS -->
[rsat]: /windows-server/remote/remote-server-administration-tools
[ldap-query-basics]: /windows/desktop/ad/creating-a-query-filter
