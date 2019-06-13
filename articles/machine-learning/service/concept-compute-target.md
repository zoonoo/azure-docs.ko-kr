---
title: 계산 대상
titleSuffix: Azure Machine Learning service
description: 계산 대상을 실행 하는 교육 스크립트 또는 호스트 서비스 배포에 계산 리소스를 지정할 수 있습니다. 이 위치는 로컬 컴퓨터 또는 클라우드 기반 계산 리소스 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/30/2019
ms.openlocfilehash: 42c0f5460a63b781aafdd43410761e2d7b17944d
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2019
ms.locfileid: "66755352"
---
#  <a name="what-is-a-compute-target-in-azure-machine-learning-service"></a>Azure Machine Learning 서비스에서 계산 대상 란? 

계산 대상을 실행 하는 교육 스크립트 또는 호스트 서비스 배포에 계산 리소스를 지정할 수 있습니다. 이 위치는 로컬 컴퓨터 또는 클라우드 기반 계산 리소스 수 있습니다.

계산 대상을 쉽게 코드를 변경 하지 않고 계산 환경을 변경 합니다.  일반적인 모델 개발 수명 주기:

* 개발/실험에 적은 양의 데이터를 사용 하 여 시작 합니다. 이 단계에서는 로컬 환경을 사용하는 것이 좋습니다. 예를 들어 로컬 컴퓨터 또는 클라우드 기반 VM입니다.
* 더 큰 데이터 집합에서 교육을 규모 확장 또는 중 하나를 사용 하 여 교육 분산 수행 합니다 [교육 대상](#train)합니다.  
* 여러 웹 호스팅 환경, 또는 중 하나를 사용 하 여 IoT 장치에 배포 합니다 [배포 대상](#deploy)합니다.

계산 대상에 대 한 사용 하는 계산 리소스에 연결 되는 [작업 영역](concept-workspace.md)합니다. 계산 영역의 사용자가 로컬 컴퓨터 이외의 리소스를 공유 해야 합니다.

## <a name="train"></a> 학습 대상

Azure Machine Learning 서비스는 다른 계산 리소스에서 다양 한 지원 합니다.  다음 설명대로 다양한 시나리오 지원이 달라질 수는 있지만 고유한 컴퓨팅 리소스를 연결할 수도 있습니다.

[!INCLUDE [aml-compute-target-train](../../../includes/aml-compute-target-train.md)]


## <a name="deploy"></a>배포 대상

모델 배포를 호스트 하는 다음 계산 리소스를 사용할 수 있습니다.

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]


## <a name="managed-compute"></a>관리되는 컴퓨팅

관리 되는 계산 리소스를 만들고 Azure Machine Learning 서비스에서 관리 합니다. 이 계산 machine learning 작업을 위한 최적화 됩니다. Azure Machine Learning Compute 2019 년 5 월 30 일 기준으로 관리 되는 유일한 계산 됩니다. 추가 관리 되는 계산 리소스는 나중에 추가할 수 있습니다.

### <a name="amlcompute"></a> Azure Machine Learning Compute

배치 추론 (미리 보기) 및 교육에 대 한 Azure Machine Learning Compute를 사용할 수 있습니다.  이 계산 리소스를 사용 하 여 다음을 수행 해야합니다.

* 단일 또는 다중 node 클러스터
* 자동 크기 조정 실행을 제출할 때마다 
* 자동 클러스터 관리 및 작업 예약 
* CPU와 GPU 리소스에 대한 지원

다음 중 하나를 사용 하 여 Azure Machine Learning Compute 인스턴스를 만들 수 있습니다.

* Azure 포털
* Azure Machine Learning SDK
* Azure CLI

다른 모든 계산 리소스 작업 영역 외부에서 생성 하 고에 연결 해야 합니다.

## <a name="unmanaged-compute"></a>관리 되지 않는 계산

관리 되지 않는 계산 리소스가 *되지* Azure Machine Learning 서비스에서 관리 합니다. 이 유형의 Azure Machine Learning을 외부 계산을 만든 작업 영역에 연결 합니다. 관리 되지 않는 계산 리소스를 유지 하기 위해 또는 machine learning 워크 로드에 대 한 성능 향상을 위해 추가 단계가 필요할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [모델 학습을 위한 계산 대상 설정](how-to-set-up-training-targets.md)
* [Azure Machine Learning 서비스를 사용 하 여 모델 배포](how-to-deploy-and-where.md)