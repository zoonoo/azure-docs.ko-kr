---
title: '디자이너: 비행 지연 예측 예제'
titleSuffix: Azure Machine Learning
description: 분류자를 빌드하고 사용자 지정 R 코드를 사용 하 여 Azure Machine Learning designer로 비행 지연을 예측 합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: a8564ae8c3d7499dd3d194ec61a9b24b1d0d2393
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660184"
---
# <a name="build-a-classifier--use-r-to-predict-flight-delays-with-azure-machine-learning-designer"></a>Azure Machine Learning 디자이너를 사용 하 여 비행 지연을 예측 하는 데 R을 사용 하 & 분류자 빌드

**Designer (미리 보기) 샘플 6**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

이 파이프라인은 과거 비행 및 날씨 데이터를 사용 하 여 예약 된 승객 비행이 15 분 넘게 지연 될 지 여부를 예측 합니다. 이 문제는 분류 문제로 인해 지연 되거나 시간에 두 가지 클래스를 예측할 수 있습니다.

이 샘플에 대 한 최종 파이프라인 그래프는 다음과 같습니다.

[파이프라인의 ![그래프](media/how-to-designer-sample-classification-flight-delay/pipeline-graph.png)](media/how-to-designer-sample-classification-flight-delay/pipeline-graph.png#lightbox)

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. 샘플 6을 클릭 하 여 엽니다.

## <a name="get-the-data"></a>데이터 가져오기

이 샘플에서는 **비행 지연 데이터** 데이터 집합을 사용 합니다. 미국 운송 부서에서의 수치 데이터 수집의 일부입니다. 데이터 집합은 4 월부터 10 월 2013 까지의 비행 지연 정보를 포함 합니다. 데이터 집합은 다음과 같이 미리 처리 되었습니다.

* 본토 미국에서 70의 가장 많은 공항을 포함 하도록 필터링 되었습니다.
* 레이블 재지정가 15 분 넘게 지연 되 면 항공편을 취소 했습니다.
* 필터링 된 항공편.
* 14 개의 열을 선택 했습니다.

비행 데이터를 보완 하기 위해 **날씨 데이터 집합이** 사용 됩니다. 날씨 데이터에는 NOAA의 시간별, 육지 기반 날씨 관측값이 포함 되어 있으며, 항공편 데이터 집합과 동일한 기간을 포함 하는 공항 날씨 스테이션의 관찰을 나타냅니다. 다음과 같이 미리 처리 되었습니다.

* 날씨 스테이션 Id는 해당 공항 Id에 매핑됩니다.
* 70의 가장 많은 공항에 연결 되지 않은 날씨 스테이션이 제거 되었습니다.
* 날짜 열은 연도, 월, 일의 개별 열로 분할 되었습니다.
* 26 개의 열을 선택 했습니다.

## <a name="pre-process-the-data"></a>데이터를 미리 처리 합니다.

일반적으로 데이터 집합을 분석 하려면 먼저 일부 전처리를 수행 해야 합니다.

![데이터-프로세스](./media/how-to-designer-sample-classification-flight-delay/data-process.png)

### <a name="flight-data"></a>비행 데이터

**OriginAirportID**및 **DestAirportID** **열은**정수로 저장 됩니다. 그러나 범주 특성 이므로 **메타 데이터 편집** 모듈을 사용 하 여 범주를 범주로 변환 합니다.

![편집-메타 데이터](./media/how-to-designer-sample-classification-flight-delay/edit-metadata.png)

그런 다음 데이터 집합에서 **열 선택** 모듈을 사용 하 여 가능한 대상 **leakers:** **DepDel15**, **arrdelay**, **Canceled**, **Year**데이터 집합 열에서 제외 합니다. 

비행 레코드를 시간별 날씨 레코드와 조인 하려면 예약 된 출발 시간을 조인 키 중 하나로 사용 합니다. 조인을 수행 하려면 CSRDepTime 열을 **R 스크립트 실행** 모듈에서 수행 하는 가장 가까운 시간으로 내림 해야 합니다. 

### <a name="weather-data"></a>날씨 데이터

누락 값이 많은 열은 **프로젝트 열** 모듈을 사용 하 여 제외 됩니다. 이러한 열에는 모든 문자열 반환 열 ( **ValueForWindCharacter**, **WetBulbFarenheit**, **wetbulbcelsius**, **PressureTendency**, **PressureChange**, **SeaLevelPressure**및 **StationPressure**)이 포함 됩니다.

그런 다음 **누락 된 데이터 정리** 모듈을 나머지 열에 적용 하 여 데이터가 누락 된 행을 제거 합니다.

날씨 관찰 시간은 가장 가까운 전체 시간으로 반올림 됩니다. 예정 된 비행 시간 및 날씨 관찰 시간은 모델이 비행 시간 이전에 날씨만 사용 하도록 반대 방향으로 반올림 됩니다. 

날씨 데이터는 현지 시간에 보고 되므로 예약 된 출발 시간 및 날씨 관찰 시간에서 표준 시간대 열을 빼서 표준 시간대 차이를 고려 합니다. 이러한 작업은 **R 스크립트 실행** 모듈을 사용 하 여 수행 됩니다.

### <a name="joining-datasets"></a>데이터 집합 조인

비행 레코드는 **데이터 조인** 모듈을 사용 하 여 비행 (**OriginAirportID**)의 원본에서 날씨 데이터와 조인 됩니다.

 ![원본에의 한 비행 및 날씨 참여](./media/how-to-designer-sample-classification-flight-delay/join-origin.png)


비행 레코드는 항공편 (**DestAirportID**)의 대상을 사용 하 여 날씨 데이터와 조인 됩니다.

 ![목적지 별 비행 및 날씨 조인](./media/how-to-designer-sample-classification-flight-delay/join-destination.png)

### <a name="preparing-training-and-test-samples"></a>학습 및 테스트 샘플 준비

**데이터 분할** 모듈은 교육을 위해 4 월부터 9 월 레코드, 테스트의 경우 10 월 레코드까지 데이터를 분할 합니다.

 ![학습 및 테스트 데이터 분할](./media/how-to-designer-sample-classification-flight-delay/split.png)

Year, month 및 timezone 열은 열 선택 모듈을 사용 하 여 학습 데이터 집합에서 제거 됩니다.

## <a name="define-features"></a>기능 정의

기계 학습에서 기능은 관심 있는 항목의 개별 측정 가능한 속성입니다. 강력한 기능 집합을 찾으려면 실험 및 도메인 지식이 필요 합니다. 일부 기능은 다른 기능에 비해 대상 예측에 더 유용합니다. 또한 일부 기능은 다른 기능과 강력한 상관 관계를 가질 수 있으며 모델에 새 정보를 추가 하지 않습니다. 이러한 기능은 제거할 수 있습니다.

모델을 작성 하려면 사용 가능한 모든 기능을 사용 하거나 기능 하위 집합을 선택할 수 있습니다.

## <a name="choose-and-apply-a-learning-algorithm"></a>학습 알고리즘 선택 및 적용

**2 클래스 로지스틱 회귀** 모듈을 사용 하 여 모델을 만들고 학습 데이터 집합에 대해 학습 합니다. 

**모델 학습** 모듈의 결과는 새 샘플의 점수를 매기고 예측을 만드는 데 사용할 수 있는 학습 된 분류 모델입니다. 테스트 집합을 사용 하 여 학습 된 모델에서 점수를 생성 합니다. 그런 다음 **모델 평가** 모듈을 사용 하 여 모델의 품질을 분석 하 고 비교 합니다.
파이프라인 파이프라인을 실행 한 후 출력 포트를 클릭 하 고 **시각화**를 선택 하 여 **모델 점수 매기기** 모듈의 출력을 볼 수 있습니다. 출력에는 점수가 매겨진 레이블과 레이블의 확률이 포함 됩니다.

마지막으로 결과의 품질을 테스트 하려면 파이프라인 캔버스에 **모델 평가** 모듈을 추가 하 고 왼쪽 입력 포트를 모델 점수 매기기 모듈의 출력에 연결 합니다. 출력 포트를 클릭 하 고 **시각화**를 선택 하 여 파이프라인을 실행 하 고 **모델 평가** 모듈의 출력을 확인 합니다.

## <a name="evaluate"></a>평가
로지스틱 회귀 모델의 테스트 집합에는 0.631의 요소가 있습니다.

 ![평가](media/how-to-designer-sample-classification-flight-delay/sample6-evaluate-1225.png)

## <a name="next-steps"></a>다음 단계

디자이너에 사용할 수 있는 다른 샘플을 탐색 합니다.

- [샘플 1-회귀: 자동차 가격 예측](how-to-designer-sample-regression-automobile-price-basic.md)
- [샘플 2-회귀: 자동차 가격 예측에 대 한 알고리즘 비교](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [샘플 3-기능 선택이 포함 된 분류: 수입 예측](how-to-designer-sample-classification-predict-income.md)
- [샘플 4-분류: 신용 위험 예측 (비용 구분)](service/how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [샘플 5-분류: 변동 예측](service/how-to-designer-sample-classification-churn.md)
- [샘플 7-텍스트 분류: 위키백과 SP 500 데이터 집합](how-to-designer-sample-text-classification.md)