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
ms.openlocfilehash: 11e8b26c81d3a8f672abbeafc153df73d063cb6e
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68612237"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>Azure App Service에 machine learning 모델 배포 (미리 보기)

Azure App Service에서 웹 앱으로 Azure Machine Learning 서비스에서 모델을 배포 하는 방법에 대해 알아봅니다.

> [!IMPORTANT]
> Azure Machine Learning 서비스와 Azure App Service를 모두 사용할 수 있지만 Machine Learning 서비스에서 App Service로 모델을 배포 하는 기능은 미리 보기 상태입니다.

Azure Machine Learning 서비스를 사용 하 여 학습 된 기계 학습 모델에서 Docker 이미지를 만들 수 있습니다. 이 이미지에는 데이터를 수신 하 고 모델에 제출한 다음 응답을 반환 하는 웹 서비스가 포함 되어 있습니다. Azure App Service는 이미지를 배포 하는 데 사용할 수 있으며 다음과 같은 기능을 제공 합니다.

* 클라이언트와 서비스 간의 보안 통신에 대 한 [SSL 지원](/azure/app-service/app-service-web-ssl-cert-load) .
* 다시 배포 하지 않고도 여러 인스턴스로 [규모를 확장할](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json) 수 있습니다.
* 보안 강화를 위한 [고급 인증](/azure/app-service/configure-authentication-provider-aad)

Azure App Service에서 제공 하는 기능에 대 한 자세한 내용은 [App Service 개요](/azure/app-service/overview)를 참조 하세요.

## <a name="prerequisites"></a>전제 조건

* Azure Machine Learning 서비스 작업 영역. 자세한 내용은 [작업 영역 만들기](setup-create-workspace.md) 문서를 참조 하세요.
* 작업 영역에 등록 된 학습 된 기계 학습 모델입니다. 모델이 없는 경우 [이미지 분류 자습서: 학습 모델 학습](tutorial-train-models-with-aml.md) 및 등록을 사용 합니다.
* 모델에서 만든 Docker 이미지입니다. 이미지가 없는 경우 [이미지 분류: 모델 배포](tutorial-deploy-models-with-aml.md) 를 사용 하 여 모델을 만듭니다.

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

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]

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