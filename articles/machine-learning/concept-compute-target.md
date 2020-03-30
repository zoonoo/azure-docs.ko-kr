---
title: 계산 대상이란?
titleSuffix: Azure Machine Learning
description: Azure 기계 학습을 사용하여 모델을 학습하거나 배포할 위치를 정의합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 11/04/2019
ms.openlocfilehash: ec2d9152bf8d3d7c60f00e902f155212ee1b81cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270421"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning"></a>Azure 기계 학습에서 계산 대상이란 무엇입니까? 

**계산 대상은** 교육 스크립트를 실행하거나 서비스 배포를 호스트하는 지정된 컴퓨팅 리소스/환경입니다. 이 위치는 로컬 컴퓨터 또는 클라우드 기반 컴퓨팅 리소스일 수 있습니다. 계산 대상을 사용하면 코드를 변경하지 않고도 나중에 계산 환경을 쉽게 변경할 수 있습니다.  

일반적인 모델 개발 수명 주기에서 다음을 수행할 수 있습니다.
1. 먼저 소량의 데이터를 개발하고 실험합니다. 이 단계에서는 로컬 환경(로컬 컴퓨터 또는 클라우드 기반 VM)을 계산 대상으로 하는 것이 좋습니다. 
2. 더 큰 데이터로 확장하거나 이러한 학습 계산 대상 중 하나를 사용하여 분산 교육을 [수행합니다.](#train)  
3. 모델이 준비되면 이러한 [배포 계산 대상](#deploy)중 하나를 사용하여 웹 호스팅 환경 또는 IoT 장치에 배포합니다.

계산 대상에 사용하는 계산 리소스가 [작업 영역에](concept-workspace.md)연결됩니다. 로컬 컴퓨터 이외의 계산 리소스는 작업 영역의 사용자가 공유합니다.

## <a name="training-compute-targets"></a><a name="train"></a>트레이닝 컴퓨팅 대상

Azure 기계 학습은 다양한 계산 리소스에 따라 다양한 지원을 제공합니다.  다양한 시나리오에 대한 지원은 다를 수 있지만 사용자 고유의 계산 리소스를 연결할 수도 있습니다.

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

[모델 학습에 계산 대상 설정 및 사용에](how-to-set-up-training-targets.md)대해 자세히 알아보십시오.

## <a name="deployment-targets"></a><a name="deploy"></a>배포 대상

다음 계산 리소스를 사용하여 모델 배포를 호스팅할 수 있습니다.

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

[모델을 계산 대상에 배포하는 위치와 방법에](how-to-deploy-and-where.md)대해 알아봅니다.

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure 기계 학습 계산(관리)

관리되는 계산 리소스는 Azure 기계 학습에 의해 만들어지고 관리됩니다. 이 계산은 기계 학습 워크로드에 최적화되어 있습니다. Azure 기계 학습 계산 클러스터 및 [계산 인스턴스는](concept-compute-instance.md) 관리되는 유일한 계산입니다. 나중에 관리되는 계산 리소스를 추가로 추가할 수 있습니다.

Azure 기계 학습 계산 인스턴스(미리 보기)를 만들거나 다음에서 클러스터를 계산할 수 있습니다.

| | Azure Machine Learning Studio | Azure portal | SDK) | Resource Manager 템플릿 | CLI |
|---| ----- | ----- | ----- | ----- | ----- |
| 컴퓨팅 인스턴스 | 예 | 예 | 예 | 예 |  |
| 컴퓨팅 클러스터 | 예 | 예 | 예 | 예 | 예 |

이러한 계산 리소스를 만들면 다른 종류의 계산 대상과 달리 작업 영역의 일부가 자동으로 생성됩니다.

### <a name="compute-clusters"></a>컴퓨팅 클러스터

Azure 기계 학습 계산 클러스터를 학습 및 일괄 처리 추론(미리 보기)에 사용할 수 있습니다.  이 계산 리소스를 사용하면 다음을 수행할 수 있습니다.

* 단일 노드 또는 다중 노드 클러스터
* 실행을 제출할 때마다 자동 크기 조정 
* 자동 클러스터 관리 및 작업 스케줄링 
* CPU와 GPU 리소스에 대한 지원



## <a name="unmanaged-compute"></a>관리되지 않는 계산

관리되지 않는 계산 대상은 Azure 기계 학습에서 *관리되지 않습니다.* Azure 기계 학습 외부에서 이러한 유형의 계산 대상을 만든 다음 작업 영역에 연결합니다. 관리되지 않는 컴퓨팅 리소스에는 기계 학습 워크로드의 성능을 유지 관리하거나 개선하기 위한 추가 단계가 필요할 수 있습니다.

## <a name="next-steps"></a>다음 단계

방법 배우기:
* [계산 대상 설정하여 모델 학습](how-to-set-up-training-targets.md)
* [계산 대상에 모델 배포](how-to-deploy-and-where.md)