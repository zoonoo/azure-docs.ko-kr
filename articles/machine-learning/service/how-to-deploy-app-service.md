---
title: Azure App Service에 ml 모델 배포 (미리 보기)
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning 서비스를 사용 하 여 Azure App Service에서 웹 앱에 모델을 배포 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/01/2019
ms.openlocfilehash: a5fd376a6da70ed68baedf44fd4c2cc47e68d3cf
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69872382"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>Azure App Service에 machine learning 모델 배포 (미리 보기)

Azure App Service에서 웹 앱으로 Azure Machine Learning 서비스에서 모델을 배포 하는 방법에 대해 알아봅니다.

> [!IMPORTANT]
> Azure Machine Learning 서비스와 Azure App Service를 모두 사용할 수 있지만 Machine Learning 서비스에서 App Service로 모델을 배포 하는 기능은 미리 보기 상태입니다.

Azure Machine Learning 서비스를 사용 하 여 학습 된 기계 학습 모델에서 Docker 이미지를 만들 수 있습니다. 이 이미지에는 데이터를 수신 하 고 모델에 제출한 다음 응답을 반환 하는 웹 서비스가 포함 되어 있습니다. Azure App Service는 이미지를 배포 하는 데 사용할 수 있으며 다음과 같은 기능을 제공 합니다.

* 보안 강화를 위한 고급 [인증](/azure/app-service/configure-authentication-provider-aad) 인증 방법에는 Azure Active Directory 및 multi-factor auth가 모두 포함 됩니다.
* 다시 배포할 필요 없이 [자동으로 크기를 조정](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json) 합니다.
* 클라이언트와 서비스 간의 보안 통신에 대 한 [SSL 지원](/azure/app-service/app-service-web-ssl-cert-load) .

Azure App Service에서 제공 하는 기능에 대 한 자세한 내용은 [App Service 개요](/azure/app-service/overview)를 참조 하세요.

> [!IMPORTANT]
> 배포 된 모델에 사용 되는 점수 매기기 데이터 또는 점수 매기기 결과를 기록 하는 기능이 필요한 경우에는 대신 Azure Kubernetes Service에 배포 해야 합니다. 자세한 내용은 [프로덕션 모델에서 데이터 수집](how-to-enable-data-collection.md)을 참조 하세요.

## <a name="prerequisites"></a>필수 구성 요소

* Azure Machine Learning 서비스 작업 영역. 자세한 내용은 [작업 영역 만들기](how-to-manage-workspace.md) 문서를 참조 하세요.
* 작업 영역에 등록 된 학습 된 기계 학습 모델입니다. 모델이 없는 경우 [이미지 분류 자습서: 학습 모델 학습](tutorial-train-models-with-aml.md) 및 등록을 사용 합니다.

    > [!IMPORTANT]
    > 이 문서의 코드 조각은 다음 변수를 설정 했다고 가정 합니다.
    >
    > * `ws`-Azure Machine Learning 작업 영역입니다.
    > * `model`-배포 될 등록 된 모델입니다.
    > * `inference_config`-모델에 대 한 유추 구성입니다.
    >
    > 이러한 변수를 설정 하는 방법에 대 한 자세한 내용은 [Azure Machine Learning 서비스를 사용 하 여 모델 배포](how-to-deploy-and-where.md)를 참조 하세요.

## <a name="prepare-for-deployment"></a>배포 준비

을 배포 하기 전에 모델을 웹 서비스로 실행 하는 데 필요한 작업을 정의 해야 합니다. 다음 목록에서는 배포에 필요한 기본 항목에 대해 설명 합니다.

* __항목 스크립트__입니다. 이 스크립트는 요청을 수락 하 고, 모델을 사용 하 여 요청 점수를 생성 하 고, 결과를 반환 합니다.

    > [!IMPORTANT]
    > 항목 스크립트는 모델에 따라 다릅니다. 들어오는 요청 데이터의 형식, 모델에 필요한 데이터의 형식 및 클라이언트에 반환 되는 데이터 형식을 이해 해야 합니다.
    >
    > 요청 데이터가 모델에서 사용할 수 없는 형식인 경우 스크립트는이를 허용 되는 형식으로 변환할 수 있습니다. 클라이언트에 반환 하기 전에 응답을 변환할 수도 있습니다.

    > [!IMPORTANT]
    > Azure Machine Learning SDK는 웹 서비스에서 데이터 저장소 또는 데이터 집합에 액세스 하는 방법을 제공 하지 않습니다. 배포 외부에 저장 된 데이터에 액세스 하기 위해 배포 된 모델이 필요한 경우 (예: Azure Storage 계정에서) 관련 SDK를 사용 하 여 사용자 지정 코드 솔루션을 개발 해야 합니다. 예를 들어 [Python 용 AZURE STORAGE SDK](https://github.com/Azure/azure-storage-python)가 있습니다.
    >
    > 시나리오에 사용할 수 있는 또 다른 대안은 점수를 매길 때 데이터 저장소에 대 한 액세스를 제공 하는 [일괄 처리 예측](how-to-run-batch-predictions.md)입니다.

    항목 스크립트에 대 한 자세한 내용은 [Azure Machine Learning 서비스를 사용 하 여 모델 배포](how-to-deploy-and-where.md)를 참조 하세요.

* 항목 스크립트나 모델을 실행 하는 데 필요한 도우미 스크립트 또는 Python/Conda 패키지와 같은 **종속성**

이러한 엔터티는 __유추 구성__에 캡슐화 됩니다. 유추 구성은 입력 스크립트 및 기타 종속성을 참조 합니다.

> [!IMPORTANT]
> Azure App Service에 사용할 유추 구성을 만드는 경우 [환경](https://docs.microsoft.com//python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py) 개체를 사용 해야 합니다. 다음 예제에서는 환경 개체를 만들고 유추 구성에서 사용 하는 방법을 보여 줍니다.
>
> ```python
> from azureml.core import Environment
> from azureml.core.environment import CondaDependencies
>
> # Create an environment and add conda dependencies to it
> myenv = Environment(name="myenv")
> # Enable Docker based environment
> myenv.docker.enabled = True
> # Build conda dependencies
> myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
> ```

환경에 대 한 자세한 내용은 [교육 및 배포를 위한 환경 만들기 및 관리](how-to-use-environments.md)를 참조 하세요.

유추 구성에 대 한 자세한 내용은 [Azure Machine Learning 서비스를 사용 하 여 모델 배포](how-to-deploy-and-where.md)를 참조 하세요.

> [!IMPORTANT]
> Azure App Service에 배포 하는 경우 __배포 구성을__만들 필요가 없습니다.

## <a name="create-the-image"></a>이미지 만들기

Azure App Service에 배포 되는 Docker 이미지를 만들려면 [모델인 패키지](https://docs.microsoft.com//python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config--generate-dockerfile-false-)를 사용 합니다. 다음 코드 조각에서는 모델 및 유추 구성에서 새 이미지를 작성 하는 방법을 보여 줍니다.

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

인 `show_output=True`경우 Docker 빌드 프로세스의 출력이 표시 됩니다. 프로세스가 완료 되 면 작업 영역에 대 한 Azure Container Registry에 이미지가 생성 됩니다.

## <a name="deploy-image-as-a-web-app"></a>이미지를 웹 앱으로 배포

1. [Azure Portal](https://portal.azure.com)에서 Azure Machine Learning 작업 영역을 선택 합니다. __개요__ 섹션에서 __레지스트리__ 링크를 사용 하 여 작업 영역에 대 한 Azure Container Registry에 액세스 합니다.

    ![작업 영역에 대 한 개요의 스크린샷](media/how-to-deploy-app-service/workspace-overview.png)

2. Azure Container Registry에서 __리포지토리__를 선택 하 고 배포 하려는 __이미지 이름을__ 선택 합니다. 배포 하려는 버전에 대해 __...__ 항목을 선택 하 고 __웹 앱에 배포__합니다.

    ![ACR에서 웹 앱으로 배포 스크린샷](media/how-to-deploy-app-service/deploy-to-web-app.png)

3. 웹 앱을 만들려면 사이트 이름, 구독, 리소스 그룹을 제공 하 고 App service 계획/위치를 선택 합니다. 마지막으로 __만들기__를 선택합니다.

    ![새 웹 앱 대화 상자의 스크린샷](media/how-to-deploy-app-service/web-app-for-containers.png)

## <a name="use-the-web-app"></a>웹 앱 사용

[Azure Portal](https://portal.azure.com)에서 이전 단계에서 만든 웹 앱을 선택 합니다. __개요__ 섹션에서 __URL__을 복사 합니다. 이 값은 서비스의 __기본 URL__ 입니다.

![웹 앱에 대 한 개요의 스크린샷](media/how-to-deploy-app-service/web-app-overview.png)

모델에 요청을 전달 하는 웹 서비스는에 `{baseurl}/score`있습니다. `https://mywebapp.azurewebsites.net/score` )을 입력합니다. 다음 Python 코드는 URL에 데이터를 전송 하 고 응답을 표시 하는 방법을 보여 줍니다.

```python
import requests
import json

scoring_uri = "https://mywebapp.azurewebsites.net/score"

headers = {'Content-Type':'application/json'}

print(headers)
    
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})

response = requests.post(scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

## <a name="next-steps"></a>다음 단계

* 웹 앱을 구성 하는 방법에 대 한 자세한 내용은 [Linux의 App Service](/azure/app-service/containers/) 설명서를 참조 하세요.
* 크기 조정에 대 한 자세한 내용은 [Azure에서 자동 크기 조정 시작](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json)을 참조 하세요.
* SSL 지원에 대 한 자세한 내용은 [Azure App Service에서 ssl 인증서 사용](/azure/app-service/app-service-web-ssl-cert-load)을 참조 하세요.
* 인증에 대 한 자세한 내용은 [Azure Active Directory 로그인을 사용 하도록 App Service 앱 구성](/azure/app-service/configure-authentication-provider-aad)을 참조 하세요.
* [웹 서비스로 배포된 ML 모델 사용](how-to-consume-web-service.md)