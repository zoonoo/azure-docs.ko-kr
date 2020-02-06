---
title: 'Designer: 분류, 소득 예측 예제'
titleSuffix: Azure Machine Learning
description: 이 예제에 따라 코드 없이 Azure Machine Learning 디자이너를 사용하여 소득을 예측하는 식별자를 빌드해 보세요.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: 560339fb04e3bbbe42c4370655e74e8536a7c015
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963375"
---
# <a name="build-a-classifier--use-feature-selection-to-predict-income-with-azure-machine-learning-designer"></a>Azure Machine Learning 디자이너를 사용하여 분류자를 빌드하고 소득을 예측하는 기능 선택 사용

**디자이너(미리 보기) 샘플 3**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

코드를 한 줄도 작성하지 않고 디자이너(미리 보기)를 사용하여 기계 학습 분류자를 빌드하는 방법에 대해 알아봅니다. 이 샘플은 성인 소득을 예측하도록(50K 이상 또는 50K 이하) **2클래스 향상된 의사 결정 트리**를 학습시킵니다.

"어떤 것?"이라는 질문의 답을 찾아야 하므로 이를 분류 문제라고 부릅니다. 하지만 동일한 기본 프로세스를 적용하여 회귀, 분류, 클러스터링 등 모든 유형의 기계 학습 문제를 해결할 수 있습니다.

다음은 이 샘플의 최종 파이프라인 그래프입니다.

![파이프라인 그래프](./media/how-to-designer-sample-classification-predict-income/overall-graph.png)

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. 샘플 3을 클릭하여 엽니다.



## <a name="data"></a>데이터

이 데이터 세트에는 14개 기능과 레이블 열 1개가 포함되어 있습니다. 숫자 및 범주를 비롯한 여러 가지 유형의 기능이 있습니다. 다음 다이어그램은 데이터 세트에서 발췌한 ![데이터](media/how-to-designer-sample-classification-predict-income/sample3-dataset-1225.png)를 보여줍니다.



## <a name="pipeline-summary"></a>파이프라인 요약

다음 단계에 따라 파이프라인을 만듭니다.

1. 성인 소득 이진 데이터 세트 모듈을 파이프라인 캔버스로 끕니다.
1. **데이터 분할** 모듈을 추가하여 학습 및 테스트 세트를 만듭니다. 첫 번째 출력 데이터 세트의 행 분수를 0.7로 설정합니다. 이렇게 설정하면 데이터의 70%는 모듈의 왼쪽 포트에, 나머지는 오른쪽 포트에 출력되도록 지정됩니다. 여기서는 학습에 왼쪽 데이터 세트를, 테스트에 오른쪽 데이터 세트를 사용합니다.
1. **필터 기반 기능 선택** 모듈을 추가하여 PearsonCorreclation에서 5개 기능을 선택합니다. 
1. **2클래스 향상된 의사 결정 트리** 모듈을 추가하여 향상된 의사 결정 트리 분류자를 초기화합니다.
1. **모델 학습** 모듈을 추가합니다. 이전 단계의 분류자를 **모델 학습**의 왼쪽 입력 포트에 연결합니다. 필터 기반 기능 선택 모듈에서 필터링된 데이터 세트를 학습 데이터 세트로 연결합니다.  그러면 **모델 학습**에서 분류자를 학습시킬 것입니다.
1. 열 변환 선택 및 변환 적용 모듈을 추가하여 테스트 데이터 세트에 동일한 변환(필터 기반 기능 선택)을 적용합니다.
![적용-변환](./media/how-to-designer-sample-classification-predict-income/transformation.png)
1. **모델 채점** 모듈을 추가하고 이 모듈에 **모델 학습** 모듈을 연결합니다. 그런 다음, 테스트 세트(테스트 세트에도 기능 선택을 적용하는 변환 적용 모듈의 출력)를 **모델 채점** 모듈에 추가합니다. **모델 채점** 모듈은 예측을 수행합니다. 이 모듈의 출력 포트를 선택하여 예측 및 긍정적 클래스 확률을 볼 수 있습니다.


    이 파이프라인에는 두 개의 채점 모듈이 있는데, 오른쪽 모듈은 예측을 수행하기 전에 레이블 열을 제외했습니다. 이는 웹 서비스 입력에 레이블이 아닌 기능만 필요하기 때문에 실시간 엔드포인트를 배포하기 위한 준비 과정입니다. 

1. **모델 평가** 모듈을 추가하고 채점된 데이터 세트를 왼쪽 입력 포트에 연결합니다. 평가 결과를 보려면 **모델 평가** 모듈의 출력 포트를 선택하고 **시각화**를 선택합니다.

## <a name="results"></a>결과

![결과 평가](media/how-to-designer-sample-classification-predict-income/sample3-evaluate-1225.png)

평가 결과에서 ROC, 정밀도-리콜, 혼동 메트릭 등의 곡선을 볼 수 있습니다. 

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>다음 단계

디자이너에 사용할 수 있는 다른 샘플을 살펴봅니다.

- [샘플 1 - 회귀: 자동차 가격 예측](how-to-designer-sample-regression-automobile-price-basic.md)
- [샘플 2 - 회귀: 자동차 가격 예측 알고리즘에 대한 비교](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [샘플 4 - 분류: 신용 위험 예측(비용 구분)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [샘플 5 - 분류: 이탈 예측](how-to-designer-sample-classification-churn.md)
- [샘플 6 - 분류: 항공편 지연 예측](how-to-designer-sample-classification-flight-delay.md)
- [샘플 7 - 텍스트 분류: Wikipedia SP 500 데이터 세트](how-to-designer-sample-text-classification.md)
