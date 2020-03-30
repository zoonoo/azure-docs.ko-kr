---
title: 인스턴스를 계산하기 위해 모델을 배포하는 방법
titleSuffix: Azure Machine Learning
description: 계산 인스턴스를 사용하여 Azure 기계 학습 모델을 웹 서비스로 배포하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mnark
author: MrudulaN
ms.reviewer: larryfr
ms.date: 03/05/2020
ms.openlocfilehash: afbd9950c31bc1c40b01ec0aaf3d2bfffb8a6b94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78398219"
---
# <a name="deploy-a-model-to-azure-machine-learning-compute-instances"></a>Azure 기계 학습 계산 인스턴스에 모델 배포

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure 기계 학습을 사용하여 Azure 기계 학습 계산 인스턴스에서 모델을 웹 서비스로 배포하는 방법을 알아봅니다. 다음 조건 중 하나가 true인 경우 계산 인스턴스를 사용합니다.

- 모델을 빠르게 배포하고 유효성을 검사해야 합니다.
- 개발 중인 모델을 테스트합니다.

> [!TIP]
> 계산 인스턴스에서 Jupyter 노트북에서 동일한 VM의 웹 서비스에 모델을 배포하는 것은 _로컬 배포입니다._ 이 경우 '로컬' 컴퓨터는 계산 인스턴스입니다. 배포에 대한 자세한 내용은 [Azure 기계 학습을 통해 모델 배포를](how-to-deploy-and-where.md)참조합니다.

## <a name="prerequisites"></a>사전 요구 사항

- 계산 인스턴스가 실행되는 Azure 기계 학습 작업 영역입니다. 자세한 내용은 [설치 환경 및 작업 영역을](tutorial-1st-experiment-sdk-setup.md)참조하십시오.

## <a name="deploy-to-the-compute-instances"></a>계산 인스턴스에 배포

로컬 배포를 보여 주는 예제 전자 필기장이 계산 인스턴스에 포함되어 있습니다. 다음 단계를 사용하여 노트북을 로드하고 모델을 VM에 웹 서비스로 배포합니다.

1. [Azure 기계 학습 스튜디오에서](https://ml.azure.com)Azure 기계 학습 계산 인스턴스를 선택합니다.

1. 하위 `samples-*` 디렉터리를 열고 을 `how-to-use-azureml/deploy-to-local/register-model-deploy-local.ipynb`엽니다. 일단 열리면 노트북을 실행합니다.

    ![노트북에서 실행 중인 로컬 서비스의 스크린샷](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service.png)

1. 전자 필기장에는 서비스가 실행 중인 URL과 포트가 표시됩니다. `https://localhost:6789`)을 입력합니다. 포함된 셀을 `print('Local service port: {}'.format(local_service.port))` 실행하여 포트를 표시할 수도 있습니다.

    ![실행 중인 로컬 서비스 포트의 스크린샷](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service-port.png)

1. 계산 인스턴스에서 서비스를 테스트하려면 URL을 `https://localhost:<local_service.port>` 사용합니다. 원격 클라이언트에서 테스트하려면 계산 인스턴스에서 실행되는 서비스의 공용 URL을 가져옵니다. 공용 URL은 다음 수식을 사용하여 결정할 수 있습니다. 
    * 노트북 VM: `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.notebooks.azureml.net/score`. 
    * 계산 인스턴스: `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.instances.azureml.net/score`. 

    예를 들면 다음과 같습니다. 
    * 노트북 VM:`https://vm-name-6789.northcentralus.notebooks.azureml.net/score` 
    * 인스턴스 계산:`https://vm-name-6789.northcentralus.instances.azureml.net/score`

## <a name="test-the-service"></a>서비스 테스트

실행 중인 서비스에 샘플 데이터를 제출하려면 다음 코드를 사용합니다. 값을 이전 `service_url` 단계의 URL로 바꿉꿉입니다.

> [!NOTE]
> 계산 인스턴스에서 배포를 인증할 때 Azure Active Directory를 사용하여 인증이 수행됩니다. 예제 `interactive_auth.get_authentication_header()` 코드에서 호출하면 AAD를 사용하여 인증한 다음 계산 인스턴스에서 서비스를 인증하는 데 사용할 수 있는 헤더를 반환합니다. 자세한 내용은 [Azure 기계 학습 리소스 및 워크플로에 대한 인증 설정을](how-to-setup-authentication.md#interactive-authentication)참조하십시오.
>
> Azure Kubernetes 서비스 또는 Azure 컨테이너 인스턴스에서 배포를 인증할 때 다른 인증 방법이 사용됩니다. 자세한 내용은 Azure [기계 학습 리소스 및 워크플로에 대한 인증 설정을](how-to-setup-authentication.md#web-service-authentication)참조하십시오.

```python
import requests
import json
from azureml.core.authentication import InteractiveLoginAuthentication

# Get a token to authenticate to the compute instance from remote
interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()

# Create and submit a request using the auth header
headers = auth_header
# Add content type header
headers.update({'Content-Type':'application/json'})

# Sample data to send to the service
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')

# Replace with the URL for your compute instance, as determined from the previous section
service_url = "https://vm-name-6789.northcentralus.notebooks.azureml.net/score"
# for a compute instance, the url would be https://vm-name-6789.northcentralus.instances.azureml.net/score
resp = requests.post(service_url, test_sample, headers=headers)
print("prediction:", resp.text)
```

## <a name="next-steps"></a>다음 단계

* [사용자 지정 Docker 이미지를 사용하여 모델을 배포하는 방법](how-to-deploy-custom-docker-image.md)
* [배포 문제 해결](how-to-troubleshoot-deployment.md)
* [SSL을 사용하여 Azure Machine Learning 웹 서비스 보호](how-to-secure-web-service.md)
* [웹 서비스로 배포된 ML 모델 사용](how-to-consume-web-service.md)
* [Application Insights를 사용하여 Azure Machine Learning 모델 모니터링](how-to-enable-app-insights.md)
* [프로덕션 환경에서 모델용 데이터 수집](how-to-enable-data-collection.md)