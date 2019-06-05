---
title: 'Azure Backup 오류 문제 해결: 게스트 에이전트 상태를 사용할 수 없음'
description: 에이전트, 확장명 및 디스크와 관련된 Azure Backup 오류의 증상, 원인 및 해결 방법
services: backup
author: genlin
manager: cshepard
keywords: Azure 백업; VM 에이전트; 네트워크 연결;
ms.service: backup
ms.topic: troubleshooting
ms.date: 12/03/2018
ms.author: genli
ms.openlocfilehash: 826a38dd80db7cd0e6e500949d2c259c3808c0a9
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64914941"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Azure Backup 오류 문제 해결: 에이전트 또는 확장 관련 문제

이 문서에서는 VM 에이전트 및 확장과의 통신에 관련된 Azure Backup 오류를 해결하는 데 도움이 될 수 있는 문제 해결 단계를 제공합니다.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]



## <a name="UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup"></a>UserErrorGuestAgentStatusUnavailable - VM 에이전트가 Azure Backup과 통신할 수 없습니다.

**오류 코드**: UserErrorGuestAgentStatusUnavailable <br>
**오류 메시지**: VM 에이전트를 Azure Backup과 통신할 수 없음<br>

Backup 서비스에 대한 VM을 등록하고 예약하면 Backup은 VM 에이전트와 통신함으로써 작업을 시작하여 지정 시간 스냅샷을 수행합니다. 다음 조건 중 하나라도 충족되지 못하면 스냅샷이 트리거되지 않을 수 있습니다. 스냅샷이 트리거되지 않으면 백업이 실패할 수 있습니다. 다음 문제 해결 단계를 나열된 순서에 완료하고 작업을 다시 시도하세요.<br>
**원인 1: [에이전트가 VM에 설치되어 있지만 응답하지 않습니다(Windows VM의 경우).](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**     
**원인 2: [VM에 설치된 에이전트가 최신이 아닙니다(Linux VM의 경우).](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**원인 3: [스냅숏 상태를 검색할 수 없거나 스냅숏을 만들 수 없습니다.](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**     
**원인 4: [백업 확장을 업데이트 또는 로드할 수 없습니다.](#the-backup-extension-fails-to-update-or-load)**  
**원인 5: [VM이 인터넷에 액세스할 수 없습니다.](#the-vm-has-no-internet-access)**

## <a name="guestagentsnapshottaskstatuserror---could-not-communicate-with-the-vm-agent-for-snapshot-status"></a>GuestAgentSnapshotTaskStatusError - 스냅샷 상태에 대해 VM 에이전트와 통신할 수 없습니다.

**오류 코드**: GuestAgentSnapshotTaskStatusError<br>
**오류 메시지**: 스냅샷 상태에 대해 VM 에이전트와 통신할 수 없음 <br>

Azure Backup 서비스에 대한 VM을 등록하고 예약하면 백업은 VM 백업 확장과 통신함으로써 작업을 시작하여 지정 시간 스냅샷을 수행합니다. 다음 조건 중 하나라도 충족되지 못하면 스냅샷이 트리거되지 않을 수 있습니다. 스냅샷이 트리거되지 않으면 백업 실패가 발생할 수 있습니다. 다음 문제 해결 단계를 나열된 순서에 완료하고 작업을 다시 시도하세요.  
**원인 1: [에이전트가 VM에 설치되어 있지만 응답하지 않습니다(Windows VM의 경우).](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**원인 2: [VM에 설치된 에이전트가 최신이 아닙니다(Linux VM의 경우).](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**원인 3: [VM이 인터넷에 액세스할 수 없습니다.](#the-vm-has-no-internet-access)**

## <a name="usererrorrpcollectionlimitreached---the-restore-point-collection-max-limit-has-reached"></a>UserErrorRpCollectionLimitReached - 복원 지점 컬렉션이 최대 한도에 도달했습니다.

**오류 코드**: UserErrorRpCollectionLimitReached <br>
**오류 메시지**: 복원 지점 컬렉션 최대 한도에 도달했습니다. <br>
* 이 문제는 복구 지점 리소스 그룹에 대한 잠금으로 인해 복구 지점을 자동으로 정리할 수 없는 경우에 발생할 수 있습니다.
* 하루에 여러 개의 백업이 트리거되는 경우에도 이 문제가 발생할 수 있습니다. 현재 좋습니다 하루 하나만 백업은 구성 된 스냅숏 보존에 따라 1 ~ 5 일에 대 한 인스턴트 복원 지점 유지 되 고 언제 든 지만 18 인스턴트 RPs VM에 연결할 수 있습니다. <br>

권장 작업:<br>
이 문제를 해결하려면 VM의 리소스 그룹에 대한 잠금을 제거하고 정리를 트리거하는 작업을 다시 시도합니다.
> [!NOTE]
> 백업 서비스는 VM의 리소스 그룹과 별개의 리소스 그룹을 만들어 복원 지점 컬렉션을 저장합니다. 고객은 백업 서비스에서 사용하기 위해 생성된 리소스 그룹을 잠그지 않는 것이 좋습니다. 백업 서비스가 만드는 리소스 그룹의 명명 형식은 AzureBackupRG_`<Geo>`_`<number>`입니다. 예: AzureBackupRG_northeurope_1

**1단계: [복원 지점 리소스 그룹에서 잠금 제거](#remove_lock_from_the_recovery_point_resource_group)** <br>
**2단계: [복원 지점 컬렉션 정리](#clean_up_restore_point_collection)**<br>

## <a name="usererrorkeyvaultpermissionsnotconfigured---backup-doesnt-have-sufficient-permissions-to-the-key-vault-for-backup-of-encrypted-vms"></a>UserErrorKeyvaultPermissionsNotConfigured - Backup에는 암호화된 VM을 백업할 수 있는 키 자격 증명 모음에 대한 충분한 사용 권한이 없습니다.

**오류 코드**: UserErrorKeyvaultPermissionsNotConfigured <br>
**오류 메시지**: Backup에 암호화된 VM의 백업을 위한 Key Vault에 대한 충분한 권한이 없습니다. <br>

백업 작업이 암호화된 VM에서 성공할 수 있도록 키 자격 증명 모음에 액세스할 수 있는 사용 권한이 있어야 합니다. 이 수행할 수 있습니다 사용 하는 [Azure portal](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption) 또는 [PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#enable-protection)합니다.

## <a name="ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>ExtensionSnapshotFailedNoNetwork - 가상 머신에 네트워크 연결이 없으므로 스냅숏 작업이 실패했습니다.

**오류 코드**: ExtensionSnapshotFailedNoNetwork<br>
**오류 메시지**: 가상 머신에 네트워크 연결이 없으므로 스냅샷 작업이 실패했습니다.<br>

Azure Backup 서비스에 대한 VM을 등록하고 예약하면 백업은 VM 백업 확장과 통신함으로써 작업을 시작하여 지정 시간 스냅샷을 수행합니다. 다음 조건 중 하나라도 충족되지 못하면 스냅샷이 트리거되지 않을 수 있습니다. 스냅샷이 트리거되지 않으면 백업 실패가 발생할 수 있습니다. 다음 문제 해결 단계를 나열된 순서에 완료하고 작업을 다시 시도하세요.    
**원인 1: [스냅숏 상태를 검색할 수 없거나 스냅숏을 만들 수 없습니다.](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**원인 2: [백업 확장을 업데이트 또는 로드할 수 없습니다.](#the-backup-extension-fails-to-update-or-load)**  
**원인 3: [VM이 인터넷에 액세스할 수 없습니다.](#the-vm-has-no-internet-access)**

## <a name="ExtentionOperationFailed-vmsnapshot-extension-operation-failed"></a>ExtentionOperationFailedForManagedDisks - VMSnapshot 확장 작업이 실패했습니다.

**오류 코드**: ExtentionOperationFailedForManagedDisks <br>
**오류 메시지**: VMSnapshot 확장 작업이 실패했습니다.<br>

Azure Backup 서비스에 대한 VM을 등록하고 예약하면 백업은 VM 백업 확장과 통신함으로써 작업을 시작하여 지정 시간 스냅샷을 수행합니다. 다음 조건 중 하나라도 충족되지 못하면 스냅샷이 트리거되지 않을 수 있습니다. 스냅샷이 트리거되지 않으면 백업 실패가 발생할 수 있습니다. 다음 문제 해결 단계를 나열된 순서에 완료하고 작업을 다시 시도하세요.  
**원인 1: [스냅숏 상태를 검색할 수 없거나 스냅숏을 만들 수 없습니다.](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**원인 2: [백업 확장을 업데이트 또는 로드할 수 없습니다.](#the-backup-extension-fails-to-update-or-load)**  
**원인 3: [에이전트가 VM에 설치되어 있지만 응답하지 않습니다(Windows VM의 경우).](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**원인 4: [VM에 설치된 에이전트가 최신이 아닙니다(Linux VM의 경우).](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backupoperationfailed--backupoperationfailedv2---backup-fails-with-an-internal-error"></a>BackUpOperationFailed / BackUpOperationFailedV2 - 내부 오류가 발생하여 백업하지 못했습니다.

**오류 코드**: BackUpOperationFailed / BackUpOperationFailedV2 <br>
**오류 메시지**: 내부 오류가 발생하여 백업하지 못했습니다. 몇 분 후에 작업을 다시 시도하세요. <br>

Azure Backup 서비스에 대한 VM을 등록하고 예약하면 백업은 VM 백업 확장과 통신함으로써 작업을 시작하여 지정 시간 스냅샷을 수행합니다. 다음 조건 중 하나라도 충족되지 못하면 스냅샷이 트리거되지 않을 수 있습니다. 스냅샷이 트리거되지 않으면 백업 실패가 발생할 수 있습니다. 다음 문제 해결 단계를 나열된 순서에 완료하고 작업을 다시 시도하세요.  
**원인 1: [에이전트가 VM에 설치되어 있지만 응답하지 않습니다(Windows VM의 경우).](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**원인 2: [VM에 설치된 에이전트가 최신이 아닙니다(Linux VM의 경우).](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**원인 3: [스냅숏 상태를 검색할 수 없거나 스냅숏을 만들 수 없습니다.](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**원인 4: [백업 확장을 업데이트 또는 로드할 수 없습니다.](#the-backup-extension-fails-to-update-or-load)**  
**원인 5: 리소스 그룹이 잠겨 있으므로 Backup 서비스에 이전 복원 지점을 삭제할 수 있는 권한이 없습니다.** <br>
**원인 6: [VM이 인터넷에 액세스할 수 없습니다.](#the-vm-has-no-internet-access)**

## <a name="usererrorunsupporteddisksize---currently-azure-backup-does-not-support-disk-sizes-greater-than-4095gb"></a>UserErrorUnsupportedDiskSize-현재 Azure Backup 4095GB 보다 큰 디스크 크기를 지원 하지 않습니다.

**오류 코드**: UserErrorUnsupportedDiskSize <br>
**오류 메시지**: 현재 Azure Backup 4095GB 보다 큰 디스크 크기 지원 하지 않습니다. <br>

4095GB 보다 큰 디스크 크기를 사용 하 여 VM을 백업 하는 경우 백업 작업이 실패할 수 있습니다. 대용량 디스크에 대 한 지원이 곧 제공 됩니다.  

## <a name="usererrorbackupoperationinprogress---unable-to-initiate-backup-as-another-backup-operation-is-currently-in-progress"></a>UserErrorBackupOperationInProgress - 다른 백업 작업이 현재 진행 중이어서 백업을 시작할 수 없습니다.

**오류 코드**: UserErrorBackupOperationInProgress <br>
**오류 메시지**: 다른 백업 작업이 현재 진행 중이어서 백업을 시작할 수 없습니다.<br>

기존 백업 작업이 진행 중이어서 최근 백업 작업이 실패했습니다. 현재 작업이 완료될 때까지 새 백업 작업을 시작할 수 없습니다. 현재 진행 중인 백업 작업이 완료된 후 다른 백업 작업을 트리거 또는 예약해야 합니다. 백업 작업 상태를 확인하려면 아래 단계를 수행합니다.

1. Azure Portal에 로그인하고, **모든 서비스**를 클릭합니다. Recovery Services를 입력하고 **Recovery Services 자격 증명 모음**을 클릭합니다. 복구 서비스 자격 증명 모음의 목록이 표시됩니다.
2. 복구 서비스 자격 증명 모음 목록에서 백업이 구성된 자격 증명 모음을 선택합니다.
3. 자격 증명 모음 대시보드 메뉴에서 **백업 작업**을 클릭하여 모든 백업 작업을 표시합니다.

    * 백업 작업이 진행 중이면 백업 작업이 완료될 때까지 기다리거나 취소합니다.
        * 백업 작업을 취소하려면 백업 작업을 마우스 오른쪽 단추로 클릭하고 **취소**를 클릭하거나 [PowerShell](https://docs.microsoft.com/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0)을 사용합니다.
    * 다른 자격 증명 모음에서 백업을 다시 구성한 경우 이전 자격 증명 모음에서 실행 중인 백업이 없는지 확인합니다. 있는 경우 백업 작업을 취소합니다.
        * 백업 작업을 취소하려면 백업 작업을 마우스 오른쪽 단추로 클릭하고 **취소**를 클릭하거나 [PowerShell](https://docs.microsoft.com/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0)을 사용합니다.
4. 백업 작업을 다시 시도합니다.

예약된 백업 작업이 다음 백업 구성과 충돌하여 오래 걸리는 경우 [모범 사례](backup-azure-vms-introduction.md#best-practices), [백업 성능](backup-azure-vms-introduction.md#backup-performance) 및 [복원 고려 사항](backup-azure-vms-introduction.md#backup-and-restore-considerations)을 검토하세요.


## <a name="causes-and-solutions"></a>원인 및 해결 방법

### <a name="the-vm-has-no-internet-access"></a>VM이 인터넷에 액세스할 수 없습니다.
배포 요구와 맞지 않게 VM이 인터넷에 액세스할 수 없습니다. 또는 제한으로 인해 Azure 인프라에 액세스하지 못할 수 있습니다.

제대로 작동하려면 Backup 확장이 Azure 공용 IP 주소에 연결되어야 합니다. 확장이 Azure Storage 엔드포인트(HTTP URL)에 명령을 보내 VM의 스냅샷을 관리합니다. 확장이 공용 인터넷에 액세스할 수 없는 경우 백업은 결국 실패합니다.

####  <a name="solution"></a>해결 방법
네트워크 문제를 해결하려면 [네트워크 연결 설정](backup-azure-arm-vms-prepare.md#establish-network-connectivity)을 참조하세요.

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>에이전트가 VM에 설치되어 있지만 응답하지 않습니다(Windows VM의 경우).

#### <a name="solution"></a>해결 방법
VM 에이전트가 손상되었거나 서비스가 중지되었습니다. VM 에이전트를 다시 설치하면 최신 버전을 가져올 수 있습니다. 또한 서비스와의 통신을 다시 시작하는 데도 도움이 됩니다.

1. VM 서비스(services.msc)에서 Microsoft Azure 게스트 에이전트 서비스가 실행 중인지 확인합니다. Windows Azure 게스트 에이전트 서비스를 다시 시작하고 백업을 시작해 보세요.    
2. Microsoft Azure 게스트 에이전트 서비스가 서비스에 표시되지 않으면 제어판에서 **프로그램 및 기능**으로 이동하여 Windows Azure 게스트 에이전트 서비스가 설치되어 있는지 확인합니다.
4. Microsoft Azure 게스트 에이전트가 **프로그램 및 기능**에 표시되면 Windows Azure 게스트 에이전트를 제거합니다.
5. [최신 버전의 에이전트 MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)를 다운로드하고 설치합니다. 설치를 완료하려면 관리자 권한이 있어야 합니다.
6. Microsoft Azure 게스트 에이전트 서비스가 서비스에 표시되는지 확인합니다.
7. 다음과 같이 주문형 백업을 실행합니다.
    * 포털에서 **지금 백업**을 선택합니다.

또는 VM에서 [Microsoft .NET 4.5가 설치되어 있는지](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) 확인합니다. .NET 4.5는 VM 에이전트가 서비스와 통신하는 데 필요합니다.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>VM에 설치된 에이전트가 최신이 아닙니다(Linux VM의 경우).

#### <a name="solution"></a>해결 방법
Linux VM에 대부분의 에이전트 관련 또는 확장 관련 오류는 이전 VM 에이전트에 영향을 주는 문제로 인해 발생합니다. 이 문제를 해결하려면 다음과 같은 일반 지침을 수행하세요.

1. [Linux VM 에이전트 업데이트](../virtual-machines/linux/update-agent.md)의 지침을 따르세요.

   > [!NOTE]
   > 배포 리포지토리를 사용할 때만 에이전트를 업데이트할 것을 *강력히 권장*합니다. GitHub에서 에이전트 코드를 직접 다운로드한 후 업데이트하는 것은 바람직하지 않습니다. 최신 에이전트를 배포할 수 없는 경우 배포 지원에 문의하여 설치 방법에 대한 지침을 얻으세요. 최신 에이전트를 확인하려면 GitHub 리포지토리의 [Microsoft Azure Linux 에이전트](https://github.com/Azure/WALinuxAgent/releases) 페이지로 이동하세요.

2. `ps -e` 명령을 실행하여 VM에 Azure 에이전트가 실행 중인지 확인합니다.

   이 프로세스가 실행되고 있지 않으면 다음 명령을 사용하여 다시 시작합니다.

   * Ubuntu의 경우: `service walinuxagent start`
   * 기타 배포의 경우: `service waagent start`

3. [에이전트 자동 다시 시작을 구성합니다](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. 새 테스트 백업을 실행합니다. 오류가 계속되면 VM에서 다음 로그를 수집합니다.

   * /var/lib/waagent/*.xml
   * /var/log/waagent.log
   * /var/log/azure/*

waagent의 자세한 로깅이 필요한 경우 다음 단계를 따르세요.

1. /etc/waagent.conf 파일에서 다음 줄을 찾습니다. **자세한 정보 로깅 사용(y|n)**
2. **Logs.Verbose** 값을 *n*에서 *y*로 변경합니다.
3. 변경 내용을 저장하고 이 섹션 앞부분에 설명된 단계를 완료하여 waagent를 다시 시작합니다.

###  <a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>스냅숏 상태를 검색할 수 없거나 스냅숏을 만들 수 없습니다.
VM 백업은 기본 저장소 계정에 대한 스냅샷 명령 실행을 사용합니다. 저장소 계정에 액세스할 수 없거나 스냅샷 작업의 실행이 지연되기 때문에 백업이 실패할 수 있습니다.

#### <a name="solution"></a>해결 방법
다음 조건으로 인해 스냅샷 작업이 실패할 수 있습니다.

| 원인 | 해결 방법 |
| --- | --- |
| VM이 RDP(원격 데스크톱 프로토콜)에서 종료되므로 VM 상태가 잘못 보고됩니다. | RDP에서 VM을 종료하는 경우 VM 상태가 올바른지 여부를 확인하려면 포털을 확인합니다. 올바르지 않으면 VM 대시보드의 **종료** 옵션을 사용하여 포털에서 VM을 종료합니다. |
| VM이 DHCP에서 호스트 또는 패브릭 주소를 가져올 수 없습니다. | IaaS VM 백업이 작동하려면 게스트 내에 DHCP를 사용하도록 설정되어야 합니다. VM이 DHCP 응답 245에서 호스트 또는 패브릭 주소를 가져올 수 없는 경우에는 어떠한 확장도 다운로드하거나 실행할 수 없습니다. 정적 개인 IP에 필요한 경우 구성 해야 통해는 **Azure portal** 또는 **PowerShell** VM 내 DHCP 옵션을 사용할 수 있는지 확인 합니다. [자세한](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface) PowerShell 사용 하 여 고정 IP 주소를 설정 하는 방법에 대 한 합니다.

### <a name="the-backup-extension-fails-to-update-or-load"></a>백업 확장을 업데이트 또는 로드할 수 없습니다.
확장을 로드할 수 없는 경우 스냅샷을 만들 수 없기 때문에 백업이 실패합니다.

#### <a name="solution"></a>해결 방법

확장을 제거하여 강제로 VMSnapshot 확장을 다시 로드합니다. 다음 백업 시도 시 확장이 다시 로드됩니다.

확장을 제거하려면

1. [Azure Portal](https://portal.azure.com/)에서 백업 실패가 발생하는 VM으로 이동합니다.
2. **설정**을 선택합니다.
3. **확장**을 섡택합니다.
4. **Vmsnapshot 확장**을 선택합니다.
5. **제거**를 선택합니다.

Linux VM의 경우 VMSnapshot 확장이 Azure Portal에 표시되지 않으면 [Azure Linux 에이전트를 업데이트](../virtual-machines/linux/update-agent.md)한 다음, 백업을 실행합니다.

이러한 단계를 완료하면 다음 백업 동안 확장을 다시 설치해야 합니다.

### <a name="remove_lock_from_the_recovery_point_resource_group"></a>복구 지점 리소스 그룹에서 잠금 제거
1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **모든 리소스 옵션**으로 이동한 다음, AzureBackupRG_`<Geo>`_`<number>` 형식의 복원 지점 컬렉션 리소스 그룹을 선택합니다.
3. **설정** 섹션에서 **잠금**을 선택하여 잠금을 표시합니다.
4. 잠금을 제거하려면 줄임표를 선택하고 **삭제**를 클릭합니다.

    ![잠금 삭제](./media/backup-azure-arm-vms-prepare/delete-lock.png)

### <a name="clean_up_restore_point_collection"></a> 복원 지점 컬렉션 정리
잠금을 제거한 후 복원 지점을 정리해야 합니다. 복원 지점을 정리하려면 다음 방법 중 하나를 따르세요.<br>
* [임시 백업을 실행 하 여 지점 컬렉션 복원 정리](#clean-up-restore-point-collection-by-running-ad-hoc-backup)<br>
* [Azure Portal에서 복원 지점 컬렉션 정리](#clean-up-restore-point-collection-from-azure-portal)<br>

#### <a name="clean-up-restore-point-collection-by-running-ad-hoc-backup"></a>임시 백업을 실행 하 여 지점 컬렉션 복원 정리
잠금을 제거한 후는 ad hoc/수동 백업을 트리거하십시오. 그러면 복원 지점이 자동으로 정리됩니다. 이 ad 임시/수동 작업이 처음으로; 실패 예상 그러나 수동 복원 지점 삭제 하는 대신 자동 정리를 해야 합니다. 정리 후에 예약된 다음 백업은 성공합니다.

> [!NOTE]
> 자동 정리 ad hoc/수동 백업 후 몇 시간 후에 발생 합니다. 예약된 백업이 여전히 실패하는 경우 [여기](#clean-up-restore-point-collection-from-azure-portal)에 나열된 단계를 따라 복원 지점 컬렉션을 수동으로 삭제해 보세요.

#### <a name="clean-up-restore-point-collection-from-azure-portal"></a>Azure Portal에서 복원 지점 컬렉션 정리 <br>

리소스 그룹에 대한 잠금으로 인해 지워지지 않은 복원 지점 컬렉션을 수동으로 지우려면 다음 단계를 따르세요.
1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **허브** 메뉴에서 **모든 리소스**를 클릭하고 사용자 VM이 있는, AzureBackupRG_`<Geo>`_`<number>` 형식의 리소스 그룹을 선택합니다.

    ![잠금 삭제](./media/backup-azure-arm-vms-prepare/resource-group.png)

3. 리소스 그룹을 클릭하면 **개요** 블레이드가 표시됩니다.
4. 숨겨진 리소스를 모두 표시하려면 **숨겨진 유형 표시** 옵션을 선택합니다. AzureBackupRG_`<VMName>`_`<number>` 형식의 복원 지점 컬렉션을 선택합니다.

    ![잠금 삭제](./media/backup-azure-arm-vms-prepare/restore-point-collection.png)

5. 복원 지점 컬렉션을 정리하려면 **삭제**를 클릭합니다.
6. 백업 작업을 다시 시도합니다.

> [!NOTE]
 >리소스(RP 컬렉션)에 여러 복원 지점이 있는 경우 포털에서 동일한 복원 지점을 삭제하면 시간 제한으로 인해 작업이 실패할 수 있습니다. 이것은 일부 복원 지점이 규정된 시간에 삭제되지 않아 작업 시간이 초과되는 알려진 CRP 문제입니다. 그러나 삭제 작업은 일반적으로 2~3회 재시도 후 성공합니다.
