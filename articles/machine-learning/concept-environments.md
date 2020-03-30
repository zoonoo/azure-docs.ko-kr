---
title: Azure 기계 학습 환경 소개
titleSuffix: Azure Machine Learning
description: 이 문서에서는 다양한 컴퓨팅 대상에서 재현 가능, 감사 가능 및 휴대용 기계 학습 종속성 정의를 가능하게 하는 기계 학습 환경의 이점을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: trbye
author: trevorbye
ms.date: 03/18/2020
ms.openlocfilehash: 50ddbffd00e0cbbd0641089613aaa40d03658c9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064201"
---
# <a name="what-are-azure-machine-learning-environments"></a>Azure 기계 학습 환경이란 무엇입니까?
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure 기계 학습 환경은 학습 및 점수 매기기 스크립트에 대한 Python 패키지, 환경 변수 및 소프트웨어 설정을 지정합니다. 또한 실행 시간(파이썬, 스파크 또는 도커)을 지정합니다. 환경은 기계 학습 작업 영역 내에서 관리되고 버전이 관리되는 엔터티로, 다양한 컴퓨팅 대상에서 재현 가능하고 감사 가능하며 휴대용 기계 학습 워크플로우를 사용할 수 있습니다.

로컬 계산의 `Environment` 개체를 사용하여 다음을 수행할 수 있습니다.
* 교육 스크립트를 개발합니다.
* 규모에 대한 모델 학습에 Azure 기계 학습 계산에서 동일한 환경을 다시 사용합니다.
* 동일한 환경에서 모델을 배포합니다.

다음 다이어그램에서는 웹 서비스 `Environment` 배포에 대해 실행 구성, 교육 및 추론 및 배포 구성 모두에서 단일 개체를 사용하는 방법을 보여 줍니다.

![기계 학습 워크플로우의 환경 다이어그램](./media/concept-environments/ml-environment.png)

## <a name="types-of-environments"></a>환경 유형

환경은 크게 세 가지 범주로 나눌 수 있습니다: *큐레이터,* *사용자 관리,* *시스템 관리.*

선별된 환경은 Azure 기계 학습에서 제공되며 기본적으로 작업 영역에서 사용할 수 있습니다. 여기에는 다양한 기계 학습 프레임워크를 시작하는 데 도움이 되는 Python 패키지 및 설정 컬렉션이 포함되어 있습니다. 

사용자 관리 환경에서는 환경을 설정하고 학습 스크립트가 계산 대상에 필요한 모든 패키지를 설치해야 합니다. Conda는 환경을 확인하거나 아무 것도 설치하지 않습니다. 사용자 고유의 환경을 정의하는 경우 버전과 `>= 1.0.45` 함께 pip 종속성으로 나열해야 `azureml-defaults` 합니다. 이 패키지에는 웹 서비스로 모델을 호스트하는 데 필요한 기능이 포함되어 있습니다.

[Conda가](https://conda.io/docs/) Python 환경과 스크립트 종속성을 관리하려는 경우 시스템 관리 환경을 사용합니다. 서비스는 수동으로 구성할 수 없는 원격 계산 대상에 대한 유용성 때문에 기본적으로 이러한 유형의 환경을 가정합니다.

## <a name="create-and-manage-environments"></a>환경 생성 및 관리

다음을 통해 환경을 만들 수 있습니다.

* 선별된 `Environment` 환경을 사용하거나 사용자 고유의 종속성을 정의하여 새 개체를 정의합니다.
* 작업 `Environment` 공간에서 기존 개체 사용 이 방법을 사용하면 종속성에 대한 일관성과 재현성을 사용할 수 있습니다.
* 기존 Anaconda 환경 정의에서 가져오기.
* Azure 기계 학습 CLI 사용

특정 코드 샘플은 [학습 및 배포를 위한 재사용 환경의](how-to-use-environments.md#create-an-environment)"환경 만들기" 섹션을 참조하십시오. 작업 영역을 통해 환경을 쉽게 관리할 수도 있습니다. 여기에는 다음 기능이 포함됩니다.

* 환경을 실험에 제출할 때 작업 영역에 자동으로 등록됩니다. 또한 수동으로 등록할 수도 있습니다.
* 작업 영역에서 환경을 가져오면 교육 또는 배포에 사용하거나 환경 정의를 편집할 수 있습니다.
* 버전 분석에서는 시간이 지남에 따라 환경의 변경 사항을 확인할 수 있으므로 재현성이 보장됩니다.
* 환경에서 Docker 이미지를 자동으로 빌드할 수 있습니다.

코드 샘플은 [교육 및 배포를 위한 재사용 환경의](how-to-use-environments.md#manage-environments)"환경 관리" 섹션을 참조하십시오.

## <a name="environment-building-caching-and-reuse"></a>환경 구축, 캐싱 및 재사용

Azure 기계 학습 서비스는 Docker 이미지 및 conda 환경에 환경 정의를 빌드합니다. 또한 후속 교육 실행 및 서비스 엔드포인트 배포에서 다시 사용할 수 있도록 환경을 캐시합니다.

### <a name="building-environments-as-docker-images"></a>Docker 이미지로 환경 구축

일반적으로 환경을 사용하여 실행을 처음 제출할 때 Azure 기계 학습 서비스는 작업 영역과 연결된 ACR(Azure 컨테이너 레지스트리)에서 [ACR 빌드 작업을](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview) 호출합니다. 그런 다음 빌드된 Docker 이미지가 작업 영역 ACR에 캐시됩니다. 실행 실행을 시작할 때 계산 대상에서 이미지를 검색합니다.

이미지 빌드는 다음 두 단계로 구성됩니다.

 1. 기본 이미지 다운로드 및 Docker 단계 실행
 2. 환경 정의에 지정된 conda 종속성에 따라 conda 환경을 빌드합니다.

[사용자 관리 종속성을](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py)지정하는 경우 두 번째 단계는 생략됩니다. 이 경우 기본 이미지에 파이썬 패키지를 포함하거나 첫 번째 단계에서 사용자 지정 Docker 단계를 지정하여 모든 파이썬 패키지를 설치해야 합니다. 또한 파이썬 실행 에 대한 올바른 위치를 지정할 책임이 있습니다.

### <a name="image-caching-and-reuse"></a>이미지 캐싱 및 재사용

다른 실행에 동일한 환경 정의를 사용하는 경우 Azure 기계 학습 서비스는 작업 영역 ACR에서 캐시된 이미지를 다시 사용합니다. 

캐시된 이미지의 세부 정보를 보려면 [Environment.get_image_details](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#get-image-details-workspace-) 메서드를 사용합니다.

캐시된 이미지를 재사용할지 아니면 새 이미지를 빌드할지 여부를 결정하기 위해 서비스는 환경 정의에서 [해시 값을](https://en.wikipedia.org/wiki/Hash_table) 계산하고 이를 기존 환경의 해시와 비교합니다. 해시는 다음을 기반으로 합니다.
 
 * 기본 이미지 속성 값
 * 사용자 지정 도커 단계 속성 값
 * 콘다 정의의 파이썬 패키지 목록
 * 스파크 정의의 패키지 목록 

해시는 환경 이름이나 버전에 의존하지 않습니다. Python 패키지를 추가 하거나 제거하거나 패키지 버전을 변경하는 등의 환경 정의 변경으로 인해 해시 값이 변경되고 이미지 재생이 트리거됩니다. 그러나 환경의 이름을 바꾸거나 기존 환경의 정확한 속성 및 패키지로 새 환경을 만드는 경우 해시 값은 동일하게 유지되고 캐시된 이미지가 사용됩니다.

세 가지 환경 정의를 보여 주는 다음 다이어그램을 참조하십시오. 그 중 두 가지는 이름과 버전이 다르지만 기본 이미지와 파이썬 패키지가 동일합니다. 동일한 해시를 가지므로 동일한 캐시된 이미지에 해당합니다. 세 번째 환경은 서로 다른 파이썬 패키지와 버전을 가지므로 다른 캐시된 이미지에 해당합니다.

![환경 캐싱을 Docker 이미지로 다이어그램](./media/concept-environments/environment-caching.png)

>[!IMPORTANT]
> 고정되지 않은 패키지 종속성이 있는 환경을 만드는 경우(예: ```numpy```해당 환경은 환경 생성 _시_설치된 패키지 버전을 계속 사용합니다. 또한 일치하는 정의가 있는 이후 환경은 이전 버전을 계속 사용하게 됩니다. 

패키지를 업데이트하려면 예를 들어 ```numpy==1.18.1```이미지 재생성하도록 하는 버전 번호를 지정합니다. 중첩된 종속을 포함한 새 종속성이 설치되어 이전에 작업중인 시나리오를 중단할 수 있습니다.

> [!WARNING]
>  [Environment.build](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#build-workspace--image-build-compute-none-) 메서드는 고정되지 않은 패키지를 업데이트하고 캐시된 이미지에 해당하는 모든 환경 정의에 대한 재현성을 깨는 부작용으로 캐시된 이미지를 다시 빌드합니다.

## <a name="next-steps"></a>다음 단계

* Azure 기계 학습에서 [환경을 만들고 사용하는](how-to-use-environments.md) 방법을 알아봅니다.
* [환경 클래스에](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py)대한 Python SDK 참조 설명서를 참조하십시오.
* [환경에](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments)대한 R SDK 참조 설명서를 참조하십시오.
