---
title: '점수 모델: 모듈 참조'
titleSuffix: Azure Machine Learning
description: Azure 기계 학습에서 모델 점수 매기기 모듈을 사용하여 학습된 분류 또는 회귀 모델을 사용하여 예측을 생성하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 56d8cad05a42da8de680ade487dddee9a97aab3a
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80364181"
---
# <a name="score-model-module"></a>모델 점수 매기기 모듈

이 문서에서는 Azure 기계 학습 디자이너(미리 보기)의 모듈에 대해 설명합니다.

이 모듈을 사용하여 학습된 분류 또는 회귀 모델을 사용하여 예측을 생성합니다.

## <a name="how-to-use"></a>사용 방법

1. 파이프라인에 **점수 모델** 모듈을 추가합니다.

2. 학습된 모델과 새 입력 데이터가 포함된 데이터 집합을 연결합니다. 

    데이터는 사용 중인 학습된 모델 유형과 호환되는 형식이어야 합니다. 입력 데이터 집합의 스키마는 일반적으로 모델을 학습하는 데 사용되는 데이터의 스키마와 일치해야 합니다.

3. 파이프라인을 제출합니다.

## <a name="results"></a>결과

[점수 모델을](./score-model.md)사용하여 점수 집합을 생성한 후:

+ 모델의 정확도(성능)를 평가하는 데 사용되는 메트릭 집합을 생성하려면 점수가 매겨짐된 데이터 집합을 [모델 평가에](./evaluate-model.md)연결할 수 있습니다. 
+ 모듈을 마우스 오른쪽 단추로 클릭하고 **시각화를** 선택하여 결과 샘플을 확인합니다.
<!-- + To Save the results to a dataset. -->

점수 또는 예측 값은 모델 및 입력 데이터에 따라 다양한 형식으로 될 수 있습니다.

- 분류 모델의 경우 [점수 모델은](./score-model.md) 클래스에 대한 예측 값과 예측 값의 확률을 출력합니다.
- 회귀 모델의 경우 [점수 모델은](./score-model.md) 예측된 숫자 값만 생성합니다.


## <a name="publish-scores-as-a-web-service"></a>점수를 웹 서비스로 게시

점수 매기기의 일반적인 용도는 예측 웹 서비스의 일부로 출력을 반환하는 것입니다. 자세한 내용은 Azure 기계 학습 디자이너의 파이프라인을 기반으로 실시간 끝점을 배포하는 방법에 대한 [이 자습서를](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy) 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure 기계 학습에 사용할 수 있는 [모듈 집합을](module-reference.md) 참조하십시오. 