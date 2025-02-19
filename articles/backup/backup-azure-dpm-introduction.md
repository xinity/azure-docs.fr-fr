---
title: Préparer le serveur DPM pour sauvegarder des charges de travail vers Azure
description: Introduction à la sauvegarde de données DPM dans un coffre Azure Recovery Services.
ms.reviewer: kasinh
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: dacurwin
ms.openlocfilehash: 71070a778e54e51cdb528041f746489bb64e979c
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/12/2019
ms.locfileid: "68954708"
---
# <a name="prepare-to-back-up-workloads-to-azure-with-system-center-dpm"></a>Préparer la sauvegarde des charges de travail dans Azure avec System Center DPM

Cet article explique comment préparer les sauvegardes de System Center Data Protection Manager (DPM) sur Azure avec le service Sauvegarde Azure.

Cet article contient :

- Une vue d’ensemble du déploiement de DPM avec Sauvegarde Azure.
- Les conditions préalables et les limitations d’utilisation de Sauvegarde Azure avec DPM.
- Les étapes de préparation d’Azure, y compris la configuration d’un coffre de sauvegarde Recovery Services et éventuellement la modification du type de stockage Azure pour le coffre.
- Les étapes de préparation du le serveur DPM, y compris le téléchargement des informations d’identification du coffre, l’installation de l’agent Sauvegarde Azure et l’inscription du serveur DPM dans le coffre.
- Conseils de résolution des erreurs courantes.


## <a name="why-back-up-dpm-to-azure"></a>Pourquoi sauvegarder DPM sur Azure ?

[System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview) sauvegarde les données des fichiers et des applications. DPM interagit avec Azure Backup comme suit :

* **DPM s’exécute sur un serveur physique ou une machine virtuelle locale** : vous pouvez sauvegarder les données dans un coffre de sauvegarde dans Azure, en plus des sauvegardes sur disque et sur bande.
* **DPM s’exécute sur une machine virtuelle Azure** : depuis System Center 2012 R2 avec Update 3 ou version ultérieure, vous pouvez déployer DPM sur une machine virtuelle Azure. Vous pouvez sauvegarder des données sur des disques Azure attachés à la machine virtuelle ou utiliser Sauvegarde Azure pour sauvegarder les données dans un coffre de sauvegarde.

La sauvegarde de serveurs DPM dans Azure présente les avantages commerciaux suivants :

* Pour un déploiement local de DPM, Sauvegarde Azure constitue une alternative au déploiement à long terme sur bande.
* Pour DPM sur une machine virtuelle Azure, Sauvegarde Azure vous permet de déplacer le stockage du disque Azure. Le stockage de données plus anciennes dans votre coffre de sauvegarde vous permet de monter en puissance en stockant les nouvelles données sur un disque.

## <a name="prerequisites-and-limitations"></a>Conditions préalables et limitations

**Paramètre** | **Prérequis**
--- | ---
DPM sur une machine virtuelle Azure | System Center 2012 R2 avec correctif cumulatif 3 pour DPM 2012 R2 ou version ultérieure.
DPM sur un serveur physique | System Center 2012 SP1 ou version ultérieure, System Center 2012 R2.
DPM sur une machine virtuelle Hyper-V | System Center 2012 SP1 ou version ultérieure, System Center 2012 R2.
DPM sur une machine virtuelle VMware | System Center 2012 R2 avec correctif cumulatif 5 ou version ultérieure.
Composants | Windows PowerShell et .NET Framework 4.5 doivent être installés sur le serveur DPM.
Applications prises en charge | [En savoir plus](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix) sur ce que DPM peut sauvegarder.
Types de fichiers pris en charge | Ces types de fichiers peuvent être sauvegardés avec le service Sauvegarde Azure : chiffré (sauvegardes complètes uniquement), compressé (sauvegardes incrémentielles prises en charge), partiellement alloué (sauvegardes incrémentielles prises en charge), compressé et partiellement alloué (traité comme partiellement alloué).
Types de fichiers non pris en charge | Serveurs sur des systèmes de fichiers respectant la casse, liens physiques (ignorés), points d’analyse (ignorés), chiffrés et compressés (ignorés), chiffrés et partiellement alloués (ignorés), flux compressés, flux partiellement alloués.
Stockage local | Chaque machine que vous voulez sauvegarder doit disposer d’un espace de stockage d’au moins 5 % de la taille des données sauvegardées. Par exemple, la sauvegarde de 100 Go de données nécessite un minimum de 5 Go d'espace libre dans l'emplacement temporaire.
Stockage dans le coffre | Il n’existe aucune limite à la quantité de données que vous pouvez sauvegarder dans un coffre Sauvegarde Azure, mais la taille d’une source de données (par exemple, une machine virtuelle ou une base de données) ne doit pas dépasser 54400 Go.
Azure ExpressRoute | Si Azure ExpressRoute est configuré avec un peering privé ou Microsoft, il ne peut pas servir à sauvegarder les données dans Azure.<br/><br/> Si Azure ExpressRoute est configuré avec un peering public, il peut servir à sauvegarder les données dans Azure.<br/><br/> **Remarque :** Le peering public Azure est déconseillé pour les nouveaux circuits.
Agent Azure Backup | Si DPM est en cours d’exécution sur System Center 2012 SP1, installez le correctif cumulatif 2 ou version ultérieure pour DPM SP1. Il est nécessaire pour l’installation de l’agent.<br/><br/> Cet article décrit comment déployer la dernière version de l’agent Sauvegarde Azure, également appelé agent Microsoft Azure Recovery Service (MARS). Si une version antérieure est déployée, mettez-la à jour vers la dernière version pour vous assurer que la sauvegarde fonctionne comme prévu.

Avant de commencer, vous devez disposer d’un compte Azure avec la fonctionnalité Sauvegarde Azure activée. Si vous ne possédez pas de compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. En savoir plus sur la [tarification d'Azure Backup](https://azure.microsoft.com/pricing/details/backup/).

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-settings"></a>Modifier les paramètres de stockage

Vous pouvez choisir entre le stockage géoredondant et le stockage localement redondant.

- Par défaut, votre archivage utilise un stockage géo-redondant.
- Si vous utilisez Azure comme sauvegarde principale, conservez l’option de stockage géoredondant. Si vous souhaitez une option plus économique, mais moins durable, suivez la procédure ci-dessous pour configurer le stockage localement redondant.
- Pour en savoir plus sur le [stockage Azure](../storage/common/storage-redundancy.md) et les options de stockage [géoredondant](../storage/common/storage-redundancy-grs.md) et [localement redondant](../storage/common/storage-redundancy-lrs.md).
- Modifiez les paramètres de stockage avant la sauvegarde initiale. Si vous avez déjà sauvegardé un élément, arrêtez la sauvegarde dans le coffre avant de modifier les paramètres de stockage.

Pour modifier le paramètre de réplication du stockage :

1. Ouvrez le tableau de bord du coffre.

2. Dans **Gérer**, cliquez sur **Infrastructure de sauvegarde**.

3. Dans le menu **Configuration de la sauvegarde**, sélectionnez une option de stockage pour le coffre.

    ![Liste des archivages de sauvegarde](./media/backup-azure-dpm-introduction/choose-storage-configuration-rs-vault.png)

## <a name="download-vault-credentials"></a>Télécharger les informations d'identification de coffre

Vous utilisez les informations d’identification du coffre lorsque vous inscrivez le serveur DPM dans le coffre.

- Le fichier d’informations d’identification de coffre est un certificat qui est généré par le portail pour chaque archivage de sauvegarde.
- Le portail télécharge ensuite la clé publique pour le Service de contrôle d’accès (ACS).
- Pendant l’inscription de la machine, la clé privée du certificat devient accessible à l’utilisateur, qui authentifie la machine.
- En fonction de l’authentification, le service Sauvegarde Azure envoie des données vers le coffre identifié.

### <a name="best-practices-for-vault-credentials"></a>Meilleures pratiques pour les informations d’identification des coffres

Pour obtenir les informations d’identification, téléchargez le fichier d’informations d’identification du coffre via un canal sécurisé à partir du portail Azure :

- Les informations d’identification de coffre sont utilisées uniquement pendant le flux de travail d’inscription.
- Il est de votre responsabilité de vous assurer que le fichier d’informations d’identification de coffre est sûr et n’est pas compromis.
    - En cas de perte du contrôle des informations d’identification, les informations d’identification du coffre peuvent servir à inscrire d’autres machines auprès du coffre.
    - Toutefois, les données de sauvegarde sont chiffrées à l’aide d’une phrase secrète qui appartient au client. De fait, les données de sauvegarde existantes ne peuvent pas être compromises.
- Vérifiez que le fichier est enregistré dans un emplacement accessible à partir de votre serveur DPM. Si elles sont stockées dans un partage de fichiers/SMB, vérifiez les autorisations d’accès.
- Les informations d’identification du coffre expirent au bout de 48 heures. Vous pouvez télécharger de nouvelles informations d’identification de coffre aussi souvent que nécessaire. Cependant, seul le dernier fichier de d’informations d’identification de coffre peut être utilisé pendant l’inscription.
- Le service Sauvegarde Azure ne connaît pas la clé privée du certificat. De plus, cette clé n’est disponible ni dans le portail, ni dans le service.

Pour télécharger les informations d’identification de coffre sur un ordinateur local, procédez comme suit :

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
2. Ouvrez le coffre dans lequel vous voulez inscrire le serveur DPM.
3. Dans **paramètres**, cliquez sur **Propriétés**.

    ![Ouvrir le menu du coffre](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)

4. Dans **propriétés** > **Sauvegarder les informations d’identification**, cliquez sur **Télécharger**. Le portail génère le fichier d’informations d’identification de coffre à partir de la combinaison du nom du coffre et de la date actuelle et le rend disponible pour téléchargement.

    ![Téléchargement](./media/backup-azure-dpm-introduction/vault-credentials.png)

5. Cliquez sur **Enregistrer** pour télécharger les informations d’identification de coffre dans le dossier ou cliquez **Enregistrer sous** et spécifiez un emplacement. La création du fichier peut prendre jusqu’à une minute.


## <a name="install-the-backup-agent"></a>Installer l’agent Sauvegarde Azure

Toutes les machines sauvegardées par Sauvegarde Azure doivent disposer de l’agent de sauvegarde, également appelé agent Microsoft Azure Recovery Service (MARS). Installez l’agent sur le serveur DPM comme suit :

1. Ouvrez le coffre dans lequel vous voulez inscrire le serveur DPM.
2. Dans **paramètres**, cliquez sur **Propriétés**.

    ![Ouvrir le menu du coffre](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
3. Sur la page **Propriétés**, téléchargez l’agent Sauvegarde Azure.

    ![Téléchargement](./media/backup-azure-dpm-introduction/azure-backup-agent.png)


4. Après l’avoir téléchargé, exécutez MARSAgentInstaller.exe. Pour installer l’agent sur la machine DPM.
5. Sélectionnez un dossier d’installation et le dossier du cache de l’agent. L’espace disponible dans l’emplacement du cache doit être d’au moins 5 % des données sauvegardées.
6. Si vous utilisez un serveur proxy pour vous connecter à Internet, dans l’écran **Configuration du proxy** , entrez les détails du serveur proxy. Si vous utilisez un proxy authentifié, entrez les informations de nom d’utilisateur et mot de passe dans cet écran.
7. L’agent Sauvegarde Azure installe .NET Framework 4.5 et Windows PowerShell (s’ils ne sont pas déjà installés) pour terminer l’installation.
8. Une fois l’agent installé, **fermez** la fenêtre.

    ![fermez](../../includes/media/backup-install-agent/dpm_FinishInstallation.png)

## <a name="register-the-dpm-server-in-the-vault"></a>Inscrire le serveur DPM dans le coffre

1. Dans la console Administrateur DPM > **Gestion**, cliquez sur **Online**. Sélectionnez **Inscription**. L’assistant d’inscription s’ouvre.
2. Dans **Configuration du proxy**, spécifiez les paramètres de proxy si nécessaire.

    ![Configuration du proxy](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Proxy.png)
9. Dans **Coffre de sauvegarde**, recherchez et sélectionnez le fichier d’informations d’identification de coffre que vous avez téléchargé.

    ![Informations d’identification du coffre](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Credentials.jpg)

10. Dans **Paramètre de limitation**, vous pouvez éventuellement activer la limitation de bande passante pour les sauvegardes. Vous pouvez définir les limites de vitesse pour certains jours et certaines heures de travail.

    ![Paramètre de limitation](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Throttling.png)

11. Dans **Paramètre du dossier de récupération**, spécifiez un emplacement qui peut être utilisé lors de la récupération de données.

    - Sauvegarde Azure utilise cet emplacement comme zone de transit temporaire pour les données récupérées.
    - Une fois la récupération des données terminée, Sauvegarde Azure nettoie les données dans cette zone.
    - Cet emplacement doit disposer de suffisamment d’espace pour stocker les éléments que vous souhaitez récupérer.

    ![Paramètre de dossier de récupération](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_RecoveryFolder.png)

12. Dans **Paramètre de chiffrement**, générez ou indiquez une phrase secrète.

    - La phrase secrète est utilisée pour chiffrer les sauvegardes vers le cloud.
    - Vous devez indiquer 16 caractères au minimum.
    - Enregistrez le fichier dans un emplacement sécurisé, car il est nécessaire pour la récupération.

    ![Chiffrement](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Encryption.png)

    > [!WARNING]
    > Vous êtes seul détenteur de la phrase secrète de chiffrement et Microsoft n’a aucune visibilité sur cette information.
    > Si la phrase secrète est perdue ou oubliée, Microsoft ne peut pas vous aider à récupérer les données de sauvegarde.

13. Cliquez sur **Inscrire** pour inscrire le serveur DPM dans le coffre.

Une fois le serveur correctement inscrit dans l’archivage, vous êtes prêt à démarrer la sauvegarde dans Microsoft Azure.

## <a name="troubleshoot-vault-credentials"></a>Résoudre les problèmes d’informations d’identification de coffre

### <a name="expiration-error"></a>Erreur d’expiration

Le fichier d’informations d’identification d’archivage est valide uniquement pendant 48 heures (à partir de son téléchargement depuis le portail). Si vous rencontrez une erreur dans cet écran (par exemple, « Le fichier d’informations d’identification de coffre fourni a expiré »), connectez-vous au portail Azure et téléchargez de nouveau le fichier d’informations d’identification du coffre.

### <a name="access-error"></a>Erreur d’accès

Vérifiez que le fichier d’informations d’identification du coffre se trouve dans un emplacement accessible par l’application d’installation. Si vous rencontrez des erreurs liées à l’accès, copiez le fichier d’informations d’identification d’archivage dans un emplacement temporaire sur cet ordinateur et recommencez l’opération.

### <a name="invalid-credentials-error"></a>Erreur d’informations d’identification non valides

Si vous rencontrez une erreur d’informations d’identification de coffre non valides (par exemple, « Les informations d’identification de coffre fournies ne sont pas valides »), cela signifie que le fichier est endommagé ou qu’il ne possède pas les dernières informations d’identification associées au service de récupération.

- Recommencez l’opération après avoir téléchargé un nouveau fichier d’informations d’identification de coffre à partir du portail.
- Cette erreur se produit généralement si vous cliquez rapidement deux fois sur l’option **Télécharger les informations d’identification de coffre** dans le portail Azure. Dans ce cas, seul le deuxième fichier d’informations d’identification de coffre est valide.
