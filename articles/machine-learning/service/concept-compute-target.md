---
title: '계산 대상: 학습 및 모델을 배포 하는 위치'
titleSuffix: Azure Machine Learning service
description: 학습 또는 Azure Machine Learning 서비스를 사용 하 여 모델을 배포할 위치를 정의 합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 07/10/2019
ms.openlocfilehash: a7944b284a9c1c0424af54874554d05d49ad4b20
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67806040"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning-service"></a>Azure Machine Learning 서비스에서 계산 대상은 무엇 인가요? 

A **계산 대상** 실행 하는 교육 스크립트 또는 호스트 서비스 배포에 지정 된 계산 리소스/환경입니다. 이 위치는 로컬 컴퓨터 또는 클라우드 기반 계산 리소스 수 있습니다. 사용 하 여 계산 대상을 쉽게 코드를 변경 하지 않고도 계산 환경의 나중에 변경할 수 있습니다.  

일반적인 모델 개발 수명 주기에서 수행할 수 있습니다.
1. 개발 하 고 적은 양의 데이터에서 실험을 시작 합니다. 이 단계에서는 로컬 좋습니다 환경 (로컬 컴퓨터 또는 클라우드 기반 VM) 계산 대상으로 합니다. 
2. 더 큰 데이터를 규모 확장 또는 다음 중 하나를 사용 하 여 교육 분산 수행 [교육 계산 대상을](#train)합니다.  
3. 모델 준비 되 면 웹 호스팅 환경 또는 다음 중 하나를 사용 하 여 IoT 장치에 배포 [목표를 계산 하는 배포](#deploy)합니다.

계산 대상에 대 한 사용 하는 계산 리소스에 연결 되는 [작업 영역](concept-workspace.md)합니다. 계산 영역의 사용자가 로컬 컴퓨터 이외의 리소스를 공유 해야 합니다.

## <a name="train"></a> 교육 계산 대상

Azure Machine Learning 서비스는 다른 계산 리소스에서 다양 한 지원 합니다.  하지만 사용자 고유의 계산 리소스를 연결할 수도 있습니다 다를 수 있습니다 다양 한 시나리오에 대 한 지원.

[!INCLUDE [aml-compute-target-train](../../../includes/aml-compute-target-train.md)]

에 대해 자세히 알아보세요 [설정 및 계산 대상을 사용 하 여 모델 학습을 위한](how-to-set-up-training-targets.md)합니다.

## <a name="deploy"></a>배포 대상

모델 배포를 호스트 하는 다음 계산 리소스를 사용할 수 있습니다.

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

에 대해 알아봅니다 [위치를 계산 대상 모델을 배포 하는 방법과](how-to-deploy-and-where.md)합니다.

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure Machine Learning compute (관리)

관리 되는 계산 리소스를 만들고 Azure Machine Learning 서비스에서 관리 합니다. 이 계산 machine learning 작업을 위한 최적화 됩니다. Azure Machine Learning Compute 2019 년 5 월 30 일 기준으로 관리 되는 유일한 계산 됩니다. 추가 관리 되는 계산 리소스는 나중에 추가할 수 있습니다.

배치 추론 (미리 보기) 및 교육에 대 한 Azure Machine Learning Compute를 사용할 수 있습니다.  이 계산 리소스를 사용 하 여 다음을 수행 해야합니다.

* 단일 또는 다중 node 클러스터
* 자동 크기 조정 실행을 제출할 때마다 
* 자동 클러스터 관리 및 작업 예약 
* CPU와 GPU 리소스에 대한 지원

Azure portal, CLI 또는 SDK를 사용 하 여 Azure Machine Learning Compute 인스턴스를 만들 수 있습니다. 만들 때는 자동으로 일부 다른 종류의 계산 대상 달리 작업 영역입니다.

## <a name="unmanaged-compute"></a>관리 되지 않는 계산

관리 되지 않는 계산 대상이 *되지* Azure Machine Learning 서비스에서 관리 합니다. 이 유형의 Azure Machine Learning을 외부 계산 대상 만든 다음 작업 영역에 연결 합니다. 관리 되지 않는 계산 리소스를 유지 하기 위해 또는 machine learning 워크 로드에 대 한 성능 향상을 위해 추가 단계가 필요할 수 있습니다.

## <a name="next-steps"></a>다음 단계

다음 작업을 수행하는 방법을 배워 보십시오.
* [모델 학습을 위한 계산 대상 설정](how-to-set-up-training-targets.md)
* [계산 대상에 모델 배포](how-to-deploy-and-where.md)