---
title: Activer le pare-feu d’applications web - Azure CLI
description: Découvrez comment limiter le trafic web avec un pare-feu d’applications web sur une passerelle d’application à l’aide d’Azure CLI.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 08/01/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 698191355ab9e014693b01cfb6546fb764a2b647
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68688206"
---
# <a name="enable-web-application-firewall-using-the-azure-cli"></a>Activer le pare-feu d’applications web à l’aide d’Azure CLI

Vous pouvez limiter le trafic sur une [passerelle d’application](overview.md) à l’aide d’un [pare-feu d’applications web](waf-overview.md) (WAF). Le WAF utilise des règles [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) pour protéger votre application. Ces règles incluent la protection contre les attaques telles que l’injection de code SQL, les attaques de script entre sites et les détournements de session.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Configurer le réseau
> * Créer une passerelle d’application avec WAF activé
> * Créer un groupe de machines virtuelles identiques
> * Créer un compte de stockage et configurer des diagnostics

![Exemple de pare-feu d’applications web](./media/tutorial-restrict-web-traffic-cli/scenario-waf.png)

Si vous préférez, vous pouvez suivre cette procédure en utilisant [Azure PowerShell](tutorial-restrict-web-traffic-powershell.md).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, cet article nécessite l’exécution d’Azure CLI version 2.0.4 ou ultérieure. Pour connaître la version de l’interface, exécutez `az --version`. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. Créez un groupe de ressources Azure nommé *myResourceGroupAG* à l’aide de la commande [az group create](/cli/azure/group#az-group-create).

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Créer des ressources réseau

Le réseau virtuel et les sous-réseaux sont utilisés pour fournir la connectivité réseau à la passerelle d’application et à ses ressources associées. Créez un réseau virtuel nommé *myVNet* et un sous-réseau nommé *myAGSubnet*. Ensuite, créez une adresse IP publique nommée *myAGPublicIPAddress*.

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myBackendSubnet \
  --subnet-prefix 10.0.1.0/24

az network vnet subnet create \
  --name myAGSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24

az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-an-application-gateway-with-a-waf"></a>Créer une passerelle d’application avec WAF

Vous pouvez utiliser la commande [az network application-gateway create](/cli/azure/network/application-gateway) pour créer la passerelle d’application nommée *myAppGateway*. Quand vous créez une passerelle d’application avec Azure CLI, vous spécifiez des informations de configuration, telles que la capacité, la référence SKU et les paramètres HTTP. La passerelle d’application est affectée à *myAGSubnet* et à *myAGPublicIPAddress*.

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --capacity 2 \
  --sku WAF_v2 \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress

az network application-gateway waf-config set \
  --enabled true \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --firewall-mode Detection \
  --rule-set-version 3.0
```

La création de la passerelle d’application peut prendre plusieurs minutes. Une fois la passerelle d’application créée, vous pouvez voir ses nouvelles fonctionnalités suivantes :

- *appGatewayBackendPool* : une passerelle d’application doit avoir au moins un pool d’adresses backend.
- *appGatewayBackendHttpSettings* : spécifie que le port 80 et le protocole HTTP sont utilisés pour la communication.
- *appGatewayHttpListener* : écouteur par défaut associé à *appGatewayBackendPool*.
- *appGatewayFrontendIP* - assigne *myAGPublicIPAddress* à *appGatewayHttpListener*.
- *rule1* : règle de routage par défaut associée à *appGatewayHttpListener*.

## <a name="create-a-virtual-machine-scale-set"></a>Créer un groupe de machines virtuelles identiques

Dans cet exemple, vous créez un groupe de machines virtuelles identiques qui fournit deux serveurs pour le pool principal dans la passerelle d’application. Les machines virtuelles dans le groupe identique sont associées au sous-réseau *myBackendSubnet*. Pour créer le groupe identique, vous pouvez utiliser la commande [az vmss create](/cli/azure/vmss#az-vmss-create).

```azurecli-interactive
az vmss create \
  --name myvmss \
  --resource-group myResourceGroupAG \
  --image UbuntuLTS \
  --admin-username azureuser \
  --admin-password Azure123456! \
  --instance-count 2 \
  --vnet-name myVNet \
  --subnet myBackendSubnet \
  --vm-sku Standard_DS2 \
  --upgrade-policy-mode Automatic \
  --app-gateway myAppGateway \
  --backend-pool-name appGatewayBackendPool
```

### <a name="install-nginx"></a>Installer NGINX

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroupAG \
  --vmss-name myvmss \
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"],"commandToExecute": "./install_nginx.sh" }'
```

## <a name="create-a-storage-account-and-configure-diagnostics"></a>Créer un compte de stockage et configurer des diagnostics

Dans cet article, la passerelle d’application utilise un compte de stockage pour stocker des données à des fins de détection et de prévention. Vous pouvez également utiliser les journaux d’activité Azure Monitor ou Event Hub pour enregistrer des données. 

### <a name="create-a-storage-account"></a>Créez un compte de stockage.

Créez un compte de stockage nommé *myagstore1* à l’aide de la commande [az storage account create](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create).

```azurecli-interactive
az storage account create \
  --name myagstore1 \
  --resource-group myResourceGroupAG \
  --location eastus \
  --sku Standard_LRS \
  --encryption blob
```

### <a name="configure-diagnostics"></a>Configuration de la collecte des diagnostics

Configurez des diagnostics pour enregistrer des données dans les journaux d’activité ApplicationGatewayAccessLog, ApplicationGatewayPerformanceLog et ApplicationGatewayFirewallLog. Remplacez `<subscriptionId>` par votre identificateur d’abonnement, puis configurez les diagnostics à l’aide de la commande [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create).

```azurecli-interactive
appgwid=$(az network application-gateway show --name myAppGateway --resource-group myResourceGroupAG --query id -o tsv)

storeid=$(az storage account show --name myagstore1 --resource-group myResourceGroupAG --query id -o tsv)

az monitor diagnostic-settings create --name appgwdiag --resource $appgwid \
  --logs '[ { "category": "ApplicationGatewayAccessLog", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "ApplicationGatewayPerformanceLog", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "ApplicationGatewayFirewallLog", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } } ]' \
  --storage-account $storeid
```

## <a name="test-the-application-gateway"></a>Tester la passerelle d’application

Pour obtenir l’adresse IP publique de la passerelle d’application, utilisez la commande [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). Copiez l’adresse IP publique, puis collez-la dans la barre d’adresses de votre navigateur.

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Tester l’URL de base dans la passerelle d’application](./media/tutorial-restrict-web-traffic-cli/application-gateway-nginxtest.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, supprimez le groupe de ressources, la passerelle d’application et toutes les ressources associées.

```azurecli-interactive
az group delete --name myResourceGroupAG 
```

## <a name="next-steps"></a>Étapes suivantes

[Créer une passerelle d’application avec arrêt SSL](./tutorial-ssl-cli.md)
