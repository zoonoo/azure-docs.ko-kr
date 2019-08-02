---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/26/2019
ms.author: larryfr
ms.openlocfilehash: fef6225812980900ad55944644310433a6105de2
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68556854"
---
새 모델을 만들 때 새 모델을 사용 하려는 각 서비스를 수동으로 업데이트 해야 합니다. 웹 서비스를 업데이트하려면 `update` 메서드를 사용합니다. 다음 코드에서는 SDK를 사용 하 여 웹 서비스에 대 한 모델을 업데이트 하는 방법을 보여 줍니다.

```python
from azureml.core.webservice import Webservice
from azureml.core.model import Model

# register new model
new_model = Model.register(model_path="outputs/sklearn_mnist_model.pkl",
                           model_name="sklearn_mnist",
                           tags={"key": "0.1"},
                           description="test",
                           workspace=ws)

service_name = 'myservice'
# Retrieve existing service
service = Webservice(name=service_name, workspace=ws)

# Update to new model(s)
service.update(models=[new_model])
print(service.state)
print(service.get_logs())
```

ML CLI를 사용 하 여 웹 서비스를 업데이트할 수도 있습니다. 다음 예에서는 새 모델을 등록 한 다음 새 모델을 사용 하도록 웹 서비스를 업데이트 하는 방법을 보여 줍니다.

```azurecli
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --output-metadata-file modelinfo.json
az ml service update -n myservice --model-metadata-file modelinfo.json
```

> [!TIP]
> 이 예제에서는 JSON 문서를 사용 하 여 등록 명령의 모델 정보를 update 명령에 전달 합니다.