---
title: "Azure VM 백업 FAQ | Microsoft Docs"
description: "Azure VM 백업 작동 방식, 제한 및 정책 변경 시 발생하는 상황과 관련된 일반적인 질문에 대한 대답입니다."
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
keywords: "Azure VM 백업, Azure VM 복원, 백업 정책"
ms.assetid: c4cd7ff6-8206-45a3-adf5-787f64dbd7e1
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/18/2017
ms.author: trinadhk;pullabhk;
ms.openlocfilehash: 0117398a1ad2a8519f50732d173bec9fbb7411b5
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2017
---
# <a name="questions-about-the-azure-vm-backup-service"></a>Azure VM 백업 서비스에 대한 질문
이 문서에서는 Azure VM 백업 구성 요소를 빨리 이해하는 데 도움이 되는 일반적인 질문에 대한 대답을 제공합니다. 대답 중 일부에는 포괄적인 정보를 포함하는 문서에 대한 링크가 있습니다. 또한 [토론 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup)에 Azure Backup 서비스에 대한 질문도 게시할 수 있습니다.

## <a name="configure-backup"></a>백업 구성
### <a name="do-recovery-services-vaults-support-classic-vms-or-resource-manager-based-vms-br"></a>Recovery Services 자격 증명 모음은 클래식 VM 또는 Resource Manager 기반 VM을 지원하나요? <br/>
Recovery Services 자격 증명 모음은 두 모델을 모두 지원합니다.  Recovery Services 자격 증명 모음에 클래식 포털에서 만들어진 클래식 VM 또는 Azure Portal에서 만들어진 Resource Manager VM을 백업할 수 있습니다.

### <a name="what-configurations-are-not-supported-by-azure-vm-backup"></a>Azure VM 백업에서 지원되지 않는 구성은 무엇인가요?
[지원되는 운영 체제](backup-azure-arm-vms-prepare.md#supported-operating-system-for-backup) 및 [VM 백업 제한](backup-azure-arm-vms-prepare.md#limitations-when-backing-up-and-restoring-a-vm)을 참조하세요.

### <a name="why-cant-i-see-my-vm-in-configure-backup-wizard"></a>백업 구성 마법사에서 내 VM을 볼 수 없는 이유는 무엇인가요?
백업 구성 마법사에서 Azure Backup은 다음과 같은 VM만 나열합니다.
  * 아직 보호되지 않음 - VM 블레이드로 이동하고 설정 메뉴에서 Backup 상태를 확인하여 VM의 백업 상태를 확인할 수 있습니다. [VM의 백업 상태를 확인하는 방법](backup-azure-vms-first-look-arm.md#configure-the-backup-job-from-the-vm-management-blade)에 대해 자세히 알아보세요.
  * VM과 동일한 지역에 속함

## <a name="backup"></a>백업
### <a name="will-on-demand-backup-job-follow-same-retention-schedule-as-scheduled-backups"></a>주문형 백업 작업은 예약된 백업과 동일한 보존 일정을 따르나요?
아니요. 주문형 백업 작업의 보존 범위를 지정해야 합니다. 기본적으로 포털에서 트리거된 이후 30일 동안 유지됩니다. 

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>최근에 일부 VM에서 Azure Disk Encryption을 사용할 수 있습니다. 내 백업이 계속 작동하나요?
Key Vault에 액세스하려면 Azure Backup 서비스에 대한 권한을 부여해야 합니다. [PowerShell 설명서](backup-azure-vms-automation.md)의 *백업 사용* 섹션에서 설명하는 단계를 사용하여 PowerShell에서 이러한 권한을 제공할 수 있습니다.

### <a name="i-migrated-disks-of-a-vm-to-managed-disks-will-my-backups-continue-to-work"></a>VM 디스크를 관리 디스크로 마이그레이션했습니다. 내 백업이 계속 작동하나요?
예, 백업이 원활하게 작동하므로 백업을 다시 구성할 필요가 없습니다. 

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>내 VM이 종료되었습니다. 요청 시 또는 예약된 백업 작업도 종료되나요?
예. 컴퓨터가 종료되어도 백업 작업 및 복구 지점은 충돌 일치로 표시됩니다. 자세한 내용은 [이 문서](backup-azure-vms-introduction.md#how-does-azure-back-up-virtual-machines)의 데이터 일관성 섹션을 참조하세요.

## <a name="restore"></a>복원
### <a name="how-do-i-decide-between-restoring-disks-versus-full-vm-restore"></a>디스크 복원과 전체 VM 복원 중에서 어떻게 결정해야 하나요?
Azure 전체 VM 복원을 빠른 만들기 옵션으로 생각해 볼 수 있습니다. VM 복원 옵션은 디스크의 이름, 해당 디스크에서 사용되는 컨테이너, 공용 IP 주소 및 네트워크 인터페이스 이름을 변경합니다. 이러한 변경은 VM 생성 동안 만들어진 리소스의 고유성을 유지하는 데 필요합니다. 하지만 가용성 집합에 VM이 추가되지는 않습니다. 

복원 디스크를 사용하여 다음을 수행합니다.
  * 크기를 변경하는 것처럼 특정 시점의 구성에서 만든 VM을 사용자 지정합니다.
  * 백업 시 존재하지 않는 구성을 추가합니다. 
  * 만드는 리소스에 대한 명명 규칙을 제어합니다.
  * 가용성 집합에 VM을 추가합니다.
  * PowerShell/선언적 템플릿 정의를 통해서만 설정할 수 있는 기타 구성

## <a name="manage-vm-backups"></a>VM 백업 관리
### <a name="what-happens-when-i-change-a-backup-policy-on-vms"></a>VM에서 백업 정책을 변경하면 어떻게 되나요?
VM에 새 정책을 적용하면 새 정책의 일정 및 보존이 수행됩니다. 보존 기간을 늘리면 기존 복구 지점이 새 정책에 따라 유지되도록 표시됩니다. 보존 기간을 줄이면 다음 정리 작업에서 정리(prune) 표시되고 결과적으로 삭제됩니다. 
