---
title: "Site Recovery 자격 증명 모음에서 Recovery Services 자격 증명 모음으로 업그레이드"
description: "Azure Site Recovery 자격 증명 모음에서 Recovery Services 자격 증명 모음으로 업그레이드하는 방법 알아보기"
ddocumentationcenter: 
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
ms.translationtype: HT
ms.sourcegitcommit: 14915593f7bfce70d7bf692a15d11f02d107706b
ms.openlocfilehash: 523cab85b195d85007bd85c45dbe3645f7a00ab1
ms.contentlocale: ko-kr
ms.lasthandoff: 08/10/2017

---
# <a name="upgrade-site-recovery-vaults-to-azure-resource-manager-based-recovery-services-vaults"></a>Site Recovery 자격 증명 모음에서 Recovery Services 자격 증명 모음 기반 Azure Resource Manager로 업그레이드

이 문서에서는 진행 중인 복제에 영향을 주지 않고 'Site Recovery 자격 증명 모음'에서 'Recovery Service 자격 증명 모음'을 기반으로 하는 Azure Resource Manager로 업그레이드하는 방법을 설명합니다. [여기에서](../azure-resource-manager/resource-group-overview.md) Azure Resource Manager 기능 및 이점에 대해 자세히 알아볼 수 있습니다.

## <a name="introduction"></a>소개
Recovery Services 자격 증명 모음은 클라우드에서 기본적으로 백업 및 재해 복구 요구를 관리하는 Azure Resource Manager 리소스입니다. 새 Azure Portal에서 사용할 수 있는 통합된 자격 증명 모음이며 기본 백업 및 Site Recovery 자격 증명 모음에 대한 대체입니다.

Recovery Services 자격 증명 모음은 다음을 포함하는 기능의 배열을 엽니다.

-   Azure Resource Manager 지원: 가상 컴퓨터 및 물리적 컴퓨터를 Azure Resource Manager 스택으로 보호 및 장애 조치(failover)할 수 있습니다.

-   디스크 제외 – 대역폭으로 사용하지 않으려는 임시 파일이나 높은 이탈 데이터가 있는 경우 복제에서 볼륨을 제외할 수 있습니다. 이 기능은 현재 ‘VMware에서 Azure로’ 및 ‘Hyper-V에서 Azure로’에서 활성화되어 있으며 다른 시나리오로도 확장될 예정입니다.

- 프리미엄 및 LRS(로컬 중복 저장소)에 대한 지원 - 이제 고객이 높은 IOP로 응용 프로그램을 보호하도록 하는 프리미엄 저장소 계정으로 서버를 보호할 수 있습니다. 이 기능은 현재 'VMware에서 Azure로'에서 활성화되어 있습니다.

-   효율적인 '시작' 환경 – 향상된 시작 환경은 재해 복구 설정이 쉬운 프로세스가 되도록 조정되었습니다.

- 동일한 자격 증명 모음에서 Backup 및 Site Recovery 관리 - 이제 동일한 자격 증명 모음에서 재해 복구 또는 백업 실행에 대해 서버를 보호할 수 있습니다. 이를 통해 관리 오버헤드를 크게 줄입니다.

업그레이드된 환경 및 기능에 대한 자세한 내용은 이 [블로그](https://azure.microsoft.com/blog/azure-site-recovery-now-available-in-a-new-experience-with-support-for-arm-and-csp/)를 확인하세요.

## <a name="salient-features"></a>핵심 기능

- **진행 중인 복제에 영향을 주지 않음**: 진행 중인 복제는 업그레이드 중과 후에 중단 없이 계속됩니다.

- **추가 비용 없음** – 추가 비용 없이 새로운 기능의 전체 집합 경험

- **데이터 손실 없음** – 마이그레이션이 아닌 업그레이드이므로 기존 복제 정보(복구 지점, 복제 설정 등)는 업그레이드 중과 후에 그대로 유지됩니다.


## <a name="what-happens-during-the-upgrade"></a>업그레이드하는 동안 어떻게 되나요?

새 서버 등록, VM에 대한 복제 활성화 등과 같은 작업은 업그레이드하는 동안 허용되지 않습니다. 자격 증명 모음으로 보호되는 항목의 진행 중인 복제와 같은 자격 증명 모음에 대해 읽거나 쓰는 데이터를 포함하는 모든 작업은 중단 없이 계속됩니다.

## <a name="changes-to-your-automation-and-tooling-after-vault-upgrade"></a>자격 증명 모음 업그레이드 후 자동화 및 도구 변경
클래식 배포 모델에서 Resource Manager 배포 모델로 자격 증명 모음 형식을 업그레이드하는 과정의 일부로, 기존 자동화 또는 도구를 업데이트하여 업그레이드 후에도 계속 작동되도록 해야 합니다.

## <a name="preparing-your-environment-for-vault-upgrade"></a>자격 증명 모음 업그레이드에 대한 환경 준비

1.  이 [링크](https://www.microsoft.com/download/details.aspx?id=50395)를 사용하여 버전 5 이상으로 PowerShell 설치/업그레이드

2.  [여기에서](https://github.com/Azure/azure-powershell/releases) 최신 Azure PowerShell MSI를 설치합니다.

3.  자격 증명 모음 업그레이드에 대한 스크립트 [다운로드](https://aka.ms/vaultupgradescript)

## <a name="prerequisites-for-upgrade"></a>업그레이드에 대한 필수 조건
다음 필수 구성 요소는 Site Recovery 자격 증명 모음에서 Recovery Service 자격 증명 모음 기반 Azure Resource Manager로 업그레이드하기 위해 충족되어야 합니다.

- 최소 에이전트 버전: 업그레이드를 위해 서버에 설치된 Azure Site Recovery Provider의 버전이 5.1.1700.0 이상이어야 합니다.

- 지원되는 구성: 자격 증명 모음은 SAN, SQL Always 가용성 그룹을 사용하여 구성되지 않아야 합니다. 다른 모든 구성은 지원됩니다.

>[!NOTE]
> 저장소 매핑은 업그레이드 후 PowerShell을 통해서만 관리될 수 있습니다.

- 지원되는 배포 시나리오 - 자격 증명 모음은 'VMware에서 Azure로' 레거시 배포 모델에 있어서는 안됩니다.  계속하기 전에 향상된 배포 모델로 먼저 이동해야 합니다.

- 관리 평면 작업을 포함하는 활성 사용자 시작 작업 없음: 업그레이드 중 관리 평면에 대한 액세스가 제한되므로 모든 관리 평면 작업을 완료한 후 업그레이드를 트리거해야 합니다. 진행 중인 복제를 포함하지 않습니다.

## <a name="frequently-asked-questions"></a>질문과 대답

- 이 업그레이드가 진행 중인 복제에 주는 영향은 무엇인가요?

  번호 진행 중인 복제는 업그레이드 중과 후에 중단 없이 계속됩니다.

- 네트워크 설정 - 사이트 간 VPN, IP 설정 등은 어떻게 되나요?

  업그레이드는 네트워크 설정에 영향을 주지 않습니다. 온-프레미스 연결에 대한 모든 Azure는 그대로 유지됩니다.
- 자격 증명 모음을 가까운 시일 안에 업그레이드할 계획이 없다면 어떻게 되나요?

  이전 Azure Portal의 Site Recovery 자격 증명 모음에 대한 지원은 9월부터 중단될 예정입니다. 따라서 업그레이드 기능을 사용하여 새 포털로 이동하는 것을 강력하게 권장합니다.

- 이 마이그레이션 계획으로 기존 도구는 어떻게 되나요?  

  업그레이드 계획에서 가장 중요하게 수행해야 할 변경 사항 중 하나는 사용자 도구를 Recovery Services 자격 증명 모음이 기반한 Resource Manager 배포 모델에 맞게 업데이트하는 것입니다.

- 관리 평면 가동 중지 시간은 얼마나 되나요?

  업그레이드에는 약 15-30분 정도가 걸립니다. 최대 1시간까지 걸릴 수 있습니다.

- 업그레이드한 후에 롤백할 수 있나요?

  번호 리소스를 성공적으로 업그레이드한 후에는 롤백이 지원되지 않습니다.

- 내 구독 또는 리소스에서 업그레이드가 가능한지 확인할 수 있나요?

  예. 플랫폼 지원 업그레이드 옵션에서 업그레이드의 첫 번째 단계는 리소스를 업그레이드할 수 있는지 여부를 확인하는 것입니다. 필수 구성 요소 유효성 검사가 실패하는 경우 적절한 오류/경고 메시지가 나타납니다.

- 업그레이드 문제를 보고하려면 어떻게 해야 하나요?

  업그레이드하는 동안 오류가 발생하는 경우 오류에 나열된 OperationId를 적어 두세요. Microsoft 지원은 적극적으로 문제를 해결하려고 노력하고 있습니다. 구독 ID, 자격 증명 모음 이름 및 작업 ID와 함께 지원 팀에 문의할 수도 있습니다. 가장 빨리 문제를 해결하기 위해 노력합니다. Microsoft에서 분명히 지시하지 않은 경우 작업을 다시 시도하지 마세요.

## <a name="how-to-run-the-script"></a>스크립트를 실행하려면 어떻게 해야 하나요?

PowerShell 프롬프트에서 다음 명령을 실행합니다.

    PS > .\RecoveryServicesVaultUpgrade-1.0.0.ps1 -SubscriptionID <subscriptionID>  -VaultName <vaultname> -Location <location> -ResourceType HyperVRecoveryManagerVault -TargetResourceGroupName <rgname>

- SubscriptionId: 업그레이드되는 자격 증명 모음과 관련된 구독 ID
- VaultName: 업그레이드되는 자격 증명 모음의 이름
- Location: 업그레이드되는 자격 증명 모음의 위치
- ResourceType: Site Recovery 자격 증명 모음에 대한 HyperVRecoveryManagerVault
- TargetResourceGroupName: 업그레이드된 자격 증명 모음을 배치하려는 리소스 그룹입니다. TargetResourceGroupName은 Azure Resource Manager의 기존 리소스 그룹 또는 새 리소스 그룹일 수 있습니다. 제공된 TargetResourceGroupName이 존재하지 않는 경우 자격 증명 모음과 동일한 위치에 업그레이드의 일부로 생성됩니다. 리소스 그룹에 대해 자세히 알아보려면 [여기](../azure-resource-manager/resource-group-overview.md#resource-groups)를 클릭합니다.

>[!NOTE]
>리소스 그룹 이름에는 제약이 있습니다. 이로 인해 실패하면 자격 증명 모음 업그레이드도 실패할 수 있으므로 동일하게 따릅니다.

예제:

    .\RecoveryServicesVaultUpgrade-1.0.0.ps1 -SubscriptionId 1234-54123-354354-56416-8645 -VaultName gen2dr -Location "north europe" -ResourceType hypervrecoverymanagervault -TargetResourceGroupName abc


또는 아래와 같이 스크립트를 실행할 수 있으며 필요한 모든 매개 변수에 대한 입력을 제공하라는 메시지가 표시됩니다.

    PS > .\RecoveryServicesVaultUpgrade-1.0.0.ps1
    cmdlet RecoveryServicesVaultUpgrade-1.0.0.ps1 at command pipeline position 1

    Supply values for the following parameters:
    SubscriptionId:
    VaultName:
    Location:
    ResourceType:
    TargetResourceGroupName:

1.  PowerShell 스크립트는 자격 증명을 입력하라는 메시지를 표시합니다. 자격 증명을 두 번 입력합니다. 한 번은 ASM 계정에서, 한 번은 Azure Resource Manager 계정에서 입력합니다.

2.  자격 증명을 입력한 후 스크립트는 필수 구성 요소 검사를 실행하여 인프라 설정이 문서의 앞부분에서 언급한 필수 구성 요소를 충족하는지 확인합니다.

3.  필수 구성 요소가 확인되면 자격 증명 모음 업그레이드를 계속할지 묻는 확인 메시지가 표시됩니다. 확인 시 업그레이드 프로세스가 자격 증명 모음 업그레이드를 시작합니다. 전체 업그레이드를 완료하는 데 15-30분이 걸릴 수 있습니다.

4.  업그레이드가 성공적으로 완료되면 새 Azure Portal에서 업그레이드된 자격 증명 모음에 액세스할 수 있습니다.

## <a name="management-experience-post-upgrade"></a>업그레이드 후 관리 환경

### <a name="how-to-replicate-using-azure-site-recovery-in-the-recovery-services-vault"></a>Recovery Services 자격 증명 모음에서 Azure Site Recovery를 사용하여 복제하는 방법

- 이제 한 지역에서 다른 지역으로 Azure VM을 보호할 수 있습니다. 자세한 내용은 [여기](site-recovery-azure-to-azure.md)에서 설명서를 참조하세요.

- VMware VM에서 Azure로 복제에 대한 자세한 내용은 [여기](vmware-walkthrough-overview.md)에서 설명서를 참조하세요.

- Hyper-VM(VMM 없이)에서 Azure로 복제에 대한 자세한 내용은 [여기](hyper-v-site-walkthrough-overview.md)에서 설명서를 참조하세요.

- Hyper-VM(VMM과 함께)에서 Azure로 복제에 대한 자세한 내용은 [여기](vmm-to-azure-walkthrough-overview.md)에서 설명서를 참조하세요.

- Hyper-VM(VMM과 함께)에서 보조 사이트로 복제에 대한 자세한 내용은 [여기](site-recovery-vmm-to-vmm.md)에서 설명서를 참조하세요.

- VMware VM에서 보조 사이트로 복제에 대한 자세한 내용은 [여기](site-recovery-vmware-to-vmware.md)에서 설명서를 참조하세요.

### <a name="how-to-view-your-replicated-items"></a>복제된 항목을 확인하는 방법

다음은 자격 증명 모음에 대한 주요 엔터티를 표시하는 Recovery Services 자격 증명 모음 대시보드 페이지를 보여 주는 화면입니다. **Site Recovery** -> **복제된 항목**을 클릭하여 자격 증명 모음의 보호된 엔터티 목록을 봅니다.


![복제된 항목](./media/upgrade-site-recovery-vaults/replicateditems.png)

다음 화면은 복제된 항목을 보기 위한 워크플로 및 장애 조치(failover)를 시작하는 방법을 보여 줍니다.

![복제된 항목](./media/upgrade-site-recovery-vaults/failover.png)

### <a name="how-to-view-your-replication-settings"></a>복제 설정을 확인하는 방법

Site Recovery 자격 증명 모음에서 각 보호 그룹은 복제 설정(복사 빈도, 복구 지점 재방문 주기, 응용 프로그램 일관성 스냅숏 빈도 등)으로 구성됩니다. Recovery Services 자격 증명 모음에서 이러한 설정은 복제 정책으로 구성됩니다. 정책 이름은 보호 그룹 또는 'primarycloud_Policy'의 이름입니다.

복제 정책에 대한 자세한 내용은 [여기](site-recovery-setup-replication-settings-vmware.md)를 참조하세요.

