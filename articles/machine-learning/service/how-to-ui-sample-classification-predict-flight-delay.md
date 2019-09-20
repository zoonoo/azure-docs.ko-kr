---
title: '시각적 인터페이스 예제 #6: 비행 지연 예측을 위한 분류'
titleSuffix: Azure Machine Learning
description: 이 문서에서는 끌어서 놓기 시각적 인터페이스와 사용자 지정 R 코드를 사용 하 여 비행 지연을 예측 하는 기계 학습 모델을 빌드하는 방법을 보여 줍니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 07/02/2019
ms.openlocfilehash: 257f6034df7d1974f3964c4d07ca96d17c7fe509
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71131650"
---
# <a name="sample-6---classification-predict-flight-delays-using-r"></a>샘플 6-분류: R을 사용 하 여 비행 지연 예측

이 실험에서는 기록 항공편 및 날씨 데이터를 사용 하 여 예약 된 승객 비행이 15 분 넘게 지연 될 지 여부를 예측 합니다.

이 문제는 분류 문제로 인 한 것 이며, 두 클래스 (지연 또는 시간)를 예측할 수 있습니다. 분류자를 빌드하기 위해이 모델에서는 기록 비행 데이터의 많은 예제를 사용 합니다.

이 샘플에 대 한 최종 실험 그래프는 다음과 같습니다.

[![실험 그래프](media/how-to-ui-sample-classification-predict-flight-delay/experiment-graph.png)](media/how-to-ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. 샘플 6 실험의 **열기** 단추를 선택 합니다.

    ![실험 열기](media/how-to-ui-sample-classification-predict-flight-delay/open-sample6.png)

## <a name="get-the-data"></a>데이터 가져오기

이 실험에서는 **비행 지연 데이터** 데이터 집합을 사용 합니다. 미국에서의 전 통계 데이터 수집의 일부입니다. 교통 부서. 데이터 집합은 4 월부터 10 월 2013 까지의 비행 지연 정보를 포함 합니다. 시각적 인터페이스에 데이터를 업로드 하기 전에 다음과 같이 미리 처리 되었습니다.

* 본토 미국에서 70의 가장 많은 공항을 포함 하도록 필터링 되었습니다.
* 취소 된 항공편의 경우 15 분 넘게 지연 된 것으로 레이블 재지정.
* 필터링 된 항공편.
* 14 개의 열을 선택 했습니다.

비행 데이터를 보완 하기 위해 **날씨 데이터 집합이** 사용 됩니다. 날씨 데이터에는 NOAA의 시간별 육지 기반 날씨 관측값이 포함 되어 있으며, 공항 날씨 스테이션의 관찰을 나타내며, 2013 년 4 월의 동일한 기간을 포함 합니다. Azure ML 시각적 인터페이스에 업로드 하기 전에 다음과 같이 미리 처리 되었습니다.

* 날씨 스테이션 Id는 해당 공항 Id에 매핑됩니다.
* 70의 가장 많은 공항에 연결 되지 않은 날씨 스테이션이 제거 되었습니다.
* 날짜 열이 개별 열로 분할 되었습니다. 년, 월, 일을 말합니다.
* 26 개의 열을 선택 했습니다.

## <a name="pre-process-the-data"></a>데이터를 미리 처리 합니다.

일반적으로 데이터 집합을 분석 하려면 먼저 일부 전처리를 수행 해야 합니다.

![데이터-프로세스](media/how-to-ui-sample-classification-predict-flight-delay/data-process.png)

### <a name="flight-data"></a>비행 데이터

**OriginAirportID**및 **DestAirportID** **열은**정수로 저장 됩니다. 그러나 범주 특성 이므로 **메타 데이터 편집** 모듈을 사용 하 여 범주를 범주로 변환 합니다.

![편집-메타 데이터](media/how-to-ui-sample-classification-predict-flight-delay/edit-metadata.png)

그런 다음 데이터 집합에서 **열 선택** 모듈을 사용 하 여 대상 leakers 가능한 데이터 집합 열에서 제외 합니다. **DepDel15**, **arrdelay**, **Canceled**, **Year**. 

비행 레코드를 시간별 날씨 레코드와 조인 하려면 예약 된 출발 시간을 조인 키 중 하나로 사용 합니다. 조인을 수행 하려면 CSRDepTime 열을 **R 스크립트 실행** 모듈에서 수행 하는 가장 가까운 시간으로 내림 해야 합니다. 

### <a name="weather-data"></a>날씨 데이터

누락 값이 많은 열은 **프로젝트 열** 모듈을 사용 하 여 제외 됩니다. 이러한 열에는 모든 문자열 값 열이 포함 됩니다. **ValueForWindCharacter**, **WetBulbFarenheit**, **wetbulbcelsius**, **PressureTendency**, **PressureChange**, **SeaLevelPressure**및 **StationPressure**입니다.

그런 다음 **누락 된 데이터 정리** 모듈을 나머지 열에 적용 하 여 데이터가 누락 된 행을 제거 합니다.

날씨 관찰 시간은 가장 가까운 전체 시간으로 반올림 됩니다. 예정 된 비행 시간 및 날씨 관찰 시간은 모델이 비행 시간 이전에 날씨만 사용 하도록 반대 방향으로 반올림 됩니다. 

날씨 데이터는 현지 시간에 보고 되므로 예약 된 출발 시간 및 날씨 관찰 시간에서 표준 시간대 열을 빼서 표준 시간대 차이를 고려 합니다. 이러한 작업은 **R 스크립트 실행** 모듈을 사용 하 여 수행 됩니다.

### <a name="joining-datasets"></a>데이터 집합 조인

비행 레코드는 **데이터 조인** 모듈을 사용 하 여 비행 (**OriginAirportID**)의 원본에서 날씨 데이터와 조인 됩니다.

 ![원본에의 한 비행 및 날씨 참여](media/how-to-ui-sample-classification-predict-flight-delay/join-origin.png)


비행 레코드는 항공편 (**DestAirportID**)의 대상을 사용 하 여 날씨 데이터와 조인 됩니다.

 ![목적지 별 비행 및 날씨 조인](media/how-to-ui-sample-classification-predict-flight-delay/join-destination.png)

### <a name="preparing-training-and-test-samples"></a>학습 및 테스트 샘플 준비

**데이터 분할** 모듈은 교육을 위해 4 월부터 9 월 레코드, 테스트의 경우 10 월 레코드까지 데이터를 분할 합니다.

 ![학습 및 테스트 데이터 분할](media/how-to-ui-sample-classification-predict-flight-delay/split.png)

Year, month 및 timezone 열은 열 선택 모듈을 사용 하 여 학습 데이터 집합에서 제거 됩니다.

## <a name="define-features"></a>기능 정의

기계 학습에서 기능은 관심 있는 항목의 개별 측정 가능한 속성입니다. 강력한 기능 집합을 찾으려면 실험 및 도메인 지식이 필요 합니다. 일부 기능은 다른 기능에 비해 대상 예측에 더 유용합니다. 또한 일부 기능은 다른 기능과 강력한 상관 관계를 가질 수 있으며 모델에 새 정보를 추가 하지 않습니다. 이러한 기능은 제거할 수 있습니다.

모델을 작성 하려면 사용 가능한 모든 기능을 사용 하거나 기능 하위 집합을 선택할 수 있습니다.

## <a name="choose-and-apply-a-learning-algorithm"></a>학습 알고리즘 선택 및 적용

**2 클래스 로지스틱 회귀** 모듈을 사용 하 여 모델을 만들고 학습 데이터 집합에 대해 학습 합니다. 

**모델 학습** 모듈의 결과는 새 샘플의 점수를 매기고 예측을 만드는 데 사용할 수 있는 학습 된 분류 모델입니다. 테스트 집합을 사용 하 여 학습 된 모델에서 점수를 생성 합니다. 그런 다음 **모델 평가** 모듈을 사용 하 여 모델의 품질을 분석 하 고 비교 합니다.

실험을 실행 한 후 출력 포트를 클릭 하 고 **시각화**를 선택 하 여 **모델 점수 매기기** 모듈의 출력을 볼 수 있습니다. 출력에는 점수가 매겨진 레이블과 레이블의 확률이 포함 됩니다.

마지막으로 결과의 품질을 테스트 하려면 실험 캔버스에 **모델 평가** 모듈을 추가 하 고 왼쪽 입력 포트를 모델 점수 매기기 모듈의 출력에 연결 합니다. 출력 포트를 클릭 하 고 **시각화**를 선택 하 여 실험을 실행 하 고 **모델 평가** 모듈의 출력을 확인 합니다.

## <a name="evaluate"></a>평가
로지스틱 회귀 모델의 테스트 집합에는 0.631의 요소가 있습니다.

 ![평가](media/how-to-ui-sample-classification-predict-flight-delay/evaluate.png)

## <a name="next-steps"></a>다음 단계

시각적 인터페이스에 사용할 수 있는 다른 샘플을 탐색 합니다.

- [샘플 1-회귀: 자동차 가격 예측](how-to-ui-sample-regression-predict-automobile-price-basic.md)
- [샘플 2-회귀: 자동차 가격 예측에 대 한 알고리즘 비교](how-to-ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [샘플 3-분류: 신용 위험 예측](how-to-ui-sample-classification-predict-credit-risk-basic.md)
- [샘플 4-분류: 신용 위험 예측 (비용 구분)](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [샘플 5-분류: 변동 예측](how-to-ui-sample-classification-predict-churn.md)
