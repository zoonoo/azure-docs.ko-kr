---
title: 일대다 다중 클래스
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning 서비스의 일대다 다중 클래스 모듈을 사용 하 여 앙상블 이진 분류 모델에서 다중 클래스 분류 모델을 만드는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: 9ded83dc5b8b8b98af66c8858214e8f82a4aa166
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518165"
---
# <a name="one-vs-all-multiclass"></a>일대다 다중 클래스

이 문서에서는 **일대다 다중 클래스** 모듈 inazure Machine Learning designer (미리 보기)를 사용 하 여 "일 대 다" 방식을 사용 하 여 여러 클래스를 예측할 수 있는 분류 모델을 만드는 방법을 설명 합니다.

이 모듈은 연속 또는 범주 예측 변수에 따라 결과가 달라 지는 경우 세 개 이상의 가능한 결과를 예측 하는 모델을 만드는 데 유용 합니다. 이 메서드를 사용 하 여 여러 출력 클래스를 필요로 하는 문제에 대해서도 이진 분류 방법을 사용할 수 있습니다.

### <a name="more-about-one-vs-all-models"></a>단일 vs 모든 모델에 대 한 자세한 정보

일부 분류 알고리즘은 설계에 따라 세 개 이상의 클래스를 사용할 수 있지만, 다른 값은 두 값 (이진 또는 2 클래스 모델) 중 하나로 제한 됩니다. 그러나 다양 한 전략을 사용 하 여 다중 클래스 분류 작업에 대해 이진 분류 알고리즘을 적용할 수도 있습니다. 

이 모듈은 여러 출력 클래스 각각에 대해 이진 모델이 생성 되는 *one 및 all 메서드*를 구현 합니다. 개별 클래스에 대 한 이러한 각 이진 모델은 이진 분류 문제인 것 처럼 보수 (모델의 다른 모든 클래스)에 대해 평가 됩니다. 그런 다음 이러한 이진 분류자를 실행 하 고 신뢰도 점수가 가장 높은 예측을 선택 하 여 예측을 수행 합니다.  

기본적으로 개별 모델의 앙상블 생성 되며 결과가 병합 되어 모든 클래스를 예측 하는 단일 모델을 만듭니다. 따라서 모든 이진 분류자를 하나의 vs-전체 모델에 대 한 기준으로 사용할 수 있습니다.  

 예를 들어 [2 클래스 지원 벡터 컴퓨터](two-class-support-vector-machine.md) 모델을 구성 하 고이를 **일대다 다중 클래스** 모듈에 대 한 입력으로 제공 한다고 가정해 보겠습니다. 이 모듈은 출력 클래스의 모든 멤버에 대해 2 클래스 지원 벡터 컴퓨터 모델을 만든 다음, 하나의 vs-all 메서드를 적용 하 여 모든 클래스에 대 한 결과를 결합 합니다.  

## <a name="how-to-configure-the-one-vs-all-classifier"></a>단일 vs 분류자를 구성 하는 방법  

이 모듈은 여러 클래스를 분석 하는 이진 분류 모델의 앙상블을 만듭니다. 따라서이 모듈을 사용 하려면 먼저 **이진 분류** 모델을 구성 하 고 학습 해야 합니다. 

그런 다음 이진 모델을 **일대다 다중 클래스** 모듈에 연결 하 고, 레이블이 지정 된 학습 데이터 집합으로 [학습 모델](train-model.md) 을 사용 하 여 모델의 앙상블을 학습 합니다.

모델을 결합할 때 학습 데이터 집합에 여러 클래스 값이 있을 수 있지만 **일대다 다중 클래스** 는 여러 개의 이진 분류 모델을 만들고 각 클래스에 대해 알고리즘을 최적화 한 다음 모델을 병합 합니다.

1. 디자이너에서 파이프라인에 **일대다 다중 클래스** 를 추가 합니다. 이 모듈은 **분류** 범주의 Machine Learning-Initialize에서 찾을 수 있습니다.

    **일대다 다중 클래스** 분류자에는 자체의 구성 가능한 매개 변수가 없습니다. 사용자 지정은 입력으로 제공 되는 이진 분류 모델에서 수행 해야 합니다.

2. 파이프라인에 이진 분류 모델을 추가 하 고 해당 모델을 구성 합니다. 예를 들어 [2 클래스 지원 벡터 컴퓨터](two-class-support-vector-machine.md) 또는 [2 클래스 승격 된 의사 결정 트리](two-class-boosted-decision-tree.md)를 사용할 수 있습니다.

3. 파이프라인에 [모델 학습](train-model.md) 모듈을 추가 하 고 **일대다 다중 클래스**출력 인 학습 되지 않은 분류자를 연결 합니다.

4. [학습 모델](train-model.md)의 다른 입력에서 여러 클래스 값이 있는 레이블이 지정 된 학습 데이터 집합을 연결 합니다.

5. 파이프라인을 실행합니다.

## <a name="results"></a>결과

학습을 완료 한 후에는 모델을 사용 하 여 다중 클래스 예측을 만들 수 있습니다.

또는 학습 되지 않은 분류자를 전달 하 여 레이블이 지정 된 유효성 검사 데이터 집합에 대 한 교차 유효성 검사를 위한 [모델 교차](cross-validate-model.md) 유효성 검사를 수행할 수 있습니다.


## <a name="next-steps"></a>다음 단계

Azure Machine Learning 서비스에 [사용할 수 있는 모듈 집합](module-reference.md) 을 참조 하세요. 
