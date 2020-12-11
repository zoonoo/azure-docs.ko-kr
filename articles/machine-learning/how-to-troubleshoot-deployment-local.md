---
title: 로컬에서 웹 서비스 배포 문제 해결
titleSuffix: Azure Machine Learning
description: 로컬 Azure Machine Learning에서 Docker 배포 오류를 해결 하 고 해결 하 고 문제를 해결 하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: gvashishtha
ms.author: gopalv
ms.reviewer: luquinta
ms.date: 11/25/2020
ms.topic: troubleshooting
ms.custom: devx-track-python, deploy, contperf-fy21q2
ms.openlocfilehash: 4f70978e7b7c2315aff4c51d8b019933df78e7e8
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97028804"
---
# <a name="troubleshoot-model-deployment-locally"></a>로컬로 모델 배포 문제 해결

일반적인 Azure Machine Learning Docker 웹 서비스 배포 오류를 해결 하 고 해결 하거나 해결 하는 방법에 대해 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

* **Azure 구독**. [Azure Machine Learning 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.
* [Azure Machine Learning SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)
* [Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)
* [Azure Machine Learning용 CLI 확장](reference-azure-machine-learning-cli.md)
* 로컬로 디버그하려면 로컬 시스템에서 작동하는 Docker가 설치되어 있어야 합니다.

    Docker 설치를 확인하려면 터미널 또는 명령 프롬프트에서 `docker run hello-world` 명령을 사용합니다. Docker 설치 또는 Docker 오류 문제 해결에 대한 자세한 내용은 [Docker 설명서](https://docs.docker.com/)를 참조하세요.

## <a name="debug-locally"></a>로컬에서 디버그

ACI 또는 AKS에 모델을 배포할 때 문제가 발생 하는 경우 로컬 웹 서비스로 배포 합니다. 로컬 웹 서비스를 사용하면 문제를 더 쉽게 해결할 수 있습니다.

[MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks) 리포지토리에서 샘플 [로컬 배포 노트북](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local.ipynb) 을 찾아 실행 가능한 예제를 탐색할 수 있습니다.

> [!WARNING]
> 프로덕션 시나리오에는 로컬 웹 서비스 배포가 지원되지 않습니다.

로컬로 배포하려면 `LocalWebservice.deploy_configuration()`을 사용하여 배포 구성을 만들도록 코드를 수정합니다. 그런 다음, `Model.deploy()`를 사용하여 서비스를 배포합니다. 다음 예제에서는 모델 변수에 포함된 모델을 로컬 웹 서비스로 배포합니다.

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import LocalWebservice


# Create inference configuration based on the environment definition and the entry script
myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
# Create a local deployment, using port 8890 for the web service endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(
    ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the web service is available on
print(service.port)
```

사용자 고유의 conda 사양 YAML을 정의 하는 경우 pip-기본값 버전 >= 1.0.45를 pip 종속성으로 나열 합니다. 이 패키지는 모델을 웹 서비스로 호스트 하는 데 필요 합니다.

이 시점에서 서비스를 정상적으로 사용할 수 있습니다. 다음 코드는 서비스에 데이터를 보내는 방법을 보여 줍니다.

```python
import json

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

test_sample = bytes(test_sample, encoding='utf8')

prediction = service.run(input_data=test_sample)
print(prediction)
```

Python 환경을 사용자 지정하는 방법에 대한 자세한 내용은 [학습 및 배포 환경 만들기 및 관리](how-to-use-environments.md)를 참조하세요. 

### <a name="update-the-service"></a>서비스 업데이트

로컬 테스트 중에 로깅을 추가하도록 `score.py` 파일을 업데이트하거나 검색한 문제를 해결해야 할 수도 있습니다. `score.py` 파일의 변경 내용을 다시 로드하려면 `reload()`를 사용합니다. 예를 들어 다음 코드에서는 서비스에 대한 스크립트를 다시 로드한 다음, 데이터를 이 스크립트에 보냅니다. 데이터는 업데이트된 `score.py` 파일을 사용하여 채점됩니다.

> [!IMPORTANT]
> `reload` 메서드는 로컬 배포에만 사용할 수 있습니다. 다른 계산 대상으로 배포를 업데이트 하는 방법에 대 한 자세한 내용은 [webservice를 업데이트 하는 방법](how-to-deploy-update-web-service.md)을 참조 하세요.

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> 스크립트는 서비스에서 사용하는 `InferenceConfig` 개체로 지정된 위치에서 다시 로드됩니다.

모델, Conda 종속성 또는 배포 구성을 변경하려면 [update()](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueupdate--args-)를 사용합니다. 다음 예제에서는 서비스에서 사용하는 모델을 업데이트합니다.

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>서비스 삭제

서비스를 삭제하려면 [delete()](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truedelete--)를 사용합니다.

### <a name="inspect-the-docker-log"></a><a id="dockerlog"></a> Docker 로그 검사

서비스 개체에서 자세한 Docker 엔진 로그 메시지를 인쇄할 수 있습니다. ACI, AKS 및 로컬 배포에 대한 로그를 볼 수 있습니다. 다음 예제에서는 로그를 출력하는 방법을 보여 줍니다.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

`Booting worker with pid: <pid>`로그에서 여러 번 발생 하는 줄이 표시 되 면 작업자를 시작 하는 데 충분 한 메모리가 없는 것입니다.
에서의 값을 늘려서 오류를 해결할 수 있습니다. `memory_gb``deployment_config`

## <a name="next-steps"></a>다음 단계

배포에 대해 자세히 알아보세요.

* [원격 배포 문제를 해결 하는 방법](how-to-troubleshoot-deployment.md)
* [배포 방법 및 위치](how-to-deploy-and-where.md)
* [자습서: 모델 학습 및 배포](tutorial-train-models-with-aml.md)
* [로컬에서 실험을 실행 하 고 디버그 하는 방법](./how-to-debug-visual-studio-code.md)
