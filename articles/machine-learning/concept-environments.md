---
title: Azure Machine Learning 환경 정보
titleSuffix: Azure Machine Learning
description: 이 문서에서는 다양 한 계산 대상에서 재현 가능 하 고 감사 가능 하며 이식 가능한 machine learning 종속성 정의를 사용할 수 있는 기계 학습 환경의 이점에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: trbye
author: trevorbye
ms.date: 03/18/2020
ms.openlocfilehash: 50ddbffd00e0cbbd0641089613aaa40d03658c9e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80064201"
---
# <a name="what-are-azure-machine-learning-environments"></a>Azure Machine Learning 환경 이란 무엇 인가요?
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning 환경에서는 학습 및 점수 매기기 스크립트와 관련 된 Python 패키지, 환경 변수 및 소프트웨어 설정을 지정 합니다. 실행 시간 (Python, Spark 또는 Docker)도 지정 합니다. 환경은 다양 한 계산 대상에서 재현 가능 하 고 감사 가능 하며 이식 가능한 기계 학습 워크플로를 사용할 수 있도록 하는 Machine Learning 작업 영역 내에서 관리 되는 버전의 엔터티입니다.

로컬 계산에서 `Environment` 개체를 사용 하 여 다음을 수행할 수 있습니다.
* 학습 스크립트를 개발 합니다.
* 대규모 모델 학습을 위해 동일한 환경을 Azure Machine Learning 계산에서 재사용 합니다.
* 동일한 환경을 사용 하 여 모델을 배포 합니다.

다음 다이어그램에서는 웹 서비스 배포를 위해 실행 구성 `Environment` , 학습 및 유추 및 배포 구성 모두에서 단일 개체를 사용할 수 있는 방법을 보여 줍니다.

![기계 학습 워크플로의 환경 다이어그램](./media/concept-environments/ml-environment.png)

## <a name="types-of-environments"></a>환경 유형

환경은 *큐 레이트*, *사용자 관리*및 *시스템 관리*의 세 가지 범주로 나눌 수 있습니다.

큐 레이트 환경은 Azure Machine Learning에서 제공 하며 기본적으로 작업 영역에서 사용할 수 있습니다. 여기에는 다양 한 기계 학습 프레임 워크를 시작 하는 데 도움이 되는 Python 패키지 및 설정 컬렉션이 포함 되어 있습니다. 

사용자 관리 환경에서는 환경 설정 및 학습 스크립트가 계산 대상에 필요한 모든 패키지를 설치 하는 작업을 담당 합니다. Conda 사용자 환경을 확인 하거나 어떤 것도 설치 하지 않습니다. 사용자 고유의 환경을 정의 하는 경우 pip 종속성으로 버전 `azureml-defaults` `>= 1.0.45` 을 나열 해야 합니다. 이 패키지에는 웹 서비스로 모델을 호스트 하는 데 필요한 기능이 포함 되어 있습니다.

[Conda](https://conda.io/docs/) 에서 Python 환경 및 스크립트 종속성을 관리 하려는 경우 시스템 관리 환경을 사용 합니다. 서비스는 수동으로 구성할 수 없는 원격 계산 대상의 유용성으로 인해 기본적으로 이러한 유형의 환경을 가정 합니다.

## <a name="create-and-manage-environments"></a>환경 만들기 및 관리

다음을 수행 하 여 환경을 만들 수 있습니다.

* 큐 레이트 환경을 `Environment` 사용 하거나 고유한 종속성을 정의 하 여 새 개체를 정의 합니다.
* 작업 영역 `Environment` 에서 기존 개체를 사용 합니다. 이 접근 방식을 사용 하면 일관성과 종속성을 재현 가능성 수 있습니다.
* 기존 Anaconda 환경 정의에서 가져오기.
* Azure Machine Learning CLI 사용

특정 코드 샘플은 [학습 및 배포를 위한 재사용 환경](how-to-use-environments.md#create-an-environment)에서 "환경 만들기" 섹션을 참조 하세요. 환경도 작업 영역을 통해 쉽게 관리할 수 있습니다. 여기에는 다음과 같은 기능이 포함 됩니다.

* 환경은 실험을 제출할 때 작업 영역에 자동으로 등록 됩니다. 수동으로 등록할 수도 있습니다.
* 작업 영역에서 교육 또는 배포에 사용할 환경을 인출 하거나 환경 정의를 편집할 수 있습니다.
* 버전 관리를 사용 하면 시간에 따라 환경에 대 한 변경 내용을 볼 수 있습니다. 그러면 재현 가능성이 보장 됩니다.
* 사용자 환경에서 자동으로 Docker 이미지를 빌드할 수 있습니다.

코드 샘플은 [학습 및 배포를 위한 재사용 환경의](how-to-use-environments.md#manage-environments)"환경 관리" 섹션을 참조 하세요.

## <a name="environment-building-caching-and-reuse"></a>환경 빌드, 캐싱 및 다시 사용

Azure Machine Learning 서비스는 환경 정의를 Docker 이미지 및 conda 환경으로 작성 합니다. 또한 이후 학습 실행 및 서비스 끝점 배포에서 다시 사용할 수 있도록 환경을 캐시 합니다.

### <a name="building-environments-as-docker-images"></a>Docker 이미지로 환경 구축

일반적으로 환경을 사용 하 여 실행을 제출할 때 Azure Machine Learning 서비스는 작업 영역과 연결 된 ACR (Azure Container Registry)에서 [Acr Build 작업](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview) 을 호출 합니다. 그러면 빌드된 Docker 이미지가 작업 영역 ACR에 캐시 됩니다. 실행을 시작할 때 이미지는 계산 대상에 의해 검색 됩니다.

이미지 빌드는 두 단계로 구성 됩니다.

 1. 기본 이미지 다운로드 및 Docker 단계 실행
 2. 환경 정의에 지정 된 conda 종속성에 따라 conda 환경을 구축 합니다.

[사용자 관리 종속성](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py)을 지정 하는 경우 두 번째 단계가 생략 됩니다. 이 경우 기본 이미지에 포함 하거나 첫 번째 단계 내에서 사용자 지정 Docker 단계를 지정 하 여 Python 패키지를 설치 해야 합니다. 또한 Python 실행 파일에 대 한 올바른 위치를 지정 해야 합니다.

### <a name="image-caching-and-reuse"></a>이미지 캐싱 및 다시 사용

다른 실행에 동일한 환경 정의를 사용 하는 경우 Azure Machine Learning 서비스는 작업 영역 ACR에서 캐시 된 이미지를 재사용 합니다. 

캐시 된 이미지의 세부 정보를 보려면 [get_image_details](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#get-image-details-workspace-) 메서드를 사용 합니다.

캐시 된 이미지를 다시 사용 하거나 새 이미지를 빌드 할지 여부를 결정 하기 위해 서비스는 환경 정의에서 [해시 값을](https://en.wikipedia.org/wiki/Hash_table) 계산 하 고이를 기존 환경의 해시와 비교 합니다. 해시는 다음을 기반으로 합니다.
 
 * 기본 이미지 속성 값
 * 사용자 지정 docker 단계 속성 값
 * Conda 정의에 있는 Python 패키지 목록
 * Spark 정의의 패키지 목록 

해시는 환경 이름이 나 버전에 따라 달라 지지 않습니다. Python 패키지를 추가 또는 제거 하거나 패키지 버전을 변경 하는 등 환경 정의를 변경 하면 해시 값이 변경 되 고 이미지 다시 작성이 트리거됩니다. 그러나 단순히 환경의 이름을 바꾸거나 기존 환경에 대 한 정확한 속성 및 패키지를 사용 하 여 새 환경을 만드는 경우 해시 값은 동일 하 게 유지 되 고 캐시 된 이미지가 사용 됩니다.

세 가지 환경 정의를 보여 주는 다음 다이어그램을 참조 하세요. 그 중 두 가지는 서로 다른 이름과 버전을 갖지만 동일한 기본 이미지 및 Python 패키지를 포함 합니다. 동일한 해시가 있으므로 동일한 캐시 된 이미지에 해당 합니다. 세 번째 환경에는 다른 Python 패키지와 버전이 있으므로 다른 캐시 된 이미지에 해당 합니다.

![Docker 이미지인 환경 캐싱 다이어그램](./media/concept-environments/environment-caching.png)

>[!IMPORTANT]
> 예 ```numpy```를 들어 고정 해제 된 패키지 종속성을 사용 하 여 환경을 만드는 경우 _환경을 만들 때 설치 된_패키지 버전을 계속 사용 하 게 됩니다. 또한 일치 하는 정의가 있는 이후의 모든 환경은 이전 버전을 계속 사용 합니다. 

패키지를 업데이트 하려면 이미지를 다시 작성 하는 예를 들어 ```numpy==1.18.1```버전 번호를 지정 합니다. 중첩 된 항목을 포함 한 새 종속성이 설치 되어 이전에 작업 중인 시나리오가 중단 될 수 있습니다.

> [!WARNING]
>  재현 가능성 [메서드는](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#build-workspace--image-build-compute-none-) 캐시 된 이미지를 다시 작성 합니다 .이는 고정 되지 않은 패키지를 업데이트 하 고 해당 캐시 된 이미지에 해당 하는 모든 환경 정의에 대해를 중단 시킬 수 있습니다.

## <a name="next-steps"></a>다음 단계

* Azure Machine Learning에서 [환경을 만들고 사용](how-to-use-environments.md) 하는 방법에 대해 알아봅니다.
* [환경 클래스](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py)에 대 한 Python SDK 참조 설명서를 참조 하세요.
* [환경](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments)에 대 한 R SDK 참조 설명서를 참조 하세요.
