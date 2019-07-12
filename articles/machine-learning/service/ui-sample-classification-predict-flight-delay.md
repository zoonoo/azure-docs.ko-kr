---
title: '분류: 비행 지연 예측'
titleSuffix: Azure Machine Learning service
description: 이 문서에서는 기계 학습 시각적 끌어서 놓기 인터페이스 및 사용자 지정 R 코드를 사용 하 여 비행 지연을 예측 하는 모델을 빌드하는 방법을 보여 줍니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 07/02/2019
ms.openlocfilehash: 773e55fe4b5ca5acf27ba1765e5a16075f625187
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607638"
---
# <a name="sample-6---classification-predict-flight-delays-using-r"></a>6-분류 샘플: R을 사용 하 여 비행 지연 예측

이 실험 기록 비행 및 날씨 데이터를 사용 하 여 예측할 경우 예정된 된 비행 15 분 이상 지연 됩니다.

이 문제는 두 개의 클래스-지연 예측 분류 문제 또는 시간에 접근할 수 있습니다. 분류자, 많은 수의 기록 비행 데이터의 예제를 사용 하 여이 모델을 빌드하려면.

최종 실험 그래프는 다음과 같습니다.

[![실험 그래프](media/ui-sample-classification-predict-flight-delay/experiment-graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. 선택 된 **열고** 샘플 6 실험에 대 한 단추:

    ![실험을 열으십시오](media/ui-sample-classification-predict-flight-delay/open-sample6.png)

## <a name="get-the-data"></a>데이터 가져오기

이 실험에서는 합니다 **비행 지연 데이터** 데이터 집합입니다. 미국에서 TranStats 데이터 컬렉션의 일부 Department of Transportation 합니다. 데이터 집합을 2013 년 10 월부터 5 월 비행 지연 정보를 포함합니다. 시각적 인터페이스에 데이터를 업로드 하기 전에 미리 처리 된 다음과 같습니다.

* 미국 본토에 70 사용자가 가장 많은 공항을 포함 하도록 필터링 합니다.
* 에 대 한 최대 15 분 지연으로 레이블 재지정 항공편을 취소 합니다.
* 우회 비행은 필터링 합니다.
* 14 개의 열을 선택 합니다.

비행 데이터를 보완 하는 **날씨 데이터 집합** 사용 됩니다. 날씨 데이터 NOAA에서 시간별 지상 기상 관측을 포함 하 고 공항 기상 관측소 같은 2013 년 4 월-10 월 기간을에서 관찰 한 결과 나타냅니다. Azure ML 시각적 인터페이스를 업로드 하기 전에 미리 처리 된 다음과 같습니다.

* 기상 관측소 Id는 해당 공항 Id에 매핑 했습니다.
* 70 사용자가 가장 많은 공항 연관 되지 않은 기상 관측소 제거 되었습니다.
* 날짜 열은 별도 열으로 분할 되었습니다. 연도, 월 및 일입니다.
* 26 개의 열을 선택 합니다.

## <a name="pre-process-the-data"></a>데이터 전처리

데이터 집합은 일반적으로 몇 가지 사전 처리를 분석할 수 있습니다 전에 필요 합니다.

![data-process](media/ui-sample-classification-predict-flight-delay/data-process.png)

### <a name="flight-data"></a>비행 데이터

열 **운송 업체**하십시오 **OriginAirportID**, 및 **DestAirportID** 정수로 저장 됩니다. 그러나 해당 정책은 범주 특성을 사용 합니다 **메타 데이터 편집** 변환 하기 위해 범주는 모듈입니다.

![메타 데이터 편집](media/ui-sample-classification-predict-flight-delay/edit-metadata.png)

다음을 사용 합니다 **열 선택** 가능한 대상 leakers 되는 데이터 집합 열에서 제외할 데이터 집합 모듈에: **DepDelay**, **DepDel15**를 **ArrDelay**를 **취소**를 **연도**합니다. 

시간별 날씨 레코드를 사용 하 여 비행 레코드에 조인 하려면 조인 키 중 하나로 예정 된 출발 시간을 사용 합니다. 조인을 위해 CSRDepTime 열 해야 내림 하 여 수행 되는 가장 가까운 시간으로는 **R 스크립트 실행** 모듈입니다. 

### <a name="weather-data"></a>날씨 데이터

사용 하 여 누락 값의 상당한 부분에 있는 열을 제외 합니다 **프로젝트 열** 모듈입니다. 이러한 열에는 문자열 값 열이 모두 포함: **ValueForWindCharacter**, **WetBulbFarenheit**합니다 **WetBulbCelsius**를 **PressureTendency**를 **PressureChange**, **SeaLevelPressure**, 및 **StationPressure**합니다.

합니다 **누락 데이터 정리** 모듈 데이터가 누락 된 행을 제거 하려면 나머지 열에 적용 됩니다.

날씨 관찰 시간이 가장 가까운 전체 시간으로 올림 됩니다. 예약 된 비행 시간과 날씨 관찰 시간 모델은 비행 시간 전에 날씨를 사용 하도록 서로 반대 방향으로 반올림 됩니다. 

날씨 데이터의 현지 시간으로 보고 되 면 이후 시간대 차이 예정 된 출발 시간 및 날씨 관찰 시간에서 표준 시간대 열을 뺀 값으로 고려 됩니다. 이러한 작업은 수행할 수를 사용 하 여 **R 스크립트 실행** 모듈.

### <a name="joining-datasets"></a>데이터 집합 조인

비행 레코드가 비행의 원본에서 날씨 데이터와 조인 됩니다 (**OriginAirportID**)를 사용 하는 **조인 데이터** 모듈입니다.

 ![원본에서 조인 비행 및 날씨](media/ui-sample-classification-predict-flight-delay/join-origin.png)


비행 레코드가 비행의 대상을 사용 하는 날씨 데이터와 조인 됩니다 (**DestAirportID**).

 ![비행 및 대상에서 날씨를 조인 합니다.](media/ui-sample-classification-predict-flight-delay/join-destination.png)

### <a name="preparing-training-and-test-samples"></a>학습 및 테스트 샘플이 준비

합니다 **분할 데이터** 학습용으로 년 9 월 레코드를 통해 4 월에 데이터를 분할 하는 모듈 및 테스트에 대 한 레코드 년 10 월입니다.

 ![분할 학습 및 테스트 데이터](media/ui-sample-classification-predict-flight-delay/split.png)

연도, 월 및 표준 시간대 열 열 선택 모듈을 사용 하 여 학습 데이터 집합에서 제거 됩니다.

## <a name="define-features"></a>기능 정의

기계 학습에서 기능에는 관심 사항에 대해 측정 가능한 개별 속성. 강력한 기능 집합을 찾기 실험 및 도메인 지식이 필요 합니다. 일부 기능은 다른 기능에 비해 대상 예측에 더 유용합니다. 또한 일부 기능은 다른 기능과 강력한 상관 관계가 있을 수 있습니다 하 고 모델에 새 정보를 추가 하지 않습니다. 이러한 기능을 제거할 수 있습니다.

모델을 작성, 사용 가능한 모든 기능을 사용할 수도 있고 기능의 하위 집합을 선택할 수 있습니다.

## <a name="choose-and-apply-a-learning-algorithm"></a>학습 알고리즘 선택 및 적용

사용 하 여 모델을 **2 클래스 로지스틱 회귀** 모듈 학습 데이터 집합에 대 한 학습 및 합니다. 

결과 **모델 학습** 모듈은 예측을 수행 하는 새 샘플의 점수를 사용할 수 있는 학습 된 분류 모델입니다. 학습된 된 모델에서 점수를 생성 하도록 설정 하는 테스트를 사용 합니다. 사용 하 여 합니다 **모델 평가** 모듈 분석 하 고 모델의 품질을 비교 합니다.

실험을 실행 한 후의 출력을 볼 수 있습니다 합니다 **모델 점수 매기기** 출력 포트를 클릭 하 고 선택 하 여 모듈 **시각화**합니다. 출력의 점수가 매겨진된 레이블 및 레이블의 확률을 포함합니다.

마지막으로 결과의 품질을 테스트 하려면 추가 합니다 **모델 평가** 모듈을 실험 캔버스 및 모델 점수 매기기 모듈의 출력을 왼쪽된 입력된 포트에 연결 합니다. 실험을 실행 하 고 출력을 볼 합니다 **모델 평가** 모듈의 출력 포트를 클릭 하 고 선택 **시각화**합니다.

## <a name="evaluate"></a>평가
로지스틱 회귀 모델 처럼 AUC는 0.631 테스트 설정에 있습니다.

 ![evaluate](media/ui-sample-classification-predict-flight-delay/evaluate.png)

## <a name="next-steps"></a>다음 단계

시각적 인터페이스에 사용할 수 있는 다른 샘플을 탐색 합니다.

- [1-회귀 샘플: 자동차의 가격 예측](ui-sample-regression-predict-automobile-price-basic.md)
- [2-회귀 샘플: 자동차 가격 예측 알고리즘 비교](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [3-분류 샘플: 신용 위험 예측](ui-sample-classification-predict-credit-risk-basic.md)
- [4-분류 샘플: (중요 한 비용) 신용 위험 예측](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [5-분류 샘플: 이탈 예측](ui-sample-classification-predict-churn.md)
