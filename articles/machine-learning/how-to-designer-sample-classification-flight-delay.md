---
title: 'Designer: 비행 지연 예측 예제'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 디자이너를 사용하여 분류자를 빌드하고 사용자 지정 R 코드를 통해 비행 지연을 예측합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: d459350572d68cc5dcbbfd56933483404b94f918
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963280"
---
# <a name="build-a-classifier--use-r-to-predict-flight-delays-with-azure-machine-learning-designer"></a>Azure Machine Learning 디자이너를 사용하여 분류자 빌드 및 R을 통해 비행 지연 예측

**디자이너(미리 보기) 샘플 6**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

이 파이프라인은 과거 비행과 날씨 데이터를 사용하여 예정된 승객 비행이 15분 넘게 지연될지 여부를 예측합니다. 이 문제는 분류 문제로 접근하여 지연 또는 정시의 두 가지 클래스를 예측할 수 있습니다.

이 샘플에 대한 최종 파이프라인 그래프는 다음과 같습니다.

[![파이프라인 그래프](media/how-to-designer-sample-classification-flight-delay/pipeline-graph.png)](media/how-to-designer-sample-classification-flight-delay/pipeline-graph.png#lightbox)

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. 샘플 6을 클릭하여 엽니다.

## <a name="get-the-data"></a>데이터 가져오기

이 샘플에서는 **비행 지연 데이터** 데이터 세트를 사용합니다. 이는 미국 교통부에서 제공하는 TranStats 데이터 수집의 일부입니다. 데이터 세트에는 2013년 4월부터 10월까지의 비행 지연 정보가 포함되어 있습니다. 이 데이터 세트는 다음과 같이 전처리되었습니다.

* 미국 대륙에서 가장 바쁜 70개의 공항을 포함하도록 필터링되었습니다.
* 취소된 항공편을 15분 넘게 지연되면 취소된 항공편의 레이블이 다시 지정되었습니다.
* 우회된 비행이 필터링되었습니다.
* 14개의 열이 선택되었습니다.

항공편 데이터를 보완하기 위해 **날씨 데이터 세트**가 사용됩니다. 날씨 데이터에는 NOAA의 시간별 육지 기반 날씨 관측값이 포함되며, 항공편 데이터 세트와 동일한 기간을 포함하는 공항 날씨 관측소의 관측값을 나타냅니다. 다음과 같이 전처리되었습니다.

* 날씨 관측소 ID가 해당 공항 ID에 매핑되었습니다.
* 가장 분주한 70개의 공항과 관련이 없는 날씨 관측소가 제거되었습니다.
* 날짜 열은 별도의 연도, 월 및 일 열로 분할되었습니다.
* 26개의 열이 선택되었습니다.

## <a name="pre-process-the-data"></a>데이터 전처리

데이터 세트는 일반적으로 먼저 전처리를 거친 후에 분석해야 합니다.

![데이터 프로세스](./media/how-to-designer-sample-classification-flight-delay/data-process.png)

### <a name="flight-data"></a>항공편 데이터

**Carrier**, **OriginAirportID** 및 **DestAirportID** 열은 정수로 저장됩니다. 그러나 이는 범주별 특성이므로 **메타데이터 편집** 모듈을 사용하여 범주별로 변환합니다.

![메타데이터 편집](./media/how-to-designer-sample-classification-flight-delay/edit-metadata.png)

그런 다음, 데이터 세트 모듈에서 **열 선택**을 사용하여 데이터 세트 열에서 가능한 대상 누출자인 **DepDelay**, **DepDel15**, **ArrDelay**, **Canceled**, **Year** 열을 제외합니다. 

항공편 레코드를 시간별 날씨 레코드와 조인하려면 예정된 출발 시간을 조인 키 중 하나로 사용합니다. 조인을 수행하려면 CSRDepTime 열을 가장 가까운 시간으로 내림해야 합니다. 이 작업은 **R 스크립트 실행** 모듈에서 수행됩니다. 

### <a name="weather-data"></a>날씨 데이터

누락된 값의 비율이 큰 열은 **프로젝트 열** 모듈을 사용하여 제외됩니다. 모든 문자열 값 열이 포함되는 열은 **ValueForWindCharacter**, **WetBulbFarenheit**, **WetBulbCelsius**, **PressureTendency**, **PressureChange**, **SeaLevelPressure** 및 **StationPressure**입니다.

그런 다음, **누락된 데이터 정리** 모듈이 나머지 열에 적용되어 누락된 데이터가 있는 행을 제거합니다.

날씨 관측 시간은 가장 가까운 전체 시간으로 반올림됩니다. 예정된 비행 시간과 날씨 관측 시간은 반대 방향으로 반올림하여 모델이 비행 시간 이전의 날씨만 사용할 수 있도록 합니다. 

날씨 데이터는 현지 시간으로 보고되므로 표준 시간대 차이는 예정된 출발 시간 및 날씨 관측 시간에서 표준 시간대 열을 빼서 계산됩니다. 이러한 작업은 **R 스크립트 실행** 모듈을 사용하여 수행됩니다.

### <a name="joining-datasets"></a>데이터 세트 조인

항공편 레코드는 **데이터 조인** 모듈을 사용하여 항공편 출발지(**OriginAirportID**)의 날씨 데이터와 조인됩니다.

 ![출발지별 항공편 및 날씨 조인](./media/how-to-designer-sample-classification-flight-delay/join-origin.png)


항공편 레코드는 항공편 도착지(**DestAirportID**)를 사용하여 날씨 데이터와 조인됩니다.

 ![도착지별 항공편 및 날씨 조인](./media/how-to-designer-sample-classification-flight-delay/join-destination.png)

### <a name="preparing-training-and-test-samples"></a>학습 및 테스트 샘플 준비

**데이터 분할** 모듈은 데이터를 학습용으로 4월부터 9월까지의 레코드와 테스트용으로 10월 레코드로 분할합니다.

 ![학습 및 테스트 데이터 분할](./media/how-to-designer-sample-classification-flight-delay/split.png)

열 선택 모듈을 사용하여 학습 데이터 세트에서 연도, 월 및 표준 시간대 열이 제거됩니다.

## <a name="define-features"></a>기능 정의

기계 학습에서 기능은 관심 있는 항목에 대한 측정 가능한 개별 속성입니다. 강력한 기능 세트를 찾으려면 실험 및 도메인 지식이 필요합니다. 일부 기능은 다른 기능에 비해 대상 예측에 더 유용합니다. 또한 일부 기능은 다른 기능과 강력한 상관 관계를 가질 수 있으며 새 정보를 모델에 추가하지 않습니다. 이러한 기능은 제거할 수 있습니다.

모델을 작성하기 위해 사용 가능한 모든 기능을 사용하거나 기능의 하위 세트를 선택할 수 있습니다.

## <a name="choose-and-apply-a-learning-algorithm"></a>학습 알고리즘 선택 및 적용

**2클래스 로지스틱 회귀** 모듈을 사용하여 모델을 만들고 학습 데이터 세트에서 이를 학습시킵니다. 

**모델 학습** 모듈의 결과는 예측을 위해 새 샘플을 채점하는 데 사용할 수 있는 학습된 분류 모델입니다. 테스트 세트를 사용하여 학습된 모델에서 점수를 생성합니다. 그런 다음, **모델 평가** 모듈을 사용하여 모델의 품질을 분석하고 비교합니다.
파이프라인이 실행되면 출력 포트를 클릭하고 **시각화**를 선택하여 **모델 점수 매기기** 모듈의 출력을 볼 수 있습니다. 출력에는 점수가 매겨진 레이블과 해당 레이블의 확률이 포함됩니다.

마지막으로, 결과의 품질을 테스트하려면 **모델 평가** 모듈을 파이프라인 캔버스에 추가하고 왼쪽 입력 포트를 스코어 모델 점수 매기기 모듈의 출력에 연결합니다. 파이프라인을 실행하고, 출력 포트를 클릭하고 **시각화**를 선택하여 **모델 평가** 모듈의 출력을 확인합니다.

## <a name="evaluate"></a>평가
로지스틱 회귀 모델의 테스트 세트에서 AUC는 0.631입니다.

 ![평가](media/how-to-designer-sample-classification-flight-delay/sample6-evaluate-1225.png)

## <a name="next-steps"></a>다음 단계

디자이너에 사용할 수 있는 다른 샘플을 살펴봅니다.

- [샘플 1 - 회귀: 자동차 가격 예측](how-to-designer-sample-regression-automobile-price-basic.md)
- [샘플 2 - 회귀: 자동차 가격 예측 알고리즘에 대한 비교](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [샘플 3 - 기능 선택을 사용하여 분류: 수입 예측](how-to-designer-sample-classification-predict-income.md)
- [샘플 4 - 분류: 신용 위험 예측(비용 구분)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [샘플 5 - 분류: 이탈 예측](how-to-designer-sample-classification-churn.md)
- [샘플 7 - 텍스트 분류: Wikipedia SP 500 데이터 세트](how-to-designer-sample-text-classification.md)