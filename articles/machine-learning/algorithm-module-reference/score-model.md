---
title: '모델 점수 매기기: 모듈 참조'
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning 서비스에서 모델 점수 매기기 모듈을 사용 하 여 학습 된 분류 또는 회귀 모델을 사용 하 여 예측을 생성 하는 방법에 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f8f7bfcbbf013f2cf32957772086d7e44d31e310
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029267"
---
# <a name="score-model-module"></a>모델 점수 매기기 모듈

이 문서에서는 Azure Machine Learning 서비스에 대 한 시각적 인터페이스 (미리 보기)의 모듈을 설명 합니다.

이 모듈을 사용 하 여 학습된 된 분류 또는 회귀 모델을 사용 하 여 예측을 생성 합니다.

## <a name="how-to-use"></a>사용 방법

1. 추가 된 **모델 점수 매기기** 모듈을 실험 합니다.

2. 학습 된 모델과 새 입력된 데이터를 포함 하는 데이터 집합을 연결 합니다. 

    데이터를 사용 하는 학습 된 모델의 유형과 호환 되는 형식에 있어야 합니다. 입력된 데이터 집합의 스키마 모델의 학습에 사용 되는 데이터의 스키마를 일치도 일반적으로 해야 합니다.

3. 실험을 실행합니다.

## <a name="results"></a>결과

사용 하 여 점수 집합을 생성 한 후 [모델 점수 매기기](./score-model.md):

+ 평가 모델의 정확도 (성능)에 사용 되는 메트릭 집합을 생성 합니다.  점수가 매겨진된 데이터 집합을 연결할 수 있습니다 [모델 평가](./evaluate-model.md), 
+ 모듈을 마우스 오른쪽 단추로 클릭 **시각화** 결과의 샘플을 보려면.
+ 데이터 집합에 결과 저장 합니다.

예측된 값을 점수는 모델과 입력된 데이터에 따라 여러 형식 수 있습니다.

- 분류 모델용 [모델 점수 매기기](./score-model.md) 예측된 값의 확률 뿐만 아니라 클래스에 대 한 예측된 값을 출력 합니다.
- 회귀 모델용 [모델 점수 매기기](./score-model.md) 예측된 숫자 값만 생성 합니다.
- 이미지 분류 모델의 경우 점수는 이미지 또는 특정 기능을 찾을 수 있는지 여부를 나타내는 부울 값을 개체의 클래스 수 있습니다.

## <a name="publish-scores-as-a-web-service"></a>점수를 웹 서비스로 게시

점수 매기기의 일반적인 용도 예측 웹 서비스의 일부로 출력을 반환 하는 것입니다. 자세한 내용은 Azure Machine Learning에서 실험에 따라 웹 서비스를 만드는 방법에이 자습서를 참조 하세요.


## <a name="next-steps"></a>다음 단계

참조를 [사용할 수 있는 모듈 집합](module-reference.md) Azure Machine Learning 서비스입니다. 