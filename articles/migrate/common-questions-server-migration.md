---
title: Azure Migrate Server 마이그레이션에 대 한 일반적인 질문
description: Azure Migrate Server Migration을 사용 하 여 컴퓨터 마이그레이션에 대 한 일반적인 질문에 대 한 답변 받기
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 0c967027457b925b45ea19d994cfadfdbd0b8ab3
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425836"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Azure Migrate 서버 마이그레이션: 일반적인 질문

이 문서에서는 Azure Migrate에 대 한 일반적인 질문에 답변 합니다. 서버 마이그레이션 도구. 이 문서를 읽은 후 궁금한 점이 있으면 다음 문서를 검토 하세요.

- Azure Migrate에 대 한 [일반적인 질문](resources-faq.md) 입니다.
- Azure Migrate 어플라이언스에 대 한 [질문](common-questions-appliance.md) 입니다.
- 검색, 평가 및 종속성 시각화에 대 한 [질문](common-questions-discovery-assessment.md) 입니다.
- [Azure Migrate 포럼](https://aka.ms/AzureMigrateForum)에 질문을 게시 합니다.


## <a name="how-does-agentless-vmware-replication-work"></a>에이전트 없는 VMware 복제는 어떻게 작동 하나요?

VMware에 대 한 에이전트 없는 복제 방법은 VMware 스냅숏과 VMware CBT (변경 된 블록 추적)를 사용 합니다.

1. 복제를 시작할 때 초기 복제 주기가 예약 됩니다. 초기 주기에는 VM의 스냅숏이 사용 됩니다. 이 스냅숏은 Vm Vmdk (디스크)를 복제 하는 데 사용 됩니다. 
2. 초기 복제 주기가 완료 된 후 델타 복제 주기가 정기적으로 예약 됩니다.
    - 델타 복제를 수행 하는 동안 스냅숏이 생성 되며 이전 복제 주기 이후에 변경 된 데이터 블록이 복제 됩니다.
    - VMware CBT는 마지막 주기 이후 변경 된 블록을 확인 하는 데 사용 됩니다.
    - 정기적 복제 주기 빈도는 동일한 데이터 저장소에서 동시에 복제 되는 다른 v m/디스크의 수에 따라 Azure Migrate에 의해 자동으로 관리 됩니다. 이상적인 조건에서 복제는 궁극적으로 각 VM에 대해 시간당 하나의 주기로 수렴 됩니다.

마이그레이션할 때 남은 데이터를 캡처하기 위해 컴퓨터에 대해 주문형 복제 주기가 예약 됩니다. 데이터 손실 및 응용 프로그램 일관성을 보장 하기 위해 마이그레이션 중에 컴퓨터를 종료 하도록 선택할 수 있습니다.

## <a name="why-isnt-resynchronization-exposed"></a>다시 동기화가 노출 되지 않는 이유는 무엇 인가요?

에이전트 없는 마이그레이션을 수행 하는 동안 모든 델타 주기에서 현재 스냅숏과 이전에 만든 스냅숏의 차이가 기록 됩니다. 항상 스냅숏의 차이가 있으므로에서 데이터를 정리 합니다. 따라서 스냅숏 간에 특정 섹터가 N 번 기록 되 면 마지막 동기화에만 관심이 있으므로 마지막 쓰기만 전송 해야 합니다. 이는 모든 쓰기를 추적 하 고 적용 하는 에이전트 기반 복제와 다릅니다. 즉, 모든 델타 주기는 다시 동기화입니다. 따라서 다시 동기화 옵션이 노출 되지 않습니다. 디스크가 오류로 인해 동기화 되지 않으면 다음 주기에서 수정 됩니다. 

## <a name="how-does-churn-rate-impact-agentless-replication"></a>변동 률은 에이전트 없는 복제에 어떤 영향을 미칩니까?

에이전트 없이 복제를 접기 한 후에 변동 패턴이 변동 률 보다 중요 합니다. 다시 파일을 다시 쓰면 속도가 큰 영향을 주지 않습니다. 그러나 다른 모든 섹터가 기록 되는 패턴은 다음 주기에 높은 변동 (code churn)을 발생 시킵니다. 전송 되는 데이터의 양을 최소화 하기 때문에 다음 주기를 예약 하기 전에 데이터를 최대한 많이 접을 수 있습니다.  

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>복제 주기는 얼마나 자주 예약 됩니까?

다음 복제 주기를 예약 하는 수식입니다. (이전 주기 시간/2) 또는 1 시간 중에서 더 큰 쪽입니다.

예를 들어 VM에서 델타 주기를 4 시간으로 사용 하는 경우 다음 주기가 2 시간 내에 예약 되며 다음 시간에는 예약 되지 않습니다. 이는 첫 번째 델타 주기가 즉시 예약 되는 초기 복제 직후에 차이가 있습니다.

## <a name="how-does-agentless-replication-impact-vmware-servers"></a>에이전트 없는 복제는 VMware 서버에 어떤 영향을 미칩니까?

VCenter Server/ESXi 호스트에는 성능에 영향을 미칠 수 있습니다. 에이전트 없는 복제는 스냅숏을 사용 하므로 저장소에서 IOPs를 사용 하 고 일부 IOPS 저장소 대역폭이 필요 합니다. 사용자 환경에서 저장소/IOPs에 대 한 제약 조건이 있는 경우 에이전트 없는 복제를 사용 하지 않는 것이 좋습니다.

## <a name="can-i-do-agentless-migration-of-uefi-vms-to-azure-gen-2"></a>Azure Gen 2로 UEFI Vm의 에이전트 없는 마이그레이션을 수행할 수 있나요?

아니요. Azure Site Recovery를 사용 하 여 이러한 Vm을 Gen 2 Azure Vm으로 마이그레이션합니다. 

## <a name="can-i-pin-vms-to-azure-availability-zones-when-i-migrate"></a>마이그레이션할 때 Vm을 Azure 가용성 영역에 고정할 수 있나요?

아니요, Azure 가용성 영역 지원 되지 않습니다.

## <a name="which-transport-protocol-is-used-by-azure-migrate-during-replication"></a>복제 하는 동안 Azure Migrate에서 사용 하는 전송 프로토콜은 무엇 인가요?

Azure Migrate는 SSL 암호화와 함께 NBD (네트워크 블록 장치) 프로토콜을 사용 합니다.

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>마이그레이션에 필요한 최소 vCenter Server 버전은 무엇 인가요?

VCenter Server 5.5 VMware vSphere ESXi 호스트 버전 5.5이 있어야 합니다.

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>고객이 Vm을 관리 되지 않는 디스크로 마이그레이션할 수 있나요?

아니요. Azure Migrate는 관리 디스크 (표준 HDD, 프리미엄 SSD)로만 마이그레이션을 지원 합니다.

## <a name="how-many-vms-can-i-replicate-together-with-agentless-migration"></a>에이전트 없는 마이그레이션과 함께 복제할 수 있는 Vm 수는 몇 개입니까?

현재 vCenter Server 당 100 Vm을 동시에 마이그레이션할 수 있습니다. 10 개의 Vm을 일괄 처리 하 여 마이그레이션합니다.
 



