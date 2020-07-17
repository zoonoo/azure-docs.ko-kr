---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/28/2020
ms.author: larryfr
ms.openlocfilehash: 5102e8f75da14c58e948e81aaa418539dd18869a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80159417"
---
문서의 항목은 `inferenceconfig.json` [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) 클래스에 대 한 매개 변수에 매핑됩니다. 다음 표에서는 JSON 문서의 엔터티 및 메서드에 대 한 매개 변수의 매핑에 대해 설명 합니다.

| JSON 엔터티 | 메서드 매개 변수 | 설명 |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | 이미지에 대해 실행할 코드를 포함 하는 로컬 파일의 경로입니다. |
| `sourceDirectory` | `source_directory` | 선택 사항입니다. 이미지를 만드는 모든 파일이 포함 된 폴더의 경로입니다 .이 폴더 또는 하위 폴더에 있는 모든 파일에 쉽게 액세스할 수 있습니다. 로컬 컴퓨터에서 웹 서비스에 대 한 종속성으로 전체 폴더를 업로드할 수 있습니다. 참고: entry_script, conda_file 및 extra_docker_file_steps 경로는 source_directory 경로에 대 한 상대 경로입니다. |
| `environment` | `environment` | 선택 사항입니다.  Azure Machine Learning [환경](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py).|

유추 구성 파일에 Azure Machine Learning [환경의](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) 전체 사양을 포함할 수 있습니다. 이 환경이 작업 영역에 없는 경우 Azure Machine Learning 만들 수 있습니다. 그렇지 않으면 필요한 경우 Azure Machine Learning에서 환경을 업데이트 합니다. 다음 JSON은 예입니다.

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

또한 기존 Azure Machine Learning [환경을](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) 분리 된 CLI 매개 변수로 사용 하 고 유추 구성 파일에서 "환경" 키를 제거할 수 있습니다. 환경 이름에-e를 사용 하 고 환경 버전에--ev을 사용 합니다. --Ev를 지정 하지 않으면 최신 버전이 사용 됩니다. 다음은 유추 구성 파일의 예입니다.

```json
{
    "entryScript": "score.py",
    "sourceDirectory": null
}
```

다음 명령은 이전 유추 구성 파일 (의 명명 된 myInferenceConfig.js)을 사용 하 여 모델을 배포 하는 방법을 보여 줍니다. 

또한 기존 Azure Machine Learning [환경의](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) 최신 버전 (AzureML-최소)을 사용 합니다.

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic myInferenceConfig.json -e AzureML-Minimal --dc deploymentconfig.json
```
