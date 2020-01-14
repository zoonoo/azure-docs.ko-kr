---
title: ML 환경 이란?
titleSuffix: Azure Machine Learning
description: 이 문서에서는 다양 한 계산 대상에서 재현 가능 하 고 감사 가능 하며 이식 가능한 machine learning 종속성 정의를 사용할 수 있도록 하는 기계 학습 환경의 이점에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: trbye
author: trevorbye
ms.date: 01/06/2020
ms.openlocfilehash: 3216248943ccc0dba788816cdba38732f9e43e14
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75930759"
---
# <a name="what-are-azure-machine-learning-environments"></a>Azure Machine Learning 환경 이란 무엇 인가요?
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

환경에서는 학습 및 점수 매기기 스크립트와 실행 시간 (Python, Spark 또는 Docker)에 대 한 Python 패키지, 환경 변수 및 소프트웨어 설정을 지정 합니다. 이러한 항목은 다양 한 계산 대상에서 재현 가능 하 고 감사 가능 하며 휴대용 기계 학습 워크플로를 가능 하 게 하는 Azure Machine Learning 작업 영역 내에서 관리 되 고 버전이 지정 된 엔터티입니다

로컬 계산의 환경 개체를 사용 하 여 학습 스크립트를 개발 하 고, 대규모로 모델 학습을 위해 동일한 환경을 Azure Machine Learning 계산에서 재사용 하 고, 동일한 환경에서 모델을 배포할 수 있습니다.

다음은 동일한 환경 개체를 학습을 위한 실행 구성과 웹 서비스 배포에 대 한 유추 및 배포 구성에서 모두 사용할 수 있음을 보여 줍니다.

![Machine learning 워크플로의 환경 다이어그램](./media/concept-environments/ml-environment.png)

## <a name="types-of-environments"></a>환경 유형

환경은 **큐 레이트**, **사용자 관리** 및 **시스템 관리**등의 세 가지 범주로 나눌 수 있습니다.

큐 레이트 환경은 Azure Machine Learning에서 제공 하며 기본적으로 작업 영역에서 사용할 수 있습니다. 여기에는 다양 한 기계 학습 프레임 워크를 시작 하는 데 도움이 되는 Python 패키지 및 설정 컬렉션이 포함 되어 있습니다. 

사용자 관리 환경에서는 사용자가 사용자 환경을 설정하고 컴퓨팅 대상에 학습 스크립트에 필요한 모든 패키지를 설치해야 합니다. Conda에서 자동으로 환경을 확인하지도 않고 아무 것도 설치하지 않습니다. 사용자 고유의 환경을 정의 하는 경우 pip 종속성으로 버전 `>= 1.0.45`를 사용 하 여 `azureml-defaults`를 나열 해야 합니다. 이 패키지에는 웹 서비스로 모델을 호스트 하는 데 필요한 기능이 포함 되어 있습니다.

[Conda](https://conda.io/docs/) 에서 Python 환경 및 스크립트 종속성을 관리 하려는 경우 시스템 관리 환경이 사용 됩니다. 서비스는 수동으로 구성할 수 없는 원격 계산 대상의 유용성으로 인해 기본적으로 이러한 유형의 환경을 가정 합니다.

## <a name="creating-and-managing-environments"></a>환경 만들기 및 관리

다음을 통해 환경을 만들 수 있습니다.

* 큐 레이트 환경을 사용 하거나 고유한 종속성을 정의 하 여 새 `Environment` 개체 정의
* 작업 영역에서 기존 `Environment` 개체 사용 이렇게 하면 일관성과 재현 가능성을 종속성에 사용할 수 있습니다.
* 기존 Anaconda 환경 정의에서 가져오기.
* Azure Machine Learning CLI 사용

특정 코드 예제는 [방법을](how-to-use-environments.md#create-an-environment) 참조 하세요. 환경도 작업 영역을 통해 쉽게 관리할 수 있으며 다음 기능을 포함 합니다.

* 환경은 실험을 제출할 때 작업 영역에 자동으로 등록 됩니다. 수동으로 등록할 수도 있습니다.
* 작업 영역에서 환경을 가져오고 환경 정의를 학습, 배포 또는 편집 하는 데 사용 합니다.
* 버전 관리를 사용 하면 시간에 따라 환경에 대 한 변경 내용을 확인 하 고 재현 가능성를 보장할 수 있습니다.
* 사용자 환경에서 자동으로 Docker 이미지 빌드

방법에 대 한 자세한 내용은 코드 샘플에 대 한 [환경 관리](how-to-use-environments.md#manage-environments) 섹션을 참조 하세요.

## <a name="next-steps"></a>다음 단계

* Azure Machine Learning에서 [환경을 만들고 사용](how-to-use-environments.md) 하는 방법 알아보기
* [환경 클래스](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py)에 대 한 Python SDK 참조 문서를 참조 하세요.
* [환경](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments)에 대 한 R SDK 참조 문서를 참조 하세요.