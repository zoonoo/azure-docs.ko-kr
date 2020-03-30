---
title: Azure 마이그레이션 서버 마이그레이션 FAQ
description: Azure 마이그레이션 서버 마이그레이션을 사용하여 컴퓨터를 마이그레이션하는 데 사용하는 것에 대한 일반적인 질문에 대한 답변을 얻을 수 있습니다.
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 507cc8088bf54b1a4f4483673ec5332efcdd36c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127811"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Azure 마이그레이션 서버 마이그레이션: 일반적인 질문

이 문서에서는 Azure 마이그레이션: 서버 마이그레이션 도구에 대한 일반적인 질문에 대해 답변합니다. 다른 질문이 있는 경우 다음 리소스를 확인하십시오.

- Azure 마이그레이션에 대한 [일반적인 질문](resources-faq.md)
- [Azure 마이그레이션 어플라이언스에](common-questions-appliance.md) 대한 질문
- [검색, 평가 및 종속성 시각화에](common-questions-discovery-assessment.md) 대한 질문
- [Azure 마이그레이션 포럼에서](https://aka.ms/AzureMigrateForum) 질문에 대한 답변 받기

## <a name="how-does-agentless-vmware-replication-work"></a>에이전트없는 VMware 복제는 어떻게 작동합니까?

VMware에 대한 에이전트 없는 복제 방법은 VMware 스냅숏과 VMware 변경 된 블록 추적(CBT)을 사용합니다.

프로세스는 다음과 같습니다.

1. 복제를 시작하면 초기 복제 주기가 예약됩니다. 초기 주기에서 VM의 스냅숏이 생성됩니다. 스냅숏은 VM VMDK(디스크)를 복제하는 데 사용됩니다. 
2. 초기 복제 주기가 끝나면 델타 복제 주기가 주기적으로 예약됩니다.
    - 델타 복제 중에 스냅숏이 생성되고 이전 복제 주기 이후 변경된 데이터 블록이 복제됩니다.
    - VMware CBT는 마지막 주기 이후 변경된 블록을 확인하는 데 사용됩니다.
    - 주기적인 복제 주기의 빈도는 Azure Migrate에서 자동으로 관리되며 동일한 데이터스토어에서 동시에 복제되는 다른 VM 및 디스크 수에 따라 달라집니다. 이상적인 조건에서 복제는 결국 각 VM에 대해 시간당 1사이클로 수렴됩니다.

마이그레이션할 때 컴퓨터가 나머지 데이터를 캡처하도록 온디맨드 복제 주기가 예약됩니다. 데이터 손실 및 응용 프로그램 일관성을 높이려면 마이그레이션 하는 동안 컴퓨터를 종료 하도록 선택할 수 있습니다.

## <a name="why-isnt-resynchronization-exposed"></a>재동기화가 노출되지 않는 이유는 무엇입니까?

에이전트 없는 마이그레이션 하는 동안 모든 델타 주기에서 현재 스냅숏과 이전에 찍은 스냅숏 간의 차이가 기록 됩니다. 스냅샷, 폴딩 데이터 의 차이는 항상 차이가 있습니다. 스냅샷 사이에 특정 섹터가 *N번* 기록된 경우 마지막 동기화에만 관심이 있으므로 마지막 쓰기만 전송하면 됩니다. 이 프로세스는 모든 쓰기를 추적하고 적용하는 에이전트 기반 복제와 는 다릅니다. 이 프로세스에서 모든 델타 주기는 다시 동기화됩니다. 따라서 재동기화 옵션이 노출되지 않습니다. 장애로 인해 디스크가 동기화되지 않으면 다음 주기에 고정됩니다. 

## <a name="how-does-churn-rate-affect-agentless-replication"></a>이탈률은 에이전트 없는 복제에 어떤 영향을 미칩니까?

에이전트 없는 복제가 데이터에서 접기 때문에 *이탈 률보다 변동 패턴이* 더 *중요합니다.* 파일을 반복해서 작성하면 속도가 큰 영향을 미치지 않습니다. 그러나 다른 모든 섹터가 기록되는 패턴은 다음 주기에서 높은 이탈을 일으킵니다. 전송하는 데이터의 양을 최소화하기 때문에 다음 주기를 예약하기 전에 데이터를 가능한 한 많이 접을 수 있습니다.  

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>복제 주기는 얼마나 자주 예약되나요?

다음 복제 주기를 예약하는 수식은 (이전 주기 시간/2) 또는 1시간 중 더 높은 값입니다.

예를 들어, VM이 델타 주기에 4시간이 걸리는 경우 다음 주기는 다음 시간이 아닌 2시간으로 예약됩니다. 첫 번째 델타 주기가 즉시 예약되는 초기 복제 직후에 프로세스가 다릅니다.

## <a name="how-does-agentless-replication-affect-vmware-servers"></a>에이전트 없는 복제는 VMware 서버에 어떤 영향을 미칩니까?

에이전트 없는 복제는 VMware vCenter 서버 및 VMware ESXi 호스트에 일부 성능 영향을 미칩니다. 에이전트 없는 복제는 스냅숏을 사용하기 때문에 저장소에서 IOPS를 사용하므로 일부 IOPS 저장소 대역폭이 필요합니다. 사용자 환경에서 저장소 또는 IoP에 제약 조건이 있는 경우 에이전트 없는 복제를 사용하지 않는 것이 좋습니다.

## <a name="can-i-do-agentless-migration-of-uefi-vms-to-azure-gen-2"></a>UEFI VM을 Azure Gen 2로 에이전트 없이 마이그레이션할 수 있습니까?

아니요. Azure 사이트 복구를 사용하여 이러한 VM을 2세대 Azure VM으로 마이그레이션합니다. 

## <a name="can-i-pin-vms-to-azure-availability-zones-when-i-migrate"></a>마이그레이션할 때 VM을 Azure 가용성 영역에 고정할 수 있습니까?

아니요. Azure 사용 가능 영역은 Azure 마이그레이션 마이그레이션에 대해 지원되지 않습니다.

## <a name="what-transport-protocol-does-azure-migrate-use-during-replication"></a>복제 중에 Azure 마이그레이션에서 사용하는 전송 프로토콜은 무엇입니까?

Azure 마이그레이션은 SSL 암호화를 사용하여 NBD(네트워크 블록 장치) 프로토콜을 사용합니다.

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>마이그레이션에 필요한 최소 vCenter 서버 버전은 무엇입니까?

vCenter 서버 5.5 및 vSphere ESXi 호스트 버전 5.5 이상이 있어야 합니다.

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>고객이 VM을 관리되지 않는 디스크로 마이그레이션할 수 있습니까?

아니요. Azure Migrate는 관리되는 디스크(표준 HDD, 프리미엄 SSD)로만 마이그레이션을 지원합니다.

## <a name="how-many-vms-can-i-replicate-at-one-time-by-using-agentless-migration"></a>에이전트 없는 마이그레이션을 사용하여 한 번에 복제할 수 있는 VM수는 몇 개입니까?

현재 vCenter 서버 인스턴스당 100개의 VM을 동시에 마이그레이션할 수 있습니다. 10개의 VM을 일괄으로 마이그레이션합니다.

## <a name="how-do-i-throttle-replication-in-using-azure-migrate-appliance-for-agentless-vmware-replication"></a>에이전트 없는 VMware 복제에 Azure Migrate 어플라이언스를 사용하여 복제를 제한하려면 어떻게 해야 합니까?  

NetQosPolicy를 사용하여 스로틀을 할 수 있습니다. 예를 들어:

NetQosPolicy에서 사용할 앱네임프리픽은 "게이트웨이윈도우서비스.exe"입니다. Azure Migrate 어플라이언스에서 다음과 같은 정책을 만들어 어플라이언스에서 복제 트래픽을 제한하기 위한 정책을 만들 수 있습니다.
 
New-NetQosPolicy -이름 "스로틀 복제" -AppPathNameMatchCondition "게이트웨이윈도우서비스.exe" -ThrottleRateActionBitsPerSecond 1MB

## <a name="when-do-i-migrate-machines-as-physical-servers"></a>컴퓨터를 물리적 서버로 마이그레이션하는 시기는 언제입니까?

물리적 서버로 처리하여 머신을 마이그레이션하는 것은 다음과 같은 다양한 시나리오에서 유용합니다.

- 온-프레미스 물리적 서버를 마이그레이션할 때
- Xen, KVM 과 같은 플랫폼에서 가상화된 VM을 마이그레이션하는 경우
- 어떤 이유로 하이퍼-V 또는 VMware 마이그레이션에 대 한 표준 마이그레이션 프로세스를 사용할 수 없는 경우 [Hyper-V](tutorial-migrate-hyper-v.md)또는 [VMware VM을](server-migrate-overview.md) 마이그레이션합니다. 예를 들어 VMware vCenter를 실행하지 않고 ESXi 호스트만 사용하는 경우입니다.
- 현재 프라이빗 클라우드에서 실행 중인 VM을 Azure로 마이그레이션하려면
- Amazon 웹 서비스(AWS) 또는 GCP(구글 클라우드 플랫폼)와 같은 퍼블릭 클라우드에서 실행되는 VM을 Azure로 마이그레이션하려는 경우

## <a name="do-i-need-vmware-vcenter-to-migrate-vmware-vms"></a>VMware VM을 마이그레이션하려면 VMware vCenter가 필요합니까?
VMware 에이전트 기반 또는 에이전트 없는 마이그레이션을 사용하여 [VMware VM을 마이그레이션하려면](server-migrate-overview.md) VM이 있는 ESXi 호스트는 vCenter Server에서 관리해야 합니다. vCenter 서버가 없는 경우 VMware VM을 실제 서버로 마이그레이션하여 마이그레이션할 수 있습니다. [자세히 알아봅니다](migrate-support-matrix-physical-migration.md).
 
## <a name="next-steps"></a>다음 단계

Azure [마이그레이션 개요를](migrate-services-overview.md)읽습니다.
