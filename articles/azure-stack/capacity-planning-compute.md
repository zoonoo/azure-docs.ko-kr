---
title: Azure Stack에 대 한 계획 계산 용량 | Microsoft Docs
description: Azure Stack 배포에 대 한 계획 계산 용량에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2018
ms.author: jeffgilb
ms.reviewer: prchint
ms.custom: mvc
ms.openlocfilehash: b98879483d35a91810c9e9ab5e0ac81151bde52f
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46369072"
---
# <a name="azure-stack-compute-capacity-planning"></a>Azure Stack 계산 용량 계획
합니다 [Azure Stack에서 지 원하는 VM 크기](.\user\azure-stack-vm-sizes.md) 기능은 Azure에서 지원 되는 하위 집합입니다. Azure는 과도 한 리소스 (로컬 및 서비스 수준에는 서버)를 방지 하려면 많은 벡터를 따라 리소스 제한을 둡니다. 테 넌 트 사용량에 대해 몇 가지 제한 없이, 다른 테 넌 트 리소스 overconsume 때 테 넌 트 환경을 저하 됩니다. VM에서 네트워크 송신에 대 한 Azure 제한 사항에 맞는 Azure Stack에서 대역폭 caps 있습니다. 저장소 리소스에 대 한 저장소 IOPs 한도 기본 과도 한 리소스를 방지 하려면 Azure Stack에서 저장소 액세스에 대 한 테 넌 트에서 구현 되었습니다.  

## <a name="vm-placement-and-virtual-to-physical-core-overprovisioning"></a>VM 배치 및 실제 가상 코어 오버 프로 비전
Azure Stack에서 VM 배치에 사용할 특정 서버를 지정 하는 테 넌 트에 대 한 없습니다 방법이 있습니다. 해당 VM 형식에 대 한 호스트에서 충분 한 메모리가 있는지는 Vm을 배치 하는 경우만 고려 합니다. Azure Stack 메모리를 과도 하 게 되지 않습니다. 그러나 코어 수가 오버 커밋을 허용 됩니다. 기존 물리적 코어 단계로 비율 과도 프로 비전 가상 배치 알고리즘 확인 하지 않습니다, 이후 다른 비율 각 호스트에 있을 수 있습니다. 

Azure에서 여러 오류 도메인 간에 분산 하기 위해 가용성 집합에 다중 VM 프로덕션 시스템의 고가용성을 달성 하기 위해 Vm 배치 됩니다. 가용성 집합에 배치 하는 Vm에서 다음 다이어그램에 나와 있는 것 처럼 실패 복원 력을 위해 있도록 랙 서로 물리적으로 격리 되어 있는지를 의미 합니다.

![장애 및 업데이트 도메인](media\azure-stack-capacity-planning\domains.png)


Azure stack 인프라 오류 시 복원할 때 기본 기술 (장애 조치 클러스터링) 여전히 일부 가동 중지 시간이 발생 Vm에 대 한 영향을 받는 실제 서버의 하드웨어 고장 시. 현재 Azure Stack 있는 가용성 Azure를 사용 하 여 일치 하는 최대 3 개의 장애 도메인을 사용 하 여 집합을 지원 합니다. Vm 가용성 집합에 배치는 여러 장애 도메인 (Azure Stack 노드)에 해당 작업을 최대한 균등 하 게 분산 하 여 물리적으로 서로 격리 됩니다. 하드웨어 오류가 있으면 실패 한 장애 도메인에서 Vm는 다른 노드를 다시 시작 되지만, 동일한 가용성 집합의 다른 Vm에서 별도 장애 도메인에서 유지 가능한 경우. 하드웨어 상태가 다시 온라인 상태가 되 면 고가용성을 유지 하기 위해 Vm 부하가 다시 분산 됩니다.

가용성 집합의 업데이트 도메인의 형태로 Azure에서 고가용성을 제공 하는 데 사용 되는 또 다른 개념 이며 업데이트 도메인은 동시에 유지 관리를 진행하거나 다시 부팅될 수 있는 기본 하드웨어의 논리적 그룹입니다. Azure Stack에서 Vm은 라이브 클러스터에 있는 다른 온라인 호스트 간에 마이그레이션되는 해당 기본 호스트를 업데이트 하기 전에 합니다. 호스트 업데이트 중에 가동 중지 시간 없이 테 넌 트 이므로 Azure Stack의 업데이트 도메인 기능 Azure 사용 하 여 템플릿 호환성을 위해서만 존재 합니다.

## <a name="azure-stack-resiliency-resources"></a>Azure Stack 복원 력 리소스
패치 및 Azure Stack의 업데이트에 대 한 허용 하도록 시스템을 통합 하 고 물리적 하드웨어 오류에 탄력적으로 대처할 수, 총 서버 메모리의 일부를 예약 하 고 테 넌 트 가상 컴퓨터 (VM) 배치에 사용할 수 없습니다.

서버에서 오류가 발생 하는 경우 VM 가용성을 제공 하기 위해 나머지에서 사용 가능한 서버에 실패 한 서버에 호스트 된 Vm 다시 시작 됩니다. 마찬가지로, 패치 및 업데이트 프로세스 중 서버에서 실행 되는 모든 Vm은 수 라이브 다른 사용 가능한 서버로 마이그레이션된 합니다. 이 VM 관리 또는 이동 되려면 다시 시작 또는 마이그레이션할 수 있도록 예약 된 용량이 있는 경우만 구현할 수 있습니다.

다음 계산을 테 넌 트 VM 배치에 사용할 수 있는 총, 사용 가능한 메모리에 발생 합니다. Azure Stack 배율 단위 전체에 대 한이 메모리 용량은입니다.

  VM 배치에 사용 가능한 메모리가 총 서버 메모리-복원 력 예약 – Azure Stack 인프라 오버 헤드를 = <sup>1</sup>

  복원 력 예약 = H + R * (N-1) + V * (N-2)

> 위치:
> - H = 단일 서버 메모리의 크기
> - N = 크기의 규모 단위 (서버의 수)
> - R = OS 오버 헤드에 대 한 운영 체제 예약<sup>2</sup>
> - V = 배율 단위에 가장 큰 VM

  <sup>1</sup> azure Stack 인프라 오버 헤드 208 GB =

  <sup>2</sup> 오버 헤드에 대 한 운영 체제 예약 = 노드 메모리의 15%입니다. 운영 시스템 예약 값은 예상 값 이며 서버 및 일반 운영 체제 오버 헤드의 실제 메모리 용량에 따라 달라 집니다.

값 V를 배율 단위에서 가장 큰 VM은 동적으로 가장 큰 테 넌 트 VM 메모리 크기 기반으로 합니다. 예를 들어, 7GB 112GB 또는 다른 지원 되는 VM 메모리 크기는 Azure Stack 솔루션의 가장 큰 VM 값을 수 있습니다.

위의 계산은 예상 값 이며 현재 버전의 Azure Stack을 기반으로 변경 합니다. 테 넌 트 Vm 및 서비스를 배포 하는 기능이 배포 된 솔루션의 세부 정보를 기반으로 합니다. 이 예제에서는 계산 가이드 뿐 이며 Vm을 배포 하는 기능 절대 응답 하지 않습니다.



## <a name="next-steps"></a>다음 단계
[저장소 용량 계획](capacity-planning-storage.md)
