---
title: 알려진 문제 및 문제 해결
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning Service에 대해 알려진 문제, 해결 방법 및 문제 해결의 목록을 가져옵니다.
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.component: core
ms.topic: article
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 4a4f1691162ab9c9fbd5bc8802ecf7ebc4894d74
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53193674"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Azure Machine Learning 서비스의 알려진 문제 및 문제 해결
 
이 문서는 Azure Machine Learning 서비스를 사용할 때 발생한 오류 또는 장애를 찾아서 수정하는 데 도움을 줍니다. 

## <a name="sdk-installation-issues"></a>SDK 설치 문제

**오류 메시지: ‘PyYAML’을 제거할 수 없습니다.** 

Python용 Azure Machine Learning SDK: PyYAML은 distutils 설치 프로젝트입니다. 따라서 부분 제거 시 속해 있는 파일을 정확히 확인할 수 없습니다. 이 오류를 무시하면서 SDK를 게속 설치하려면 다음을 사용합니다.
```Python 
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

## <a name="trouble-creating-azure-machine-learning-compute"></a>Azure Machine Learning 컴퓨팅을 만드는 문제
GA 릴리스 전에 Azure Portal에서 Azure Machine Learning 작업 영역을 만든 일부 사용자가 해당 작업 영역에서 Azure Machine Learning 컴퓨팅을 만들 수 없는 경우는 거의 없습니다. 서비스에 대해 지원 요청을 제기하거나 포털 또는 SDK를 통해 새 작업 영역을 만들어 즉시 차단을 직접 해제할 수 있습니다. 

## <a name="image-building-failure"></a>이미지 빌드 실패

웹 서비스를 배포할 때 이미지 빌드가 실패했습니다. 해결 방법은 이미지 구성을 위한 Conda 파일에 "pynacl==1.2.1"을 pip 종속성으로 추가하는 것입니다.  

## <a name="fpgas"></a>FPGA
요청을 하고 FPGA 할당량의 승인을 받을 때까지 FPGA에 모델을 배포할 수 없습니다. 액세스를 요청하려면 할당량 요청 양식 https://aka.ms/aml-real-time-ai를 작성합니다.

## <a name="databricks"></a>Databricks

Databricks 및 Azure Machine Learning 문제.

1. Databricks 클러스터 권장 사항:
   
   Python 3를 사용하여 v4.x로 Azure Databricks 클러스터를 만듭니다. 동시성이 높은 클러스터를 사용하는 것이 좋습니다.
 
2. Databricks에 다른 패키지가 설치되어 있는 경우 AML SDK를 설치하는 작업이 실패합니다.

   `psutil` 같은 일부 패키지가 충돌을 일으킬 수 있습니다. 라이브러리 버전을 동결하여 패키지를 설치하면 설치 오류를 방지할 수 있습니다. 이 문제는 Databricks와 관련돼 있으며 Azure ML SDK와는 관련이 없습니다. 이 문제는 기타 라이브러리에서도 발생할 수 있습니다. 예제:
   ```python
   pstuil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
   ```
   또는 설치 문제가 계속되는 경우 Python 라이브러리를 사용하여 초기화 스크립트를 사용할 수 있습니다. 이 방법은 공식적으로 지원되지는 않습니다. [이 문서](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts)를 참조할 수 있습니다.

3. Databricks에서 자동화된 Machine Learning을 사용할 때 `Import error: numpy.core.multiarray failed to import`가 표시되는 경우

   해결 방법: 라이브러리 만들기를 사용하여 `numpy==1.14.5` Python 라이브러리를 사용자의 Databricks 클러스터로 가져와서 [설치 및 연결](https://docs.databricks.com/user-guide/libraries.html#create-a-library)합니다.

## <a name="azure-portal"></a>Azure portal
SDK 또는 포털의 공유 링크에서 작업 영역을 직접 확인하려는 경우 확장에서 구독 정보가 포함된 일반 개요 페이지를 확인할 수 없습니다. 다른 작업 영역으로 전환할 수 없습니다. 다른 작업 영역을 확인해야 하는 경우 해결 방법은 [Azure Portal](https://portal.azure.com)로 직접 이동하여 작업 영역 이름을 검색하는 것입니다.

## <a name="diagnostic-logs"></a>진단 로그
도움말을 요청할 때 진단 정보를 제공할 수 있는 경우에 유용할 수 있습니다. 로그 파일 위치는 다음과 같습니다.

## <a name="resource-quotas"></a>리소스 할당량

Azure Machine Learning을 사용할 때 발생할 수 있는 [리소스 할당량](how-to-manage-quotas.md)에 대해 알아보세요.

## <a name="get-more-support"></a>더 많은 지원 받기

지원 요청을 제출하여 기술 지원, 포럼 등의 도움을 받을 수 있습니다. [자세한 정보...](support-for-aml-services.md)
