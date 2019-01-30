---
title: Azure VM 백업 FAQ
description: Azure VM 백업 작동 방식, 제한 및 정책 변경 시 발생하는 상황과 관련된 일반적인 질문에 대한 대답입니다.
services: backup
author: trinadhk
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 8/16/2018
ms.author: trinadhk
ms.openlocfilehash: 31a708f3a0da76ab13e789b099f312cca1f86e08
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54332254"
---
# <a name="frequently-asked-questions-azure-backup"></a>질문과 대답-Azure Backup

이 문서에서는 [Azure Backup](backup-introduction-to-azure-backup.md) 서비스에 대한 일반적인 질문과 대답을 제공합니다.

## <a name="general-questions"></a>일반적인 질문


### <a name="what-azure-vms-can-you-back-up-using-azure-backup"></a>Azure Backup을 사용하여 어떤 Azure VM을 백업할 수 있나요?
지원되는 운영 체제 및 제한을 [검토](backup-azure-arm-vms-prepare.md#before-you-start)하세요.



## <a name="backup"></a>Backup

### <a name="does-an-on-demand-backup-job-use-the-same-retention-schedule-as-scheduled-backups"></a>주문형 백업 작업은 예약된 백업과 동일한 보존 일정을 사용하나요?
아니요. 주문형 백업 작업의 보존 범위를 지정해야 합니다. 기본적으로 포털에서 트리거된 이후 30일 동안 유지됩니다.

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>최근에 일부 VM에서 Azure Disk Encryption을 사용할 수 있습니다. 내 백업이 계속 작동하나요?
Azure Backup에 Key Vault 액세스 권한을 부여해야 합니다. [Azure Backup PowerShell](backup-azure-vms-automation.md) 설명서의 **백업 사용** 섹션에 설명된 대로 PowerShell에서 권한을 지정합니다.

### <a name="i-migrated-vm-disks-to-managed-disks-will-my-backups-continue-to-work"></a>VM 디스크를 관리 디스크로 마이그레이션했습니다. 내 백업이 계속 작동하나요?
예. 백업은 원활하게 작동합니다. 아무 것도 다시 구성할 필요가 없습니다.

### <a name="why-cant-i-see-my-vm-in-the-configure-backup-wizard"></a>백업 구성 마법사에서 내 VM을 볼 수 없는 이유는 무엇인가요?
이 마법사는 자격 증명 모음과 동일한 지역에 있고 아직 백업되지 않은 VM만 나열합니다.


### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>내 VM이 종료되었습니다. 요청 시 또는 예약된 백업 작업도 종료되나요?
예. 머신이 종료되면 백업이 실행됩니다. 복구 지점은 충돌 일치로 표시됩니다.

### <a name="can-i-cancel-an-in-progress-backup-job"></a>진행 중인 백업 작업을 취소할 수 있나요?
예. **스냅숏 만들기** 단계에 있는 백업 작업을 취소할 수 있습니다. 스냅숏에서 데이터 전송이 진행 중인 경우 작업을 취소할 수 없습니다.

### <a name="i-enabled-resource-group-lock-on-my-backed-up-managed-disk-vms-will-my-backups-continue-to-work"></a>백업한 관리 디스크 VM에서 리소스 그룹 잠금을 사용하도록 설정했습니다. 내 백업이 계속 작동하나요?
리소스 그룹을 잠그면 Azure Backup 서비스가 이전 복원 지점을 삭제할 수 없습니다.
- 복원 지점 수는 18개로 제한되므로 새 백업을 시작할 수 없습니다.
- 잠금을 사용한 후 내부 오류와 함께 백업이 실패하면 [다음 단계에 따라](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-azure-portal) 복원 지점 컬렉션을 제거합니다.

### <a name="does-the-backup-policy-consider-daylight-saving-time-dst"></a>백업 정책에서 DST(일광 절약 시간)를 고려하나요?
아니요. 로컬 컴퓨터의 시간과 날짜는 현재 적용되는 일광 절약 시간에 로컬입니다. DST 때문에 예약된 백업에 설정이 현지 시간과 다를 수 있습니다.

### <a name="how-many-data-disks-can-i-attach-to-a-vm-backed-up-by-azure-backup"></a>Azure Backup을 통해 백업된 VM에 데이터 디스크를 몇 개까지 연결할 수 있나요?
Azure Backup은 최대 16개 디스크가 연결된 VM을 백업할 수 있습니다. 16개 디스크에 대한 지원이 [즉시 복원](backup-instant-restore-capability.md)에서 제공됩니다.

### <a name="does-azure-backup-support-standard-ssd-managed-disk"></a>Azure Backup은 표준 SSD 관리 디스크를 지원하나요?
Azure Backup은 [표준 SSD 관리 디스크](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/)를 지원합니다. SSD 관리 디스크는 Azure VM에 새로운 유형의 영구 스토리지를 제공합니다. SSD Managed Disks에 대한 지원이 [즉시 복원](backup-instant-restore-capability.md)에서 제공됩니다.

### <a name="can-we-back-up-a-vm-with-a-write-accelerator-wa-enabled-disk"></a>WA(쓰기 가속기) 지원 디스크를 사용하여 VM을 백업할 수 있나요?
WA 지원 디스크에는 스냅숏을 만들 수 없습니다. 그러나 Azure Backup 서비스는 백업에서 WA 지원 디스크를 제외할 수 있습니다. WA 지원 디스크를 사용하는 VM에 대한 디스크 제외 기능은 즉시 복원으로 업그레이드한 구독에만 지원됩니다.

### <a name="i-have-a-vm-with-write-accelerator-wa-disks-and-sap-hana-installed-how-do-i-back-up"></a>WA(쓰기 가속기) 디스크를 사용하고 SAP HANA가 설치된 VM을 갖고 있습니다. 어떻게 백업해야 하나요?
Azure Backup은 WA 지원 디스크를 백업할 수 없지만 백업에서 제외할 수는 있습니다. 그러나 WA 지원 디스크의 정보가 백업되지 않으므로 백업하더라도 데이터베이스 일관성이 제공되지 않습니다. 운영 체제 디스크 백업 및 WA 미사용 디스크 백업을 원하는 경우 이 구성으로 디스크를 백업하면 됩니다.

RPO가 15분인 SAP HANA 백업의 비공개 미리 보기가 준비되어 있습니다. SQL DB 백업과 비슷한 방식으로 빌드되었으며, SAP HANA에서 인증한 backInt 인터페이스를 타사 솔루션에 사용합니다. 비공개 미리 보기에서 관심이 있는 경우 **Azure VM에서 SAP HANA를 백업하기 위해 비공개 미리 보기 가입**이라는 제목으로 ` AskAzureBackupTeam@microsoft.com `에 이메일을 보내주세요.


## <a name="restore"></a>복원

### <a name="how-do-i-decide-whether-to-restore-disks-only-or-a-full-vm"></a>디스크만 복원할지 아니면 전체 VM을 복원할지 결정하는 방법은 무엇인가요?
VM 복원은 Azure VM의 빠른 만들기 옵션이라고 생각하시면 됩니다. 이 옵션은 디스크 이름, 디스크에서 사용하는 컨테이너, 공용 IP 주소 및 네트워크 인터페이스 이름을 변경합니다. VM이 만들어지면 변경 내용이 고유한 리소스를 유지합니다. VM은 가용성 집합에 추가되지 않습니다.

원할 경우 다음과 같이 디스크 복원 옵션을 사용할 수 있습니다.
  * 생성되는 VM을 사용자 지정합니다. 예를 들어 크기를 변경할 수 있습니다.
  * 백업 당시에는 없던 구성 설정을 추가합니다.
  * 생성되는 리소스의 명명 규칙을 제어합니다.
  * 가용성 집합에 VM을 추가합니다.
  * PowerShell 또는 템플릿을 사용하여 구성해야 하는 다른 설정을 추가합니다.  w

### <a name="can-i-restore-backups-of-unmanaged-vm-disks-after-i-upgrade-to-managed-disks"></a>관리 디스크로 업그레이드한 후 비관리형 VM 디스크의 백업을 복원할 수 있나요?
예, 디스크를 비관리형에서 관리형으로 마이그레이션하기 전에 만든 백업을 사용할 수 있습니다.
- 기본적으로 VM 복원 작업은 비관리형 VM을 만듭니다.
- 하지만 디스크를 복원하여 관리형 VM을 만드는 데 사용할 수 있습니다.

### <a name="how-do-i-restore-a-vm-to-a-restore-point-before-the-vm-was-migrated-to-managed-disks"></a>VM이 관리 디스크로 마이그레이션되기 전에 VM을 복원 지점으로 복원하려면 어떻게 할까요?
기본적으로 VM 복원 작업은 비관리형 디스크를 사용하는 VM을 만듭니다. 관리 디스크가 있는 VM을 만들려면 다음을 수행합니다.
1. [비관리형 디스크에 복원](tutorial-restore-disk.md#restore-a-vm-disk).
2. [복원된 디스크를 관리 디스크로 변환](tutorial-restore-disk.md#convert-the-restored-disk-to-a-managed-disk).
3. [관리 디스크를 사용하는 VM 만들기](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk)

PowerShell에서 이 작업을 수행하는 방법을 [자세히 알아보세요](backup-azure-vms-automation.md#restore-an-azure-vm).

### <a name="can-i-restore-the-vm-thats-been-deleted"></a>삭제된 VM을 복원할 수 있나요?
예. VM을 삭제하더라도 자격 증명 모음에서 해당 백업으로 이동하여 복구 지점으로 복원할 수 있습니다.

### <a name="how-to-restore-a-vm-to-the-same-availability-sets"></a>VM을 동일한 가용성 집합으로 복원하려면 어떻게 해야 하나요?
관리 디스크 Azure VM의 경우 관리 디스크로 복원하는 동안 템플릿에서 옵션을 제공하여 가용성 집합에 복원하는 기능을 설정합니다. 이 템플릿에 **가용성 집합**이라는 입력 매개 변수가 있습니다.

### <a name="how-do-we-get-faster-restore-performances"></a>복원 성능을 높이려면 어떻게 해야 하나요?
복원 성능 향상을 위해 [즉시 복원](backup-instant-restore-capability.md) 기능으로 전환합니다.

## <a name="manage-vm-backups"></a>VM 백업 관리

### <a name="what-happens-if-i-modify-a-backup-policy"></a>백업 정책을 수정하면 어떻게 되나요?
VM은 수정된 정책 또는 새 정책의 일정 및 보존 설정을 사용하여 백업됩니다.

- 보존 기간을 늘리면 기존 복구 지점이 새 정책에 따라 표시되고 유지됩니다.
- 보존 기간을 줄이면 복구 지점이 다음 정리 작업에서 정리하도록 표시되고, 결국 삭제됩니다.

### <a name="how-do-i-move-a-vm-backed-up-by-azure-backup-to-a-different-resource-group"></a>Azure Backup에서 백업한 VM을 다른 리소스 그룹으로 이동하려면 어떻게 할까요?

1. 일시적으로 백업을 중지하고 백업 데이터를 보존합니다.
2. VM을 대상 리소스 그룹으로 이동합니다.
3. 동일한 자격 증명 모음 또는 새 자격 증명 모음에서 백업을 다시 사용하도록 설정합니다.

이동 작업 전에 만든 사용 가능한 복원 지점에서 VM을 복원할 수 있습니다.
