---
title: DenseNet
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 디자이너에서 DenseNet 모듈을 사용하여 DenseNet 알고리즘을 사용한 이미지 분류 모델을 만드는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/26/2020
ms.openlocfilehash: d21c7443f6b30d0b7d6e8295c0c9b060a80d9a62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93421331"
---
# <a name="densenet"></a>DenseNet

이 문서에서는 Azure Machine Learning Designer에서 **DenseNet** 모듈을 사용하여 Densenet 알고리즘을 사용한 이미지 분류 모델을 만드는 방법을 설명합니다.  

이 분류 알고리즘은 감독 학습 메서드이며 레이블이 지정된 이미지 디렉터리를 요구합니다. 

> [!NOTE]
> 이 모듈은 스튜디오의 *데이터 레이블 지정* 에서 생성된 레이블이 지정된 데이터 집합을 지원하지 않으며 [이미지 디렉터리로 변환](convert-to-image-directory.md) 모듈에서 생성된 레이블이 지정된 이미지 디렉터리만 지원합니다. 

모델 및 레이블이 있는 이미지 디렉터리를 [Pytorch 모델 학습](train-pytorch-model.md)에 대한 입력으로 제공하여 모델을 학습할 수 있습니다. 그러면 학습된 모델을 사용하여 [이미지 모델 채점](score-image-model.md)으로 새로운 입력 예제의 값을 예측할 수 있습니다.

### <a name="more-about-densenet"></a>DenseNet에 대한 자세한 정보

DenseNet에 대한 자세한 내용은 연구 논문인 [조밀하게 연결된 나선 네트워크](https://arxiv.org/abs/1608.06993)를 참조하세요.

## <a name="how-to-configure-densenet"></a>DenseNet을 구성하는 방법

1.  디자이너에서 **DenseNet** 모듈을 파이프라인에 추가합니다.  

2.  **모델 이름** 의 경우 특정 DenseNet 구조의 이름을 지정하고 지원되는 DenseNet: 'densenet121', 'densenet161', 'densenet169', 'densenet201' 중에서 선택할 수 있습니다.

3.  **사전 학습** 에 ImageNet에서 사전 학습된 모델 사용 여부를 지정합니다. 선택하면 선택한 사전 학습된 모델을 기준으로 미세 조정할 수 있습니다. 선택을 취소하면 처음부터 학습시킬 수 있습니다.

4.  **메모리 효율** 의 경우 훨씬 더 메모리 효율이 높지만 속도가 느린 검사점 사용 여부를 지정합니다. 자세한 내용은 연구 논문 [DenseNets의 메모리 효율적인 구현](https://arxiv.org/pdf/1707.06990.pdf)을 참조하세요.

5.  **DenseNet** 모듈, 학습 및 유효성 검사 이미지 데이터 집합 모듈의 출력을 [Pytorch 모델 학습](train-pytorch-model.md)에 연결합니다. 

6. 파이프라인을 제출합니다.


## <a name="results"></a>결과

파이프라인 실행이 완료된 후 채점에 모델을 사용하려면 [PyTorch 모델 학습](train-pytorch-model.md) 을 [이미지 모델 채점](score-image-model.md)에 연결하여 새 입력 예제의 값을 예측합니다.

## <a name="technical-notes"></a>기술 정보  

###  <a name="module-parameters"></a>모듈 매개 변수  

| 이름             | 범위 | Type    | 기본값     | 설명                              |
| ---------------- | ----- | ------- | ----------- | ---------------------------------------- |
| 모델 이름       | 모두   | 모드    | densenet201 | 특정 DenseNet 구조체의 이름     |
| 사전 학습       | 모두   | 부울 | True        | ImageNet에서 사전 학습된 모델 사용 여부 |
| 메모리 효율 | 모두   | 부울 | 아니요       | 메모리 효율 훨씬 더 높지만 속도가 느린 검사점 사용 여부 |

###  <a name="output"></a>출력  

| 이름            | 유형                    | 설명                              |
| --------------- | ----------------------- | ---------------------------------------- |
| 학습되지 않은 모델 | UntrainedModelDirectory | Pytorch 모델 학습에 연결할 수 있는 학습 되지 않은 DenseNet 모델입니다. |

## <a name="next-steps"></a>다음 단계

Azure Machine Learning에서 [사용 가능한 모듈 세트](module-reference.md)를 참조하세요. 
