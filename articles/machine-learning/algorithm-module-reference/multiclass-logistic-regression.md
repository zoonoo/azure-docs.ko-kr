---
title: '다중 클래스 로지스틱 회귀 분석: 모듈 참조'
titleSuffix: Azure Machine Learning service
description: 여러 값을 예측에 사용할 수 있는 로지스틱 회귀 모델을 만드는 Azure Machine Learning 서비스에서 다중 클래스 로지스틱 회귀 분석 모듈을 사용 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ac4310e851808d6e6d89d1a2b506975eea3b1d69
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029327"
---
# <a name="multiclass-logistic-regression-module"></a>다중 클래스 로지스틱 회귀 모듈도

이 문서에서는 Azure Machine Learning 서비스에 대 한 시각적 인터페이스 (미리 보기)의 모듈을 설명 합니다.

이 모듈을 사용 하 여 여러 값을 예측에 사용할 수 있는 로지스틱 회귀 모델을 만듭니다.

로지스틱 회귀를 사용 하 여 분류는 지도 학습 방법 이며 따라서 레이블이 지정 된 데이터 집합을 필요로 합니다. 모델 및 레이블이 지정 된 데이터 집합 모듈에 대 한 입력으로 같은 제공 하 여 모델을 학습할 [모델 학습](./train-model.md)합니다. 그런 다음 학습 된 모델은 새 입력된 예제에 대 한 값을 예측 하 사용할 수 있습니다.

Azure Machine Learning에서는 [2 클래스 로지스틱 회귀](./two-class-logistic-regression.md) 이진 또는 이분법 변수의 분류에 적합 한 모듈입니다.

## <a name="about-multiclass-logistic-regression"></a>다중 클래스 로지스틱 회귀에 대 한

로지스틱 회귀는 분류 작업에 널리 사용 되 고 결과의 확률을 예측 하는 데 사용 되는 통계는 잘 알려진 방법입니다. 알고리즘으로 데이터를 로지스틱 함수 이벤트 발생 확률을 예측 합니다. 

다중 클래스 로지스틱 회귀에서 분류자 여러 결과 예측에 사용할 수 있습니다.

## <a name="configure-a-multiclass-logistic-regression"></a>다중 클래스 로지스틱 회귀를 구성 합니다.

1. 추가 된 **다중 클래스 로지스틱 회귀** 모듈을 실험 합니다.

2. 설정 하 여 학습 모델을 지정 합니다 **트레이너 모드 만들기** 옵션입니다.

    + **매개 변수를 단일**: 모델을 구성 하 고 값을 인수로 특정 집합을 제공 하고자 하는 방법을 알고 있는 경우이 옵션을 사용 합니다.

    + **매개 변수 범위**: 최상의 매개 변수를 정확히 알지 못하는 경우 매개 변수 비우기를 사용 하려면이 옵션을 사용 합니다.

3. **최적화 허용 오차**, 최적화 프로그램 수렴의 임계값을 지정 합니다. 반복 간의 개선 된 임계값 보다 적은 경우 알고리즘을 중지 하 고 현재 모델이 반환 합니다.

4. **L1 정규화 가중치**하십시오 **L2 정규화 가중치**: L1 및 L2 정규화 매개 변수에 사용할 값을 입력 합니다. 둘 다에 대해 0이 아닌 값이 좋습니다.

    정규화는 극단적인 계수 값을 사용 하 여 페널티를 부여 모델 과잉 맞춤을 방지 하기 위한 메서드입니다. 정규화는 계수 값 가설의 오류와 연결 된 페널티를 추가 하 여 작동 합니다. 더 극단적인 계수 값을 사용 하 여 정확한 모델에는 페널티가 되지만 더욱 일반적인 값을 사용 하 여 정확도 떨어지는 모델은 페널티가 적게 부여 합니다.

     L1 및 L2 정규화에 미치는 영향이 달라 하 고 사용 합니다. L1 차원 데이터로 작업할 때 유용 스파스 모델에 적용할 수 있습니다. 반면, L2 정규화는 스파스가 아닌 데이터에 대 한 것이 좋습니다.  이 알고리즘은 L1 및 L2 정규화 값의 선형 조합을 지원 합니다: 즉, 경우 `x = L1` 및 `y = L2`, `ax + by = c` 정규화 조건의 선형 범위를 정의 합니다.

     L1 및 L2 조건의 다양 한 선형 조합을 고안 되 었어야 로지스틱 회귀 모델의 경우와 같은 [탄력적 net 정규화](https://wikipedia.org/wiki/Elastic_net_regularization)합니다.

6. **난수 초기값**: 결과 반복 실행 가능 하도록 하는 경우 알고리즘에 대 한 초기값으로 사용 하는 정수 값을 입력 합니다. 그렇지 않으면 시스템 클록 동일한 실험의 실행에서 약간 다른 결과 산출 하는 초기값으로 사용 됩니다.

8. 레이블이 지정 된 데이터 집합을 지정 하 고 학습 모듈 중 하나를 연결 합니다.

    + 설정 하는 경우 **트레이너 모드 만들기** 를 **단일 매개 변수**를 사용 합니다 [모델 학습](./train-model.md) 모듈입니다.

9. 실험을 실행합니다.

## <a name="results"></a>결과

교육을 완료 한 후에 교육에서 배운 기능 가중치와 함께 모델의 매개 변수를 요약의 출력을 마우스 오른쪽 단추로 클릭 수 있습니다 합니다 [모델 학습](./train-model.md) 모듈과 선택 **시각화**.


## <a name="next-steps"></a>다음 단계

참조를 [사용할 수 있는 모듈 집합](module-reference.md) Azure Machine Learning 서비스입니다. 