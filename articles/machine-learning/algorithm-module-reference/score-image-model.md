---
title: 이미지 모델 점수 매기기 모듈 사용
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에서 학습된 이미지 모델을 사용하여 예측을 생성하기 위해 이미지 모델 점수 매기기 모듈을 사용하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: fe57a9e8ce9b14f7d1346d819965576770afef3b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93324881"
---
# <a name="score-image-model"></a>이미지 모델 채점

이 문서에서는 Azure Machine Learning 디자이너의 모듈에 대해 설명합니다.

이 모듈을 사용하여 입력 이미지 데이터에 대해 학습된 이미지 모델을 사용하여 예측을 생성합니다.

## <a name="how-to-configure-score-image-model"></a>이미지 모델 점수 매기기를 구성하는 방법

1. 파이프라인에 **이미지 모델 점수 매기기** 모듈을 추가합니다.

2. 학습된 이미지 모델과 입력 이미지 데이터가 포함된 데이터 세트를 연결합니다. 

    데이터는 ImageDirectory 유형이어야 합니다. 이미지 디렉터리를 가져오는 방법에 대한 자세한 내용은 [이미지 디렉터리로 변환](convert-to-image-directory.md) 모듈을 참조하세요. 입력 데이터 세트의 스키마는 일반적으로 모델 학습에 사용되는 데이터의 스키마와도 일치해야 합니다.

3. 파이프라인을 제출합니다.

## <a name="results"></a>결과

[이미지 모델 점수 매기기](score-image-model.md)를 사용하여 점수 집합을 생성한 후 모델의 정확도(성능)를 평가하는 데 사용되는 메트릭 집합을 생성하려면 이 모듈과 점수가 매겨진 데이터 세트를 [모델 평가](evaluate-model.md)에 연결할 수 있습니다. 

### <a name="publish-scores-as-a-web-service"></a>웹 서비스로 점수 게시

점수 매기기의 일반적인 용도는 예측 웹 서비스의 일부로 출력을 반환하는 것입니다. 자세한 내용은 Azure Machine Learning 디자이너에서 파이프라인을 기반으로 실시간 엔드포인트를 배포하는 방법에 대한 [이 자습서](../tutorial-designer-automobile-price-deploy.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure Machine Learning에서 [사용 가능한 모듈 세트](module-reference.md)를 참조하세요.