---
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: include
ms.date: 04/21/2021
ms.author: gopalv
ms.openlocfilehash: 72b085870b8a7926b1fd182af424d1a59900f969
ms.sourcegitcommit: 42ac9d148cc3e9a1c0d771bc5eea632d8c70b92a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2021
ms.locfileid: "109846075"
---
# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

`bidaf_onnx:1`을 모델 이름 및 해당 버전 번호로 바꿉니다.

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/2.deploy-local-cli.ipynb?name=deploy-model-code)]

# <a name="python"></a>[Python](#tab/python)


[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=deploy-model-code)]

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=deploy-model-print-logs)]

자세한 내용은 [Model.deploy()](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) 및 [Webservice](/python/api/azureml-core/azureml.core.webservice.webservice)에 대한 설명서를 참조하세요.

---
