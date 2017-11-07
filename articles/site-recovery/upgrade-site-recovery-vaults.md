---
title: "Site Recovery 자격 증명 모음에서 Azure Recovery Services 자격 증명 모음으로 업그레이드"
description: "Azure Site Recovery 자격 증명 모음에서 Recovery Services 자격 증명 모음으로 업그레이드하는 방법 알아보기"
documentationcenter: 
author: rajani-janaki-ram
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/31/2017
ms.author: rajani-janaki-ram
ms.openlocfilehash: fdb33ea0d08353b491f2934fcf885fcb6910b9a2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="upgrade-a-site-recovery-vault-to-an-azure-resource-manager-based-recovery-services-vault"></a>Site Recovery 자격 증명 모음에서 Azure Resource Manager 기반 Recovery Services 자격 증명 모음으로 업그레이드

이 문서에서는 진행 중인 복제에 영향을 주지 않고 Azure Site Recovery 자격 증명 모음에서 Azure Resource Manager 기반 Recovery Service 자격 증명 모음으로 업그레이드하는 방법을 설명합니다. Azure Resource Manager 기능 및 이점에 대한 자세한 내용은 [Azure Resource Manager 개요](../azure-resource-manager/resource-group-overview.md)를 참조하세요.

## <a name="introduction"></a>소개
Recovery Services 자격 증명 모음은 클라우드에서 기본적으로 백업 및 재해 복구 요구를 관리하는 Azure Resource Manager 리소스입니다. 새 Azure Portal에서 사용할 수 있는 통합된 자격 증명 모음이며 기본 백업 및 Site Recovery 자격 증명 모음을 대신합니다.

Recovery Services 자격 증명 모음은 다음을 포함하는 기능 배열을 제공합니다.

* Azure Resource Manager 지원: 가상 컴퓨터 및 물리적 컴퓨터를 Azure Resource Manager 스택으로 보호 및 장애 조치(failover)할 수 있습니다.

* 디스크 제외 – 대역폭을 모두 사용하지는 않으려는 임시 파일이나 높은 이탈 데이터가 있는 경우 복제에서 볼륨을 제외할 수 있습니다. 이 기능은 현재 *VMware에서 Azure로* 및 *Hyper-V에서 Azure로*에서 활성화되어 있으며 다른 시나리오로도 확장되고 있습니다.

* 프리미엄 및 LRS(로컬 중복 저장소)에 대한 지원 - 이제 고객이 높은 IOPS(초당 입/출력 작업)로 응용 프로그램을 보호하도록 하는 프리미엄 저장소 계정으로 서버를 보호할 수 있습니다. 이 기능은 현재 *VMware에서 Azure로*에서 사용되도록 설정되어 있습니다.

* 효율적인 시작 환경 – 향상된 시작 환경은 재해 복구를 보다 쉽게 설정할 수 있게 디자인되었습니다.

* 동일한 자격 증명 모음에서 Backup 및 Site Recovery 관리 - 이제 동일한 자격 증명 모음에서 재해 복구 또는 백업 실행에 대해 서버를 보호할 수 있습니다. 이를 통해 관리 오버헤드를 크게 줄입니다.

업그레이드된 환경 및 기능에 대한 자세한 내용은 [저장소, 백업 및 복구 블로그](https://azure.microsoft.com/blog/azure-site-recovery-now-available-in-a-new-experience-with-support-for-arm-and-csp/)를 참조하세요.

## <a name="salient-features"></a>핵심 기능

* 진행 중인 복제에 영향을 주지 않음: 진행 중인 복제는 업그레이드 중과 후에 중단 없이 계속됩니다.

* 추가 비용 없음: 추가 비용 없이 업데이트된 기능의 전체 집합을 가져올 수 있습니다.

* 데이터 손실 없음 – 이 프로세스는 마이그레이션이 아닌 업그레이드이므로 기존 복구 지점 및 설정이 업그레이드 도중 및 이후에 그대로 유지됩니다.


## <a name="what-happens-during-the-vault-upgrade"></a>자격 증명 모음이 업그레이드되는 동안에는 어떻게 되나요?

업그레이드 동안 새 서버 등록 또는 VM(가상 컴퓨터)에 대한 복제 설정 등의 작업을 수행할 수 없습니다. 자격 증명 모음으로의 보호된 항목 복제가 진행 중인 경우처럼, 자격 증명 모음에서의 데이터 읽기 및 쓰기와 관련된 작업은 중단 없이 계속됩니다.

### <a name="changes-to-automation-and-tooling-after-the-upgrade"></a>업그레이드 후 자동화 및 도구 변경
클래식 배포 모델에서 Resource Manager 배포 모델로 자격 증명 모음 형식을 업그레이드할 때 기존 자동화 또는 도구를 업데이트하여 업그레이드 후에도 계속 작동되도록 합니다.

### <a name="prepare-your-environment-for-the-upgrade"></a>환경의 업그레이드 준비

* [PowerShell 설치 또는 버전 5 이상으로 업그레이드](https://www.microsoft.com/download/details.aspx?id=50395)
* [최신 버전의 Azure PowerShell MSI 설치](https://github.com/Azure/azure-powershell/releases)
* [Recovery Services 자격 증명 모음 업그레이드 스크립트 다운로드](https://aka.ms/vaultupgradescript)

### <a name="prerequisites"></a>필수 조건
Site Recovery 자격 증명 모음에서 Azure Resource Manager 기반 Recovery Service 자격 증명 모음으로 업그레이드하려면 다음 필수 구성 요소를 충족해야 합니다.

* 최소 에이전트 버전: 서버에 설치된 Azure Site Recovery Provider 버전은 5.1.1700.0 이상이어야 합니다.

* 지원되는 구성: SAN(저장 영역 네트워크) 또는 SQL Server AlwaysOn 가용성 그룹으로 자격 증명 모음을 구성할 수 없습니다. 다른 모든 구성은 지원됩니다.

    >[!NOTE]
    >업그레이드 후 PowerShell을 통해서만 저장소 매핑을 관리할 수 있습니다.

* 지원되는 배포 시나리오 - 자격 증명 모음은 *VMware에서 Azure로* 레거시 배포 모델에 있어서는 안됩니다. 계속 진행하기 전에 먼저 향상된 배포 모델로 전환합니다.

* 관리 평면 작업을 포함하는 활성 사용자 시작 작업 없음: 업그레이드 중 관리 평면에 대한 액세스가 제한되므로 모든 관리 평면 작업을 완료한 후 업그레이드를 트리거해야 합니다. 이 프로세스에는 진행 중인 복제가 포함되지 않습니다.

## <a name="frequently-asked-questions"></a>질문과 대답

**이 업그레이드가 진행 중인 복제에 주는 영향은 무엇인가요?**

아니요. 진행 중인 복제는 업그레이드 중과 후에 중단 없이 계속됩니다.

**사이트 간 VPN, IP 설정 등의 네트워크 설정은 어떻게 되나요?**

업그레이드는 네트워크 설정에 영향을 주지 않습니다. 모든 Azure-온-프레미스 연결은 그대로 유지됩니다.

**자격 증명 모음을 가까운 시일 안에 업그레이드할 계획이 없다면 어떻게 되나요?**

이전 Azure Portal의 Site Recovery 자격 증명 모음에 대한 지원은 2017년 9월부터 중단될 예정입니다. 업그레이드 기능을 사용하여 새 포털로 이동하는 것을 강력하게 권장합니다.

**이 마이그레이션 계획으로 기존 도구는 어떻게 되나요?**  

마이그레이션 계획에서 가장 중요하게 수행해야 할 변경 사항 중 하나는 사용자 도구를 Resource Manager 배포 모델에 맞게 업데이트하는 것입니다. Recovery Services 자격 증명 모음은 Resource Manager 배포 모델을 기준으로 합니다. 

**관리 평면 가동 중지 시간은 남아 있나요?**

일반적으로 업그레이드하는 데 15-30분 정도 걸리며 최대 1시간이 걸릴 수 있습니다.

**업그레이드한 후에 롤백할 수 있나요?**

아니요. 리소스를 성공적으로 업그레이드한 후에는 롤백이 지원되지 않습니다.

**내 구독 또는 리소스에서 업그레이드가 가능한지 확인할 수 있나요?**

예. 플랫폼 지원 업그레이드 옵션에서 업그레이드의 첫 번째 단계는 리소스를 업그레이드할 수 있는지 여부를 확인하는 것입니다. 유효성 검사가 실패하는 경우 적절한 오류 메시지 또는 경고가 나타납니다.

**업그레이드 문제를 보고하려면 어떻게 해야 하나요?**

업그레이드 동안 오류가 발생하면 오류에 나열된 작업 ID를 기록해 둡니다. Microsoft 지원 서비스는 이 문제를 해결하기 위해 적극적으로 노력하고 있습니다. 지원 팀에 문의할 때 구독 ID, 자격 증명 모음 이름 및 작업 ID를 제공할 수도 있습니다. 지원 서비스는 최대한 빨리 문제를 해결하려고 합니다. Microsoft에서 분명히 지시하지 않은 경우 작업을 다시 시도하지 마세요.

## <a name="run-the-script"></a>스크립트 실행

PowerShell에서 다음 명령을 실행합니다.

    PS > .\RecoveryServicesVaultUpgrade-1.0.0.ps1 -SubscriptionID <subscriptionID>  -VaultName <vaultname> -Location <location> -ResourceType HyperVRecoveryManagerVault -TargetResourceGroupName <rgname>

* SubscriptionID: 업그레이드하는 자격 증명 모음과 관련된 구독 ID입니다.

* VaultName: 업그레이드하는 자격 증명 모음의 이름입니다.

* Location: 업그레이드하는 자격 증명 모음의 위치입니다.

* ResourceType: Site Recovery 자격 증명 모음에 대한 HyperVRecoveryManagerVault입니다.

* TargetResourceGroupName: 업그레이드된 자격 증명 모음을 배치하려는 리소스 그룹입니다. TargetResourceGroupName은 Azure Resource Manager의 기존 리소스 그룹 또는 새 리소스 그룹일 수 있습니다. 제공된 TargetResourceGroupName이 존재하지 않는 경우 자격 증명 모음과 동일한 위치에 업그레이드의 일부로 생성됩니다. 자세한 내용은 [Azure Resource Manager 개요](../azure-resource-manager/resource-group-overview.md#resource-groups)의 "리소스 그룹"을 참조하세요.

    >[!NOTE]
    >리소스 그룹 이름을 지정할 때는 특정 제약 조건이 적용됩니다. 자격 증명 모음 오류를 방지하려면 명명 규칙을 신중히 확인해야 합니다.
    >
    >예:
    >
    >.\RecoveryServicesVaultUpgrade-1.0.0.ps1 -SubscriptionId 1234-54123-354354-56416-8645 -VaultName gen2dr -Location "north europe" -ResourceType hypervrecoverymanagervault -TargetResourceGroupName abc

또는 다음 스크립트를 실행할 수 있습니다. 필수 매개 변수의 값을 입력합니다.

    PS > .\RecoveryServicesVaultUpgrade-1.0.0.ps1
    cmdlet RecoveryServicesVaultUpgrade-1.0.0.ps1 at command pipeline position 1

    Supply values for the following parameters:
    SubscriptionId:
    VaultName:
    Location:
    ResourceType:
    TargetResourceGroupName:

1. PowerShell 스크립트는 자격 증명을 입력하라는 메시지를 표시합니다. 한 번은 클래식 배포 모델 계정용으로, 다른 한 번은 Azure Resource Manager 계정용으로 2번 입력합니다.

2. 자격 증명을 입력하면 스크립트는 인프라 설정이 앞서 언급한 요구 사항을 충족하는지 여부를 확인하는 검사를 실행합니다.

3. 필수 구성 요소를 검사하고 확인한 후에는 자격 증명 모음 업그레이드를 진행하라는 메시지가 표시됩니다. 업그레이드 프로세스에 따라 자격 증명 모음 업그레이드가 시작됩니다. 전체 업그레이드를 완료하는 데 15-30분이 걸릴 수 있습니다.

4. 업그레이드가 성공적으로 완료된 후에는 새 Azure Portal에서 업그레이드된 자격 증명 모음에 액세스할 수 있습니다.

## <a name="post-upgrade-vault-management"></a>업그레이드 후 자격 증명 모음 관리

### <a name="replicate-by-using-azure-site-recovery-in-the-recovery-services-vault"></a>Recovery Services 자격 증명 모음에서 Azure Site Recovery를 사용하여 복제

* 이제 한 지역에서 다른 지역으로 Azure VM을 보호할 수 있습니다. 자세한 내용은 [Azure Site Recovery를 사용하여 지역 간 Azure VM 복제](site-recovery-azure-to-azure.md)를 참조하세요.

* VMware VM을 Azure에 복제 하는 방법에 대한 자세한 내용은 [Site Recovery를 사용하여 Azure에 VMware VM 복제](vmware-walkthrough-overview.md)를 참조하세요.

* Hyper-V VM(VMM 없음)을 Azure에 복제하는 방법에 대한 자세한 내용은 [Azure로 Hyper-V 가상 컴퓨터(VMM 없음) 복제](hyper-v-site-walkthrough-overview.md)를 참조하세요.

* Hyper-V VM(VMM 있음)을 Azure에 복제하는 방법에 대한 자세한 내용은 [Azure Portal에서 Site Recovery를 사용하여 VMM 클라우드의 Hyper-V 가상 컴퓨터를 Azure에 복제](vmm-to-azure-walkthrough-overview.md)를 참조하세요.

* Hyper-VM(VMM 있음)을 보조 사이트로 복제하는 방법에 대한 자세한 내용은 [Azure Portal을 사용하여 보조 VMM 사이트에 VMM 클라우드의 Hyper-V 가상 컴퓨터 복제](site-recovery-vmm-to-vmm.md)를 참조하세요.

* VMware VM에서 보조 사이트로 복제하는 방법에 대한 자세한 내용은 [Azure 클래식 포털에서 보조 사이트에 온-프레미스 VMware 가상 컴퓨터 또는 물리적 서버 복제](site-recovery-vmware-to-vmware.md)를 참조하세요.

### <a name="view-your-replicated-items"></a>복제된 항목 보기

다음 이미지는 자격 증명 모음에 대한 주요 엔터티를 표시하는 Recovery Services 자격 증명 모음 대시보드 페이지를 보여 줍니다. 자격 증명 모음에서 보호된 엔터티 목록을 보려면 **Site Recovery** > **복제된 항목**을 선택합니다.


![복제된 항목](./media/upgrade-site-recovery-vaults/replicateditems.png)

다음 이미지는 복제된 항목을 보기 위한 워크플로와 장애 조치(failover)를 시작하기 위한 **Failover** 명령을 보여 줍니다.

![복제된 항목](./media/upgrade-site-recovery-vaults/failover.png)

### <a name="view-your-replication-settings"></a>복제 설정 보기

Site Recovery 자격 증명 모음에서 각 보호 그룹은 복사 빈도, 복구 지점 재방문 주기, 응용 프로그램 일관성 스냅숏 빈도 및 기타 복제 설정으로 구성됩니다. Recovery Services 자격 증명 모음에서 이러한 설정은 복제 정책으로 구성됩니다. 정책 이름은 보호 그룹 또는 *primarycloud_Policy*의 이름입니다.

복제 정책에 대한 자세한 내용은 [Azure에 대한 VMware의 복제 정책 관리](site-recovery-setup-replication-settings-vmware.md)를 참조하세요.
