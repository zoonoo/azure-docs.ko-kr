---
title: '순열 기능 중요도: 모듈 참조'
titleSuffix: Azure Machine Learning
description: 학습 된 모델 및 테스트 데이터 집합을 사용 하 여 Azure Machine Learning에서 순열 기능 중요도 모듈을 사용 하 여 기능 변수의 순열 기능 중요도 점수를 계산 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: b939e88606fba5d7759e55239838d5308bf0563c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75428481"
---
# <a name="permutation-feature-importance"></a>순열 기능 중요도

이 문서에서는 Azure Machine Learning designer (미리 보기)에서 순열 기능 중요도 모듈을 사용 하 여 데이터 집합에 대 한 기능 중요도 점수 집합을 계산 하는 방법을 설명 합니다. 이러한 점수를 사용 하 여 모델에서 사용 하기에 가장 적합 한 기능을 결정 하는 데 도움을 줍니다.

이 모듈에서 기능 값은 한 번에 한 열에 임의로 섞은 됩니다. 모델의 성능은 이전 및 이후로 측정 됩니다. 표준 메트릭 중 하나를 선택 하 여 성능을 측정할 수 있습니다.

모듈에서 반환 하는 점수는 순열의 후 학습 된 모델의 성능 *변화* 를 나타냅니다. 중요 한 기능은 일반적으로 순서 섞기 프로세스에 더 중요 하므로 중요도 점수가 높아집니다. 

이 문서에서는 기계 학습: [순열 기능 중요도](https://blogs.technet.com/b/machinelearning/archive/2015/04/14/permutation-feature-importance.aspx)의 순열 기능, 이론상의 해당 응용 프로그램에 대 한 개요를 제공 합니다.  

## <a name="how-to-use-permutation-feature-importance"></a>순열 기능 중요도를 사용 하는 방법

기능 점수 집합을 생성 하려면 이미 학습 된 모델 및 테스트 데이터 집합이 있어야 합니다.  

1.  순열 기능 중요도 모듈을 파이프라인에 추가 합니다. 이 모듈은 **기능 선택** 범주에서 찾을 수 있습니다. 

2.  학습 된 모델을 왼쪽 입력에 연결 합니다. 모델은 회귀 모델 또는 분류 모델 이어야 합니다.  

3.  오른쪽 입력에서 데이터 집합을 연결 합니다. 모델 학습에 사용 하는 데이터 집합과 다른 하나를 선택 하는 것이 좋습니다. 이 데이터 집합은 학습 된 모델을 기준으로 점수를 매기는 데 사용 됩니다. 또한 기능 값이 변경 된 후 모델을 평가 하는 데 사용 됩니다.  

4.  **임의 초기값**의 경우 임의 값의 초기값으로 사용할 값을 입력 합니다. 0 (기본값)을 지정 하면 시스템 클록을 기준으로 숫자가 생성 됩니다.

     초기값은 선택 사항 이지만 동일한 파이프라인의 실행 간에 재현 가능성 하려는 경우 값을 제공 해야 합니다.  

5.  **성능 측정 메트릭에**대해 순열 후 모델 품질을 계산 하는 경우 사용할 단일 메트릭을 선택 합니다.  

     Azure Machine Learning designer는 분류 또는 회귀 모델을 평가 하 고 있는지 여부에 따라 다음과 같은 메트릭을 지원 합니다.  

    -   **분류**

        정확도, 전체 자릿수, 회수, 평균 로그 손실  

    -   **회귀**

        전체 자릿수, 회수, 평균 절대 오차, 제곱 평균 오차, 상대 절대 오차, 상대 제곱 오차, 결정 계수  

     이러한 평가 메트릭에 대 한 자세한 설명과 계산 방법에 대 한 자세한 내용은 [모델 평가](evaluate-model.md)를 참조 하세요.  

6.  파이프라인을 실행합니다.  

7.  모듈은 기능 열 목록과 이와 연결 된 점수를 출력 합니다. 이 목록은 점수를 기준으로 내림차순으로 순위가 매겨집니다.  


##  <a name="technical-notes"></a>기술 정보

순열 기능 중요도는 한 번에 하나의 열로 각 기능 열의 값을 임의로 변경 하 여 작동 합니다. 그런 다음 모델을 평가 합니다. 

모듈에서 제공 하는 순위는 [필터 기반 기능 선택](filter-based-feature-selection.md)에서 가져온 순위와는 대체로 다릅니다. 필터 기반 기능 선택은 모델을 만들기 *전의* 점수를 계산 합니다. 

차이점은 순열 기능 중요도가 기능과 대상 값 간의 연결을 측정 하지 않기 때문입니다. 대신 각 기능에 모델의 예측에 대 한 영향을 주는 정도를 캡처합니다.
  
## <a name="next-steps"></a>다음 단계

Azure Machine Learning [사용할 수 있는 모듈 집합](module-reference.md) 을 참조 하세요. 
