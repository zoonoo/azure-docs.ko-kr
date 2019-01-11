---
title: 일반적인 질문 - Azure Site Recovery를 사용하는 Azure 간 재해 복구 | Microsoft Docs
description: 이 문서에는 Azure Site Recovery를 사용하여 다른 Azure 지역으로 Azure VM의 재해 복구를 설정할 때 발생하는 일반적인 질문이 요약되어 있습니다.
author: asgang
manager: rochakm
ms.service: site-recovery
ms.date: 12/12/2018
ms.topic: conceptual
ms.author: asgang
ms.openlocfilehash: 6fe7152e43640a809ab9f4de39b1c6b599975a20
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/02/2019
ms.locfileid: "53969950"
---
# <a name="common-questions---azure-to-azure-replication"></a>일반적인 질문 - Azure 간 복제

이 문서에서는 Azure VM의 재해 복구를 다른 Azure 지역에 배포할 때 발생하는 일반적인 질문에 대한 답변을 제공합니다. 이 문서를 읽은 후 질문이 있다면 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)에 게시해 주세요.


## <a name="general"></a>일반
### <a name="how-is-site-recovery-priced"></a>Site Recovery 비용은 어떻게 책정하는가요?
[Azure Site Recovery 가격](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) 세부 정보를 검토하세요.

### <a name="how-to-configure-site-recovery-on-azure-vms-what-are-the-best-practices"></a>Azure VM에서 Site Recovery를 구성하는 방법입니다. 모범 사례는 무엇인가요?
1. [Azure 간 아키텍처 이해](azure-to-azure-architecture.md)
2. [지원되는 구성 및 지원되지 않는 구성 검토](azure-to-azure-support-matrix.md)
3. [Azure VM에 대한 재해 복구 설정](azure-to-azure-how-to-enable-replication.md)
4. [테스트 장애 조치 실행](azure-to-azure-tutorial-dr-drill.md)
5. [장애 조치(failover) 및 주 지역으로 장애 복구(failback)](azure-to-azure-tutorial-failover-failback.md)

## <a name="replication"></a>복제

### <a name="can-i-replicate-azure-disk-encryption-enabled-vms"></a>Azure Disk Encryption 지원 VM을 복제할 수 있나요?
예, 복제할 수 있습니다. ADE(Azure Disk Encryption) 지원 VM의 복제를 사용하려면 [문서](azure-to-azure-how-to-enable-replication-ade-vms.md)를 참조하세요. Azure Site Recovery에서는 Windows OS에서 실행 중이며 Azure AD 앱을 통한 암호화가 가능한 Azure VM만 지원됩니다.

### <a name="can-i-replicate-vms-to-another-subscription"></a>VM을 다른 구독에 복제할 수 있나요?
예, Azure VM을 동일한 Azure Active Directory 테넌트의 다른 구독에 복제할 수 있습니다.
[구독](https://azure.microsoft.com/blog/cross-subscription-dr) 간에 DR을 구성하는 것은 간단합니다. 복제 시 다른 구독을 선택하면 됩니다.

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>영역이 고정된 Azure VM을 다른 지역에 복제할 수 있나요?
예, [영역이 고정된 VM을 다른 지역에 복제](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region)할 수 있습니다.

### <a name="can-i-exclude-disks"></a>디스크를 제외할 수 있나요?

예, PowerShell을 사용하여 보호 시 디스크를 제외할 수 있습니다. 디스크를 제외하려면 [PowerShell 지침](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#replicate-azure-virtual-machine)을 참조하세요.

###<a name="how-often-can-i-replicate-to-azure"></a>Azure에 얼마나 자주 복제할 수 있나요?
Azure VM을 다른 Azure 지역에 복제하는 경우에는 복제가 연속적입니다. 자세한 내용을 파악하려면 [Azure 간](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-architecture#replication-process) 복제 아키텍처를 확인하세요.

### <a name="can-i-replicate-virtual-machines-within-a-same-region-i-need-this-to-migrate-vms"></a>동일한 지역 내에서 가상 머신을 복제할 수 있나요? VM을 마이그레이션하기 위해 이 작업이 필요한가요?
Azure 간 DR 솔루션을 사용하여 동일한 지역 내에 VM을 복제할 수는 없습니다.

### <a name="can-i-replicate-vms-to-any-azure-region"></a>VM을 다른 Azure 지역에 복제할 수 있나요?
Site Recovery를 사용하면 동일한 지리적 클러스터 내의 두 지역 간에 VM을 복제 및 복구할 수 있습니다. 지리적 클러스터는 데이터 대기 시간 및 주권에 유의해서 정의해야 합니다. 자세한 내용은 Site Recovery [지역 지원 매트릭스](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)를 참조하세요.

### <a name="does-site-recovery-require-internet-connectivity"></a>Site Recovery에 인터넷 연결이 필요한가요?

아니요, Site Recovery에 인터넷 연결이 필요하지는 않지만 이 [문서](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges)에 언급된 Site Recovery URL 및 IP 범위에 액세스할 수 있어야 합니다.

## <a name="replication-policy"></a>복제 정책

### <a name="what-is-a-replication-policy"></a>복제 정책이란?
복구 지점 보존 기록 및 앱 일치 스냅숏 빈도에 대한 설정을 정의합니다. 기본적으로 Azure Site Recovery는 복구 지점 보존의 경우 '24시간', 앱 일치 스냅숏 빈도의 경우 '60분'인 기본 설정으로 새 복제 정책을 만듭니다.

### <a name="what-is-crash-consistent-recovery-point"></a>크래시 일관성이 있는 복구 지점이란?
크래시 일관성이 있는 복구 지점은 스냅숏을 만들 때 서버에서 전원 코드가 빠졌거나 VM이 손상된 경우의 디스크 데이터를 나타냅니다. 스냅숏을 만들 때 메모리에 있던 내용이 포함되지 않습니다. 오늘날 대부분의 애플리케이션은 크래시 일관성이 있는 스냅숏에서도 제대로 복구할 수 있습니다. 크래시 일관성이 있는 복구 지점은 일반적으로 데이터베이스가 없는 운영 체제와 파일 서버, DHCP 서버, 인쇄 서버 등의 애플리케이션에 충분합니다.

### <a name="what-is-application-consistent-recovery-point"></a>애플리케이션 일관성이 있는 복구 지점이란? 
애플리케이션 일관성이 있는 복구 지점은 크래시 일관성이 있는 스냅숏과 동일한 데이터를 캡처하는 앱 일관성이 있는 스냅숏에서 생성되며, 메모리에 있던 모든 데이터와 처리 중인 모든 트랜잭션이 추가됩니다. 추가 콘텐츠로 인해, 애플리케이션 일관성이 있는 스냅숏은 가장 복잡하며 가장 오랜 시간이 걸립니다. 애플리케이션 일관성이 있는 복구 지점은 데이터베이스 운영 체제와 SQL 등의 애플리케이션에 권장됩니다.

### <a name="how-are-recovery-points-generated-and-saved"></a>복구 지점은 어떻게 생성 및 저장되나요?
Site Recovery에서 복구 지점을 생성하는 방법을 이해하기 쉽도록 복구 지점 보존 기간이 24시간이고, 앱 일관성이 있는 빈도 스냅숏이 1시간인 복제 정책을 예로 들어 설명하겠습니다.
Site Recovery에서 5분마다 크래시 일관성이 있는 지점을 만들며, 사용자는 이 빈도를 변경할 수 없습니다. 따라서 마지막 1시간에 대해 사용자는 크래시 일관성이 있는 지점 12개와 앱 일관성이 있는 지점 1개 중에서 선택할 수 있습니다. 시간이 진행됨에 따라 Site Recovery는 마지막 1시간이 넘은 복구 지점을 모두 정리하고 시간당 하나의 복구 지점만 저장합니다.
아래 스크린샷을 통해 살펴보겠습니다.


1. 마지막 1시간 미만의 시간에서는 빈도가 5분인 복구 지점이 있습니다.
2. 마지막 1시간이 넘은 시간에서는 시간당 하나의 복구 지점만 유지됩니다.

  ![복구 지점 생성](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)


### <a name="how-far-back-can-i-recover"></a>복구할 수 있는 가장 오랜 복구 지점은 어떻게 되나요?
사용할 수 있는 가장 오래된 복구 지점은 72시간입니다.

### <a name="what-will-happen-if-i-have-a-replication-policy-of-24-hours-and-there-is-no-recovery-points-generation-due-to-some-issue-for-more-than-24-hours-does-my-previous-recovery-points-will-be-pruned"></a>24시간 복제 정책이 있고 어떤 문제로든 24시간 이상 복구 지점이 생성되지 않을 경우 어떻게 되나요? 이전 복구 지점이 정리되나요?
아니요, 이 경우 Site Recovery에서 이전 복구 지점을 모두 유지합니다. 

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>VM에서 복제를 사용하도록 설정한 후 복제 정책을 변경하려면 어떻게 할까요? 
Site Recovery 자격 증명 모음 > Site Recovery 인프라 > 복제 정책으로 이동합니다. 편집할 정책을 선택하고 변경 내용을 저장합니다. 변경 내용은 모든 기존 복제에도 적용됩니다. 

### <a name="are-all-the-recovery-points-complete-copy-of-the-vm-or-differential"></a>모든 복구 지점이 VM의 전체 복사본인가요, 아니면 차등 복사본인가요?
초기 복제의 경우 생성되는 첫 번째 복구 지점에는 전체 복사본이 포함되고, 후속 복구 지점에는 델타 변경 내용이 포함됩니다.

### <a name="does-increasing-recovery-points-retention-windows-increases-the-storage-cost"></a>복구 지점 보존 기간을 늘리면 스토리지 비용도 증가하나요?
예, 보존 기간을 24시간에서 72시간으로 늘리면 Site Recovery에서 추가 48시간의 복구 지점을 저장하므로 스토리지 비용이 발생합니다. 예를 들어 단일 복구 지점에 10GB의 델타 변경 내용이 있고 GB당 비용이 매월 $0.16인 경우 $1.6 * 48의 추가 요금이 매월 부과됩니다.

## <a name="failover"></a>장애 조치(failover)

### <a name="is-failover-automatic"></a>장애 조치(failover)는 자동입니까?

자동이 아닙니다. 포털에서 클릭 한 번으로 장애 조치(failover)를 시작하거나, Site Recovery [PowerShell](azure-to-azure-powershell.md)을 사용하여 장애 조치(failover)를 트리거할 수 있습니다. 

### <a name="can-i-retain-public-ip-address-after-failover"></a>장애 조치(failover) 후 공용 IP 주소를 유지할 수 있나요?

프로덕션 애플리케이션의 공용 IP 주소는 **장애 조치(failover) 시 유지할 수 없습니다**. 장애 조치(failover) 프로세스의 일부로 포함된 워크로드에는 대상 지역에서 사용 가능한 Azure 공용 IP 리소스를 할당해야 합니다. 이 단계는 수동으로 수행할 수도 있고 복구 계획을 통해 자동으로 수행할 수도 있습니다. 복구 플랜을 사용하여 공용 IP 주소를 할당하려면 [문서](https://docs.microsoft.com/azure/site-recovery/concepts-public-ip-address-with-site-recovery#public-ip-address-assignment-using-recovery-plan)를 참조하세요.  

### <a name="can-i-retain-private-ip-address-during-failover"></a>장애 조치(failover) 중에 개인 IP 주소를 유지할 수 있나요?
예, 개인 IP 주소를 유지할 수 있습니다. 기본적으로 Azure VM에 재해 복구를 사용하도록 설정하면 Site Recovery는 원본 리소스 설정에 따라 대상 리소스를 만듭니다. 고정 IP 주소를 사용하여 구성된 Azure VM의 경우 Site Recovery는 대상 VM에 동일한 IP 주소(사용 중이 아닌 경우)를 프로비전하려고 시도합니다. 다양한 조건에서 개인 IP 주소를 유지하려면 [문서](site-recovery-retain-ip-azure-vm-failover.md)를 참조하세요.

### <a name="after-failover-the-server-does-not-have-the-same-ip-address-as-the-source-vm-why-is-it-assigned-with-a-new-ip-address"></a>장애 조치(failover) 후에는 서버에 원본 VM과 동일한 IP 주소가 없습니다. 새 IP 주소가 할당되는 이유는 무엇인가요?

Site Recovery는 장애 조치(failover) 시 IP 주소를 제공하려고 최선을 다합니다. IP 주소가 다른 가상 머신에서 사용 중인 경우 사용 가능한 다음 IP 주소가 대상으로 설정됩니다. Site Recovery에서 주소 지정을 처리하는 방법에 대한 자세한 설명을 보려면 [이 문서를 검토](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-network-mapping#set-up-ip-addressing-for-target-vms)하세요.

### <a name="what-does-latestlowest-rpo-recovery-points-means"></a>최신(가장 낮은 RPO) 복구 지점이란 무엇을 의미하나요?
이 옵션은 Site Recovery 서비스로 보낸 모든 데이터를 먼저 처리한 다음, 각 VM에 대한 복구 지점을 만든 후에 해당 복구 지점으로 장애 조치(failover)합니다. 이 옵션은 장애 조치(failover) 후에 생성된 VM은 장애 조치(failover)가 트리거되었을 때 Site Recovery로 복제된 모든 데이터를 보유하므로 가장 낮은 RPO(복구 지점 목표)를 제공합니다.

### <a name="does-latest-lowest-rpo-recovery-points-have-impact-on-failover-rto"></a>최신(가장 낮은 RPO) 복구 지점이 장애 조치(failover) RTO에 영향을 주나요?
예, Site Recovery에서 장애 조치(failover) 전에 보류 중인 모든 데이터를 처리하므로 이 옵션은 다른 옵션에 비해 RTO가 더 높습니다.

### <a name="what-does-latest-processed-option-in-recovery-points-mean"></a>복구 지점에서 가장 최근에 처리됨 옵션은 무엇을 의미하나요?
이 옵션은 계획의 모든 VM을 Site Recovery에서 처리된 최신 복구 지점으로 장애 조치(failover)합니다. 특정 VM에 대한 최신 복구 지점을 보려면 VM 설정에서 최신 복구 지점을 선택합니다. 이 옵션은 처리되지 않은 데이터를 처리하는 데 시간이 투입되지 않으므로 낮은 RTO(복구 시간 목표)를 제공합니다.

### <a name="if-im-replicating-between-two-azure-regions-what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>두 개의 Azure 지역 간에 복제하는 경우 주 지역에서 예기치 않은 중단이 발생하면 어떻게 되나요?
중단 후에 장애 조치(failover)를 트리거할 수 있습니다. Site Recovery는 주 지역에서의 연결이 없어도 장애 조치(failover)를 수행할 수 있습니다.

## <a name="recovery-plan"></a>복구 계획

### <a name="what-is-a-recovery-plan-"></a>복구 플랜이란?
Site Recovery의 복구 플랜은 VM의 장애 조치(failover) 복구를 오케스트레이션합니다. 복구 플랜을 통해 복구가 일관성 있게 정확하고, 반복 가능하며, 자동화되도록 유지할 수 있습니다. 복구 플랜은 사용자의 다음 요구 사항을 처리합니다.

- 함께 장애 조치(failover)되는 가상 머신 그룹 정의
- 애플리케이션이 정확하게 작동하도록 가상 머신 간의 종속성 정의
- 가상 머신 장애 조치(failover) 이외의 작업도 수행할 수 있도록 사용자 지정 수동 작업과 함께 복구 자동화

[자세히 알아봅니다](site-recovery-create-recovery-plans.md) .

### <a name="how-does-sequencing-is-achieved-in-a-recovery-plan"></a>복구 플랜에서 순서를 지정하려면 어떻게 하나요?

복구 플랜에서 여러 개의 그룹을 만들어 순서를 지정할 수 있습니다. 각 그룹이 한 번에 장애 조치(failover)되므로 동일한 그룹에 속한 VM은 함께 장애 조치(failover)되고, 그 뒤에 다른 그룹이 장애 조치(failover)됩니다. [복구 플랜을 사용하여 애플리케이션을 모델링](https://review.docs.microsoft.com/azure/site-recovery/recovery-plan-overview?branch=pr-en-us-61681#model-apps)하는 방법을 확인합니다. 

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>복구 플랜의 RTO를 확인하려면 어떻게 할까요?
복구 플랜의 RTO를 확인하려면 복구 플랜을 테스트 장애 조치(failover)하고 Site Recovery 작업으로 이동합니다.
아래 표시된 예제에서는 SAP 테스트 복구 플랜이 모든 가상 머신을 장애 조치(failover)하고 지정된 작업을 수행하는 데 8분 59초가 걸렸습니다.

  ![com-error](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>복구 플랜에 자동화 Runbook을 추가할 수 있나요?
예, Azure Automation Runbook을 복구 플랜에 통합할 수 있습니다. [자세히 알아보기](site-recovery-runbook-automation.md)

## <a name="reprotect-and-failback"></a>다시 보호 및 장애 복구(failback) 

### <a name="after-doing-a-failover-from-primary-region-to-disaster-recovery-region-does-vms-in-a-dr-region-gets-protected-automatically"></a>주 지역에서 재해 복구 지역으로 장애 조치(failover)를 수행한 후에는 DR 지역의 VM이 자동으로 보호되나요?
아니요, Azure VM을 한 지역에서 다른 지역으로 [장애 조치(failover)](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback)할 경우 VM은 DR 지역에서 보호되지 않는 상태로 부팅됩니다. VM을 주 지역으로 장애 복구(failback)하려면 보조 지역에서 VM을 [다시 보호](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect)해야 합니다.

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-secondary-region-to-primary-region"></a>다시 보호 시 Site Recovery에서 보조 지역의 전체 데이터를 주 지역에 복제하나요?
이는 상황에 따라 다릅니다. 예를 들어 원본 지역 VM이 있는 경우 원본 디스크와 대상 디스크 사이의 변경 내용만 동기화됩니다. 차등 백업은 디스크를 모두 비교하여 계산된 다음, 전달됩니다. 일반적으로 이 작업은 완료하는 데 몇 시간이 걸립니다. 다시 보호 중에 발생하는 사항에 대한 자세한 내용은 [문서]( https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection)를 참조하세요.

### <a name="how-much-time-does-it-take-to-failback"></a>장애 복구(failback)하는 데 시간이 얼마나 걸리나요?
다시 보호가 완료되고 나면, 일반적으로 주 지역에서 보조 지역으로 장애 조치(failover)하는 데 걸리는 시간과 비슷합니다. 

## <a name="security"></a>보안
### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>복제 데이터가 Site Recovery 서비스로 전송되나요?
아니요, Site Recovery는 복제된 데이터를 가로채지 않으며 가상 머신에서 실행되는 항목에 대한 정보가 없습니다. 복제 오케스트레이션 및 장애 조치(failover)에 필요한 메타데이터만 Site Recovery 서비스로 전송됩니다.  
Site Recovery는 ISO 27001:2013, 27018, HIPAA, DPA 인증을 받았으며, SOC2 및 FedRAMP JAB 평가를 수행하는 중입니다.

### <a name="does-site-recovery-encrypt-replication"></a>Site Recovery는 복제를 암호화합니까?
예, 전송 중 암호화 및 [Azure의 암호화](https://docs.microsoft.com/azure/storage/storage-service-encryption)가 모두 지원됩니다.

## <a name="next-steps"></a>다음 단계
* 지원 요구 사항을 [검토](azure-to-azure-support-matrix.md)합니다.
* Azure 간 복제를[설정](azure-to-azure-tutorial-enable-replication.md)합니다.
