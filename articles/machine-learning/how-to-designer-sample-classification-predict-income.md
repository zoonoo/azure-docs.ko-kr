---
title: 'Designer: 분류, 수익 예측 예제'
titleSuffix: Azure Machine Learning
description: 이 예제에 따라 코드를 작성 하지 않고 Azure Machine Learning designer를 사용 하 여 수입을 예측 합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: 227bf490d1ac264bb54ff3d1ecf0ccc2b3ef2e0e
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75763555"
---
# <a name="build-a-classifier--use-feature-selection-to-predict-income-with-azure-machine-learning-designer"></a>기능 선택을 사용 하 여 Azure Machine Learning 디자이너와 수입을 예측 & 분류자 빌드

**Designer (미리 보기) 샘플 3**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

디자이너 (미리 보기)를 사용 하 여 코드를 한 줄도 작성 하지 않고 기계 학습 분류자를 빌드하는 방법에 대해 알아봅니다. 이 샘플은 성인용 인구 조사 수입 (> = 50K 또는 < = 50K)을 예측 하는 **2 클래스 승격 된 의사 결정 트리** 를 학습 합니다.

질문에 대 한 응답은 이를 분류 문제 라고 합니다. 그러나 동일한 기본 프로세스를 적용 하 여 회귀, 분류, 클러스터링 등 모든 유형의 기계 학습 문제를 다룰 수 있습니다.

이 샘플에 대 한 최종 파이프라인 그래프는 다음과 같습니다.

![파이프라인 그래프](./media/how-to-designer-sample-classification-predict-income/overall-graph.png)

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. 샘플 3을 클릭 하 여 엽니다.



## <a name="data"></a>데이터

데이터 집합에는 14 개의 기능과 레이블 열이 포함 되어 있습니다. 숫자 및 범주를 비롯 한 여러 가지 유형의 기능이 있습니다. 다음 다이어그램에서는 데이터 집합의 발췌를 보여 줍니다. ![데이터](media/how-to-designer-sample-classification-predict-income/sample3-dataset-1225.png)



## <a name="pipeline-summary"></a>파이프라인 요약

파이프라인을 만들려면 다음 단계를 수행 합니다.

1. 성인용 인구 조사 수입 이진 데이터 집합 모듈을 파이프라인 캔버스로 끕니다.
1. **데이터 분할** 모듈을 추가 하 여 학습 및 테스트 집합을 만듭니다. 첫 번째 출력 데이터 집합의 행 비율을 0.7로 설정 합니다. 이 설정은 데이터의 70%가 모듈의 왼쪽 포트에 출력 되 고 나머지는 오른쪽 포트에 출력 되도록 지정 합니다. 학습에 왼쪽 데이터 집합을 사용 하 고 테스트에 적합 한 데이터 집합을 사용 합니다.
1. **필터 기반 기능 선택** 모듈을 추가 하 여 PearsonCorreclation에서 5 개의 기능을 선택 합니다. 
1. **2 클래스 승격 된 의사 결정 트리** 모듈을 추가 하 여 승격 된 의사 결정 트리 분류자를 초기화 합니다.
1. **모델 학습** 모듈을 추가 합니다. 이전 단계의 분류자를 **학습 모델**의 왼쪽 입력 포트에 연결 합니다. 필터 기반 기능 선택 모듈에서 필터링 된 데이터 집합을 학습 데이터 집합으로 연결 합니다.  **모델 학습** 은 분류자를 학습 합니다.
1. 열 선택 변환 및 변환 모듈 적용을 추가 하 여 테스트 데이터 집합에 동일한 변환 (필터링 된 기반 기능 선택)을 적용 합니다.
![적용-변환](./media/how-to-designer-sample-classification-predict-income/transformation.png)
1. **모델 점수 매기기** 모듈을 추가 하 고 **모델 학습** 모듈을 연결 합니다. 그런 다음 테스트 집합을 추가 하 고 (기능 선택을 테스트에 적용 하는 변형 모듈 적용의 출력) **점수 모델**에 추가 합니다. **점수 모델** 은 예측을 수행 합니다. 해당 출력 포트를 선택 하 여 예측 및 긍정 클래스 확률을 확인할 수 있습니다.


    이 파이프라인에는 예측을 만들기 전에 레이블 열을 제외 하는 두 개의 점수 매기기 모듈이 있습니다. 이는 웹 서비스 입력에 레이블이 아닌 기능만 나타날 것 이기 때문에 실시간 끝점을 배포할 준비가 되었습니다. 

1. **모델 평가** 모듈을 추가 하 고 점수가 매겨진 데이터 집합을 왼쪽 입력 포트에 연결 합니다. 평가 결과를 보려면 **모델 평가** 모듈의 출력 포트를 선택 하 고 **시각화**를 선택 합니다.

## <a name="results"></a>결과

![결과 평가](media/how-to-designer-sample-classification-predict-income/sample3-evaluate-1225.png)

평가 결과에서 ROC, 전체 자릿수 리콜 및 혼동 메트릭과 같은 곡선이 표시 되는 것을 볼 수 있습니다. 

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>다음 단계

디자이너에 사용할 수 있는 다른 샘플을 탐색 합니다.

- [샘플 1-회귀: 자동차 가격 예측](how-to-designer-sample-regression-automobile-price-basic.md)
- [샘플 2-회귀: 자동차 가격 예측에 대 한 알고리즘 비교](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [샘플 4-분류: 신용 위험 예측 (비용 구분)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [샘플 5-분류: 변동 예측](how-to-designer-sample-classification-churn.md)
- [샘플 6-분류: 비행 지연 예측](how-to-designer-sample-classification-flight-delay.md)
- [샘플 7-텍스트 분류: 위키백과 SP 500 데이터 집합](how-to-designer-sample-text-classification.md)
