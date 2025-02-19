---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/26/2019
ms.author: larryfr
ms.openlocfilehash: 11cc93e0eb11d0422fae4fbbbf0a549df067daca
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390621"
---
Pour mettre à jour un service web, utilisez la méthode `update`. Vous pouvez mettre à jour le service web pour utiliser un nouveau modèle, un nouveau script d’entrée ou de nouvelles dépendances qui peuvent être spécifiées dans une configuration d’inférence. Pour plus d’informations, consultez la documentation sur [Webservice.update](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py#update--args-).

> [!IMPORTANT]
> Lorsque vous créez une nouvelle version d’un modèle, vous devez mettre à jour manuellement chacun des services pour lesquels vous souhaitez l’utiliser.

**Avec le kit SDK**

Le code suivant montre comment utiliser le SDK pour mettre à jour le modèle, l’environnement et le script d’entrée d’un service web :

```python
from azureml.core import Environment
from azureml.core.webservice import Webservice
from azureml.core.model import Model, InferenceConfig

# Register new model.
new_model = Model.register(model_path="outputs/sklearn_mnist_model.pkl",
                           model_name="sklearn_mnist",
                           tags={"key": "0.1"},
                           description="test",
                           workspace=ws)

# Use version 3 of the environment.
deploy_env = Environment.get(workspace=ws,name="myenv",version="3")
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=deploy_env)

service_name = 'myservice'
# Retrieve existing service.
service = Webservice(name=service_name, workspace=ws)



# Update to new model(s).
service.update(models=[new_model], inference_config=inference_config)
print(service.state)
print(service.get_logs())
```

**Avec l’interface CLI**

Vous pouvez également mettre à jour un service web en utilisant ML CLI. L’exemple suivant explique comment enregistrer un nouveau modèle, puis mettre à jour le service web pour utiliser ce nouveau modèle :

```azurecli
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --output-metadata-file modelinfo.json
az ml service update -n myservice --model-metadata-file modelinfo.json
```

> [!TIP]
> Dans cet exemple, un document JSON est utilisé pour transférer les informations de modèle à partir de la commande d’inscription vers la commande de mise à jour.
>
> Pour mettre à jour le service afin d’utiliser un nouveau script ou environnement d’entrée, créez un [fichier de configuration d’inférence](/azure/machine-learning/service/reference-azure-machine-learning-cli#inference-configuration-schema) et spécifiez-le avec le paramètre `ic`.

Pour plus d’informations, consultez la documentation [az ml service update](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-update).