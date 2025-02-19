---
title: Cinq étapes pour sécuriser votre infrastructure d’identité dans Azure Active Directory
description: Ce document présente une liste d’actions importantes que les administrateurs doivent implémenter pour mieux sécuriser leur organisation à l’aide des fonctionnalités d’Azure AD
author: martincoetzer
manager: manmeetb
tags: azuread
ms.service: security
ms.subservice: security-fundamentals
ms.topic: conceptual
ms.workload: identity
ms.date: 06/18/2018
ms.author: martinco
ms.openlocfilehash: fb17d1b95d74a67f220651cf198f367bdd31f19f
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129313"
---
# <a name="five-steps-to-securing-your-identity-infrastructure"></a>Cinq étapes pour sécuriser votre infrastructure d’identité

Si vous lisez ce document, c'est que vous connaissez l’importance de la sécurité. Vous êtes sans doute déjà responsable de la sécurisation de votre organisation. Si vous avez besoin de convaincre d’autres utilisateurs de l’importance de la sécurité, demandez-leur de lire le dernier [Rapport de renseignement sur la sécurité (SIR) de Microsoft](https://go.microsoft.com/fwlink/p/?linkid=2073747).

Ce document vous aidera à obtenir une position plus sécurisée à l’aide des fonctionnalités d’Azure Active Directory grâce à une liste de vérification en cinq étapes pour protéger votre organisation contre les cyberattaques.

Cette liste de vérification vous aidera à déployer rapidement les actions recommandées critiques pour protéger votre organisation immédiatement et explique comment :

* Renforcer vos informations d’identification.
* Réduire votre surface d’attaque.
* Automatiser la réponse aux menaces.
* Augmenter votre connaissance de l’audit et du monitoring.
* Permettre une sécurité de l’utilisateur plus prévisible et complète grâce à l’auto-assistance.

> [!NOTE]
> La plupart des recommandations de ce document s’appliquent seulement aux applications qui sont configurées pour utiliser Azure Active Directory comme fournisseur d’identité. La configuration des applications pour l’authentification unique garantit que les avantages des stratégies pour les informations d’identification, la détection des menaces, l’audit, la journalisation et d’autres fonctionnalités s’ajoutent à ces applications. [L’authentification unique via Azure Active Directory](../../active-directory/manage-apps/configure-single-sign-on-portal.md) est le fondement sur lequel se basent toutes ces recommandations.

Les suggestions faites dans ce document sont alignées sur [Identity Secure Score](../../active-directory/fundamentals/identity-secure-score.md), une évaluation automatisée de la configuration de sécurité de l'identité de votre locataire Azure AD. Les organisations peuvent utiliser la page Identity Secure Score dans le portail Azure AD pour trouver des failles dans leur configuration de sécurité actuelle et s’assurer qu’elles respectent les meilleures pratiques de sécurité actuelles de Microsoft. L’implémentation de chaque suggestion de la page Secure Score augmentera votre score et vous permettra de suivre vos progrès, tout en vous aidant à comparer votre implémentation à celle d’autres organisations de taille comparable de votre secteur d’activité.

![Identity Secure Score](./media/steps-secure-identity/azure-ad-sec-steps0.png)

## <a name="before-you-begin-protect-privileged-accounts-with-mfa"></a>Avant de commencer : Protéger des comptes privilégiés avec l'authentification multifacteur (MFA)

Avant de commencer, assurez-vous de ne pas être compromis pendant que vous lisez cette liste de vérification. Vous devez d’abord protéger vos comptes privilégiés.

Les attaquants qui prennent le contrôle de comptes privilégiés peuvent causer des dégâts considérables. Il est donc essentiel de protéger d’abord ces comptes. Activez et exigez [Azure multi-Factor Authentication](../../active-directory/authentication/multi-factor-authentication.md) (MFA) pour tous les administrateurs de votre organisation utilisant la [Protection de la ligne de base](../../active-directory/conditional-access/baseline-protection.md). Si vous n’avez pas encore implémenté l’authentification MFA, faites-le maintenant ! C’est particulièrement important.

Vous êtes prêt ? Nous pouvons commencer la liste de vérification.

## <a name="step-1---strengthen-your-credentials"></a>Étape 1 - Renforcer vos informations d’identification 

La plupart des failles de sécurité en entreprise proviennent d’un compte compromis avec l’une des méthodes, telles que le jet de mot de passe, la réexécution de violation ou le hameçonnage. En savoir plus sur ces attaques dans cette vidéo (45 min) :
> [!VIDEO https://www.youtube.com/embed/uy0j1_t5Hd4]

### <a name="make-sure-your-organization-use-strong-authentication"></a>Vérifier que votre organisation utilise une authentification renforcée

Étant donné la fréquence à laquelle les mots de passe sont devinés, hameçonnés, volés par un logiciel malveillant ou réutilisés, il est essentiel de renforcer le mot de passe à l’aide d’une certaine forme d’informations d’identification fortes : en savoir plus sur [Azure multi-Factor Authentication](../../active-directory/authentication/multi-factor-authentication.md).

### <a name="start-banning-commonly-attacked-passwords-and-turn-off-traditional-complexity-and-expiration-rules"></a>Commencer à interdire des mots de passe couramment attaqués et à désactiver la complexité traditionnelle et les règles d’expiration.

De nombreuses organisations utilisent la complexité traditionnelle (qui requiert des caractères spéciaux, des chiffres, des majuscules et des minuscules) et les règles d’expiration des mots de passe. Les[ recherches de Microsoft](https://aka.ms/passwordguidance) ont montré que ces stratégies amènent les utilisateurs à choisir des mots de passe plus faciles à deviner.

La fonctionnalité [mot de passe interdit dynamiquement](../../active-directory/authentication/concept-password-ban-bad.md) d’Azure AD utilise le comportement actuel des attaquants pour empêcher les utilisateurs de définir des mots de passe faciles à deviner. Cette fonctionnalité est toujours activée lorsque des utilisateurs sont créés dans le cloud, mais est désormais également disponible pour les organisations hybrides lorsqu’elles déploient [Azure AD password protection for Windows Server Active Directory](../../active-directory/authentication/concept-password-ban-bad-on-premises.md). La protection de mot de passe Azure AD empêche les utilisateurs de choisir ces mots de passe communs et peut être étendue pour bloquer tout mot de passe contenant des mots clés personnalisés spécifiés par vos soins. Vous pouvez par exemple empêcher vos utilisateurs de choisir des mots de passe contenant les noms de produits de votre entreprise ou d’une équipe de sport locale.

Microsoft recommande d’adopter la stratégie de mot de passe moderne suivante conformément à l’[aide NIST](https://pages.nist.gov/800-63-3/sp800-63b.html) :

1. Exiger des mots de passe comptant au moins 8 caractères. Un mot de passe plus long n’est pas forcément mieux adapté, car les utilisateurs choisissent alors des mots de passe prévisibles, enregistrent leurs mots de passe dans des fichiers ou les notent quelque part.
2. Désactivez les règles d’expiration, qui incitent les utilisateurs à choisir des mots de passe faciles à deviner comme **Spring2019!** .
3. Désactivez les exigences de composition de caractères et empêchez les utilisateurs de choisir des mots de passe fréquemment attaqués, car cela incite les utilisateurs à choisir des substitutions de caractères prévisibles dans les mots de passe.

Vous pouvez utiliser [PowerShell pour empêcher l’expiration des mots de passe](../../active-directory/authentication/concept-sspr-policy.md) des utilisateurs en créant des identités directement dans Azure AD. Les organisations hybrides doivent implémenter ces stratégies à l’aide de [paramètres de stratégie de groupe de domaine](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/hh994572(v%3dws.10)) ou de [Windows PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/set-addefaultdomainpasswordpolicy).

### <a name="protect-against-leaked-credentials-and-add-resilience-against-outages"></a>Protection contre la fuite d’informations d’identification et ajout de la tolérance aux pannes

Si votre organisation utilise une solution d’identité hybride avec authentification ou fédération directe, vous devez activer la synchronisation du hachage de mot de passe pour les deux raisons suivantes :

* Le rapport [Utilisateurs avec des informations d’identification volées](../../active-directory/reports-monitoring/concept-risk-events.md) dans l’administration d’Azure AD vous avertit si des paires nom d’utilisateur/mot de passe ont été exposées sur le « dark web ». Un volume incroyable de mots de passe fait l’objet d’une fuite via le hameçonnage, les programmes malveillants et la réutilisation de mot de passe sur des sites tiers qui sont ensuite victimes d’une violation de la sécurité. Microsoft recherche un grand nombre de ces informations d'identification ayant fuité et vous indiquera, dans ce rapport, si elles correspondent aux informations d’identification de votre organisation, mais uniquement si vous [activez la synchronisation de hachage du mot de passe](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md) !
* En cas de panne locale (par exemple, au cours d’une attaque de ransomware), vous pourrez basculer vers [l’authentification cloud à l’aide de la synchronisation de hachage du mot de passe](choose-ad-authn.md). Cette méthode d’authentification de secours vous permettra de continuer à accéder aux applications configurées pour l’authentification avec Azure Active Directory, notamment Office 365. Dans ce cas, le personnel informatique n’aura pas besoin de recourir à des comptes e-mail personnels pour partager des données jusqu’à ce que la panne locale soit résolue.

En savoir plus sur le fonctionnement de la [synchronisation de hachage du mot de passe](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md).

> [!NOTE]
> Si vous activez la synchronisation du hachage de mot de passe et que vous utilisez Azure AD Domain Services, les hachages Kerberos (AES 256) et éventuellement NTLM (RC4, no salt) sont également chiffrés et synchronisés dans Azure AD. 

### <a name="implement-ad-fs-extranet-smart-lockout"></a>Implémenter le verrouillage intelligent extranet AD FS

Les organisations qui configurent des applications pour s’authentifier directement auprès d’Azure AD bénéficient du [verrouillage intelligent Azure AD](../../active-directory/authentication/concept-sspr-howitworks.md). Si vous utilisez AD FS dans Windows Server 2012 R2, implémentez la [protection par verrouillage extranet](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection) AD FS. Si vous utilisez AD FS sur Windows Server 2016, implémentez le [verrouillage extranet intelligent](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016). Le verrouillage extranet intelligent d’AD FS protège contre les attaques par force brute qui ciblent AD FS tout en empêchant le verrouillage des utilisateurs dans Active Directory.

### <a name="take-advantage-of-intrinsically-secure-easier-to-use-credentials"></a>Tirer parti d’informations d’identification intrinsèquement sécurisées et plus faciles à utiliser

Avec [Windows Hello](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification), vous pouvez remplacer les mots de passe par une authentification à deux facteurs forte sur les PC et appareils mobiles. Cette authentification se compose d’un nouveau type d’informations d’identification utilisateur liées de manière sécurisée à un appareil et utilise un code biométriques ou un code PIN.

## <a name="step-2---reduce-your-attack-surface"></a>Étape 2 - Réduire votre surface d’attaque

Étant donné l’omniprésence de la compromission des mots de passe, il est essentiel de réduire la surface d’attaque de votre organisation. Éliminer l’utilisation des protocoles plus anciens et moins sécurisés, limiter les points d’entrée et exercer un contrôle plus important de l’accès administratif aux ressources peuvent aider à réduire la surface d’attaque.

### <a name="block-legacy-authentication"></a>Bloquer l’authentification héritée

Les applications utilisant leurs propres méthodes héritées pour s’authentifier auprès d’Azure AD et accéder aux données d’entreprise présentent un autre risque pour les organisations. Exemples d’applications utilisant une authentification héritée : clients POP3, IMAP4 ou SMTP. Les applications à authentification héritée s’authentifient au nom de l’utilisateur et empêchent Azure AD de procéder à des évaluations avancées de la sécurité. L’authentification alternative et moderne permet de réduire les risques de sécurité, car elle prend en charge l’authentification multifacteur et l’accès conditionnel. Nous vous recommandons les trois actions suivantes :

1. Bloquez [l’authentification héritée, si vous utilisez AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12).
2. Configurez [SharePoint Online et Exchange Online pour utiliser l’authentification moderne](../../active-directory/conditional-access/conditional-access-for-exo-and-spo.md).
3. Utilisez [des stratégies d’accès conditionnel pour bloquer l’authentification héritée](../../active-directory/conditional-access/conditions.md).

### <a name="block-invalid-authentication-entry-points"></a>Bloquer les points d’entrée d’authentification non valide

En adoptant l’idée qu’une violation de la sécurité peut se produire, vous devez réduire l’impact des informations d’identification utilisateur compromises le cas échéant. Pour chaque application dans votre environnement, considérez les cas d’utilisation valides : quels groupes, réseaux, appareils et autres éléments sont autorisés, puis bloquez le reste. Avec [l’accès conditionnel Azure AD](../../active-directory/conditional-access/overview.md), vous pouvez contrôler comment les utilisateurs autorisés accèdent à leurs applications et ressources selon des conditions spécifiques que vous définissez.

### <a name="block-end-user-consent"></a>Bloquer le consentement de l'utilisateur final

Par défaut, dans Azure AD tous les utilisateurs peuvent autoriser les applications tirant parti d’OAuth 2.0 et du [framework de consentement](../../active-directory/develop/consent-framework.md) de Microsoft à accéder aux données d’entreprise. Ce consentement, qui permet aux utilisateurs d’acquérir facilement des applications utiles qui s’intègrent à Microsoft 365 et à Azure, peut cependant représenter un risque s’il n’est pas utilisé et surveillé avec précaution. La [désactivation de toutes les opérations futures de consentement d'utilisateurs](../../active-directory/manage-apps/methods-for-removing-user-access.md) peut aider à réduire votre surface d'exposition et à atténuer ce risque. Si le consentement de l’utilisateur final est désactivé, les autorisations de consentement préalables sont toujours respectées, mais toutes les opérations de consentement futures doivent être effectuées par un administrateur. Avant de désactiver cette fonctionnalité, il est recommandé de vous assurer que les utilisateurs comprendront comment demander une approbation d’administrateur pour les nouvelles applications : ceci doit vous aider à réduire l’insatisfaction des utilisateurs, à limiter le volume de support et à garantir que les utilisateurs ne s’inscrivent pas à des applications avec des informations d’identification autres que celles d’Azure AD.

### <a name="implement-azure-ad-privileged-identity-management"></a>Mettre en œuvre Azure AD Privileged Identity Management

Un autre impact de la « violation supposée » est le besoin de réduire la probabilité qu’un compte compromis peut fonctionner avec un rôle privilégié. 

[Azure AD Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-configure.md) vous aide à réduire les privilèges de compte en vous aidant à :

* Identifier et gérer les utilisateurs affectés aux rôles d’administration.
* Comprendre les rôles avec des privilèges inutilisés ou excessifs que vous devez supprimer.
* Établir des règles pour vous assurer que les rôles privilégiés sont protégés par l’authentification multifacteur.
* Établir des règles pour vous assurer que les rôles privilégiés ne sont accordés que pendant le temps nécessaire pour accomplir la tâche privilégiée.

Activez Azure AD PIM, puis affichez les utilisateurs qui sont affectés à des rôles d’administration et supprimez les comptes inutiles dans ces rôles. Pour les autres utilisateurs privilégiés, déplacez-les de l’état permanent à l’état éligible. Enfin, établissez des stratégies appropriées pour vous assurer que lorsque ces utilisateurs ont besoin d’accéder à ces rôles privilégiés, ils peuvent le faire en toute sécurité, avec le contrôle nécessaire des modifications.

Dans le cadre du déploiement de votre processus de compte privilégié, respectez les [meilleures pratiques pour créer au moins deux comptes d’urgence](../../active-directory/users-groups-roles/directory-admin-roles-secure.md) pour être sûr de pouvoir accéder à Azure AD si vous êtes bloqué.

## <a name="step-3---automate-threat-response"></a>Étape 3 - Automatiser la réponse aux menaces

Azure Active Directory comporte de nombreuses fonctionnalités qui interceptent automatiquement les attaques, afin de supprimer la latence entre la détection et la réponse. Vous pouvez limiter les coûts et les risques lorsque vous réduisez le temps dont les attaquants ont besoin pour s’insérer dans votre environnement. Voici les étapes concrètes que vous pouvez appliquer.

### <a name="implement-user-risk-security-policy-using-azure-ad-identity-protection"></a>Mettre en œuvre une stratégie de sécurité en matière de risque des utilisateurs à l’aide d’Azure AD Identity Protection

Le risque utilisateur indique la probabilité que l'identité d'un utilisateur ait été compromise et est calculé en fonction des [détections de risque utilisateur](../../active-directory/identity-protection/overview.md) associées à l'identité d'un utilisateur. Une stratégie en matière de risque des utilisateurs est une stratégie d’accès conditionnel qui évalue le niveau de risque pour un groupe ou un utilisateur spécifique. Selon un niveau de risque Faible, Moyen ou Élevé, une stratégie peut être configurée pour bloquer l’accès ou exiger une modification du mot de passe sécurisée à l’aide de l’authentification multifacteur. La recommandation de Microsoft est d’exiger une modification du mot de passe sécurisée pour les utilisateurs présentant un risque élevé.

![Utilisateurs associés à un indicateur de risque](./media/steps-secure-identity/azure-ad-sec-steps1.png)

### <a name="implement-sign-in-risk-policy-using-azure-ad-identity-protection"></a>Mettre en œuvre une stratégie en matière de risque à la connexion à l’aide d’Azure AD Identity Protection

Le risque à la connexion est la probabilité qu’une personne autre que le propriétaire du compte tente de se connecter à l’aide de l’identité. Une [stratégie en matière de risque à la connexion](../../active-directory/identity-protection/overview.md) est une stratégie d’accès conditionnel qui évalue le niveau de risque pour un groupe ou un utilisateur spécifique. Selon un niveau de risque (élevé, moyen, faible), une stratégie peut être configurée pour bloquer l’accès ou exiger l’authentification multifacteur. Veillez à exiger l’authentification multifacteur pour les connexions présentant un risque Moyen ou supérieur.

![Connexion à partir d’adresses IP anonymes](./media/steps-secure-identity/azure-ad-sec-steps2.png)

## <a name="step-4---increase-your-awareness"></a>Étape 4 - Augmenter votre connaissance

L’audit et la journalisation des événements liés à la sécurité, de même que les alertes associées, constituent des composants essentiels dans une stratégie de protection efficace. Les journaux d’activité et les rapports de sécurité vous fournissent un enregistrement électronique des activités suspectes et vous aident à détecter les motifs pouvant indiquer une tentative d’intrusion ou une intrusion externe effective sur le réseau, ainsi que des attaques internes. Vous pouvez avoir recours aux audits pour surveiller les activités des utilisateurs, documenter la conformité aux exigences réglementaires, effectuer des analyses d’investigation, etc. Les alertes fournissent des notifications des événements de sécurité.

### <a name="monitor-azure-ad"></a>Surveiller Azure AD

Les services et fonctionnalités Microsoft Azure vous proposent des options d’audit et de journalisation configurables qui permettent d’identifier les lacunes dans vos stratégies et mécanismes de sécurité et d’y remédier pour empêcher les violations éventuelles. Vous pouvez utiliser [l’audit et la journalisation Azure](log-audit.md) et les [Rapports d’activité d’audit dans le portail Azure Active Directory](../../active-directory/reports-monitoring/concept-audit-logs.md).

### <a name="monitor-azure-ad-connect-health-in-hybrid-environments"></a>Surveiller Azure AD Connect Health dans les environnements hybrides

La [surveillance d’AD FS avec Azure AD Connect Health](../../active-directory/hybrid/how-to-connect-health-adfs.md) vous offre un meilleur insight sur les problèmes potentiels et une meilleure visibilité des attaques sur votre infrastructure AD FS. Azure AD Connect Health fournit des alertes avec des détails, des étapes de résolution et des liens vers la documentation associée ; l’analytique de l’utilisation pour plusieurs métriques relatives au trafic de l’authentification ; la surveillance des rapports pour les performances.

![Azure AD Connect Health](./media/steps-secure-identity/azure-ad-sec-steps4.png)

### <a name="monitor-azure-ad-identity-protection-events"></a>Surveiller les événements Azure AD Identity Protection

[Azure AD Identity Protection](../../active-directory/identity-protection/overview.md) est un outil de notification, de surveillance et de rapports que vous pouvez utiliser pour détecter les vulnérabilités potentielles qui affectent les identités de votre organisation. Il repère les détections à risque, telles que les fuites d'informations d'identification, les déplacements impossibles, les connexions à partir d'appareils infectés, les adresses IP anonymes, les adresses IP associées à une activité suspecte et les emplacements inconnus. Activez les alertes de notification pour recevoir par un e-mail désignant les utilisateurs exposés et/ou un e-mail de résumé hebdomadaire.

Azure AD Identity Protection fournit deux rapports importants que vous devez surveiller tous les jours :
1. Les rapports sur les connexions à risque mettent en avant les activités de connexion d’utilisateurs que vous devriez examiner parce qu’il est possible que la connexion n’ait pas été établie par le propriétaire légitime.
2. Les rapports sur les utilisateurs à risque mettent l’accent sur des comptes d’utilisateurs qui ont pu être compromis, par exemple, parce qu’une fuite des informations d’identification a été détectée ou que l’utilisateur s’est connecté à partir de différents emplacements alors qu’un déplacement était objectivement impossible. 

![Utilisateurs associés à un indicateur de risque](./media/steps-secure-identity/azure-ad-sec-steps3.png)

### <a name="audit-apps-and-consented-permissions"></a>Applications d’audit et autorisations accordées

Les utilisateurs peuvent accéder à leur insu à un site web ou à des applications compromis qui accéderont aux informations de leur profil et à leurs données utilisateur, telles que leur adresse e-mail. Un intervenant malveillant peut utiliser les autorisations accordées pour chiffrer le contenu de leur boîte aux lettres et leur demander une rançon pour récupérer les données de leur boîte aux lettres. [Les administrateurs doivent examiner et auditer](https://docs.microsoft.com/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) les autorisations données par les utilisateurs.

## <a name="step-5---enable-end-user-self-help"></a>Étape 5 - Activer l’auto-assistance pour l’utilisateur final

Vous voulez, autant que possible, équilibrer la sécurité avec la productivité. Tout comme vous vous efforcez d’établir une base pour la sécurité sur le long terme, vous pouvez supprimer toute friction dans votre organisation en habilitant vos utilisateurs tout en restant vigilant. 

### <a name="implement-self-service-password-reset"></a>Mettre en œuvre la réinitialisation du mot de passe libre-service

La [réinitialisation de mot de passe en libre-service](../../active-directory/authentication/quickstart-sspr.md) d’Azure offre aux administrateurs informatiques un moyen simple pour permettre aux utilisateurs de réinitialiser ou de déverrouiller leurs comptes ou leurs mots de passe, sans intervention de l’administrateur. Le système inclut des rapports détaillés de suivi d’accès au système, ainsi que des notifications pour vous prévenir de toute utilisation malveillante ou de tout abus. 

### <a name="implement-self-service-group-management"></a>Mettre en œuvre la gestion de groupes en libre-service

Azure AD fournit la possibilité de gérer l’accès aux ressources à l’aide de groupes de sécurité et de groupes Office 365. Ces groupes peuvent être gérés par des propriétaires de groupe au lieu des administrateurs informatiques. Également appelée [gestion de groupes en libre-service](../../active-directory/users-groups-roles/groups-self-service-management.md), cette fonctionnalité permet aux propriétaires de groupe, qui n’ont pas un rôle d’administrateur, de créer et gérer des groupes sans avoir à demander aux administrateurs de gérer leurs requêtes.

### <a name="implement-azure-ad-access-reviews"></a>Mettre en œuvre les révisions d’accès Azure AD

Avec les [révisions d’accès Azure](../../active-directory/governance/access-reviews-overview.md), vous pouvez gérer les appartenances à un groupe, l’accès aux applications d’entreprise et les affectations de rôle privilégié pour maintenir une norme de sécurité qui n’offre pas aux utilisateurs un accès pendant de longues périodes lorsqu’ils n’en ont pas besoin.

## <a name="summary"></a>Résumé

Une infrastructure d’identité sécurisée est composée de nombreux aspects, mais cette liste de vérification en cinq étapes vous aidera à obtenir rapidement une infrastructure d’identité plus sûre et sécurisée :

* Renforcer vos informations d’identification.
* Réduire votre surface d’attaque.
* Automatiser la réponse aux menaces.
* Augmenter votre connaissance de l’audit et du monitoring.
* Permettre une sécurité de l’utilisateur plus prévisible et complète grâce à l’auto-assistance.

Nous sommes heureux de l’importance que vous accordez à l’identité de sécurité et nous espérons que ce document constitue une feuille de route utile vers une position plus sécurisée pour votre organisation.

## <a name="next-steps"></a>Étapes suivantes
Si vous avez besoin d’aide pour planifier et déployer les recommandations, consultez les [plans de déploiement de projet d’Azure AD](https://aka.ms/deploymentplans).
