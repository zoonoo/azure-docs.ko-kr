---
title: '의사 결정 포리스트 회귀: 모듈 참조'
titleSuffix: Azure Machine Learning service
description: 평균된 퍼셉트론 알고리즘을 기반으로 모델을 학습 하는 컴퓨터를 만드는 Azure Machine Learning 서비스에서 2 클래스 평균 퍼셉트론 모듈을 사용 하는 방법에 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f0fec525ed87f91cf102053383b2934aac4b71c0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65029237"
---
# <a name="two-class-averaged-perceptron-module"></a>2 클래스 평균 퍼셉트론 모듈

이 문서에서는 Azure Machine Learning 서비스에 대 한 시각적 인터페이스 (미리 보기)의 모듈을 설명 합니다.

평균된 퍼셉트론 알고리즘을 기반으로 모델을 학습 하는 컴퓨터를 만들려면이 모듈을 사용 합니다.  
  
이 분류 알고리즘을 감독된 학습 방법 이며 필요는 *데이터 집합을 태그가 지정 된*, 레이블 열을 포함 하는 합니다. 모델 및 태그가 지정 된 데이터 집합에 대 한 입력으로 제공 하 여 모델을 학습할 수 있습니다 [모델 학습](./train-model.md)합니다. 그런 다음 학습 된 모델은 새 입력된 예제에 대 한 값을 예측 하 사용할 수 있습니다.  

### <a name="about-averaged-perceptron-models"></a>평균된 퍼셉트론 모델 정보

합니다 *평균 퍼셉트론 방법은* 신경망의 간단한 초기 버전입니다. 이 방법에서는 입력 가능한 여러 출력을 선형 함수를 기반으로 하며 기능 벡터에서 파생 된 가중치 집합과 결합 한 다음으로 분류 됩니다-따라서 "퍼셉트론."

반면 보다 단순한 퍼셉트론 모델은 선형으로 구분 가능한 패턴을 학습에 적합 한 신경망 (특히 심층 신경망) 더 복잡 한 클래스 경계를 모델링할 수 있습니다. 그러나 퍼셉트론은 속도가 더 및 퍼셉트론 사례를 직렬로 처리 하므로 연속 학습 사용할 수 있습니다.

## <a name="how-to-configure-two-class-averaged-perceptron"></a>2 클래스 평균 퍼셉트론을 구성 하는 방법

1.  추가 된 **2 클래스 평균 퍼셉트론** 모듈을 실험 합니다.  

2.  설정 하 여 학습 모델을 지정 합니다 **트레이너 모드 만들기** 옵션입니다.  
  
    -   **매개 변수를 단일**: 모델을 구성 하려는 방법를 알고 있으면 특정 값 집합을 인수로 제공 합니다.
  
3.  에 대 한 **학습 속도**에 대 한 값을 지정 합니다 *학습 속도*합니다. 학습 속도 값 모델을 테스트 하 고 수정할 때마다 추측 기울기 하강에 사용 되는 단계의 크기를 제어 합니다.
  
     여 속도 작게, 테스트 모델 더 자주 지엽에서 막히면 수 있는 위험이 있습니다. 단계를 더 큰 여 있습니다 더 빨리 수렴할 수, true 최소값이 과도 overshooting 위험이 있는 합니다.
  
4.  에 대 한 **최대 반복 횟수**, 번호를 입력 학습 데이터를 검사 하는 알고리즘을 원하는 횟수입니다.  
  
     종종 일찍 중지 하면 일반화가 향상을 제공 합니다. 반복 횟수를 늘리면 맞춤이 향상 되지만 과잉 맞춤 위험이 있는 합니다.
  
5.  에 대 한 **임의 숫자 초기값**에서 필요에 따라 초기값으로 사용 하는 정수 값을 입력 합니다. 재현에서 실험의 실행 되도록 하려는 경우 초기값을 사용 하 여 것이 좋습니다.  
  
1.  학습 데이터 집합 및 학습 모듈 중 하나를 연결 합니다.
  
    -   설정 하는 경우 **트레이너 모드 만들기** 를 **단일 매개 변수**를 사용 합니다 [모델 학습](train-model.md) 모듈입니다.

## <a name="results"></a>결과

완료 된 후 교육:

+ 학습에서 배운 기능 가중치와 함께 모델의 매개 변수 요약을 볼의 출력을 마우스 오른쪽 단추로 [모델 학습](./train-model.md)합니다.


## <a name="next-steps"></a>다음 단계

참조를 [사용할 수 있는 모듈 집합](module-reference.md) Azure Machine Learning 서비스입니다. 