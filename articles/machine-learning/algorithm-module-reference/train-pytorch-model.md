---
title: Pytorch 모델 학습
titleSuffix: Azure Machine Learning
description: Pytorch 모델을 처음부터 학습 하거나 finetune 하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/26/2020
ms.openlocfilehash: 9127df2805a7eef5b119a64fd8d8ccdab52f22f8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91439092"
---
# <a name="train-pytorch-model"></a>Pytorch 모델 학습

이 문서에서는 Azure Machine Learning designer에서 **Pytorch 모델 학습** 모듈을 사용 하 여 DenseNet와 같은 Pytorch 모델을 학습 하는 방법을 설명 합니다. 학습은 모델을 정의 하 고 해당 매개 변수를 설정한 후에 발생 하며 레이블이 지정 된 데이터를 요구 합니다. 

## <a name="how-to-use-train-pytorch-model"></a>Pytorch 학습 모델을 사용 하는 방법 

1. 디자이너에서 파이프라인 초안에 [DenseNet](densenet.md) 모듈 또는 [resnet](resnet.md) 을 추가 합니다.

2. **Pytorch 모델 학습** 모듈을 파이프라인에 추가 합니다. **모델 학습** 범주에서이 모듈을 찾을 수 있습니다. **학습**을 확장 하 고 **Pytorch 모델 학습** 모듈을 파이프라인으로 끌어 놓습니다.

   > [!NOTE]
   > **Pytorch 모델 학습** 모듈은 대량 데이터 집합에 대 한 **GPU** 유형 계산에서 더 효율적으로 실행 됩니다. 그렇지 않으면 파이프라인이 실패 합니다. **다른 계산 대상 사용**을 설정 하 여 모듈의 오른쪽 창에서 특정 모듈에 대 한 compute를 선택할 수 있습니다.

3.  왼쪽 입력에서 학습 되지 않은 모델을 연결 합니다. 학습 **Pytorch 모델**의 가운데 및 오른쪽 입력에 학습 데이터 집합 및 유효성 검사 데이터 집합을 연결 합니다.

    학습 되지 않은 모델의 경우 DenseNet와 같은 pytorch 모델 이어야 합니다. 그렇지 않으면 ' InvalidModelDirectoryError '가 throw 됩니다.

    데이터 집합의 경우 학습 데이터 집합은 레이블이 지정 된 이미지 디렉터리 여야 합니다. 레이블이 지정 된 이미지 디렉터리를 가져오는 방법은 **이미지 디렉터리로 변환** 을 참조 하세요. 레이블이 지정 되지 않은 경우 ' NotLabeledDatasetError '가 throw 됩니다.

    학습 데이터 집합 및 유효성 검사 데이터 집합에 동일한 레이블 범주가 있습니다. 그렇지 않으면 InvalidDatasetError가 throw 됩니다.

4.  **Epoch**의 경우 학습 하고자 하는 epoch의 수를 지정 합니다. 전체 데이터 집합은 기본적으로 5 개의 epoch 마다 반복 됩니다.

5.  **일괄 처리 크기**의 경우 일괄 처리에서 학습 하는 인스턴스 수를 기본적으로 16으로 지정 합니다.

6.  **학습 률**에 대해 *학습 률*값을 지정 합니다. 학습 률 값은 모델을 테스트 하 고 수정할 때마다 sgd와 같은 최적화 프로그램에서 사용 되는 단계의 크기를 제어 합니다.

    이 속도로 더 작게 설정 하면 로컬 정체 되기에서 중단 될 수 있는 위험이 있으므로 모델을 더 자주 테스트할 수 있습니다. 단계를 크게 만들면 더 빨리 수렴할 수 있지만 진짜 최소값을 초과할 위험이 있습니다. 기본적으로 0.001입니다.

7.  **임의 초기값**으로 초기값으로 사용할 정수 값을 입력 합니다. 실행 간에 실험의 재현 가능성 보장 하려면 초기값을 사용 하는 것이 좋습니다.

8.  그 동안에는 유효성 검사 손실이 연속으로 감소 하지 않는 경우 조기에 교육을 중지할 epoch의 **수를 지정**합니다. 기본적으로 3입니다.

9.  파이프라인을 제출합니다. 데이터 집합의 크기가 더 큰 경우 시간이 소요 되 고 GPU 계산을 사용 하는 것이 좋습니다.

## <a name="results"></a>결과

파이프라인 실행이 완료 된 후 점수 매기기에 모델을 사용 하려면 [Pytorch 학습 모델](train-pytorch-model.md) 을 [점수](score-image-model.md)매기기에 연결 하 여 새 입력 예제에 대 한 값을 예측 합니다.

## <a name="technical-notes"></a>기술 정보
###  <a name="expected-inputs"></a>예상 입력  

| Name               | 유형                    | 설명                              |
| ------------------ | ----------------------- | ---------------------------------------- |
| 학습되지 않은 모델    | UntrainedModelDirectory | 학습 되지 않은 모델, pytorch 필요         |
| 학습 데이터 세트   | ImageDirectory          | 학습 데이터 세트                         |
| 유효성 검사 데이터 세트 | ImageDirectory          | 모든 epoch의 평가를 위한 유효성 검사 데이터 집합 |

###  <a name="module-parameters"></a>모듈 매개 변수  

| Name          | 범위            | Type    | 기본값 | 설명                              |
| ------------- | ---------------- | ------- | ------- | ---------------------------------------- |
| Epoch        | >0               | 정수 | 5       | 레이블 또는 결과 열이 포함된 열을 선택합니다. |
| Batch 크기    | >0               | 정수 | 16      | 일괄 처리에서 학습 하는 인스턴스 수   |
| 학습 속도 | >=double.Epsilon | Float   | 0.001   | 추측 기울기 하강 최적화 프로그램의 초기 학습 속도입니다. |
| 무작위 초기값   | 모두              | 정수 | 1       | 모델에서 사용하는 난수 생성기의 초기값입니다. |
| 주셔서      | >0               | 정수 | 3       | 조기에 교육을 중지할 epoch 수   |

###  <a name="outputs"></a>outputs  

| Name          | 유형           | 설명   |
| ------------- | -------------- | ------------- |
| 학습된 모델 | ModelDirectory | 학습된 모델 |

## <a name="next-steps"></a>다음 단계

Azure Machine Learning에서 [사용 가능한 모듈 세트](module-reference.md)를 참조하세요. 



