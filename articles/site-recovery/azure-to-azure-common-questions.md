---
title: Azure Site Recovery를 사용한 Azure VM 재해 복구에 대한 일반적인 질문
description: 이 문서에서는 Azure Site Recovery를 사용할 경우의 Azure VM 재해 복구에 대한 일반적인 질문에 답변합니다.
author: sideeksh
manager: rochakm
ms.date: 04/29/2019
ms.topic: conceptual
ms.openlocfilehash: 22848d84896989b1872c55e687c4a5e73da31de8
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86134042"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>일반적인 질문: Azure 간 재해 복구

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md)를 사용하는 경우 다른 Azure 지역으로 Azure VM 재해 복구를 수행할 때 발생하는 일반적인 질문에 답변합니다.

## <a name="general"></a>일반

### <a name="how-is-site-recovery-priced"></a>Site Recovery 비용은 어떻게 책정하는가요?

[VM의 Azure Site Recovery 가격 책정](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/)을 검토하세요.

### <a name="how-does-the-free-tier-for-azure-site-recovery-work"></a>Azure Site Recovery의 무료 계층은 어떻게 작동하나요?

Azure Site Recovery로 보호되는 모든 인스턴스는 처음 31일 동안 무료로 보호됩니다. 이 기간이 지나면 각 인스턴스에 대한 보호에는 [Azure Virtual Machines에 대한 Azure Site Recovery 가격 책정](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/)에 지정된 요금이 부과됩니다.

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>처음 31일 동안 다른 Azure 요금이 부과되나요?

예. 보호된 인스턴스를 처음으로 사용하는 31일 동안 Azure Site Recovery는 무료이지만, Azure Storage, Storage 트랜잭션 및 데이터 전송 요금이 부과될 수 있습니다. 복구된 가상 머신도 Azure Compute 요금이 발생할 수 있습니다. 가격 책정에 대한 자세한 내용은 [Azure Site Recovery 가격 책정](https://azure.microsoft.com/pricing/details/site-recovery)을 참조하세요.

### <a name="what-are-the-best-practices-for-azure-virtual-machines-disaster-recovery"></a>Azure Virtual Machines 재해 복구에 대한 모범 사례는 무엇인가요?

1. [Azure 간 아키텍처 이해](azure-to-azure-architecture.md)
1. [지원되는 구성 및 지원되지 않는 구성 검토](azure-to-azure-support-matrix.md)
1. [Azure VM에 대한 재해 복구 설정](azure-to-azure-how-to-enable-replication.md)
1. [테스트 장애 조치 실행](azure-to-azure-tutorial-dr-drill.md)
1. [주 지역에 대한 장애 조치 및 장애 복구](azure-to-azure-tutorial-failover-failback.md)

### <a name="how-is-capacity-ensured-in-the-target-region"></a>대상 지역에서 용량을 어떻게 보장하나요?

Site Recovery 팀 및 Azure 용량 관리 팀은 충분한 인프라 용량을 계획하고 있습니다. 장애 조치(failover)를 시작할 때 팀은 Site Recovery로 보호되는 VM 인스턴스가 대상 지역에 배포되도록 하는 데도 도움을 줍니다.

## <a name="replication"></a>복제

### <a name="can-i-replicate-vms-enabled-through-azure-disk-encryption"></a>Azure 디스크 암호화를 통해 지원되는 VM을 복제할 수 있나요?

예. Site Recovery는 Azure Disk Encryption을 사용하도록 설정한 VM의 재해 복구를 지원합니다. 복제를 사용하도록 설정하면 Azure는 필요한 모든 디스크 암호화 키와 암호를 원본 지역에서 사용자 컨텍스트의 대상 지역으로 복사합니다. 적절한 권한이 없는 경우 보안 관리자는 스크립트를 사용하여 키와 비밀을 복사할 수 있습니다.

- Site Recovery는 Windows를 실행하는 Azure VM에 대해 Azure Disk Encryption을 지원합니다.
- Site Recovery는 Azure AD(Azure Active Directory)가 필요한 스키마가 있는 Azure Disk Encryption 버전 0.1을 지원합니다. Site Recovery는 Azure AD가 필요하지 않은 버전 1.1도 지원합니다. [Azure Disk Encryption의 확장 스키마에 대해 자세히 알아보세요](../virtual-machines/extensions/azure-disk-enc-windows.md#extension-schema).
  - Azure Disk Encryption 버전 1.1의 경우 Managed Disks가 있는 Windows VM을 사용해야 합니다.
  - 암호화된 VM에 대해 복제를 사용하도록 설정하는 방법을 [자세히 알아보세요](azure-to-azure-how-to-enable-replication-ade-vms.md).

### <a name="can-i-select-an-automation-account-from-a-different-resource-group"></a>다른 리소스 그룹에서 Automation 계정을 선택할 수 있나요?

현재는 포털을 통해 지원 되지 않지만 Powershell을 통해 다른 리소스 그룹에서 Automation 계정을 선택할 수 있습니다.

### <a name="after-specifying-an-automation-account-that-is-in-a-different-resource-group-than-the-vault-am-i-permitted-to-delete-the-runbook-if-there-is-no-other-vault-to-specify"></a>자격 증명 모음과 다른 리소스 그룹에 있는 Automation 계정을 지정한 후 지정할 다른 자격 증명 모음이 없는 경우 runbook을 삭제할 수 있나요?

만든 사용자 지정 runbook은 도구 이며 동일한 항목이 더 이상 필요 하지 않은 경우 삭제 하는 것이 안전 합니다.

### <a name="can-i-replicate-vms-to-another-subscription"></a>VM을 다른 구독에 복제할 수 있나요?

예, Azure VM을 동일한 Azure AD 테넌트의 다른 구독에 복제할 수 있습니다.

복제 시 다른 구독을 선택하여 [구독에서](https://azure.microsoft.com/blog/cross-subscription-dr) 재해 복구를 구성합니다.

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>영역이 고정된 Azure VM을 다른 지역에 복제할 수 있나요?

예, [영역이 고정된 VM](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region)을 다른 지역에 복제할 수 있습니다.

### <a name="can-i-exclude-disks"></a>디스크를 제외할 수 있나요?

예, PowerShell을 사용하여 보호 시 디스크를 제외할 수 있습니다. 자세한 내용은 [복제에서 디스크를 제외하는 방법](azure-to-azure-exclude-disks.md)을 참조하세요.

### <a name="can-i-add-new-disks-to-replicated-vms-and-enable-replication-for-them"></a>복제된 VM에 새 디스크를 추가하고 해당 디스크에 대해 복제를 사용하도록 설정할 수 있나요?

예, 복제된 VM에 새 디스크를 추가하고 이러한 디스크에 대한 복제를 사용하도록 설정하는 것은 Managed Disks가 있는 Azure VM에서 지원됩니다. 복제를 사용하도록 설정한 Azure VM에 새 디스크를 추가하면 VM에 대한 복제 상태가 경고가 표시합니다. 이 경고는 VM에서 하나 이상의 디스크를 보호할 수 있음을 명시합니다. 추가된 디스크에 대해 복제를 사용하도록 설정할 수 있습니다.

- 추가된 디스크에 대한 보호를 사용하도록 설정하면 초기 복제 후에 경고가 사라집니다.
- 디스크에 대해 복제를 사용하도록 설정하지 않은 경우 경고를 해제할 수 있습니다.
- 추가된 디스크 및 복제를 사용하도록 설정한 VM을 장애 조치하는 경우 복제 지점이 있습니다. 복제 지점은 복구에 사용할 수 있는 디스크를 표시합니다.

예를 들어, VM에 단일 디스크가 있고 새 디스크를 추가한다고 가정해 보겠습니다. 디스크를 추가하기 전에 생성된 복제 지점이 있을 수 있습니다. 이 복제 지점이 "디스크 2개 중 1개"로 구성되었다고 표시됩니다.

Site Recovery는 복제된 VM에서 디스크의 "핫 제거"를 지원하지 않습니다. VM 디스크를 제거하는 경우 VM에 대한 복제를 사용하지 않도록 설정했다가 다시 사용하도록 설정해야 합니다.

### <a name="how-often-can-i-replicate-to-azure"></a>Azure에 얼마나 자주 복제할 수 있나요?

Azure VM을 다른 Azure 지역에 복제할 때는 복제가 계속 진행됩니다. 자세한 내용은 [Azure 간 복제 아키텍처](./azure-to-azure-architecture.md#replication-process)를 참조하세요.

### <a name="can-i-replicate-virtual-machines-within-a-region-i-need-this-functionality-to-migrate-vms"></a>한 지역 내에서 가상 머신을 복제할 수 있나요? VM을 마이그레이션하려면 이 기능이 필요합니다.

Azure 간 디스크 복구 솔루션을 사용하여 지역 내에서 VM을 복제할 수는 없습니다.

### <a name="can-i-replicate-vm-instances-to-any-azure-region"></a>VM 인스턴스를 Azure 지역에 복제할 수 있나요?

Site Recovery를 사용하면 동일한 지리적 클러스터 내의 두 지역 간에 VM을 복제 및 복구할 수 있습니다. 지리적 클러스터는 데이터 대기 시간 및 주권에 유의하여 정의됩니다. 자세한 내용은 Site Recovery [지역 지원 행렬](./azure-to-azure-support-matrix.md#region-support)을 참조하세요.

### <a name="does-site-recovery-require-internet-connectivity"></a>Site Recovery에 인터넷 연결이 필요한가요?

아니요, Site Recovery에는 인터넷 연결이 필요하지 않습니다. 하지만 [Azure VM 재해 복구의 네트워킹](./azure-to-azure-about-networking.md#outbound-connectivity-for-urls)에서 설명한 대로 Site Recovery URL 및 IP 범위에 대한 액세스 권한이 필요합니다.

### <a name="can-i-replicate-an-application-that-has-a-separate-resource-group-for-separate-tiers"></a>계층별로 별도의 리소스 그룹이 있는 애플리케이션을 복제할 수 있나요?

예, 애플리케이션을 복제하고 재해 복구 구성을 별도의 리소스 그룹에 유지할 수도 있습니다.

예를 들어, 애플리케이션에서 각 계층 애플리케이션, 데이터베이스 및 웹이 별도의 리소스 그룹에 있는 경우 [복제 마법사](./azure-to-azure-how-to-enable-replication.md#enable-replication)를 3번 선택하여 모든 계층을 보호해야 합니다. Site Recovery는 이러한 세 계층을 세 개의 다른 리소스 그룹에 복제합니다.

### <a name="can-i-move-storage-accounts-across-resource-groups"></a>리소스 그룹에서 스토리지 계정을 이동할 수 있나요?

아니요, 이것은 지원되지 않는 시나리오입니다. 그러나 실수로 스토리지 계정을 다른 리소스 그룹으로 이동하고 원래 리소스 그룹을 삭제하는 경우 이전 리소스 그룹과 같은 이름으로 새 리소스 그룹을 만든 다음, 스토리지 계정을 이 리소스 그룹으로 이동할 수 있습니다.

## <a name="replication-policy"></a>복제 정책

### <a name="what-is-a-replication-policy"></a>복제 정책은 무엇인가요?

복제 정책은 복구 지점의 보존 기록에 대한 설정을 정의합니다. 또한 정책은 앱 일치 스냅샷의 빈도를 정의합니다. 기본적으로 Azure Site Recovery는 다음 기본 설정을 사용하여 새 복제 정책을 만듭니다.

- 복구 지점의 보존 기록의 경우 24시간으로 설정합니다.
- 앱 일치 스냅샷 빈도의 경우 60분으로 설정합니다.

[복제 설정에 대해 자세히 알아보세요](./azure-to-azure-tutorial-enable-replication.md#configure-replication-settings).

### <a name="what-is-a-crash-consistent-recovery-point"></a>크래시 일치 복구 지점은 무엇인가요?

크래시 일치 복구 지점에는 마치 스냅샷 중에 서버에서 전원 코드가 빠진 경우의 디스크 데이터가 있습니다. 크래시 일치 복구 지점에 스냅샷을 만들 때 메모리에 있던 내용은 포함되지 않습니다.

현재 대부분의 애플리케이션은 크래시 일치 스냅샷을 제대로 복구할 수 있습니다. 크래시 일치 복구 지점은 데이터베이스가 없는 운영 체제와 파일 서버, DHCP 서버, 인쇄 서버 등의 애플리케이션에 일반적으로 적합합니다.

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>충돌 일치 복구 지점 생성 빈도는 어느 정도인가요?

Site Recovery는 5분 마다 크래시 일치 복구 지점을 만듭니다.

### <a name="what-is-an-application-consistent-recovery-point"></a>애플리케이션 일치 복구 지점은 무엇인가요?

애플리케이션 일치 복구 지점은 애플리케이션 일치 스냅샷에서 만들어집니다. 애플리케이션 일치 복구 지점은 크래시 일치 스냅샷과 동일한 데이터를 캡처하고 메모리에 있는 데이터와 처리 중인 모든 트랜잭션을 캡처합니다.

추가 콘텐츠로 인해, 애플리케이션 일관성이 있는 스냅샷은 가장 복잡하며 가장 오랜 시간이 걸립니다. 애플리케이션 일치 복구 지점은 데이터베이스 운영 체제와 SQL 서버 등의 애플리케이션에 권장됩니다.

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>애플리케이션 일치 복구 지점이 애플리케이션 성능에 미치는 영향은 무엇입니까?

애플리케이션 일치 복구 지점은 메모리에 있는 데이터와 처리 중인 모든 데이터를 캡처합니다. 복구 지점은 해당 데이터를 캡처하기 때문에 애플리케이션을 정지하려면 Windows에서 볼륨 섀도 복사본 서비스와 같은 프레임워크가 필요합니다. 캡처 프로세스가 빈번하게 발생하는 경우 워크로드가 이미 다른 작업을 진행 중이면 성능에 영향을 줄 수 있습니다. 비데이터베이스 워크로드의 경우 앱 일치 복구 지점을 자주 사용하는 것이 좋습니다. 데이터베이스 워크로드의 경우에도 1시간으로 충분합니다.

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>애플리케이션 일치 복구 지점 생성의 최소 빈도는 어느 정도인가요?

Site Recovery는 최소 1시간의 빈도로 애플리케이션 일치 복구 지점을 생성할 수 있습니다.

### <a name="how-are-recovery-points-generated-and-saved"></a>복구 지점은 어떻게 생성 및 저장되나요?

Site Recovery에서 복구 지점이 생성되는 방식을 이해하기 위해 복제 정책 예제를 살펴보겠습니다. 이 복제 정책에는 24시간 보존 기간 및 1시간의 앱 일치 빈도 스냅샷이 있는 복구 지점이 포함되어 있습니다.

Site Recovery는 5분 마다 크래시 일치 복구 지점을 만듭니다. 사용자가 이 빈도를 변경할 수는 없습니다. 지난 1시간 동안에 대한 12개의 크래시 일치 지점과 1개의 앱 일치 지점 중에서 선택할 수 있습니다. 시간이 지남에 따라 Site Recovery는 마지막 1시간 이전에 만든 모든 복구 지점을 정리하여 시간당 한 개의 복구 지점만 저장합니다.

다음 스크린샷은 예제를 보여줍니다. 스크린샷:

- 지난 1 시간 내에 5분 간격으로 만든 복구 지점이 있습니다.
- 마지막 1시간 이전에 Site Recovery는 1개의 복구 지점만 유지합니다.

   ![생성된 복구 지점 목록](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)

### <a name="how-far-back-can-i-recover"></a>복구할 수 있는 가장 오랜 복구 지점은 어떻게 되나요?

사용할 수 있는 가장 오래된 복구 지점은 72시간입니다.

### <a name="i-have-a-replication-policy-of-24-hours-what-will-happen-if-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>24시간 동안의 복제 정책이 있습니다. 문제가 있어서 Site Recovery에서 24시간을 초과하는 지난 복구 지점을 생성하지 못하는 경우 어떻게 되나요? 이전 복구 지점이 손실되나요?

아니요, Site Recovery는 이전 복구 지점을 모두 유지합니다. 복구 지점의 보존 기간에 따라 Site Recovery는 새 지점을 생성하는 경우에만 가장 오래된 지점을 대체합니다. 문제로 인해 Site Recovery에서 새 복구 지점이 생성할 수 없습니다. 새 복구 지점이 있을 때까지 보존 기간에 도달한 후에도 이전 모든 지점은 유지됩니다.

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>VM에서 복제를 사용하도록 설정한 후 복제 정책을 변경하려면 어떻게 할까요?

**Site Recovery 자격 증명 모음** > **Site Recovery 인프라** > **복제 정책**으로 이동합니다. 편집할 정책을 선택하고 변경 내용을 저장합니다. 변경 내용은 모든 기존 복제에도 적용됩니다.

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>모든 복구 지점이 VM의 전체 복사본인가요, 아니면 차등 복사본인가요?

생성되는 첫 번째 복구 지점에 전체 복사본이 있습니다. 모든 연속 복구 지점에는 델타 변경 내용이 있습니다.

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>복구 지점의 보존 기간을 늘리면 스토리지 비용도 증가하나요?

예, 보존 기간을 24시간에서 72시간으로 늘리면 Site Recovery에서 추가 48시간의 복구 지점을 저장합니다. 추가된 시간에 대해 스토리지 요금이 부과됩니다. 예를 들어, 단일 복구 지점에는 월별 $0.16의 GB당 비용에 해당하는 10GB의 델타 변경이 있을 수 있습니다. 추가 요금은 월별 $1.60×48이 됩니다.

### <a name="can-i-enable-replication-with-app-consistency-in-linux-servers"></a>Linux 서버에서 응용 프로그램 일관성을 사용 하 여 복제를 사용 하도록 설정할 수 있나요?

예. Linux 운영 체제 Azure Site Recovery는 응용 프로그램 일관성을 위한 응용 프로그램 사용자 지정 스크립트를 지원 합니다. 사전 및 사후 옵션을 포함 하는 사용자 지정 스크립트는 앱 일관성을 유지 하는 동안 Azure Site Recovery 모바일 에이전트에서 사용 됩니다. [자세한 정보](./site-recovery-faq.md#can-i-enable-replication-with-app-consistency-in-linux-servers)

## <a name="multi-vm-consistency"></a>다중 VM 일관성

### <a name="what-is-multi-vm-consistency"></a>다중 VM 일관성이란 무엇인가요?

다중 VM 일관성은 복구 지점이 모든 복제된 가상 머신에서 일관되도록 합니다.

Site Recovery는 모든 컴퓨터의 복제 그룹을 만드는 **다중 VM 일관성** 옵션을 제공합니다.

가상 머신을 장애 조치하면 크래시 일치 및 앱 일치 복구 지점을 공유하게 됩니다.

자습서를 통해 [다중 VM 일관성을 사용하도록 설정](./azure-to-azure-tutorial-enable-replication.md#enable-replication-for-a-vm)합니다.

### <a name="can-i-fail-over-a-single-virtual-machine-within-a-multi-vm-consistency-replication-group"></a>다중 VM 일관성 복제 그룹 내에서 단일 가상 머신을 장애 조치(failover)할 수 있나요?

**다중 VM 일관성** 옵션을 선택하면 애플리케이션은 한 그룹 내의 모든 가상 머신에서 종속성을 가지게 됩니다. 단일 가상 머신 장애 조치는 허용되지 않습니다.

### <a name="how-many-virtual-machines-can-i-replicate-as-a-part-of-a-multi-vm-consistency-replication-group"></a>다중 VM 일관성 복제 그룹의 일부분으로 얼마나 많은 가상 머신을 복제할 수 있나요?

복제 그룹에서 16개의 가상 머신을 함께 복제할 수 있습니다.

### <a name="when-should-i-enable-multi-vm-consistency"></a>다중 VM 일관성을 언제 사용하도록 설정해야 하나요?

CPU를 많이 사용하기 때문에 다중 VM 일관성을 사용하도록 설정하면 워크로드 성능에 영향을 줄 수 있습니다. 다중 VM 일관성은 머신이 동일한 워크로드를 실행 중이며 다중 머신에서 일관성이 필요한 경우에만 사용해야 합니다. 예를 들어, 한 애플리케이션에서 두 개의 SQL Server 인스턴스와 두 개의 웹 서버를 사용하는 경우 SQL Server 인스턴스에 대해서만 다중 VM 일관성이 있어야 합니다.

### <a name="can-you-add-an-already-replicating-vm-to-a-replication-group"></a>복제 그룹에 이미 복제 중인 VM을 추가할 수 있나요?
복제를 사용하도록 설정하는 동안 새 복제 그룹에 VM을 추가할 수 있습니다. 복제를 사용하도록 설정하는 동안 기존 복제 그룹에 VM을 추가할 수도 있습니다. 그러나 이미 복제 중인 VM을 새 복제 그룹 또는 기존 복제 그룹에 추가할 수는 없습니다.
 
## <a name="failover"></a>장애 조치


### <a name="how-is-capacity-ensured-in-the-target-region-for-azure-vms"></a>Azure VM의 대상 지역에서 용량을 어떻게 보장하나요?

Site Recovery 팀 및 Azure 용량 관리 팀은 충분한 인프라 용량을 계획하고 있습니다. 장애 조치(failover)를 시작할 때 팀은 Site Recovery로 보호되는 VM 인스턴스가 대상 지역에 배포되도록 하는 데도 도움을 줍니다.

### <a name="is-failover-automatic"></a>장애 조치(failover)는 자동입니까?

자동이 아닙니다. 포털에서 클릭 한 번으로 장애 조치를 시작하거나 [PowerShell](azure-to-azure-powershell.md)을 사용하여 장애 조치를 트리거할 수 있습니다.

### <a name="can-i-keep-a-public-ip-address-after-a-failover"></a>장애 조치 후 퍼블릭 IP 주소를 유지할 수 있나요?

장애 조치 후에는 프로덕션 애플리케이션의 퍼블릭 IP 주소를 유지할 수 없습니다.

장애 조치 프로세스의 일부로 워크로드를 가져오는 경우 워크로드에 Azure 퍼블릭 IP 리소스를 할당해야 합니다. Azure 퍼블릭 IP 리소스는 대상 지역에서 사용할 수 있어야 합니다. Azure 퍼블릭 IP 리소스를 수동으로 할당하거나 복구 계획을 사용하여 자동화할 수 있습니다. [장애 조치 후 퍼블릭 IP 주소 설정](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan) 방법을 알아보세요.

### <a name="can-i-keep-a-private-ip-address-during-a-failover"></a>장애 조치 중에 프라이빗 IP 주소를 유지할 수 있나요?

예, 프라이빗 IP 주소를 유지할 수 있습니다. 기본적으로 Azure VM에 재해 복구를 사용하도록 설정하면 Site Recovery는 원본 리소스 설정에 따라 대상 리소스를 만듭니다. 고정 IP 주소를 사용하여 구성된 Azure Virtual Machines의 경우 Site Recovery는 대상 VM에 동일한 IP 주소(사용 중이 아닌 경우)를 프로비저닝하려고 시도합니다.
[장애 조치 동안 IP 주소 유지](site-recovery-retain-ip-azure-vm-failover.md)에 대해 알아봅니다.

### <a name="after-a-failover-why-is-the-server-assigned-a-new-ip-address"></a>장애 조치 후에 서버에 새 IP 주소가 할당되는 이유는 무엇인가요?

Site Recovery는 장애 조치(failover) 시 IP 주소를 제공하려고 시도합니다. 다른 가상 머신에서 해당 IP 주소를 가져가는 경우 Site Recovery는 대상으로 사용 가능한 다음 IP 주소를 설정합니다.

[가상 네트워크에 대한 네트워크 매핑 및 IP 주소 지정 설정](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms)에 대해 자세히 알아보세요.

### <a name="what-are-latest-lowest-rpo-recovery-points"></a>**최신(가장 낮은 RPO)** 복구 지점은 무엇인가요?

**최신(가장 낮은 RPO)** 옵션은 먼저 Site Recovery에 전송된 모든 데이터를 처리합니다. 서비스는 데이터를 처리한 후 VM으로 장애 조치하기 전에 각 VM에 대한 복구 지점을 만듭니다. 이 옵션은 가장 낮은 RPO(복구 지점 목표)를 제공합니다. 장애 조치 후 생성된 VM의 모든 데이터는 장애 조치가 트리거될 때 Site Recovery로 복제됩니다.

### <a name="do-latest-lowest-rpo-recovery-points-have-an-impact-on-failover-rto"></a>**최신(가장 낮은 RPO)** 복구 지점이 장애 조치(failover) RTO에 영향을 주나요?

예. Site Recovery는 장애 조치 전에 모든 보류 중인 데이터를 처리하므로, 이 옵션은 다른 옵션에 비해 RTO(복구 시간 목표)가 더 높습니다.

### <a name="what-does-the-latest-processed-option-in-recovery-points-mean"></a>복구 지점에서 **가장 최근 처리됨** 옵션은 무엇을 의미하나요?

**가장 최근에 처리됨** 옵션은 계획 내의 모든 VM을 Site Recovery에서 처리한 최신 복구 지점으로 장애 조치합니다. 특정 VM에 대한 최신 복구 지점을 보려면 VM 설정에서 **최신 복구 지점**을 선택하세요. 이 옵션은 처리되지 않은 데이터를 처리하는 데 시간이 투입되지 않으므로 낮은 RTO를 제공합니다.

### <a name="what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>주 지역에서 예기치 않은 중단이 발생하면 어떻게 되나요?

중단 후에 장애 조치(failover)를 트리거할 수 있습니다. Site Recovery는 주 지역에서의 연결이 없어도 장애 조치를 수행할 수 있습니다.

### <a name="what-is-an-rto-of-a-vm-failover"></a>VM 장애 조치의 RTO는 무엇인가요?

Site Recovery의 [RTO SLA는 2시간](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/)입니다. 그러나 대부분의 경우 Site Recovery는 몇 분 내에 가상 머신을 장애 조치합니다. VM이 작동할 때까지 걸린 시간이 표시되는 장애 조치 작업으로 이동하여 RTO를 계산할 수 있습니다. 복구 계획 RTO의 경우 다음 섹션을 참조하세요.

## <a name="recovery-plans"></a>복구 계획

### <a name="what-is-a-recovery-plan"></a>복구 플랜은 무엇인가요?

Site Recovery의 복구 플랜은 VM의 장애 조치(failover) 복구를 조정합니다. 복구 플랜을 통해 복구가 일관성 있게 정확하고, 반복 가능하며, 자동화되도록 유지할 수 있습니다. 복구 플랜은 다음과 같은 요구 사항을 처리합니다.

- 함께 장애 조치(failover)되는 가상 머신의 그룹 정의
- 애플리케이션이 정확하게 작동하도록 가상 머신 간의 종속성 정의
- 가상 머신 장애 조치(failover) 이외의 작업도 수행할 수 있도록 사용자 지정 수동 작업과 함께 복구 자동화

[복구 계획 만들기에 대한](site-recovery-create-recovery-plans.md) 자세한 정보

### <a name="how-is-sequencing-achieved-in-a-recovery-plan"></a>복구 플랜에서 순서를 지정하려면 어떻게 하나요?

복구 플랜에서 여러 개의 그룹을 만들어 순서를 지정할 수 있습니다. 모든 그룹을 한 번에 장애 조치합니다. 함께 장애 조치된 동일한 그룹의 일부인 가상 머신은 다른 그룹 다음에 옵니다. 복구 계획을 사용하여 애플리케이션을 모델링하는 방법을 알아보려면 [복구 계획](recovery-plan-overview.md#model-apps)을 참조하세요.

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>복구 플랜의 RTO를 확인하려면 어떻게 할까요?

복구 플랜의 RTO를 확인하려면 복구 플랜을 테스트 장애 조치(failover)를 수행하고 **Site Recovery 작업**으로 이동합니다.
다음 예제에서는 작업 **SAPTestRecoveryPlan**을 참조하세요. 작업은 모든 가상 머신을 장애 조치하고 지정된 작업을 수행하는 데 8분 59초가 걸렸습니다.

![Site Recovery 작업 목록](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>복구 플랜에 자동화 Runbook을 추가할 수 있나요?

예, Azure Automation Runbook을 복구 플랜에 통합할 수 있습니다. [Azure Automation Runbook 추가](site-recovery-runbook-automation.md)에 대해 자세히 알아봅니다.

## <a name="reprotection-and-failback"></a>다시 보호 및 장애 복구

### <a name="i-failed-over-from-the-primary-region-to-a-disaster-recovery-region-are-vms-in-a-dr-region-protected-automatically"></a>주 지역에서 재해 복구 지역으로 장애 조치했습니다. DR 지역의 VM이 자동으로 보호되나요?

아니요. Azure VM을 한 지역에서 다른 지역으로 [장애 조치(failover)](./azure-to-azure-tutorial-failover-failback.md)할 경우 VM은 DR 지역에서 보호되지 않는 상태로 부팅됩니다. VM을 주 지역으로 장애 복구(failback)하려면 보조 지역에서 VM을 [다시 보호](./azure-to-azure-how-to-reprotect.md)해야 합니다.

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-the-secondary-region-to-the-primary-region"></a>다시 보호 시 Site Recovery에서 보조 지역의 전체 데이터를 주 지역에 복제하나요?

상황에 따라 다릅니다. 원본 지역 VM이 있는 경우 원본 디스크와 대상 디스크 사이의 변경 내용만 동기화됩니다. Site Recovery는 디스크를 비교하여 차등을 계산한 다음, 데이터를 전송합니다. 이 프로세스는 일반적으로 몇 시간 정도 걸립니다. 다시 보호 중 발생하는 작업에 대한 자세한 내용은 [주 지역으로 장애 조치된 Azure VM 인스턴스 다시 보호](./azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection)를 참조하세요.

### <a name="how-much-time-does-it-take-to-fail-back"></a>장애 복구(failback)하는 데 시간이 얼마나 걸리나요?

다시 보호 이후의 장애 조치도 주 지역에서 보조 지역으로 장애 조치하는 데 걸리는 시간과 동일하게 소요됩니다.

## <a name="capacity"></a><a name="capacity"></a>용량

### <a name="how-is-capacity-ensured-in-the-target-region-for-azure-vms"></a>Azure VM의 대상 지역에서 용량을 어떻게 보장하나요?

Site Recovery 팀 및 Azure 용량 관리 팀은 충분한 인프라 용량을 계획하고 있습니다. 장애 조치(failover)를 시작할 때 팀은 Site Recovery로 보호되는 VM 인스턴스가 대상 지역에 배포되도록 하는 데도 도움을 줍니다.

### <a name="does-site-recovery-work-with-reserved-instances"></a>Site Recovery를 예약 인스턴스에 사용할 수 있나요?

예, 재해 복구 지역에서 [예약된 Azure VM](https://azure.microsoft.com/pricing/reserved-vm-instances/)을 구매할 수 있으며, Site Recovery 장애 조치 작업에서 이 VM을 사용합니다. 추가 구성은 필요하지 않습니다.

## <a name="security"></a>보안

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>복제 데이터가 Site Recovery 서비스로 전송되나요?

아니요, Site Recovery는 복제된 데이터를 가로채지 않으며, VM에서 실행되는 항목에 대한 정보를 갖지 않습니다. 복제 오케스트레이션 및 장애 조치(failover)에 필요한 메타데이터만 Site Recovery 서비스로 전송됩니다.

Site Recovery는 ISO 27001:2013, 27018, HIPAA 및 DPA 인증을 받았습니다. 이 서비스에 대해 SOC2 및 FedRAMP JAB 평가가 진행 중입니다.

### <a name="does-site-recovery-encrypt-replication"></a>Site Recovery는 복제를 암호화합니까?

예, 전송 중 암호화 및 [Azure의 미사용 데이터 암호화](../storage/common/storage-service-encryption.md)가 모두 지원됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure 간 지원 요구 사항을 검토합니다](azure-to-azure-support-matrix.md).
- [Azure 간 복제를 설정합니다](azure-to-azure-tutorial-enable-replication.md).
- 이 문서를 읽은 후 질문이 있으면 [Azure Recovery Services에 대한 Microsoft Q&A 질문 페이지](/answers/topics/azure-site-recovery.html)에 게시해 주세요.
