---
title: 암호화된 Azure VM 백업 및 복원
description: Azure 백업 서비스를 사용하여 암호화된 Azure VM을 백업하고 복원하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: 98febe9f91cb4b71d546300d4e65ade073d19e67
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461772"
---
# <a name="back-up-and-restore-encrypted-azure-vm"></a>암호화된 Azure VM 백업 및 복원

이 문서에서는 [Azure Backup](backup-overview.md) 서비스를 사용하여 암호화된 디스크를 사용하여 Windows 또는 Linux Azure 가상 시스템(VM)을 백업하고 복원하는 방법을 설명합니다.

시작하기 전에 Azure Backup이 Azure VM과 상호 작용하는 방법에 대해 자세히 알아보려면 다음 리소스를 검토하십시오.

- Azure VM 백업 아키텍처를 [검토합니다.](backup-architecture.md#architecture-built-in-azure-vm-backup)
- [자세히 알아보기](backup-azure-vms-introduction.md) Azure VM 백업 및 Azure 백업 확장.

## <a name="encryption-support"></a>암호화 지원

Azure Backup은 ADE(Azure 디스크 암호화)로 암호화된 OS/데이터 디스크가 있는 Azure VM의 백업을 지원합니다. ADE는 Windows VM의 암호화와 Linux VM용 DM 토굴 기능을 위해 BitLocker를 사용합니다. ADE는 Azure Key Vault와 통합되어 디스크 암호화 키 및 비밀을 관리합니다. 키 볼트 키 암호화 키(KEK)를 사용하여 키 볼트에 쓰기 전에 암호화 암호를 암호화하는 추가 보안 계층을 추가할 수 있습니다.

Azure Backup은 다음 표에 요약된 대로 Azure AD 앱의 유무에 관계없이 ADE를 사용하여 Azure VM을 백업하고 복원할 수 있습니다.

**VM 디스크 유형** | **ADE (BEK/dm-crypt)** | **에이드와 케크**
--- | --- | ---
**관리 되지 않는** | 예 | 예
**관리**  | 예 | 예

- [ADE,](../security/azure-security-disk-encryption-overview.md)키 [볼트](../key-vault/general/overview.md)및 [KEK에](https://docs.microsoft.com/azure/virtual-machine-scale-sets/disk-encryption-key-vault#set-up-a-key-encryption-key-kek)대해 자세히 알아보십시오.
- Azure VM 디스크 암호화에 대한 [FAQ를](../security/azure-security-disk-encryption-faq.md) 읽어보십시오.

### <a name="limitations"></a>제한 사항

- 동일한 구독 및 리전 내에서 암호화된 VM을 백업하고 복원할 수 있습니다.
- Azure Backup은 독립 실행형 키를 사용하여 암호화된 VM을 지원합니다. VM을 암호화하는 데 사용되는 인증서의 일부인 키는 현재 지원되지 않습니다.
- 복구 서비스 백업 자격 증명 모음과 동일한 구독 및 리전에서 암호화된 VM을 백업하고 복원할 수 있습니다.
- 암호화된 VM은 파일/폴더 수준에서 복구할 수 없습니다. 파일 및 폴더를 복원하려면 전체 VM을 복구해야합니다.
- VM을 복원할 때 암호화된 [VM에](backup-azure-arm-restore-vms.md#restore-options) 대해 기존 VM 대체 옵션을 사용할 수 없습니다. 이 옵션은 암호화되지 않은 관리 디스크에대해서만 지원됩니다.

## <a name="before-you-start"></a>시작하기 전에

시작하기 전에 다음을 수행합니다.

1. ADE를 사용하도록 설정한 하나 이상의 [Windows](../security/azure-security-disk-encryption-windows.md) 또는 [Linux](../virtual-machines/linux/disk-encryption-overview.md) VM이 있는지 확인합니다.
2. Azure VM 백업에 대한 [지원 매트릭스 검토](backup-support-matrix-iaas.md)
3. 복구 서비스 백업 자격 증명 모음이 없는 경우 [만듭니다.](backup-azure-arm-vms-prepare.md#create-a-vault)
4. 이미 백업에 사용하도록 설정된 VM에 대한 암호화를 사용하도록 설정한 경우 백업이 중단 없이 계속될 수 있도록 키 볼트에 액세스할 수 있는 권한을 백업에 제공하기만 하면 됩니다. 이러한 사용 권한 할당에 대해 [자세히 알아보세요.](#provide-permissions)

또한 몇 가지 상황에서 수행해야 할 몇 가지 사항이 있습니다.

- **VM에 VM 에이전트 설치**: Azure Backup은 컴퓨터에서 실행중인 Azure VM 에이전트에 대한 확장을 설치하여 Azure VM을 백업합니다. Azure 마켓플레이스 이미지에서 VM을 만든 경우 에이전트가 설치되고 실행됩니다. 사용자 지정 VM을 만들거나 온-프레미스 컴퓨터를 마이그레이션하는 경우 [에이전트를 수동으로 설치해야](backup-azure-arm-vms-prepare.md#install-the-vm-agent)할 수 있습니다.

## <a name="configure-a-backup-policy"></a>백업 정책 구성

1. 복구 서비스 백업 자격 증명 모음을 아직 만들지 않은 경우 [다음 지침을](backup-azure-arm-vms-prepare.md#create-a-vault) 따르십시오.
2. 포털에서 자격 증명 모음을 열고 **시작 하기** 섹션에서 **백업을** 선택 합니다.

    ![백업 블레이드](./media/backup-azure-vms-encryption/select-backup.png)

3. **백업 목표에서** > **워크로드가 실행 중인 위치는** **Azure**를 선택합니다.
4. **Virtual machine** > **OK** **무엇을 백업하시겠습니까?**

      ![시나리오 블레이드](./media/backup-azure-vms-encryption/select-backup-goal-one.png)

5. **백업 정책** > **백업 정책 선택 백업 정책에서**볼트와 연결할 정책을 선택합니다. 그런 다음 **확인**을 클릭합니다.
    - 백업 정책은 백업이 수행되는 시기와 백업 시간이 저장되는 길이를 지정합니다.
    - 드롭다운 메뉴 아래에 기본 정책의 상세 정보가 나열됩니다.

    ![시나리오 블레이드 열기](./media/backup-azure-vms-encryption/select-backup-goal-two.png)

6. 기본 정책을 사용하지 않으려면 **새 만들기를**선택하고 [사용자 지정 정책을 만듭니다.](backup-azure-arm-vms-prepare.md#create-a-custom-policy)

7. 선택 정책을 사용하여 백업할 암호화된 VM을 선택하고 **확인을**선택합니다.

      ![암호화된 VM 선택](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)

8. 볼트 페이지에서 Azure 키 볼트를 사용하는 경우 Azure Backup에서 키 자격 증명 모음의 키 및 비밀에 대한 읽기 전용 액세스가 필요하다는 메시지가 표시됩니다.

    - 이 메시지가 수신되면 아무 작업도 필요하지 않습니다.

        ![액세스 확인](./media/backup-azure-vms-encryption/access-ok.png)

    - 이 메시지가 수신되면 [아래 절차에](#provide-permissions)설명된 대로 사용 권한을 설정해야 합니다.

        ![액세스 경고](./media/backup-azure-vms-encryption/access-warning.png)

9. **백업 활성화를** 클릭하여 볼트에 백업 정책을 배포하고 선택한 VM에 대한 백업을 사용하도록 설정합니다.

## <a name="trigger-a-backup-job"></a>백업 작업 트리거

초기 백업은 일정에 따라 실행되지만 다음과 같이 즉시 실행할 수 있습니다.

1. 자격 증명 모음 메뉴에서 **백업 항목**을 클릭합니다.
2. **백업 항목에서** **Azure 가상 컴퓨터를**클릭합니다.
3. 백업 **항목** 목록에서 타원(...)을 클릭합니다.
4. **지금 백업**을 클릭합니다.
5. **지금 백업에서**캘린더 컨트롤을 사용하여 복구 지점을 유지해야 하는 마지막 날을 선택합니다. 그런 다음 **확인**을 클릭합니다.
6. 포털 알림을 모니터링합니다. **백업 작업** > 진행 중인 > 볼트 대시보드에서 작업**진행률을 모니터링할**수 있습니다. VM의 크기에 따라 초기 백업을 만드는 데 시간이 걸릴 수 있습니다.

## <a name="provide-permissions"></a>사용 권한 제공

Azure VM은 연결된 VM과 함께 키와 비밀을 백업하기 위해 읽기 전용 액세스가 필요합니다.

- 키 볼트는 Azure 구독의 Azure AD 테넌트와 연결됩니다. **멤버 사용자인**경우 Azure Backup은 추가 작업 없이 키 자격 증명 모음에 대한 액세스를 획득합니다.
- **게스트 사용자인**경우 Azure Backup이 키 자격 증명 모음에 액세스할 수 있는 권한을 제공해야 합니다.

사용 권한을 설정하려면 다음을 수행하십시오.

1. Azure 포털에서 **모든 서비스를**선택하고 키 자격 **증명 모음을 검색합니다.**
2. 백업할 암호화된 VM과 연결된 키 자격 증명 모음을 선택합니다.
3. **액세스 정책** > **추가 합니다.**
4. **보안 주체 선택을**선택한 다음 백업 관리를 **입력합니다.**
5. **백업 관리 서비스** > **선택 을**선택합니다.

    ![백업 서비스 선택](./media/backup-azure-vms-encryption/select-backup-service.png)

6. **액세스 추가 정책** > **템플릿에서 구성(선택 사항)** Azure **백업**을 선택합니다.
    - **키 권한** 및 **비밀 권한**에서 필요한 권한이 미리 입력됩니다.
    - **BEK만**사용하여 VM을 암호화하는 경우 비밀에 대한 권한만 필요하므로 키 사용 권한에 대한 선택을 **제거합니다.**

    ![Azure Backup 선택](./media/backup-azure-vms-encryption/select-backup-template.png)

7. **확인**을 클릭합니다. **백업 관리 서비스가** **액세스 정책에**추가됩니다.

    ![액세스 정책](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

8. **저장을** 클릭하여 Azure 백업에 사용 권한을 제공합니다.

## <a name="restore-an-encrypted-vm"></a>암호화된 VM 복원

다음과 같이 암호화된 VM을 복원합니다.

1. [VM 디스크 복원](backup-azure-arm-restore-vms.md#restore-disks).
2. 다음 중 하나를 수행하여 가상 시스템 인스턴스를 다시 만듭니다.
    1. 복원 작업 중에 생성된 템플릿을 사용하여 VM 설정을 사용자 지정하고 VM 배포를 트리거합니다. [자세히 알아보기](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm).
    2. PowerShell을 사용하여 복원된 디스크에서 새 VM을 만듭니다. [자세히 알아보기](backup-azure-vms-automation.md#create-a-vm-from-restored-disks).
3. Linux VM의 경우 데이터 디스크가 열리고 탑재되도록 ADE 확장을 다시 설치합니다.

## <a name="next-steps"></a>다음 단계

문제가 발생하면 다음 문서를 검토하십시오.

- 암호화된 Azure VM을 백업하고 복원할 때 [일반적인 오류입니다.](backup-azure-vms-troubleshoot.md)
- [Azure VM 에이전트/백업 확장](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) 문제.
