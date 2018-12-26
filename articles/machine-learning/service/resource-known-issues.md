---
title: Azure Machine Learning 서비스에 대해 알려진 문제 및 문제 해결
description: 알려진 문제, 해결 방법 및 문제 해결 목록 받기
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.component: core
ms.topic: article
ms.date: 10/01/2018
ms.openlocfilehash: 02cee5a3e088c919ec94aee6f46ef6f428b9bb48
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2018
ms.locfileid: "48249420"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Azure Machine Learning 서비스의 알려진 문제 및 문제 해결
 
이 문서는 Azure Machine Learning 서비스를 사용할 때 발생한 오류 또는 장애를 찾아서 수정하는 데 도움을 줍니다. 

## <a name="sdk-installation-issues"></a>SDK 설치 문제

**오류 메시지: ‘PyYAML’을 제거할 수 없습니다.** 

PyYAML은 distutils 설치 프로젝트입니다. 따라서 부분 제거 시 속해 있는 파일을 정확히 확인할 수 없습니다. 이 오류를 무시하면서 SDK를 게속 설치하려면 다음을 사용합니다.
```Python 
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

## <a name="image-building-failure"></a>이미지 빌드 실패

웹 서비스를 배포할 때 이미지 빌드가 실패했습니다. 해결 방법은 이미지 구성을 위한 Conda 파일에 "pynacl==1.2.1"을 pip 종속성으로 추가하는 것입니다.  

## <a name="pipelines"></a>파이프라인
스크립트 또는 매개 변수를 변경하지 않고 하나의 행에서 PythonScriptStep을 여러 번 호출하면 오류가 발생합니다. 해결 방법은 PipelineData 개체를 다시 빌드하는 것입니다.

## <a name="fpgas"></a>FPGA
요청을 하고 FPGA 할당량의 승인을 받을 때까지 FPGA에 모델을 배포할 수 없습니다. 액세스를 요청하려면 할당량 요청 양식 https://aka.ms/aml-real-time-ai를 작성합니다.

## <a name="databricks"></a>Databricks

Databricks 및 Azure Machine Learning 문제.

1. Databricks 클러스터 권장 사항:
   
   Python 3를 사용하여 v4.x로 Azure Databricks 클러스터를 만듭니다. 동시성이 높은 클러스터를 사용하는 것이 좋습니다.
 
1. Databricks에 다른 패키지가 설치되어 있는 경우 AML SDK를 설치하는 작업이 실패합니다.

   `psutil upgrade libs` 같은 일부 패키지가 충돌을 일으킬 수 있습니다. 라이브러리 버전을 동결하여 패키지를 설치하면 설치 오류를 방지할 수 있습니다. 이 문제는 Databricks와 관련이 있으며 AML SDK와는 관련이 없습니다. 예제:
   ```python
   pstuil cryptography==1.5 pyopenssl==16.0.0 ipython=2.2.0
   ```

## <a name="diagnostic-logs"></a>진단 로그
도움말을 요청할 때 진단 정보를 제공할 수 있는 경우에 유용할 수 있습니다. 로그 파일 위치는 다음과 같습니다.

## <a name="resource-quotas"></a>리소스 할당량

Azure Machine Learning을 사용할 때 발생할 수 있는 [리소스 할당량](how-to-manage-quotas.md)에 대해 알아보세요.

## <a name="get-more-support"></a>더 많은 지원 받기

지원 요청을 제출하여 기술 지원, 포럼 등의 도움을 받을 수 있습니다. [자세한 정보...](support-for-aml-services.md)
