---
title: Recovery Services 자격 증명 모음에 Azure VM 백업
description: Azure 백업을 사용하여 복구 서비스 자격 증명 모음에서 Azure VM을 백업하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: aeadd7bc798f690c67eef38c6dc645204ff39115
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273515"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음에 Azure VM 백업

이 문서에서는 [Azure 백업](backup-overview.md) 서비스를 사용하여 복구 서비스 자격 증명 모음에서 Azure VM을 백업하는 방법을 설명합니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
>
> * Azure VM을 준비합니다.
> * 자격 증명 모음을 만듭니다.
> * VM을 검색하고 백업 정책을 구성합니다.
> * Azure VM에 대한 백업을 사용하도록 설정합니다.
> * 초기 백업을 실행합니다.

> [!NOTE]
> 이 문서에서는 볼트를 설정하고 백업할 VM을 선택하는 방법에 대해 설명합니다. 여러 VM을 백업하는 경우에 유용합니다. 또는 VM 설정에서 직접 [단일 Azure VM을 백업할](backup-azure-vms-first-look-arm.md) 수 있습니다.

## <a name="before-you-start"></a>시작하기 전에

* Azure VM 백업 아키텍처를 [검토합니다.](backup-architecture.md#architecture-built-in-azure-vm-backup)
* Azure VM 백업 및 백업 확장에 대해 [자세히 알아봅니다](backup-azure-vms-introduction.md).
* 백업을 구성하기 전에 [지원 매트릭스를 검토합니다.](backup-support-matrix-iaas.md)

또한 몇 가지 상황에서 수행해야 할 몇 가지 사항이 있습니다.

* **VM에 VM 에이전트 설치**: Azure Backup은 컴퓨터에서 실행중인 Azure VM 에이전트에 대한 확장을 설치하여 Azure VM을 백업합니다. Azure 마켓플레이스 이미지에서 VM을 만든 경우 에이전트가 설치되고 실행됩니다. 사용자 지정 VM을 만들거나 온-프레미스 컴퓨터를 마이그레이션하는 경우 [에이전트를 수동으로 설치해야](#install-the-vm-agent)할 수 있습니다.

## <a name="create-a-vault"></a>자격 증명 모음 만들기

 자격 증명 모음은 백업과 시간 경과에 따라 생성된 복구 지점을 저장하고 백업된 머신과 연결된 백업 정책을 저장합니다. 다음과 같이 자격 증명 모음을 만듭니다.

1. [Azure 포털에](https://portal.azure.com/)로그인합니다.
2. 검색에서 **복구 서비스를**입력합니다. **서비스**에서 **복구 서비스 자격 증명 모음을 클릭합니다.**

     ![복구 서비스 자격 증명 모음 검색](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png)

3. **복구 서비스 볼트** 메뉴에서 **+Add**를 클릭합니다.

     ![Recovery Services 자격 증명 모음 만들기 2단계](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

4. **복구 서비스 자격 증명 모음에서**친숙한 이름을 입력하여 볼트를 식별합니다.
    * 이름은 Azure 구독에 대해 고유해야 합니다.
    * 2~50자를 포함할 수 있습니다.
    * 문자로 시작해야 하며, 문자, 숫자, 하이픈만 사용할 수 있습니다.
5. 볼트를 만들어야 하는 Azure 구독, 리소스 그룹 및 지리적 지역을 선택합니다. 그런 다음 **만들기**를 클릭합니다.
    * 자격 증명 모음을 만드는 데 시간이 걸릴 수 있습니다.
    * 포털의 오른쪽 위 영역에 있는 상태 알림을 모니터링합니다.

볼트를 만든 후 복구 서비스 자격 증명 모음 목록에 나타납니다. 자격 증명 모음이 표시되지 않으면 **새로 고침**을 선택합니다.

![백업 자격 증명 모음 목록](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

>[!NOTE]
> 이제 Azure Backup에서 만든 리소스 그룹 이름을 사용자 지정할 수 있습니다. 자세한 내용은 [가상 컴퓨터에 대한 Azure 백업 리소스 그룹을](backup-during-vm-creation.md#azure-backup-resource-group-for-virtual-machines)참조하십시오.

### <a name="modify-storage-replication"></a>저장소 복제 수정

기본적으로 볼트는 [지리적 중복 저장소(GRS)를](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)사용합니다.

* 볼트가 기본 백업 메커니즘인 경우 GRS를 사용하는 것이 좋습니다.
* 더 저렴한 옵션을 위해 [로컬 중복 저장소(LRS)를](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 사용할 수 있습니다.

다음과 같이 저장소 복제 유형을 수정합니다.

1. 새 볼트에서 **설정** 섹션에서 **속성을** 클릭합니다.
2. **속성에서** **백업 구성에서** **을 클릭합니다.**
3. 저장소 복제 유형을 선택하고 **저장**을 클릭합니다.

      ![새 자격 증명 모음의 스토리지 구성 설정](./media/backup-try-azure-backup-in-10-mins/full-blade.png)

> [!NOTE]
   > 볼트를 설정하고 백업 항목을 포함하는 후에는 저장소 복제 유형을 수정할 수 없습니다. 이 작업을 수행하려면 볼트를 다시 만들어야 합니다.

## <a name="apply-a-backup-policy"></a>백업 정책 적용

볼트에 대한 백업 정책을 구성합니다.

1. 볼트에서 개요 섹션에서 **+백업을** **클릭합니다.**

   ![백업 단추](./media/backup-azure-arm-vms-prepare/backup-button.png)

2. **백업 목표에서** > **워크로드가 실행 중인 위치는** **Azure**를 선택합니다. **Virtual machine** >  **OK** **무엇을 백업하시겠습니까?** 이렇게 하면 자격 증명 모음에 VM 확장을 등록합니다.

   ![백업 및 백업 목표 창](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

3. **백업 정책**에서 자격 증명 모음과 연결하려는 정책을 선택합니다.
    * 기본 정책은 하루에 한 번 VM을 백업합니다. 일일 백업은 30일 동안 유지됩니다. 인스턴트 복구 스냅숏은 2일 동안 유지됩니다.
    * 기본 정책을 사용하지 않으려면 **새 만들기를**선택하고 다음 절차에 설명된 대로 사용자 지정 정책을 만듭니다.

      ![기본 백업 정책](./media/backup-azure-arm-vms-prepare/default-policy.png)

4. **가상 시스템 선택에서**정책을 사용하여 백업할 VM을 선택합니다. 그런 다음 **확인**을 클릭합니다.

   * 선택한 VM의 유효성이 검사됩니다.
   * 자격 증명 모음과 동일한 지역에만 VM을 선택할 수 있습니다.
   * VM은 단일 자격 증명 모음에만 백업할 수 있습니다.

     !["가상 머신 선택" 창](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

5. **백업에서** **백업 활성화를**클릭합니다. 이렇게 하면 자격 증명 모음과 VM에 정책을 배포하고 Azure VM에서 실행되는 VM 에이전트에 백업 확장을 설치합니다.

     !["백업 사용" 단추](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

백업 사용 단추를 클릭한 후 다음을 수행합니다.

* 백업 서비스는 VM의 실행 여부와 상관 없이 백업 확장을 설치합니다.
* 초기 백업은 백업 일정에 따라 실행됩니다.
* 백업이 실행되면 다음을 유의하십시오.
  * 실행 중인 VM은 응용 프로그램 일관된 복구 지점을 캡처할 가능성이 가장 큽니다.
  * 그러나 VM이 꺼져 있더라도 백업됩니다. 이러한 VM을 오프라인 VM이라고 합니다. 이 경우 복구 지점은 충돌 일관성이 됩니다.
* Azure VM의 백업을 허용하기 위해 명시적 아웃바운드 연결이 필요하지 않습니다.

### <a name="create-a-custom-policy"></a>사용자 지정 정책 만들기

새 백업 정책을 만들도록 선택한 경우 정책 설정을 입력합니다.

1. **정책 이름에서**의미 있는 이름을 지정합니다.
2. **백업 일정에서**백업을 수행해야 하는 시기를 지정합니다. Azure VM에 대한 일일 또는 주간 백업을 수행할 수 있습니다.
3. **인스턴트 복원에서**즉시 복원을 위해 로컬로 스냅샷을 유지할 길이 지정합니다.
    * 복원하면 백업된 VM 디스크가 네트워크를 통해 저장소에서 복구 저장소 위치로 복사됩니다. 즉시 복원을 사용하면 백업 데이터가 볼트로 전송될 때까지 기다리지 않고 백업 작업 중에 로컬로 저장된 스냅샷을 활용할 수 있습니다.
    * 1~5일 동안 즉시 복원할 수 있도록 스냅샷을 유지할 수 있습니다. 2일이 기본 설정입니다.
4. **보존 범위에서**일별 또는 주간 백업 지점을 유지할 기간을 지정합니다.
5. **월별 백업 지점 보존에서**일별 또는 주간 백업의 월별 백업을 유지할지 여부를 지정합니다.
6. **확인**을 클릭하여 정책을 저장합니다.

    ![새 백업 정책](./media/backup-azure-arm-vms-prepare/new-policy.png)

> [!NOTE]
   > Azure Backup은 Azure VM 백업을 위한 일광 절약 시간 변경에 대한 자동 시계 조정을 지원하지 않습니다. 시간이 변경되면 필요에 따라 백업 정책을 수동으로 수정합니다.

## <a name="trigger-the-initial-backup"></a>초기 백업 트리거

초기 백업은 일정에 따라 실행되지만 다음과 같이 즉시 실행할 수 있습니다.

1. 자격 증명 모음 메뉴에서 **백업 항목**을 클릭합니다.
2. **백업 항목에서** **Azure 가상 컴퓨터를**클릭합니다.
3. 백업 **항목** 목록에서 타원(...)을 클릭합니다.
4. **지금 백업**을 클릭합니다.
5. **지금 백업에서**캘린더 컨트롤을 사용하여 복구 지점을 유지해야 하는 마지막 날을 선택합니다. 그런 다음 **확인**을 클릭합니다.
6. 포털 알림을 모니터링합니다. **백업 작업** > 진행 중인 > 볼트 대시보드에서 작업**진행률을 모니터링할**수 있습니다. VM의 크기에 따라 초기 백업을 만드는 데 시간이 걸릴 수 있습니다.

## <a name="verify-backup-job-status"></a>백업 작업 상태 확인

각 VM 백업에 대한 백업 작업 세부 정보는 두 단계로 구성되는데, **스냅숏** 단계 다음에 볼트 로 **데이터를 전송합니다.**<br/>
스냅숏 단계는 **인스턴트 복원을** 위한 디스크와 함께 저장된 복구 지점의 가용성을 보장하며 사용자가 구성한 스냅숏 보존에 따라 최대 5일 동안 사용할 수 있습니다. 볼트로 데이터를 전송하면 장기 보존을 위해 볼트에 복구 지점이 만들어집니다. 스냅숏 단계가 완료된 후에만 데이터를 볼트로 전송합니다.

  ![백업 작업 상태](./media/backup-azure-arm-vms-prepare/backup-job-status.png)

백 엔드에서 실행되는 두 개의 **하위 작업이** 있으며, 하나는 아래와 같이 **백업 작업** 세부 정보 블레이드에서 확인할 수 있는 프런트 엔드 백업 작업에 대한 작업입니다.

  ![백업 작업 상태](./media/backup-azure-arm-vms-prepare/backup-job-phase.png)

데이터를 볼트 로 **전송하는** 단계는 디스크 크기, 디스크당 변동 및 기타 여러 요인에 따라 완료하는 데 며칠이 걸릴 수 있습니다.

작업 상태는 다음 시나리오에 따라 달라질 수 있습니다.

**스냅숏** | **자격 증명 모음에 데이터 전송** | **작업 상태**
--- | --- | ---
Completed | 진행 중 | 진행 중
Completed | 건너뜀 | Completed
Completed | Completed | Completed
Completed | 실패 | 경고와 함께 완료됨
실패 | 실패 | 실패

이제 이 기능을 사용하면 동일한 VM에 대해 두 개의 백업을 병렬로 실행할 수 있지만 두 단계(스냅샷, 볼트로 데이터 전송)에서는 하나의 하위 작업만 실행할 수 있습니다. 따라서 시나리오에서 진행 중인 백업 작업이 발생하여 다음 날의 백업이 실패하면 이 분리 기능을 피할 수 있습니다. 이전 날의 백업 작업이 진행 중인 상태인 경우 **데이터를 볼트로 전송하는** 동안 다음 날의 백업이 완료될 수 있습니다.
볼트에 생성된 증분 복구 지점은 볼트에서 만든 마지막 복구 지점에서 모든 변동을 캡처합니다. 사용자에게 는 비용 영향이 없습니다.

## <a name="optional-steps"></a>선택적 단계

### <a name="install-the-vm-agent"></a>VM 에이전트 설치

Azure Backup은 컴퓨터에서 실행 중인 Azure VM 에이전트에 확장을 설치하여 Azure VM을 백업합니다. Azure Marketplace 이미지에서 VM을 만든 경우 에이전트가 설치되고 실행됩니다. 사용자 지정 VM을 만들거나 온-프레미스 컴퓨터를 마이그레이션하는 경우 테이블에 요약된 대로 에이전트를 수동으로 설치해야 할 수 있습니다.

**Vm** | **세부 정보**
--- | ---
**Windows** | 1. 에이전트 MSI 파일을 [다운로드하여 설치합니다.](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)<br/><br/> 2. 컴퓨터에 관리자 권한으로 설치합니다.<br/><br/> 3. 설치를 확인합니다. *VM의 C:\WindowsAzure\패키지에서* **WaAppAgent.exe** > **속성을**마우스 오른쪽 단추로 클릭합니다. 세부 **정보** 탭에서 **제품 버전은** 2.6.1198.718 이상이어야 합니다.<br/><br/> 에이전트를 업데이트하는 경우 백업 작업이 실행되고 있지 않은지 확인하고 [에이전트를 다시 설치합니다.](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)
**Linux** | 배포판의 패키지 리포지토리에서 RPM 또는 DEB 패키지를 사용하여 설치합니다. 이 방법은 Azure Linux 에이전트를 설치하고 업그레이드하는 데 선호되는 방법입니다. 모든 [인증 배포 공급자](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)는 이미지 및 리포지토리에 Azure Linux 에이전트 패키지를 통합합니다. 에이전트는 [GitHub](https://github.com/Azure/WALinuxAgent)에서 사용할 수 있지만 설치하지 않는 것이 좋습니다.<br/><br/> 에이전트를 업데이트하는 경우 백업 작업이 실행되고 있지 않은지 확인하고 바이너리를 업데이트합니다.

>[!NOTE]
> **Azure Backup은 이제 Azure 가상 시스템 백업 솔루션을 사용하여 선택적 디스크 백업 및 복원을 지원합니다.**
>
>현재 Azure Backup은 가상 시스템 백업 솔루션을 사용하여 VM의 모든 디스크(운영 체제 및 데이터)를 함께 백업할 수 있도록 지원합니다. 제외 디스크 기능을 사용하면 VM의 여러 데이터 디스크에서 하나 또는 몇 개를 백업할 수 있는 옵션이 있습니다. 이를 통해 백업 및 복원 요구 사항에 대한 효율적이고 비용 효율적인 솔루션을 제공합니다. 각 복구 지점에는 백업 작업에 포함된 디스크 데이터가 포함되어 있으므로 복원 작업 중에 지정된 복구 지점에서 디스크 하위 집합을 복원할 수 있습니다. 이는 스냅샷과 볼트모두에서 복원하는 데 적용됩니다.
>
>**미리 보기에 등록하려면AskAzureBackupTeam@microsoft.com**

## <a name="next-steps"></a>다음 단계

* [Azure VM 에이전트](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) 또는 Azure [VM 백업에](backup-azure-vms-troubleshoot.md)대한 모든 문제 해결.
* [복원](backup-azure-arm-restore-vms.md) Azure VM.
