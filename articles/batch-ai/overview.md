---
title: Azure Batch AI 서비스 - AI 학습 | Microsoft Docs
description: 관리 Azure Batch AI 서비스를 사용하여 GPU 및 CPU 클러스터에서 AI(인공 지능) 및 기타 기계 학습 모델을 교육하는 방법을 알아봅니다.
services: batch-ai
documentationcenter: ''
author: alexsuttonms
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 08/01/2018
ms.author: danlep
ms.openlocfilehash: 98497812e75d07fc153e0e351331c05484164fdd
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44052702"
---
# <a name="what-is-azure-batch-ai"></a>Azure Batch AI란?

Azure Batch AI는 데이터 과학자 및 AI 연구원이 복잡한 인프라를 관리할 필요 없이 Azure에서 대규모로 가상 머신 및 AI 모델을 학습하고 테스트하도록 돕는 관리 서비스입니다. Batch AI는 계산 리소스, 실행하려는 작업, 모델 입력 및 출력을 저장할 위치를 기술하고, 나머지 작업을 관리합니다.

Batch AI를 독립 실행형으로 사용하거나 좀 더 큰 개발 워크플로의 일부로 모델 학습을 수행할 수 있습니다.

* 자체적으로 Batch AI를 사용하여 [GPU](../virtual-machines/linux/sizes-gpu.md) 또는 CPU 클러스터에서 Machine Learning 및 AI 모델을 학습 및 테스트하고 일괄로 채점할 수 있습니다. 

* [Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md) 또는 다른 [Azure AI 플랫폼 도구](https://azure.microsoft.com/overview/ai-platform/)를 사용하여 워크플로의 Batch AI 클러스터를 대상으로 지정할 수 있습니다. Azure ML은 데이터 준비, 실험 및 작업 기록에 대한 풍부한 환경을 제공합니다. 또한 Azure ML은 학습된 모델을 컨테이터에 패키지하고, 유추를 위해 모델을 배포하거나 IoT 장치에 배포할 수도 있습니다.  

## <a name="train-machine-learning-and-ai-models"></a>Machine learning 및 AI 모델 학습

Batch AI를 사용하여 Deep 신경망(Deep Learning) 및 기타 AI 접근 방법 뿐만 아니라 Machine Learning 모델을 학습합니다. Batch AI는 [TensorFlow](https://github.com/tensorflow/tensorflow), [PyTorch](https://github.com/pytorch/pytorch), [Chainer](https://github.com/chainer/chainer) 및 [Microsoft CNTK(Cognitive Toolkit)](https://github.com/Microsoft/CNTK)를 비롯한 인기 있는 오픈 소스 AI 라이브러리 및 프레임워크를 기본적으로 지원합니다.

문제 영역을 식별하고 데이터를 준비한 후에는 Batch AI와 대화형으로 작업하여 모델 아이디어를 테스트합니다. 그런 다음, 대규모로 실험을 진행할 준비가 되면 MPI 또는 기타 통신 라이브러리를 사용하여 여러 GPU를 통해 작업을 시작하고 더 많은 실험을 동시에 실행합니다.

Batch AI는 여러 가지 방법을 사용하여 대규모로 모델을 학습하도록 지원합니다. 예:  

|  |  |
|---------|---------|
| **학습 분산**<br/>![분산 학습](./media/overview/distributed-training.png)  | 네트워크로 연결된 여러 GPU를 통해 단일 작업에 대한 학습 규모를 확장하여 고용량 데이터로 이루어진 더 큰 네트워크를 학습할 수 있습니다.|
| **실험**<br/>![실험](./media/overview/experimentation.png) | 여러 작업을 사용하여 학습을 스케일 아웃합니다. 매개 변수 스윕을 실행하여 새로운 아이디어를 테스트하거나, 하이퍼 매개 변수를 튜닝하여 정확도 및 성능을 최적화합니다. |
| **병렬로 실행**![병렬 실행](./media/overview/parallel-execution.png) | 한 번에 많은 모델을 학습하거나 채점하고, 서버 집합을 병렬로 실행하여 작업을 더 빠르게 완료합니다.|

모델을 학습할 때, 모델이 학습 스크립트에 속하지 않으면 Batch AI를 사용하여 모델을 테스트하고, 학습 스크립트에 속하면 일괄 채점을 수행합니다.

## <a name="how-it-works"></a>작동 방법

Batch AI SDK, 명령줄 스크립트 또는 Azure Portal을 사용하여 계산 리소스를 관리하고 AI 학습 및 테스트를 위한 작업을 예약합니다. 

* **VM 클러스터 프로비전 및 크기 조정** - 노드(VM)의 수를 선택하고, 학습 요구를 충족하는 GPU 지원 또는 기타 VM 크기를 선택합니다. 필요할 때만 리소스를 사용하도록 노드 수를 자동 또는 수동으로 확장하거나 축소합니다. 

* **종속성 및 컨테이너 관리** - 기본적으로 Batch AI 클러스터GPU 또는 CPU에서 컨테이너 기반 학습 프레임워크를 실행하기 위해 종속성이 미리 설치된 Linux VM 이미지를 실행합니다. 추가 구성을 위해 사용자 지정 이미지를 불러오거나 시작 스크립트를 실행합니다.

* **데이터 분산** - 입력 데이터 및 스크립트와 작업 출력을 관리하기 위한 하나 이상의 저장소 옵션(Azure Files, Azure Blob 저장소 또는 관리되는 NFS 서버) 중에서 선택합니다. 또한 Batch AI는 고성능 병렬 파일 시스템을 비롯한 사용자 지정 저장소 솔루션도 지원합니다. 간단한 구성 파일을 사용하여 저장소 파일 시스템을 클러스터 노드 및 작업 컨테이너에 탑재합니다.

* **작업 예약** - 클러스터를 공유하고 우선 순위가 낮은 VM 및 예약된 인스턴스를 활용하는 우선 순위 기반 작업 큐로 작업을 제출합니다.

* **오류 처리** - 오래 실행될 수 있는 작업 동안 VM 오류가 발생할 경우 작업 상태를 모니터링하고 작업을 다시 시작합니다.

* **결과 수집** - 출력 로그, Stdout, Stderr 및 학습된 모델에 쉽게 액세스합니다. 탑재된 저장소에 직접 출력을 푸시하도록 Batch AI 작업을 구성합니다.

Azure Batch AI 서비스는 보안을 위해 RBAC(역할 기반 액세스 제어) 및 Azure Virtual Network와 같은 일반 도구를 지원합니다.  

## <a name="next-steps"></a>다음 단계

* Machine Learning 또는 AI 모델 학습에 대한 자세한 내용은 [Batch AI 리소스](resource-concepts.md)에 대해 알아봅니다.

* Batch AI를 사용하여 [샘플 Deep Learning 모델 학습](quickstart-tensorflow-training-cli.md)을 시작합니다.

* 샘플 [학습 레시피](https://github.com/Azure/BatchAI/blob/master/recipes)에서 자주 사용되는 AI 프레임워크를 확인합니다.
