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
ms.date: 09/24/2018
ms.openlocfilehash: d84040dc440c373ae9bae6dbac7a95109a387ba7
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47162749"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Azure Machine Learning 서비스의 알려진 문제 및 문제 해결
 
이 문서는 Azure Machine Learning 서비스를 사용할 때 발생한 오류 또는 장애를 찾아서 수정하는 데 도움을 줍니다. 


## <a name="databricks"></a>Databricks

Databricks 및 Azure Machine Learning 문제.

1. Databricks 클러스터 권장 사항:
   
   Python 3를 사용하여 v4.x로 Azure Databricks 클러스터를 만듭니다. 동시성이 높은 클러스터를 사용하는 것이 좋습니다.
 
1. Databricks에 다른 패키지가 설치되어 있는 경우 AML SDK를 설치하는 작업이 실패합니다.

   `psutil upgrade libs` 같은 일부 패키지가 충돌을 일으킬 수 있습니다. 라이브러리 버전을 동결하여 패키지를 설치하면 설치 오류를 방지할 수 있습니다. 이 문제는 Databricks와 관련이 있으며 AML SDK와는 관련이 없습니다. 예제:
   ```python
   pstuil cryptography==1.5 pyopenssl==16.0.0 ipython=2.2.0
   ```

## <a name="gather-diagnostics-information"></a>진단 정보 수집
도움말을 요청할 때 진단 정보를 제공할 수 있는 경우에 유용할 수 있습니다. 로그 파일 위치는 다음과 같습니다.

## <a name="resource-quotas"></a>리소스 할당량

Azure Machine Learning을 사용할 때 발생할 수 있는 [리소스 할당량](how-to-manage-quotas.md)에 대해 알아보세요.

## <a name="get-more-support"></a>더 많은 지원 받기

지원 요청을 제출하여 기술 지원, 포럼 등의 도움을 받을 수 있습니다. [자세한 정보...](support-for-aml-services.md)
