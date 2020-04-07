---
title: Azure VM을 만들 때 백업 사용
description: Azure 백업을 사용하여 Azure VM을 만들 때 백업을 활성화하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: 7739109eb8bad88c9b723e67e13adc78c127499a
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80672817"
---
# <a name="enable-backup-when-you-create-an-azure-vm"></a>Azure VM을 만들 때 백업 사용

Azure 백업 서비스를 사용하여 Azure 가상 시스템(VM)을 백업합니다. VM은 백업 정책에 지정된 일정에 따라 백업되고 복구 지점은 백업에서 만들어집니다. 복구 지점은 복구 서비스 자격 증명 모음에 저장됩니다.

이 문서에서는 Azure 포털에서 가상 시스템(VM)을 만들 때 백업을 활성화하는 방법에 대해 자세히 설명합니다.  

## <a name="before-you-start"></a>시작하기 전에

- VM을 만들 때 백업을 사용하도록 설정하는 경우 지원되는 운영 체제를 [확인합니다.](backup-support-matrix-iaas.md#supported-backup-actions)

## <a name="sign-in-to-azure"></a>Azure에 로그인

계정에 아직 로그인하지 않은 경우 [Azure 포털에](https://portal.azure.com)로그인합니다.

## <a name="create-a-vm-with-backup-configured"></a>백업이 구성된 VM 만들기

1. Azure 포털에서 **리소스 만들기를 클릭합니다.**

2. Azure 마켓플레이스에서 **계산을**클릭한 다음 VM 이미지를 선택합니다.

3. [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal) 또는 [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) 지침에 따라 VM을 설정합니다.

4. **관리** 탭에서 **백업 사용에서** **를 클릭합니다.**
5. 복구 서비스 자격 증명 모음에 대한 Azure 백업 백업입니다. 기존 자격 증명 모음이 없는 경우 **새 만들기를** 클릭합니다.
6. 제안된 볼트 이름을 수락하거나 직접 지정합니다.
7. 볼트가 위치할 리소스 그룹을 지정하거나 만듭니다. 리소스 그룹 볼트는 VM 리소스 그룹과 다를 수 있습니다.

    ![VM에 대한 백업 사용](./media/backup-during-vm-creation/enable-backup.png)

8. 기본 백업 정책을 수락하거나 설정을 수정합니다.
    - 백업 정책은 VM의 백업 스냅숏을 얼마나 자주 생성하고 백업 복사본을 보관하는 기간까지 지정합니다.
    - 기본 정책은 하루에 한 번 VM을 백업합니다.
    - Azure VM이 매일 또는 매주 백업을 수행할 수 있도록 사용자 고유의 백업 정책을 사용자 지정할 수 있습니다.
    - Azure VM에 대한 백업 고려 사항에 대해 [자세히 알아보세요.](backup-azure-vms-introduction.md#backup-and-restore-considerations)
    - 인스턴트 복원 기능에 대해 [자세히 알아보세요.](backup-instant-restore-capability.md)

      ![기본 백업 정책](./media/backup-during-vm-creation/daily-policy.png)

## <a name="azure-backup-resource-group-for-virtual-machines"></a>가상 시스템에 대한 Azure 백업 리소스 그룹

백업 서비스는 RPC(복원 지점 수집)를 저장할 VM의 리소스 그룹과 다른 별도의 리소스 그룹(RG)을 만듭니다. RPC에는 관리되는 VM의 즉각적인 복구 지점이 있습니다. Backup 서비스에서 만든 리소스 그룹의 기본 이름 지정 `AzureBackupRG_<Geo>_<number>`형식은 다음과 입니다. 예: *AzureBackupRG_northeurope_1*. 이제 Azure Backup에서 만든 리소스 그룹 이름을 사용자 지정할 수 있습니다.

주의할 사항:

1. RG의 기본 이름을 사용하거나 회사 요구 사항에 따라 편집할 수 있습니다.
2. VM 백업 정책을 만드는 동안 RG 이름 패턴을 입력으로 제공합니다. RG 이름은 다음 형식이어야 `<alpha-numeric string>* n <alpha-numeric string>`합니다. 'n'은 정수(1부터 시작)로 대체되며 첫 번째 RG가 가득 차면 확장에 사용됩니다. 하나의 RG는 현재 최대 600RPC를 가질 수 있습니다.
              ![정책을 만들 때 이름 선택](./media/backup-during-vm-creation/create-policy.png)
3. 패턴은 아래의 RG 명명 규칙을 따라야 하며 총 길이가 허용되는 최대 RG 이름 길이를 초과해서는 안 됩니다.
    1. 리소스 그룹 이름은 숫자 문자, 마침표, 밑줄, 하이픈 및 괄호만 허용합니다. 그들은 마침표에서 끝날 수 없습니다.
    2. 리소스 그룹 이름은 RG 이름과 접미사를 포함하여 최대 74자까지 포함될 수 있습니다.
4. 첫 `<alpha-numeric-string>` 번째는 필수이며 'n' 다음으로 두 번째는 선택 사항입니다. 사용자 지정 이름을 지정하는 경우에만 적용됩니다. 텍스트 상자 중 하나에 아무 것도 입력하지 않으면 기본 이름이 사용됩니다.
5. 필요한 경우 정책을 수정하여 RG 이름을 편집할 수 있습니다. 이름 패턴이 변경되면 새 RG에 새 LP가 만들어집니다. 그러나 이전 RP는 여전히 이전 RG에 상주하며 RP 컬렉션은 리소스 이동을 지원하지 않으므로 이동되지 않습니다. 결국 LP는 포인트가 만료되면 가비지 수집을 받게 됩니다.
![정책을 수정할 때 이름 변경](./media/backup-during-vm-creation/modify-policy.png)
6. Backup 서비스에서 사용하도록 만든 리소스 그룹을 잠그지 않는 것이 좋습니다.

PowerShell을 사용하여 가상 컴퓨터에 대한 Azure 백업 리소스 그룹을 구성하려면 [스냅숏 보존 중에 Azure 백업 리소스 그룹 만들기를](backup-azure-vms-automation.md#creating-azure-backup-resource-group-during-snapshot-retention)참조하십시오.

## <a name="start-a-backup-after-creating-the-vm"></a>VM을 만든 후 백업 시작

VM 백업은 백업 정책에 따라 실행됩니다. 그러나 초기 백업을 실행하는 것이 좋습니다.

VM을 만든 후 다음을 수행합니다.

1. VM 속성에서 **백업**을 클릭합니다. VM 상태는 초기 백업이 실행될 때까지 초기 백업 보류 중입니다.
2. **지금 백업을** 클릭하여 주문형 백업을 실행합니다.

    ![주문형 백업 실행](./media/backup-during-vm-creation/run-backup.png)

## <a name="use-a-resource-manager-template-to-deploy-a-protected-vm"></a>리소스 관리자 템플릿을 사용하여 보호된 VM 배포

이전 단계에서는 Azure 포털을 사용하여 가상 컴퓨터를 만들고 복구 서비스 자격 증명 모음에서 보호하는 방법을 설명합니다. 하나 이상의 VM을 신속하게 배포하고 복구 서비스 자격 증명 모음에서 보호하려면 템플릿을 [참조하여 Windows VM 배포 및 백업을 사용하도록 설정합니다.](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/)

## <a name="next-steps"></a>다음 단계

이제 VM을 보호한 다음 VM을 관리하고 복원하는 방법을 알아봅니다.

- [VM 관리 및 모니터링](backup-azure-manage-vms.md)
- [VM 복원](backup-azure-arm-restore-vms.md)

문제가 발생하면 문제 해결 가이드를 [검토하십시오.](backup-azure-vms-troubleshoot.md)
