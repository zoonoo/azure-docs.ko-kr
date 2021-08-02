---
title: Recovery Services 자격 증명 모음에 Azure VM 백업
description: Azure Backup을 사용하여 Recovery Services 자격 증명 모음에 Azure VM을 백업하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 42ce8990e4593fc734be619aa363f080f3055878
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/02/2021
ms.locfileid: "110783627"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음에 Azure VM 백업

이 문서에서는 [Azure Backup](backup-overview.md) 서비스를 사용하여 Recovery Services 자격 증명 모음에 Azure VM을 백업하는 방법을 설명합니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
>
> * Azure VM을 준비합니다.
> * 자격 증명 모음을 만듭니다.
> * VM을 검색하고 백업 정책을 구성합니다.
> * Azure VM에 백업을 사용하도록 설정합니다.
> * 초기 백업을 실행합니다.

> [!NOTE]
> 이 문서에서는 자격 증명 모음을 설정하고 백업할 VM을 선택하는 방법을 설명합니다. 여러 VM을 백업하는 경우에 유용합니다. 또한 VM 설정에서 직접 [Azure VM을 백업](backup-azure-vms-first-look-arm.md)할 수 있습니다.

## <a name="before-you-start"></a>시작하기 전에

* Azure VM 백업 아키텍처를 [검토](backup-architecture.md#architecture-built-in-azure-vm-backup)합니다.
* Azure VM 백업 및 백업 확장에 대해 [자세히 알아봅니다](backup-azure-vms-introduction.md).
* 백업을 구성하기 전에 [지원 매트릭스를 검토](backup-support-matrix-iaas.md)합니다.

또한 경우에 따라 몇 가지 작업을 수행해야 할 수도 있습니다.

* **VM에 VM 에이전트 설치**: Azure Backup은 컴퓨터에서 실행 중인 Azure VM 에이전트에 확장을 설치하여 Azure VM을 백업합니다. Azure Marketplace 이미지에서 VM을 만든 경우 에이전트가 설치되어 실행됩니다. 사용자 지정 VM을 만들거나 온-프레미스 컴퓨터를 마이그레이션하는 경우에는 [에이전트를 수동으로 설치](#install-the-vm-agent)해야 할 수 있습니다.

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

### <a name="modify-storage-replication"></a>스토리지 복제 수정

기본적으로 자격 증명 모음은 [GRS(지역 중복 스토리지)](../storage/common/storage-redundancy.md#geo-redundant-storage)를 사용합니다.

* 자격 증명 모음이 기본 백업 메커니즘인 경우 GRS를 사용하는 것이 좋습니다.
* 보다 경제적인 옵션으로 [LRS(로컬 중복 스토리지)](../storage/common/storage-redundancy.md#locally-redundant-storage)를 사용할 수 있습니다.
* [ZRS(영역 중복 스토리지)](../storage/common/storage-redundancy.md#zone-redundant-storage)는 [가용성 영역](../availability-zones/az-overview.md#availability-zones)에서 데이터를 복제하여 동일한 지역에 데이터 상주 및 복원력을 보장합니다.

스토리지 복제 형식을 다음과 같이 수정하십시오.

1. 새 자격 증명 모음에서 **설정** 섹션의 **속성** 을 선택합니다.
2. **속성** 의 **백업 구성** 에서 **업데이트** 를 선택합니다.
3. 스토리지 복제 형식을 선택하고 **저장** 을 선택합니다.

      ![새 자격 증명 모음의 스토리지 구성 설정](./media/backup-azure-arm-vms-prepare/full-blade.png)

> [!NOTE]
   > 자격 증명 모음을 설정하고 백업 항목을 포함한 후에는 스토리지 복제 유형을 수정할 수 없습니다. 이 작업을 수행하려는 경우 자격 증명 모음을 다시 만들어야 합니다.

## <a name="apply-a-backup-policy"></a>백업 정책 적용

Azure VM에 백업 정책을 적용하려면 다음 단계를 수행합니다.

1. 백업 센터로 이동하고 **개요** 탭에서 **+백업** 을 클릭합니다.

   ![백업 단추](./media/backup-azure-arm-vms-prepare/backup-button.png)

1. **데이터 원본 유형** 으로 **Azure Virtual Machines** 를 선택하고, 만든 자격 증명 모음을 선택합니다. 그런 후 **계속** 을 클릭합니다.

   ![백업 및 백업 목표 창](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

1. 백업 정책을 할당합니다.

    - 기본 정책은 하루에 한 번 VM을 백업합니다. 매일 백업은 30일 동안 보존됩니다. 즉시 복구 스냅샷은 이틀 동안 보존됩니다.

      ![기본 백업 정책](./media/backup-azure-arm-vms-prepare/default-policy.png)

    - 기본 정책을 사용하지 않으려면 **새로 만들기** 를 선택하고 다음 절차에 설명된 대로 사용자 지정 정책을 만듭니다.

1. **Virtual Machines** 에서 **추가** 를 선택합니다.

      ![가상 컴퓨터 추가](./media/backup-azure-arm-vms-prepare/add-virtual-machines.png)

1. **가상 머신 선택** 창이 열립니다. 정책을 사용하여 백업하려는 VM을 선택합니다. 그런 다음, **확인** 을 선택합니다.

   * 선택한 VM에 대해 유효성이 검사됩니다.
   * 자격 증명 모음과 동일한 지역에만 VM을 선택할 수 있습니다.
   * VM은 단일 자격 증명 모음에만 백업할 수 있습니다.

     !["가상 머신 선택" 창](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

    >[!NOTE]
    > 자격 증명 모음과 동일한 지역 및 구독에 위치한 모든 VM은 백업을 구성하는 데 사용할 수 있습니다. 백업을 구성할 때 필수 권한이 없는 가상 머신의 이름 및 리소스 그룹도 찾아볼 수 있습니다. 일시 삭제된 상태의 VM은 이 목록에 표시되지 않습니다. VM을 다시 보호해야 하는 경우 일시 삭제 기간이 만료될 때까지 기다리거나 일시 삭제된 목록에서 VM의 삭제를 취소해야 합니다. 자세한 내용은 [VM 일시 삭제 문서](soft-delete-virtual-machines.md#soft-delete-for-vms-using-azure-portal)를 참조하십시오.

1. **백업** 에서 **백업 사용** 을 선택합니다. 이렇게 하면 자격 증명 모음과 VM에 정책을 배포하고 Azure VM에서 실행되는 VM 에이전트에 백업 확장을 설치합니다.

백업 사용 단추를 클릭한 후 다음을 수행합니다.

* 백업 서비스는 VM의 실행 여부와 상관 없이 백업 확장을 설치합니다.
* 초기 백업은 백업 일정에 따라 실행됩니다.
* 백업이 실행될 때 다음을 참고하십시오.
  * 실행 중인 VM이 애플리케이션 일치 복구 지점을 캡처할 확률이 가장 높습니다.
  * 그러나 VM이 해제된 경우라도 백업은 됩니다. 이러한 VM을 오프라인 VM이라고 합니다. 이 경우 복구 지점은 충돌 일치가 됩니다.
* Azure VM의 백업 허용을 위해 명시적 아웃바운드 연결을 할 필요는 없습니다.

### <a name="create-a-custom-policy"></a>사용자 지정 정책 만들기

새 백업 정책을 만들도록 선택한 경우 정책 설정을 입력합니다.

1. **정책 이름** 에서 의미 있는 이름을 지정합니다.
2. **백업 일정** 에서 백업을 수행해야 하는 경우를 지정합니다. Azure VM에 대해 매일 또는 매주 백업을 수행할 수 있습니다.
3. **인스턴트 복원** 에서 즉시 복원에 대한 스냅샷을 로컬로 보존할 기간을 지정합니다.
    * 복원하면 백업된 VM 디스크가 스토리지에서 네트워크를 통해 복구 스토리지 위치로 복사됩니다. 즉시 복원을 통해 백업 데이터가 자격 증명 모음에 전송될 때까지 기다릴 필요 없이 백업 작업 중에 캡처된 로컬로 저장되어 있는 스냅샷을 활용할 수 있습니다.
    * 하루에서 5일 간의 즉시 복원에 대한 스냅샷을 보존할 수 있습니다. 2일이 기본 설정입니다.
4. **보존 범위** 에서 유지하려는 기간을 매일 또는 매주 백업 지점으로 지정할 수 있습니다.
5. **월간 백업 지점 보존** 및 **연간 백업 지점 보존** 에서 매일 또는 주간 백업의 월간 또는 연간 백업을 유지할지 여부를 지정합니다.
6. **확인** 을 클릭하여 정책을 저장합니다.
    > [!NOTE]
    > Backup 서비스는 RPC(복원 지점 컬렉션)을 저장하기 위해 별도의 RG(리소스 그룹)을 만듭니다. 이 RG는 VM의 RG와는 다릅니다. [자세히 알아보기](backup-during-vm-creation.md#azure-backup-resource-group-for-virtual-machines).

    ![새로운 백업 정책](./media/backup-azure-arm-vms-prepare/new-policy.png)

> [!NOTE]
   > Azure Backup은 Azure VM 백업을 위한 일광 절약 시간 변경에 대한 자동 시계 조정을 지원하지 않습니다. 변경 내용이 있으면 필요에 따라 수동으로 백업 정책을 수정합니다.

## <a name="trigger-the-initial-backup"></a>초기 백업 트리거

초기 백업은 일정에 따라 실행되지만, 다음과 같이 즉시 실행할 수 있습니다.

1. 백업 센터로 이동하여 **백업 인스턴스** 메뉴 항목을 선택합니다.
1. **Azure Virtual Machines** 를 **데이터 원본 유형** 으로 선택합니다. 그런 다음, 백업용으로 구성한 VM을 검색합니다.
1. 관련 행을 마우스 오른쪽 단추로 클릭하거나 추가 아이콘(...)을 선택한 후 **지금 백업** 을 클릭합니다.
1. **지금 백업** 에서 달력 컨트롤을 사용하여 복구 지점을 유지해야 하는 마지막 날을 선택합니다. 그런 다음, **확인** 을 선택합니다.
1. 포털 알림을 모니터링합니다.
   작업 진행 상황을 모니터링하려면 **백업 센터** > **백업 작업** 으로 이동하여 **진행 중** 인 작업에 대한 목록을 필터링합니다.
   VM의 크기에 따라 초기 백업을 만드는 데 시간이 걸릴 수 있습니다.

## <a name="verify-backup-job-status"></a>백업 작업 상태 확인

각 VM 백업에 대한 백업 작업 세부 정보는 **스냅샷** 단계와 **자격 증명 모음으로 데이터 전송** 단계의 두 단계로 구성됩니다.<br/>
스냅샷 단계는 **즉시 복원** 에 대한 디스크와 함께 저장되는 복구 지점의 가용성을 보장하고 사용자가 구성한 스냅샷 보존 기간에 따라 최대 5일 동안 사용할 수 있도록 합니다. 자격 증명 모음으로 데이터 전송 단계에서는 장기 보존을 위해 자격 증명 모음에서 복구 지점을 만듭니다. 데이터를 자격 증명 모음으로 전송은 스냅샷 단계가 완료된 후에만 시작됩니다.

  ![백업 작업 상태](./media/backup-azure-arm-vms-prepare/backup-job-status.png)

백 엔드에는 두 개의 **하위 작업** 이 실행되고 있는데, 하나는 아래에 나와 있는 것처럼 **백업 작업** 세부 정보 창에서 확인할 수 있는 프런트 엔드 백업 작업용입니다.

  ![백업 작업 상태 하위 작업](./media/backup-azure-arm-vms-prepare/backup-job-phase.png)

**자격 증명 모음으로 데이터 전송** 단계는 디스크 크기, 디스크당 변동 및 여러 가지 다른 요인에 따라 완료하기까지 며칠이 걸릴 수 있습니다.

작업 상태는 다음 시나리오에 따라 달라질 수 있습니다.

**스냅샷** | **자격 증명 모음으로 데이터 전송** | **작업 상태**
--- | --- | ---
Completed | 진행 중 | 진행 중
Completed | 건너뜀 | Completed
Completed | Completed | Completed
Completed | 실패 | 경고와 함께 완료
실패 | 실패 | 실패

이제 이 기능을 사용하면 동일한 VM에 대해 두 개의 백업이 병렬로 실행될 수 있지만, 각 단계(스냅샷, 자격 증명 모음으로 데이터 전송)에서 하나의 하위 작업만 실행할 수 있습니다. 따라서 진행 중인 백업 작업으로 인해 익일의 백업이 실패한 시나리오에서는 디커플링 기능으로 그러한 상황을 예방할 수 있습니다. 이전 날짜의 백업 작업이 진행 상태인 경우 **자격 증명 모음으로 데이터 전송** 은 건너뛰지만 이후 백업에서 스냅샷을 완료할 수 있습니다.
자격 증명 모음에 생성된 증분 복구 지점은 자격 증명 모음에서 생성된 가장 최근의 복구 지점부터 모든 변동을 캡처합니다. 사용자에게 비용적인 영향은 없습니다.

## <a name="optional-steps"></a>선택적 단계

### <a name="install-the-vm-agent"></a>VM 에이전트 설치

Azure Backup은 컴퓨터에서 실행 중인 Azure VM 에이전트에 확장을 설치하여 Azure VM을 백업합니다. Azure Marketplace 이미지에서 VM을 만든 경우 에이전트가 설치되어 실행됩니다. 사용자 지정 VM을 만들거나 온-프레미스 컴퓨터를 마이그레이션하는 경우에는 표에 요약된 것처럼 에이전트를 수동으로 설치해야 할 수 있습니다.

**VM** | **세부 정보**
--- | ---
**Windows** | 1. 에이전트 MSI 파일을 [다운로드하여 설치](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)합니다.<br/><br/> 2. 컴퓨터의 관리자 권한으로 설치합니다.<br/><br/> 3. 설치를 확인합니다. VM의 *C:\WindowsAzure\Packages* 에서 **WaAppAgent.exe** > **속성** 을 마우스 오른쪽 단추로 클릭합니다. **세부 정보** 탭에서 **제품 버전** 이 2.6.1198.718 이상이어야 합니다.<br/><br/> 에이전트를 업데이트하는 경우 백업 작업이 실행되고 있지 않은지 확인하고 [에이전트를 다시 설치](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)합니다.
**Linux** | 배포의 패키지 리포지토리에서 RPM 또는 DEB 패키지를 사용하여를 설치합니다. 이는 Azure Linux 에이전트를 설치 및 업그레이드하는 데 선호되는 방법입니다. 모든 [인증 배포 공급자](../virtual-machines/linux/endorsed-distros.md)는 이미지 및 리포지토리에 Azure Linux 에이전트 패키지를 통합합니다. 에이전트는 [GitHub](https://github.com/Azure/WALinuxAgent)에서 사용할 수 있지만 설치하지 않는 것이 좋습니다.<br/><br/> 에이전트를 업데이트하는 경우 백업 작업이 실행되고 있는지 확인하고 이진 파일을 업데이트합니다.

## <a name="next-steps"></a>다음 단계

* [Azure VM 에이전트](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) 또는 [Azure VM 백업](backup-azure-vms-troubleshoot.md)과 관련된 문제를 해결합니다.
* Azure VM을 [복원](backup-azure-arm-restore-vms.md)합니다.
