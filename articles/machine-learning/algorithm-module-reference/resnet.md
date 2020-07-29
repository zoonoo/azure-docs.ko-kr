---
title: ResNet
titleSuffix: Azure Machine Learning
description: ResNet 알고리즘을 사용 하 여 이미지 분류 모델을 만드는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: ea8c13e134eceeb27bd064e794d46d711092a867
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84450107"
---
# <a name="resnet"></a>ResNet

이 문서에서는 Azure Machine Learning designer (미리 보기)에서 **resnet** 모듈을 사용 하 여 resnet 알고리즘을 사용 하 여 이미지 분류 모델을 만드는 방법을 설명 합니다.  

이 분류 알고리즘은 감독 된 학습 방법 이며 레이블이 지정 된 데이터 집합이 필요 합니다. 레이블이 지정 된 이미지 디렉터리를 가져오는 방법에 대 한 자세한 내용은 [이미지 디렉터리로 변환](convert-to-image-directory.md) 모듈을 참조 하세요. 모델 및 레이블이 지정 된 이미지 디렉터리를 [학습 Pytorch 모델](train-pytorch-model.md)에 대 한 입력으로 제공 하 여 모델을 학습 시킬 수 있습니다. 그러면 학습 된 모델을 사용 하 여 [점수 매기기 이미지 모델](score-image-model.md)을 사용 하는 새 입력 예제의 값을 예측할 수 있습니다.

### <a name="more-about-resnet"></a>ResNet에 대 한 자세한 정보

ResNet에 대 한 자세한 내용은 [이 문서](https://pytorch.org/docs/stable/torchvision/models.html?highlight=resnext101_32x8d#torchvision.models.resnext101_32x8d) 를 참조 하세요.

## <a name="how-to-configure-resnet"></a>ResNet을 구성 하는 방법

1.  디자이너에서 **Resnet** 모듈을 파이프라인에 추가 합니다.  

2.  **모델 이름**에 대해 특정 resnet 구조의 이름을 지정 하 고 지원 되는 resnet: ' resnet18 ', ' resnet34 ', ' resnet50 ', ' resnet101 ', ' resnet152 ', ' resnet152 ', ' resnext50 \_ 32x4d ', ' resnext101 \_ 32x8d ', ' wide_resnet50 \_ 2 ', ' wide_resnet101 \_ 2 ' 중에서 선택할 수 있습니다.

3.  사전 **학습**에 대해 ImageNet에서 미리 학습 된 모델을 사용할지 여부를 지정 합니다. 선택 하는 경우 선택한 미리 학습 된 모델을 기반으로 모델을 미세 조정할 수 있습니다. 선택 취소 하면 처음부터 학습할 수 있습니다.

4.  **DenseNet** 모듈, 학습 및 유효성 검사 이미지 데이터 집합 모듈의 출력을 [학습 Pytorch 모델](train-pytorch-model.md)에 연결 합니다. 

5. 파이프라인을 제출합니다.

## <a name="results"></a>결과

파이프라인 실행이 완료 된 후 점수 매기기에 모델을 사용 하려면 [Pytorch 학습 모델](train-pytorch-model.md) 을 [점수](score-image-model.md)매기기에 연결 하 여 새 입력 예제에 대 한 값을 예측 합니다.

## <a name="technical-notes"></a>기술 정보  

###  <a name="module-parameters"></a>모듈 매개 변수  

| 이름       | 범위 | Type    | 기본값           | Description                              |
| ---------- | ----- | ------- | ----------------- | ---------------------------------------- |
| 모델 이름 | 모두   | Mode    | resnext101 \_ 32x8d | 특정 ResNet 구조의 이름입니다.       |
| 미리 학습 된 | 모두   | 부울 | True              | ImageNet에서 미리 학습 된 모델을 사용할지 여부 |
|            |       |         |                   |                                          |

###  <a name="output"></a>출력  

| 이름            | 유형                    | 설명                              |
| --------------- | ----------------------- | ---------------------------------------- |
| 학습되지 않은 모델 | UntrainedModelDirectory | 학습 Pytorch 모델에 연결할 수 있는 학습 되지 않은 ResNet 모델입니다. |

## <a name="next-steps"></a>다음 단계

Azure Machine Learning에서 [사용 가능한 모듈 세트](module-reference.md)를 참조하세요. 