---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/28/2020
ms.author: larryfr
ms.openlocfilehash: 5102e8f75da14c58e948e81aaa418539dd18869a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159417"
---
문서의 `inferenceconfig.json` 항목은 [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) 클래스의 매개 변수에 매핑됩니다. 다음 표에서는 JSON 문서의 엔터티와 메서드의 매개 변수 간의 매핑에 대해 설명합니다.

| JSON 엔티티 | 메서드 매개 변수 | 설명 |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | 이미지에 대해 실행할 코드가 포함된 로컬 파일에 대한 경로입니다. |
| `sourceDirectory` | `source_directory` | (선택 사항) 이미지를 만들기 위해 모든 파일이 포함된 폴더로 이동하여 이 폴더 또는 하위 폴더 내의 모든 파일에 쉽게 액세스할 수 있습니다. 로컬 컴퓨터에서 전체 폴더를 웹 서비스에 대한 종속성으로 업로드할 수 있습니다. 참고: entry_script, conda_file 및 extra_docker_file_steps 경로는 source_directory 경로의 상대 경로입니다. |
| `environment` | `environment` | (선택 사항)  Azure 기계 학습 [환경](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py).|

추론 구성 파일에 Azure 기계 학습 [환경의](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) 전체 사양을 포함할 수 있습니다. 이 환경이 작업 영역에 없는 경우 Azure 기계 학습에서 해당 환경을 만듭니다. 그렇지 않으면 Azure 기계 학습이 필요한 경우 환경을 업데이트합니다. 다음은 다음과 같은 예입니다.

```json
{
    "entryScript": "score.py",
    "environment": {
        "docker": {
            "arguments": [],
            "baseDockerfile": null,
            "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
            "enabled": false,
            "sharedVolumes": true,
            "shmSize": null
        },
        "environmentVariables": {
            "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
        },
        "name": "my-deploy-env",
        "python": {
            "baseCondaEnvironment": null,
            "condaDependencies": {
                "channels": [
                    "conda-forge"
                ],
                "dependencies": [
                    "python=3.6.2",
                    {
                        "pip": [
                            "azureml-defaults",
                            "azureml-telemetry",
                            "scikit-learn",
                            "inference-schema[numpy-support]"
                        ]
                    }
                ],
                "name": "project_environment"
            },
            "condaDependenciesFile": null,
            "interpreterPath": "python",
            "userManagedDependencies": false
        },
        "version": "1"
    }
}
```

분리된 CLI 매개 변수에서 기존 Azure 기계 학습 [환경을](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) 사용하고 추론 구성 파일에서 "환경" 키를 제거할 수도 있습니다. 환경 이름에는 -e를 사용하고 환경 버전에는 --ev를 사용합니다. --ev를 지정하지 않으면 최신 버전이 사용됩니다. 다음은 추론 구성 파일의 예입니다.

```json
{
    "entryScript": "score.py",
    "sourceDirectory": null
}
```

다음 명령은 이전 추론 구성 파일(myInferenceConfig.json)을 사용하여 모델을 배포하는 방법을 보여 줍니다. 

또한 기존 Azure 기계 학습 환경(AzureML-최소값)의 최신 버전을 사용합니다. [environment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic myInferenceConfig.json -e AzureML-Minimal --dc deploymentconfig.json
```
