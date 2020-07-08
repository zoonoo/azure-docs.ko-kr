---
title: 데이터 과학의 기능 엔지니어링 - Team Data Science Process
description: 기계 학습의 데이터 향상 프로세스에서 기능 엔지니어링 및 해당 역할에 대해 알아봅니다.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: conceptual
ms.date: 05/14/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath, contperfq4
ms.openlocfilehash: c31cf0e5c655f53e8838c92f5463d3a85c2f6f65
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836824"
---
# <a name="feature-engineering-in-data-science"></a>데이터 과학의 기능 엔지니어링

이 문서에서는 기계 학습에서 데이터를 향상시키는 기능 엔지니어링 및 해당 역할에 대해 알아봅니다. [Azure Machine Learning Studio(클래식)](../studio/what-is-ml-studio.md) 실험에서 얻은 설명 예제에 대해 알아보세요. 

* **기능 엔지니어링**: 원시 데이터에서 새로운 기능을 만들어 학습 알고리즘의 예측 능력을 높이는 프로세스입니다. 엔지니어링된 기능은 원래 기능 집합에서 쉽게 드러나지 않는 추가 정보를 캡처해야 합니다.
* **기능 선택**: 이 프로세스에서는 학습 문제의 차원 수를 줄이기 위해 기능의 주요 하위 집합을 선택합니다.

일반적으로 추가 기능을 생성하기 위해 **기능 엔지니어링**을 먼저 적용한 다음, 관련이 없는 중복 기능이나 고도로 상관된 기능을 제거하기 위해 **기능 선택**이 실행됩니다.

기능 엔지니어링 및 선택은 TDSP(Team Data Science Process)의 [모델링 단계](lifecycle-modeling.md)의 일부입니다. TDSP 및 데이터 과학 수명 주기에 대한 자세한 내용은 [TDSP란 무엇입니까?](overview.md)를 참조하세요.

## <a name="what-is-feature-engineering"></a>기능 엔지니어링이란 무엇입니까?

학습 데이터는 행과 열로 이루어진 매트릭스로 구성됩니다. 매트릭스의 각 행은 관찰 또는 레코드입니다. 각 행의 열은 각 레코드를 설명하는 기능입니다. 실험 디자인에 지정된 기능은 데이터에서 패턴의 특징을 나타내야 합니다.

많은 원시 데이터 필드를 직접 사용하여 모델을 학습시킬 수 있지만, 향상된 학습 데이터 세트에 대한 추가(엔지니어링된) 기능을 만들어야 하는 경우가 많습니다.

학습을 향상시키는 엔지니어링된 기능에서는 데이터에서 패턴을 더욱 잘 구분할 수 있는 정보를 제공합니다. 그러나 이 프로세스는 정교한 작업입니다. 안정되고 생산성이 있는 결정을 내리려면 도메인에 대한 전문 지식이 필요한 경우가 많습니다.

## <a name="example-1-add-temporal-features-for-a-regression-model"></a>예제 1: 회귀 모델을 위해 temporal 기능 추가

Azure Machine Learning Studio(클래식)의 [자전거 수요 예측](https://gallery.azure.ai/Experiment/Regression-Demand-estimation-4) 실험을 사용하여 회귀 작업을 위해 기능을 엔지니어링하는 방법을 설명해 보겠습니다. 이 실험의 목표는 특정 월/일/시간에 자전거 대여 수요를 예측하는 것입니다.

### <a name="bike-rental-dataset"></a>자전거 임대 데이터 세트

[자전거 임대 UCI 데이터 세트](http://archive.ics.uci.edu/ml/datasets/Bike+Sharing+Dataset/)는 미국에 기반한 자전거 공유 회사의 실제 데이터를 기반으로 합니다. 이 데이터 세트에는 2011년과 2012년의 특정 시간 내의 자전거 대여 수를 표시합니다. 여기에는 17,379개의 행과 17개의 열이 포함됩니다.

원시 기능 집합에는 날씨 조건(온도/습도/풍속) 및 날의 유형(휴일/주중)이 포함되어 있습니다. 예측되는 필드는 개수로, 특정 시간 내의 자전거 대여 수를 나타내는 수입니다. 개수의 범위는 1에서 977까지입니다.

### <a name="create-a-feature-engineering-experiment"></a>기능 엔지니어링 실험 만들기

학습 데이터에 효율적인 기능을 생성한다는 목표를 갖고, 알고리즘은 동일하지만 네 개의 서로 다른 학습 데이터 세트가 있는 네 개의 회귀 모델을 빌드합니다. 네 개의 데이터 세트에서는 동일한 원시 입력 데이터를 표시하지만 기능 집합의 수는 증가합니다. 이러한 기능은 다음 네 가지 범주로 그룹화됩니다.

1. A = 예측 날에 대한 날씨 + 휴일 + 주중 + 주말 기능
2. B = 지난 12시간마다 대여된 자전거 대수
3. C= 지난 12일마다 같은 시간에 대여된 자전거 대수
4. D = 지난 12주마다 같은 시간, 같은 요일에 대여된 자전거 대수

이미 원래 원시 데이터에 있던 기능 집합 A를 제외하고 나머지 세 개의 기능 집합은 기능 엔지니어링 프로세스를 통해 생성됩니다. 기능 집합 B에서는 자전거의 최신 수요를 파악합니다. 기능 집합 C에서는 특정 시간의 자전거 수요를 파악합니다. 기능 집합 D에서는 특정 시간 및 특정 요일에 자전거에 대한 수요를 파악합니다. 네 개의 학습 데이터 세트 각각에는 A, A+B, A+B+C 및 A+B+C+D가 포함되어 있습니다.

### <a name="feature-engineering-using-studio-classic"></a>Studio(클래식)를 사용하는 기능 엔지니어링

Studio(클래식) 실험에서는 사전 처리된 입력 데이터 세트에서 4개의 분기를 통해 이러한 4개의 학습 데이터 세트를 구성합니다. 가장 왼쪽 분기를 제외한 각 분기에는 [R 스크립트 실행](https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/) 모듈이 포함되어 있습니다. 이 모듈에는 파생 기능(기능 집합 B, C 및 D)이 구성되어 있고, 가져온 데이터 세트에 추가되어 있습니다.

다음 그림에서는 두 번째 왼쪽 분기에서 기능 집합 B를 생성하는 데 사용하는 R 스크립트를 보여줍니다.

![기능 만들기](./media/create-features/addFeature-Rscripts.png)

### <a name="results"></a>결과

네 가지 모델의 성능 결과를 비교한 내용이 다음 표에 요약되어 있습니다. 

![결과 비교](./media/create-features/result1.png)

최상의 결과는 A+B+C 기능으로 표시됩니다. 학습 데이터에 추가 기능 집합이 포함되면 오류 비율이 감소합니다. 따라서 기능 집합 B와 C에서 회귀 작업을 위한 추가 관련 정보를 제공한다는 가정이 검증됩니다. 그러나 D 기능은 오류 비율을 추가적으로 감소시키지 않는 것으로 보입니다.

## <a name="example-2-create-features-for-text-mining"></a><a name="example2"></a> 예제 2: 텍스트 마이닝용 기능 만들기

기능 엔지니어링은 문서 분류 및 감성 분석 등의 텍스트 마이닝 관련 작업에 광범위하게 적용됩니다. 개별 원시 텍스트가 일반적으로 입력 데이터로 제공되므로, 단어/문구 빈도와 관련된 기능을 생성하려면 기능 엔지니어링 프로세스가 필요합니다.

### <a name="feature-hashing"></a>기능 해싱

이 작업을 수행하기 위해 [기능 해싱](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/feature-hashing)이라는 기술을 적용하여 임의의 텍스트 기능을 인덱스로 전환합니다. 각 텍스트 기능(단어/문구)을 특정 인덱스에 연관시키는 대신, 이 메서드에서는 해시 함수를 기능에 적용하고 해시 값을 인덱스로 직접 사용합니다.

Studio(클래식)에는 단어/문구 기능을 편리하게 생성하는 [기능 해싱](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/feature-hashing) 모듈이 있습니다. 다음 그림에서는 이 모듈을 사용하는 예를 보여줍니다. 입력 데이터 세트에는 두 개의 열, 즉 1 ~ 5의 서적 등급과 실제 검토 내용이 들어 있습니다. 이 모듈의 목표는 특정 서적 검토에서 해당하는 단어/문구의 발생 빈도를 표시하는 여러 새 기능을 검색하는 것입니다. 이 모듈을 사용하려면 다음 단계를 완료합니다.

* 먼저 입력 텍스트를 포함하는 열을 선택합니다(이 예에서는 "Col2").
* 둘째, "Hashing bitsize"를 8로 설정합니다. 즉, 2^8=256개의 기능이 생성됩니다. 모든 텍스트의 단어/문구가 256개의 인덱스로 해시됩니다. "Hashing bitsize" 매개 변수의 범위는 1 ~ 31입니다. 이 변수를 더 큰 수로 설정하면 단어/문구가 동일한 인덱스에 해시될 가능성이 적습니다.
* 셋째, "N-grams" 매개 변수를 2로 설정합니다. 이 값은 입력 텍스트에서 유니그램(단일 단어마다 하나의 기능) 및 바이그램(인접한 단어 쌍마다 하나의 기능) 발생 빈도를 가져옵니다. 매개 변수 "N-grams”의 범위는 0 ~ 10입니다. 즉, 순차 단어의 최대 수가 기능에 포함됩니다.  

!["기능 해싱"모듈](./media/create-features/feature-Hashing1.png)

다음 그림에서는 이러한 새 기능이 어떻게 표시되는지 보여 줍니다.

!["기능 해싱" 예](./media/create-features/feature-Hashing2.png)

## <a name="conclusion"></a>결론
기능을 엔지니어링하고 선택하면 데이터에 포함된 주요 정보를 추출하려고 하는 학습 프로세스의 효율성이 증가됩니다. 이러한 모델이 입력 데이터를 정확하게 분류하고 원하는 결과를 더욱 안정적으로 예측하는 기능도 향상됩니다.

컴퓨터를 통해 학습을 다루기가 더욱 쉽도록 기능 엔지니어링 및 선택을 결합할 수도 있습니다. 이 작업은 모델을 보정하거나 학습하는 데 필요한 기능을 향상시키고 해당 수를 줄여 수행합니다. 수학적으로 선택한 기능은 데이터의 패턴을 설명하고 결과를 성공적으로 예측하는 최소한의 독립 변수 집합입니다.

기능 엔지니어링이나 기능 선택을 반드시 항상 수행할 필요는 없습니다. 이것은 데이터, 선택한 알고리즘 및 실험 목적에 따라 달라집니다.

## <a name="next-steps"></a>다음 단계

특정 환경의 데이터에 대한 기능을 만들려면 다음 문서를 참조하세요.

* [SQL Server의 데이터에 대한 기능 만들기](create-features-sql-server.md)
* [Hive 쿼리를 사용하여 Hadoop 클러스터의 데이터에 대한 기능 만들기](create-features-hive.md)