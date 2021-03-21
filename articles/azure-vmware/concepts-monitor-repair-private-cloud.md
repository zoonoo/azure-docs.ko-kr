---
title: 개념-Azure VMware 솔루션 사설 클라우드 모니터링 및 복구
description: Azure vmware 솔루션 사설 클라우드에서 VMware ESXi 서버를 모니터링 하 고 복구 하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.custom: contperf-fy21q2
ms.date: 02/16/2021
ms.openlocfilehash: 59319b5598be9770e82b9676a28444648230a019
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100633141"
---
# <a name="monitor-and-repair-azure-vmware-solution-private-clouds"></a>Azure VMware 솔루션 사설 클라우드 모니터링 및 복구

Azure VMware 솔루션은 Azure VMware 솔루션 사설 클라우드의 VMware ESXi 서버를 지속적으로 모니터링 합니다. 

## <a name="what-azure-vmware-solution-monitors"></a>Azure VMware 솔루션 모니터

Azure VMware 솔루션은 호스트에서 다음 조건을 모니터링 합니다.  

- 프로세서 상태 
- 메모리 상태 
- 연결 및 전원 상태 
- 하드웨어 팬 상태 
- 네트워크 연결 손실 
- 하드웨어 시스템 보드 상태 
- VSAN 호스트의 디스크에서 오류가 발생 했습니다. 
- 하드웨어 전압 
- 하드웨어 온도 상태 
- 하드웨어 전원 상태 
- 저장소 상태 
- 연결 실패 

> [!NOTE]
> Azure VMware 솔루션 테 넌 트 관리자는 vCenter의 Azure VMware 솔루션 제어 평면에서 관리 되므로 위의 정의 된 VMware vCenter 경보를 편집 하거나 삭제 해서는 안 됩니다. 이러한 경보는 azure vmware 솔루션 모니터링에서 Azure VMware 솔루션 호스트 재구성 프로세스를 트리거하는 데 사용 됩니다.

## <a name="azure-vmware-solution-host-remediation"></a>Azure VMware 솔루션 호스트 재구성  

Azure vmware 솔루션이 Azure VMware 솔루션 노드에서 성능 저하 또는 실패를 검색 하면 호스트 재구성 프로세스를 트리거합니다. 호스트 재구성에는 잘못 된 노드를 새로운 정상 노드로 대체 하는 작업이 포함 됩니다.  

호스트 재구성은 클러스터에 새로운 정상 노드를 추가 하 여 시작 됩니다. 그런 다음 가능 하면 잘못 된 호스트를 VMware vSphere 유지 관리 모드로 전환 합니다. VMware vMotion는 오류가 발생 한 호스트의 Vm을 클러스터의 다른 사용 가능한 서버로 이동 하 여 워크 로드의 실시간 마이그레이션에 대 한 가동 중지 시간을 최소화할 수 있습니다. 오류가 있는 호스트를 유지 관리 모드로 전환할 수 없는 경우 호스트는 클러스터에서 제거 됩니다.

## <a name="next-steps"></a>다음 단계

이제 Azure VMware 솔루션에서 사설 클라우드를 모니터링 하 고 복구 하는 방법을 설명 했으므로 다음에 대해 알아볼 수 있습니다.

- [Azure VMware 솔루션 사설 클라우드 업그레이드](concepts-upgrades.md).
- [Azure VMware 솔루션 리소스를 사용 하도록 설정 하는 방법](enable-azure-vmware-solution.md)
