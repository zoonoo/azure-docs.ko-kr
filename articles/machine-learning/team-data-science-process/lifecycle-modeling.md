---
title: "팀 데이터 과학 프로세스 수명 주기의 모델링 단계 - Azure | Microsoft Docs"
description: "데이터 과학 프로젝트의 모델링 단계에 대한 목표, 작업 및 결과물입니다."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/02/2017
ms.author: bradsev;
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 0c855faa96d7feb9f762ecaed7654669a5a8a5b7
ms.contentlocale: ko-kr
ms.lasthandoff: 09/25/2017

---
# <a name="modeling"></a>모델링

이 항목에서는 팀 데이터 과학 프로세스의 **모델링 단계**와 관련된 목표, 작업 및 결과물을 설명합니다. 이 프로세스는 데이터 과학 프로젝트를 구조화하는 데 사용할 수 있는 권장 수명 주기를 제공합니다. 수명 주기는 프로젝트에서 일반적으로, 종종 반복적으로 실행하는 주요 단계를 설명합니다.

* **비즈니스 이해**
* **데이터 취득 및 이해**
* **모델링**
* **배포웹사이트를**
* **고객 승인**

다음은 **팀 데이터 과학 프로세스 수명 주기**의 시각적 표현입니다. 

![TDSP-수명 주기2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>목표
* ML(기계 학습) 모델을 위한 최적의 데이터 기능
* 대상에 대해 가장 정확하게 예측하는 정보 제공용 ML 모델
* 프로덕션에 적합한 ML 모델

## <a name="how-to-do-it"></a>수행 방법
이 단계에서 설명하는 세 가지 기본 작업은 다음과 같습니다.

* **기능 엔지니어링**: 모델 학습을 용이하게 하기 위해 원시 데이터로부터 데이터 기능을 만듭니다.
* **모델 학습**: 성공 메트릭을 비교하여 질문에 가장 정확하게 대답하는 모델을 찾습니다.
* **프로덕션에 적합한** 모델인지 확인합니다.

### <a name="31-feature-engineering"></a>3.1 기능 엔지니어링
기능 엔지니어링은 원시 변수의 포함, 집계 및 변환을 포함하여 분석에 사용되는 기능을 만듭니다. 모델에 영향을 주는 요인들에 대한 통찰력을 원하면 기능이 서로 어떻게 관련되어 있고 기계 학습 알고리즘에서 이러한 기능을 어떻게 사용하는지를 이해해야 합니다. 이 단계에서는 도메인 전문 지식과 데이터 탐색 단계에서 획득한 통찰력을 창의적으로 결합해야 합니다. 이렇게 하면 관련이 없는 변수를 상당히 많이 제거하면서 유용한 정보를 제공하는 변수를 찾아 포함시킬 수 있습니다. 정보 제공 변수는 결과를 향상시키지만 관련이 없는 변수는 모델에 불필요한 혼란만 가져옵니다. 또한 점수 매기기 중에 가져온 새로운 데이터에 대해 이러한 기능을 생성해야 합니다. 따라서 이러한 기능은 점수를 매길 때 사용할 수 있는 데이터에만 기반하여 생성할 수 있습니다. 다양한 Azure 데이터 기술을 사용하는 경우 기능 엔지니어링에 대한 기술 지침은 [데이터 과학 프로세스의 기능 엔지니어링](create-features.md)을 참조하세요. 

### <a name="32-model-training"></a>3.2 모델 학습
대답하려는 질문 유형에 따라 다양한 모델링 알고리즘을 사용할 수 있습니다. 알고리즘 선택에 대한 지침은 [Microsoft Azure Machine Learning을 위한 알고리즘 선택 방법](../studio/algorithm-choice.md)을 참조하세요. 이 문서는 Azure Machine Learning을 위해 작성되었지만 제공되는 지침은 모든 기계 학습 프로젝트에 유용합니다. 

모델 학습 프로세스에는 다음 단계가 포함됩니다. 

* 모델링을 위해 학습 데이터 집합과 테스트 데이터 집합으로 **입력 데이터를 임의로 분할**합니다.
* 학습 데이터 집합을 사용하여 **모델을 빌드**합니다.
* 현재 데이터로 관심 있는 질문에 대답하는 데 관련된 다양한 튜닝 매개 변수(매개 변수 스윕이라고 함)와 함께 일련의 경쟁적인 컴퓨터 학습 알고리즘을 **평가**합니다(학습 및 테스트 데이터 집합).
* 대체 방법 간의 성공 메트릭을 비교하여 질문에 대답하는 **"최상"의 솔루션을 결정**합니다.

> [!NOTE]
> **유출 방지**: 모델 또는 기계 학습 알고리즘을 통해 비현실적으로 좋은 예측을 만들기 위해 학습 데이터 집합 외부에서 데이터를 포함하면 데이터 유출이 발생할 수 있습니다. 유출은 데이터 과학자들이 진실하기에는 너무 좋아 보이는 예측 결과를 가져올 때 불안해 하는 일반적 이유입니다. 이러한 종속성은 감지하기 어려울 수 있습니다. 유출을 방지하려면 종종 분석 데이터 집합을 빌드하고 모델을 만들어 정확성을 평가하는 과정을 반복해야 합니다. 
> 
> 

여러 알고리즘 및 매개 변수 스윕을 통해 실행하여 기본 모델을 생성할 수 있는 [자동화된 모델링 및 보고 도구](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/Modeling)에 TDSP를 제공합니다. 또한 각 모델의 성능과 변수 중요도를 포함한 매개 변수 조합을 요약한 기본 모델링 보고서를 생성합니다. 이 프로세스는 추가 기능 엔지니어링을 진행할 수 있으므로 반복적입니다. 

## <a name="artifacts"></a>아티팩트
이 단계에서 생성되는 아티팩트는 다음과 같습니다.

* [**기능 집합**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/Data%20Defintion.md#feature-sets): 모델링을 위해 개발된 기능은 **데이터 정의** 보고서의 **기능 집합** 섹션에 설명되어 있습니다. 여기에는 기능을 생성하는 코드에 대한 조언과 기능을 생성한 방법에 대한 설명이 있습니다.
* [**모델 보고서**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Model/Model%201/Model%20Report.md): 시도하는 각 모델에 대해 각 실험에 대한 세부 정보를 제공하는 템플릿 기반 표준 보고서가 생성됩니다.
* **검사점 의사 결정**: 모델이 프로덕션 시스템에 배포 할 수 있을 만큼 성능이 좋은지 평가합니다. 몇 가지 주요 질문은 다음과 같습니다.
  * 테스트 데이터가 제공되면 질문에 대해 모델에서 매우 신뢰성 있게 대답합니까? 
  * 다른 방법을 시도해야 합니다. 추가 데이터를 수집하고 더 많은 기능 엔지니어링을 수행하거나 다른 알고리즘으로 시험해 보겠습니까?

## <a name="next-steps"></a>다음 단계

팀 데이터 과학 프로세스의 수명 주기에서 각 단계에 대한 링크는 다음과 같습니다.

* [1. 비즈니스 이해](lifecycle-business-understanding.md)
* [2. 데이터 취득 및 이해](lifecycle-data.md)
* [3. 모델링](lifecycle-modeling.md)
* [4. 배포](lifecycle-deployment.md)
* [5. 고객 승인](lifecycle-acceptance.md)

**특정 시나리오** 에 대한 프로세스의 모든 단계를 보여 주는 종합적인 전체 연습도 제공됩니다. 이러한 단계들은 [예제 연습](walkthroughs.md) 항목에서 미리 보기 설명과 함께 나열되고 연결되어 있습니다. 이 연습에서는 클라우드 및 온-프레미스 도구와 서비스를 워크플로 또는 파이프라인에 결합하여 지능형 응용 프로그램을 만드는 방법을 보여 줍니다. 

Azure Machine Learning Studio를 사용하는 팀 데이터 과학 프로세스의 단계를 실행하는 예제는 [Azure ML을 사용하여](http://aka.ms/datascienceprocess) 학습 경로를 참조하세요. 
