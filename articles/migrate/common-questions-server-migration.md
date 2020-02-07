---
title: Azure Migrate Server 마이그레이션에 대 한 일반적인 질문
description: Azure Migrate Server 마이그레이션에 대 한 일반적인 질문에 대 한 답변 받기
ms.topic: conceptual
ms.date: 02/06/2020
ms.openlocfilehash: bae3447f0fada18de5473e1ef1a1c1d431535f63
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77067384"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Azure Migrate 서버 마이그레이션: 일반적인 질문

이 문서에서는 Azure Migrate에 대 한 일반적인 질문에 답변 합니다. 서버 마이그레이션. 이 문서를 읽은 후 추가 쿼리가 있는 경우 [Azure Migrate 포럼](https://aka.ms/AzureMigrateForum)에 게시 하세요. 다른 질문이 있는 경우 다음 문서를 검토 하세요.

- Azure Migrate에 대 한 [일반적인 질문](resources-faq.md) 입니다.
- Azure Migrate 어플라이언스에 대 한 [질문](common-questions-appliance.md) 입니다.
- 검색, 평가 및 종속성 시각화에 대 한 [질문](common-questions-discovery-assessment.md) 입니다.


## <a name="how-does-agentless-vmware-replication-work"></a>에이전트 없는 VMware 복제는 어떻게 작동 하나요?

VMware에 대 한 에이전트 없는 복제 방법은 VMware 스냅숏과 VMware CBT (변경 된 블록 추적)를 사용 합니다. 사용자가 복제를 시작할 때 초기 복제 주기가 예약 됩니다. 초기 복제 주기에서 VM의 스냅숏을 만들고이 스냅숏을 사용 하 여 Vm Vmdk (디스크)를 복제 합니다. 초기 복제 주기가 완료 되 면 델타 복제 주기가 정기적으로 예약 됩니다. 델타 복제 주기에는 스냅숏이 생성 되며 이전 복제 주기 이후에 변경 된 데이터 블록은 복제 됩니다. VMware 변경 블록 추적은 마지막 주기 이후에 변경 된 블록을 확인 하는 데 사용 됩니다.
정기적 복제 주기 빈도는 동일한 데이터 저장소에서 동시에 복제 되는 다른 v m/디스크의 수에 따라 서비스에 의해 자동으로 관리 되며 이상적인 조건은 VM 당 시간당 1 주기로 수렴 됩니다.

마이그레이션할 때 VM에서 남은 데이터를 캡처하기 위해 주문형 복제 주기가 예약 됩니다. 데이터 손실 및 응용 프로그램 일관성을 보장 하기 위해 마이그레이션의 일부로 VM을 종료 하도록 선택할 수 있습니다.

## <a name="why-is-the-resynchronization-option-not-exposed-in-agentless-stack"></a>다시 동기화 옵션이 에이전트 없는 스택에서 노출 되지 않는 이유는 무엇 인가요?

에이전트 없는 스택에서 모든 델타 주기에서 현재 스냅숏과 이전 스냅숏 간의 차이를 이전 합니다. 스냅숏 간의 차이는 항상 서로 다르므로 데이터를 정리 하는 이점이 있습니다. 즉, 특정 섹터가 스냅숏 간에 ' n ' 번 기록 되 면 마지막 동기화에만 관심이 있으므로 마지막 쓰기를 전송 하기만 하면 됩니다. 모든 쓰기를 추적 하 고 적용 하는 에이전트 기반 스택과는 다릅니다. 즉, 모든 델타 주기는 다시 동기화입니다. 따라서 다시 동기화 옵션이 노출 되지 않습니다. 

디스크가 오류로 인해 동기화 되지 않은 경우 다음 주기에서 수정 됩니다. 

## <a name="what-is-the-impact-of-churn-rate-if-i-use-agentless-replication"></a>에이전트 없는 복제를 사용 하는 경우 변동 률의 영향은 무엇 인가요?

스택은 변동 율을 초과 하는 데이터에 따라 달라 지므로이 스택에서는 변동 패턴이 중요 합니다. 파일이 다시 작성 되는 패턴으로, 많은 영향을 주지 않습니다. 그러나 다른 모든 섹터가 기록 되는 패턴은 다음 주기에 높은 변동 (code churn)을 발생 시킵니다. 전송 되는 데이터의 양을 최소화 하기 때문에 다음 주기를 예약 하기 전에 데이터를 최대한 많이 접을 수 있습니다.  

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>복제 주기는 얼마나 자주 예약 됩니까?

다음 복제 주기를 예약 하는 수식은 (이전 주기 시간/2) 또는 1 시간 중 더 큰 것입니다. 예를 들어 VM에서 델타 주기를 4 시간 동안 걸린 경우 다음 시간이 아닌 2 시간 내에 다음 주기를 예약 합니다. 이는 주기가 IR 바로 뒤에 있을 때 다르며, 여기서 첫 번째 델타 주기를 즉시 예약 합니다.

## <a name="what-is-the-impact-on-performance-of-vcenter-server-or-esxi-host-while-using-agentless-replication"></a>에이전트 없는 복제를 사용 하는 동안 vCenter Server 또는 ESXi 호스트의 성능에 미치는 영향은 무엇입니까?

에이전트 없는 복제는 스냅숏을 사용 하므로 저장소에 대 한 IOPs 사용량이 있으며, 고객은 저장소에 대 한 IOPs 여유를 필요로 합니다. 저장소/IOPs 제한 환경에서는이 스택을 사용 하지 않는 것이 좋습니다.

## <a name="does-agentless-migration-stack-support-migration-of-uefi-vms-to-azure-gen-2-vms"></a>에이전트 없는 마이그레이션 스택은 UEFI Vm을 Azure Gen 2 Vm으로 마이그레이션할 수 있나요?

아니요, Azure Site Recovery를 사용 하 여 이러한 Vm을 Gen 2 Azure Vm으로 마이그레이션해야 합니다. 

## <a name="can-i-pin-my-vms-to-azure-availability-zones-when-i-migrate"></a>마이그레이션할 때 Vm을 Azure 가용성 영역에 고정할 수 있나요?

아니요, Azure 가용성 영역에 대 한 지원은 없습니다.

## <a name="which-transport-protocol-is-used-by-azure-migrate-during-replication"></a>복제 하는 동안 Azure Migrate에서 사용 하는 전송 프로토콜은 무엇 인가요?

Azure Migrate는 SSL 암호화와 함께 NBD (네트워크 블록 장치) 프로토콜을 사용 합니다.

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>마이그레이션에 필요한 최소 vCenter Server 버전은 무엇 인가요?

VCenter Server 5.5 VMware vSphere ESXi 호스트 버전 5.5이 있어야 합니다.

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>고객이 Vm을 관리 되지 않는 디스크로 마이그레이션할 수 있나요?

아니요. Azure Migrate는 관리 디스크 (표준 HDD, 프리미엄 SSD)로만 마이그레이션을 지원 합니다.

## <a name="how-many-vms-can-replicate-simultaneously-using-agentless-vmware-stack"></a>에이전트 없는 VMware stack을 사용 하 여 동시에 복제할 수 있는 Vm 수는 몇 개입니까?

현재 고객은 vCenter Server 당 100 Vm을 동시에 마이그레이션할 수 있습니다. 10 개의 Vm으로 일괄 처리할 수 있습니다.




