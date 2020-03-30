---
title: 분산 교육이란 무엇입니까?
titleSuffix: Azure Machine Learning
description: 분산 교육 및 Azure 기계 학습이 이를 지원하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
author: nibaccam
ms.author: nibaccam
ms.subservice: core
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: a0d5bf795e4759a105b9a235770f37aa10bd6751
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385546"
---
# <a name="distributed-training-with-azure-machine-learning"></a>Azure 기계 학습을 위한 분산 교육

이 문서에서는 분산 학습및 Azure Machine Learning이 딥 러닝 모델에 지원하는 방법에 대해 알아봅니다. 

분산 교육에서 모델을 학습하는 워크로드는 작업자 노드라고 하는 여러 미니 프로세서 간에 분할되고 공유됩니다. 이러한 작업자 노드는 병렬로 작동하여 모델 학습 속도를 높일 수 있습니다. 분산 교육은 기존 ML 모델에 사용할 수 있지만 심층 신경망 교육을 위한 [딥 러닝과](concept-deep-learning-vs-machine-learning.md) 같이 컴퓨팅 및 시간 집약적인 작업에 더 적합합니다.

## <a name="deep-learning-and-distributed-training"></a>딥 러닝 및 분산 교육 

분산 학습에는 데이터 병렬 [처리와](#data-parallelism) [모델 병렬 처리의](#model-parallelism)두 가지 주요 유형이 있습니다. 딥 러닝 모델에 대한 분산 교육의 경우 [파이썬의 Azure 기계 학습 SDK는](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 인기 있는 프레임워크인 PyTorch 및 TensorFlow와의 통합을 지원합니다. 두 프레임워크 모두 분산 교육을 위해 데이터 병렬 처리를 사용하며, 컴퓨팅 속도를 최적화하기 위해 [horovod를](https://horovod.readthedocs.io/en/latest/summary_include.html) 활용할 수 있습니다. 

* [파이토치와 함께 하는 분산 교육](how-to-train-pytorch.md#distributed-training)

* [텐서플로우를 통해 분산 교육](how-to-train-tensorflow.md#distributed-training)

분산 교육이 필요하지 않은 ML 모델의 경우 Python SDK를 사용하여 모델을 학습하는 다양한 방법에 대한 [Azure 기계 학습이 있는 학습 모델을](concept-train-machine-learning-model.md#python-sdk) 참조하십시오.

## <a name="data-parallelism"></a>데이터 병렬 처리

데이터 병렬 처리는 두 분산 학습 방법을 구현하는 가장 쉬운 방법이며 대부분의 사용 사례에 충분합니다.

이 방법에서 데이터는 파티션 수와 계산 클러스터의 사용 가능한 총 노드 수와 동일한 파티션으로 나뉩니다. 모델은 이러한 각 작업자 노드에서 복사되며 각 작업자는 데이터의 자체 하위 집합에서 작동합니다. 각 노드는 학습 중인 모델을 지원할 수 있는 능력이 있어야 하며, 즉 모델이 각 노드에 완전히 맞아야 한다는 점에 유의해야 합니다.

각 노드는 학습 샘플에 대한 예측과 레이블이 지정된 출력 간의 오류를 독립적으로 계산합니다. 차례로 각 노드는 오류를 기반으로 모델을 업데이트하고 해당 모델을 업데이트하기 위해 모든 변경 내용을 다른 노드에 전달해야 합니다. 즉, 작업자 노드는 일괄 처리 계산이 끝날 때 모델 매개 변수 또는 그라데이션을 동기화하여 일관된 모델을 학습해야 합니다. 

## <a name="model-parallelism"></a>모델 병렬 처리

네트워크 병렬처리라고도 하는 모델 병렬처리에서 모델은 서로 다른 노드에서 동시에 실행할 수 있는 여러 부분으로 분할되고 각 모델은 동일한 데이터에서 실행됩니다. 이 메서드의 확장성은 알고리즘의 작업 병렬화 정도에 따라 달라지며 데이터 병렬 처리보다 구현하는 것이 더 복잡합니다. 

모델 병렬 처리에서 작업자 노드는 공유 매개 변수를 동기화하기만 하면 됩니다(일반적으로 각 전진 또는 역방향 전파 단계에 대해 한 번만). 또한 각 노드가 동일한 학습 데이터에 대한 모델의 하위 섹션에서 작동하므로 더 큰 모델은 문제가 되지 않습니다.

## <a name="next-steps"></a>다음 단계

* Python SDK를 사용하여 [교육 환경을 설정하는](how-to-set-up-training-targets.md) 방법에 대해 알아봅니다.
* 기술적인 예는 [참조 아키텍처 시나리오를](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/training-deep-learning)참조하십시오.
* [텐서플로우로 ML 모델을 훈련.](how-to-train-tensorflow.md)
* [PyTorch로 ML 모델을 훈련하십시오.](how-to-train-pytorch.md) 