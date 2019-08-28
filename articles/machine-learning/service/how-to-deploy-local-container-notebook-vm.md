---
title: 노트북 Vm에 모델을 배포 하는 방법
titleSuffix: Azure Machine Learning service
description: 노트북 Vm을 사용 하 여 Azure Machine Learning 서비스 모델을 웹 서비스로 배포 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mnark
author: MrudulaN
ms.reviewer: larryfr
ms.date: 08/08/2019
ms.openlocfilehash: d6b26dfe1eb8ea65dd7c751a148c599123b0f6db
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947761"
---
# <a name="deploy-a-model-to-notebook-vms"></a>노트북 Vm에 모델 배포

Azure Machine Learning 서비스를 사용 하 여 노트북 VM에 웹 서비스로 모델을 배포 하는 방법을 알아봅니다. 다음 조건 중 하나에 해당 하는 경우 노트북 Vm을 사용 합니다.

- 모델을 빠르게 배포하고 유효성을 검사해야 합니다.
- 개발 중인 모델을 테스트합니다.

> [!TIP]
> 노트북 VM의 Jupyter Notebook에서 동일한 VM의 웹 서비스로 모델을 배포 하는 것은 _로컬 배포_입니다. 이 경우 ' 로컬 ' 컴퓨터는 노트북 VM입니다. 배포에 대 한 자세한 내용은 [Azure Machine Learning 서비스를 사용 하 여 모델 배포](how-to-deploy-and-where.md)를 참조 하세요.

## <a name="prerequisites"></a>필수 구성 요소

- 노트북 VM을 실행 하는 Azure Machine Learning 서비스 작업 영역. 자세한 내용은 [환경 및 작업 영역 설정](tutorial-1st-experiment-sdk-setup.md)을 참조 하세요.

## <a name="deploy-to-the-notebook-vms"></a>노트북 Vm에 배포

로컬 배포를 보여 주는 예제 노트북은 노트북 VM에 포함 되어 있습니다. 다음 단계를 사용 하 여 노트북을 로드 하 고 모델을 VM에 웹 서비스로 배포 합니다.

1. [Azure Portal](https://portal.azure.com)에서 Azure Machine Learning 노트북 vm을 선택 합니다.

1. 하위 디렉터리를 열고를 엽니다 `how-to-use-azureml/deploy-to-local/register-model-deploy-local.ipynb`. `samples-*` 열린 후에는 노트북을 실행 합니다.

    ![노트북에서 실행 중인 로컬 서비스의 스크린샷](media/how-to-deploy-local-container-notebookvm/deploy-local-service.png)

1. 노트북에는 서비스가 실행 되는 URL 및 포트가 표시 됩니다. `https://localhost:6789` )을 입력합니다. 가 포함 `print('Local service port: {}'.format(local_service.port))` 된 셀을 실행 하 여 포트를 표시할 수도 있습니다.

    ![실행 중인 로컬 서비스 포트의 스크린샷](media/how-to-deploy-local-container-notebookvm/deploy-local-service-port.png)

1. 노트북 VM에서 서비스를 테스트 하려면 `https://localhost:<local_service.port>` URL을 사용 합니다. 원격 클라이언트에서 테스트 하려면 노트북 VM에서 실행 되는 서비스의 공용 URL을 가져옵니다. 공용 URL은 다음 수식을 사용 하 여 확인할 수 있습니다. `https://<notebookvm_name>-<local_service_port>.<azure_region_of_notebook>.notebooks.azureml.net/score`. `https://mynotebookvm-6789.eastus2.notebooks.azureml.net/score` )을 입력합니다.

## <a name="test-the-service"></a>서비스 테스트

실행 중인 서비스에 샘플 데이터를 제출 하려면 다음 코드를 사용 합니다. 의 `service_url` 값을 이전 단계의 URL로 바꿉니다.

```python
import requests
import json
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')
access_token = "your bearer token"
headers = {'Content-Type':'application/json', 'Authorization': 'Bearer ' + access_token}
service_url = "https://mynotebookvm-6789.eastus2.notebooks.azureml.net/score"
resp = requests.post(service_url, test_sample, headers=headers)
print("prediction:", resp.text)
```

## <a name="next-steps"></a>다음 단계

* [사용자 지정 Docker 이미지를 사용 하 여 모델을 배포 하는 방법](how-to-deploy-custom-docker-image.md)
* [배포 문제 해결](how-to-troubleshoot-deployment.md)
* [SSL을 사용하여 Azure Machine Learning 웹 서비스 보호](how-to-secure-web-service.md)
* [웹 서비스로 배포된 ML 모델 사용](how-to-consume-web-service.md)
* [Application Insights를 사용하여 Azure Machine Learning 모델 모니터링](how-to-enable-app-insights.md)
* [프로덕션 환경에서 모델용 데이터 수집](how-to-enable-data-collection.md)