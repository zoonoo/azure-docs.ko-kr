---
title: 이미지 모델 점수 매기기 모듈 사용
titleSuffix: Azure Machine Learning
description: Azure Machine Learning의 이미지 점수 매기기 모듈을 사용 하 여 학습 된 이미지 모델을 사용 하 여 예측을 생성 하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: 88ca997e2d22283babf582b10d9b0eeb7de122c0
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90905189"
---
# <a name="score-image-model"></a>이미지 모델 채점

이 문서에서는 Azure Machine Learning 디자이너의 모듈을 설명 합니다.

이 모듈을 사용 하 여 입력 이미지 데이터에 대해 학습 된 이미지 모델을 사용 하 여 예측을 생성 합니다.

## <a name="how-to-configure-score-image-model"></a>점수 이미지 모델을 구성 하는 방법

1. 파이프라인에 **이미지 점수 매기기** 모듈을 추가 합니다.

2. 학습 된 이미지 모델과 입력 이미지 데이터를 포함 하는 데이터 집합을 연결 합니다. 

    데이터는 ImageDirectory 유형 이어야 합니다. 이미지 디렉터리를 가져오는 방법에 대 한 자세한 내용은 [이미지 디렉터리로 변환](convert-to-image-directory.md) 모듈을 참조 하세요. 입력 데이터 집합의 스키마도 일반적으로 모델을 학습 하는 데 사용 되는 데이터의 스키마와 일치 해야 합니다.

3. 파이프라인을 제출합니다.

## <a name="results"></a>결과

[점수 이미지 모델](score-image-model.md)을 사용 하 여 점수 집합을 생성 하 고 모델의 정확도 (성능)를 평가 하는 데 사용 되는 메트릭 집합을 생성 한 후에는이 모듈과 점수가 매겨진 데이터 집합을 연결 하 여 [모델을 평가할](evaluate-model.md)수 있습니다. 

### <a name="publish-scores-as-a-web-service"></a>점수를 웹 서비스로 게시

점수 매기기의 일반적인 용도는 예측 웹 서비스의 일부로 출력을 반환 하는 것입니다. 자세한 내용은 Azure Machine Learning designer에서 파이프라인을 기반으로 실시간 끝점을 배포 하는 방법에 대 한 [자습서](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy) 를 참조 하세요.

## <a name="next-steps"></a>다음 단계

Azure Machine Learning에서 [사용 가능한 모듈 세트](module-reference.md)를 참조하세요. 
