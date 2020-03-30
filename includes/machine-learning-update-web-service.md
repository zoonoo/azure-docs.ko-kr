---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: d36bf2db05113656a77e76ff900d95910f313c73
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477258"
---
웹 서비스를 업데이트하려면 메서드를 `update` 사용합니다. 웹 서비스를 업데이트하여 추론 구성에 지정할 수 있는 새 모델, 새 항목 스크립트 또는 새 종속성을 사용할 수 있습니다. 자세한 내용은 [Webservice.update에](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py#update--args-)대한 설명서를 참조하십시오.

> [!IMPORTANT]
> 모델의 새 버전을 만들 때 사용할 각 서비스를 수동으로 업데이트해야 합니다.
>
> SDK를 사용하여 Azure 기계 학습 디자이너에서 게시된 웹 서비스를 업데이트할 수 없습니다.

**SDK 사용**

다음 코드는 SDK를 사용하여 웹 서비스의 모델, 환경 및 항목 스크립트를 업데이트하는 방법을 보여 주며 있습니다.

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

**CLI 사용**

ML CLI를 사용하여 웹 서비스를 업데이트할 수도 있습니다. 다음 예제에서는 새 모델을 등록한 다음 새 모델을 사용하도록 웹 서비스를 업데이트하는 방법을 보여 줍니다.

```azurecli
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --output-metadata-file modelinfo.json
az ml service update -n myservice --model-metadata-file modelinfo.json
```

> [!TIP]
> 이 예제에서는 JSON 문서를 사용하여 등록 명령에서 업데이트 명령으로 모델 정보를 전달합니다.
>
> 새 엔트리 스크립트 또는 환경을 사용하도록 서비스를 업데이트하려면 [추론 구성 파일을](/azure/machine-learning/service/reference-azure-machine-learning-cli#inference-configuration-schema) 만들고 `ic` 매개 변수로 지정합니다.

자세한 내용은 az [ml 서비스 업데이트](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-update) 설명서를 참조하십시오.