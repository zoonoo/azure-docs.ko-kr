---
title: "Azure Backup 오류 문제 해결: 게스트 에이전트 상태 사용할 수 없음 | Microsoft Docs"
description: "오류: VM 에이전트와 통신할 수 없음과 관련된 Azure Backup 오류의 증상, 원인 및 해결 방법"
services: backup
documentationcenter: 
author: genlin
manager: cshepard
editor: 
keywords: "Azure 백업; VM 에이전트; 네트워크 연결;"
ms.assetid: 4b02ffa4-c48e-45f6-8363-73d536be4639
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: genli;markgal;
ms.translationtype: HT
ms.sourcegitcommit: 368589509b163cacf495fd0be893a8953fe2066e
ms.openlocfilehash: 6ed651bb8caafd18cec93e68ac70e27f92133e5c
ms.contentlocale: ko-kr
ms.lasthandoff: 08/17/2017

---

# <a name="troubleshoot-azure-backup-failure-issues-with-agent-andor-extension"></a>Azure Backup 오류 문제 해결: 에이전트 및/또는 확장 관련 문제

이 문서에서는 VM 에이전트와 통신 및 확장의 문제와 관련된 백업 실패를 해결하는 데 도움이 되는 문제 해결 단계를 제공합니다.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="vm-agent-unable-to-communicate-with-azure-backup"></a>VM 에이전트를 Azure Backup과 통신할 수 없음
Azure Backup 서비스에 대한 VM을 등록하고 예약하면 Backup은 VM 에이전트와 통신함으로써 작업을 시작하여 지정 시간 스냅숏을 수행합니다. 다음 조건 중 하나라도 충족되지 못하면 스냅숏이 트리거되지 않아 결국 Backup 실패로 이어질 수도 있습니다. 아래 문제 해결 단계를 지정된 순서대로 따르고 작업을 다시 시도합니다.
##### <a name="cause-1-the-vm-has-no-internet-accessthe-vm-has-no-internet-access"></a>원인 1: [VM이 인터넷에 연결되어 있지 않습니다.](#the-vm-has-no-internet-access)
##### <a name="cause-2-the-agent-is-installed-in-the-vm-but-is-unresponsive-for-windows-vmsthe-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>원인 2: [에이전트가 VM에 설치되어 있지만 응답하지 않습니다(Windows VM의 경우).](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
##### <a name="cause-3-the-agent-installed-in-the-vm-is-out-of-date-for-linux-vmsthe-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>원인 3: [VM에 설치된 에이전트가 최신이 아닙니다(Linux VM의 경우).](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
##### <a name="cause-4-the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-takenthe-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>원인 4: [스냅숏 상태를 검색할 수 없거나 스냅숏을 만들 수 없습니다.](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)
##### <a name="cause-5-the-backup-extension-fails-to-update-or-loadthe-backup-extension-fails-to-update-or-load"></a>원인 5: [백업 확장을 업데이트 또는 로드할 수 없습니다.](#the-backup-extension-fails-to-update-or-load)

## <a name="snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>가상 컴퓨터에 네트워크 연결이 없으므로 스냅숏 작업이 실패했습니다.
Azure Backup 서비스에 대한 VM을 등록하고 예약하면 백업은 VM 백업 확장과 통신함으로써 작업을 시작하여 지정 시간 스냅숏을 수행합니다. 다음 조건 중 하나라도 충족되지 못하면 스냅숏이 트리거되지 않아 결국 Backup 실패로 이어질 수도 있습니다. 아래 문제 해결 단계를 지정된 순서대로 따르고 작업을 다시 시도합니다.
##### <a name="cause-1-the-vm-has-no-internet-accessthe-vm-has-no-internet-access"></a>원인 1: [VM이 인터넷에 연결되어 있지 않습니다.](#the-vm-has-no-internet-access)
##### <a name="cause-2-the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-takenthe-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>원인 2: [스냅숏 상태를 검색할 수 없거나 스냅숏을 만들 수 없습니다.](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)
##### <a name="cause-3-the-backup-extension-fails-to-update-or-loadthe-backup-extension-fails-to-update-or-load"></a>원인 3: [백업 확장을 업데이트 또는 로드할 수 없습니다.](#the-backup-extension-fails-to-update-or-load)

## <a name="vmsnapshot-extension-operation-failed"></a>VMSnapshot 확장 작업이 실패했습니다.

Azure Backup 서비스에 대한 VM을 등록하고 예약하면 백업은 VM 백업 확장과 통신함으로써 작업을 시작하여 지정 시간 스냅숏을 수행합니다. 다음 조건 중 하나라도 충족되지 못하면 스냅숏이 트리거되지 않아 결국 Backup 실패로 이어질 수도 있습니다. 아래 문제 해결 단계를 지정된 순서대로 따르고 작업을 다시 시도합니다.
##### <a name="cause-1-the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-takenthe-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>원인 1: [스냅숏 상태를 검색할 수 없거나 스냅숏을 만들 수 없습니다.](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)
##### <a name="cause-2-the-backup-extension-fails-to-update-or-loadthe-backup-extension-fails-to-update-or-load"></a>원인 2: [백업 확장을 업데이트 또는 로드할 수 없습니다.](#the-backup-extension-fails-to-update-or-load)
##### <a name="cause-3-the-vm-has-no-internet-accessthe-vm-has-no-internet-access"></a>원인 3: [VM이 인터넷에 연결되어 있지 않습니다.](#the-vm-has-no-internet-access)
##### <a name="cause-4-the-agent-is-installed-in-the-vm-but-is-unresponsive-for-windows-vmsthe-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>원인 4: [에이전트가 VM에 설치되어 있지만 응답하지 않습니다(Windows VM의 경우).](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
##### <a name="cause-5-the-agent-installed-in-the-vm-is-out-of-date-for-linux-vmsthe-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>원인 5: [VM에 설치된 에이전트가 최신이 아닙니다(Linux VM의 경우).](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)

## <a name="unable-to-perform-the-operation-as-the-vm-agent-is-not-responsive"></a>VM 에이전트가 응답하지 않으므로 작업을 수행할 수 없습니다.

Azure Backup 서비스에 대한 VM을 등록하고 예약하면 백업은 VM 백업 확장과 통신함으로써 작업을 시작하여 지정 시간 스냅숏을 수행합니다. 다음 조건 중 하나라도 충족되지 못하면 스냅숏이 트리거되지 않아 결국 Backup 실패로 이어질 수도 있습니다. 아래 문제 해결 단계를 지정된 순서대로 따르고 작업을 다시 시도합니다.
##### <a name="cause-1-the-agent-is-installed-in-the-vm-but-is-unresponsive-for-windows-vmsthe-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>원인 1: [에이전트가 VM에 설치되어 있지만 응답하지 않습니다(Windows VM의 경우).](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
##### <a name="cause-2-the-agent-installed-in-the-vm-is-out-of-date-for-linux-vmsthe-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>원인 2: [VM에 설치된 에이전트가 최신이 아닙니다(Linux VM의 경우).](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
##### <a name="cause-3-the-vm-has-no-internet-accessthe-vm-has-no-internet-access"></a>원인 3: [VM이 인터넷에 연결되어 있지 않습니다.](#the-vm-has-no-internet-access)

## <a name="backup-failed-with-an-internal-error---please-retry-the-operation-in-a-few-minutes"></a>내부 오류가 발생하여 백업하지 못했습니다. 몇 분 후에 작업을 다시 시도하세요.

Azure Backup 서비스에 대한 VM을 등록하고 예약하면 백업은 VM 백업 확장과 통신함으로써 작업을 시작하여 지정 시간 스냅숏을 수행합니다. 다음 조건 중 하나라도 충족되지 못하면 스냅숏이 트리거되지 않아 결국 Backup 실패로 이어질 수도 있습니다. 아래 문제 해결 단계를 지정된 순서대로 따르고 작업을 다시 시도합니다.
##### <a name="cause-1-the-vm-has-no-internet-accessthe-vm-has-no-internet-access"></a>원인 1: [VM이 인터넷에 연결되어 있지 않습니다.](#the-vm-has-no-internet-access)
##### <a name="cause-2-the-agent-installed-in-the-vm-but-unresponsive-for-windows-vmsthe-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>원인 2: [에이전트가 VM에 설치되어 있지만 응답하지 않습니다(Windows VM의 경우).](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
##### <a name="cause-3-the-agent-installed-in-the-vm-is-out-of-date-for-linux-vmsthe-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>원인 3: [VM에 설치된 에이전트가 최신이 아닙니다(Linux VM의 경우).](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
##### <a name="cause-4-the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-takenthe-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>원인 4: [스냅숏 상태를 검색할 수 없거나 스냅숏을 만들 수 없습니다.](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)
##### <a name="cause-5-the-backup-extension-fails-to-update-or-loadthe-backup-extension-fails-to-update-or-load"></a>원인 5: [백업 확장을 업데이트 또는 로드할 수 없습니다.](#the-backup-extension-fails-to-update-or-load)


## <a name="causes-and-solutions"></a>원인 및 해결 방법

### <a name="the-vm-has-no-internet-access"></a>VM이 인터넷에 연결되어 있지 않습니다.
배포 요구 사항에 따라 VM이 인터넷에 연결되어 있지 않거나 Azure 인프라에 대한 액세스를 차단하는 위치에 제한 사항이 있습니다.

제대로 작동하려면 백업 확장이 Azure 공용 IP 주소에 연결되어야 합니다. 확장이 Azure Storage 끝점(HTTP URL)에 명령을 보내 VM의 스냅숏을 관리합니다. 확장이 공용 인터넷에 액세스할 수 없는 경우 Backup은 결국 실패합니다.

####  <a name="solution"></a>해결 방법
문제를 해결하려면 다음 방법 중 하나를 사용해 보세요.
##### <a name="allow-access-to-the-azure-datacenter-ip-ranges"></a>Azure 데이터 센터 IP 범위에 대한 액세스 허용

1. [Azure 데이터 센터 IP 목록](https://www.microsoft.com/download/details.aspx?id=41653)을 가져와서 액세스를 허용합니다.
2. 표시된 PowerShell 창의 Azure VM에서 **New-NetRoute** cmdlet을 실행하여 IP를 차단 해제합니다. 관리자 권한으로 cmdlet을 실행합니다.
3. IP에 대한 액세스를 허용하려면 네트워크 보안 그룹이 있는 경우 규칙을 추가합니다.

##### <a name="create-a-path-for-http-traffic-to-flow"></a>HTTP 트래픽을 보내는 경로 만들기

1. 네트워크 제한이 있는 경우(예를 들어 네트워크 보안 그룹) 트래픽을 라우트하도록 HTTP 프록시 서버를 배포합니다.
2. HTTP 프록시 서버에서 인터넷에 액세스하도록 허용하려면 네트워크 보안 그룹이 있는 경우 규칙을 추가합니다.

VM 백업에 대한 HTTP 프록시를 설정하는 방법을 알아보려면 [Azure Virtual Machines를 백업하기 위한 환경 준비](backup-azure-vms-prepare.md#using-an-http-proxy-for-vm-backups)를 참조하세요.

Managed Disks를 사용하고 있는 경우 방화벽에서 열리는 추가 포트(8443)가 필요할 수도 있습니다.

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>에이전트가 VM에 설치되어 있지만 응답하지 않습니다(Windows VM의 경우).

#### <a name="solution"></a>해결 방법
VM 에이전트가 손상되었거나 서비스가 중지되었습니다. VM 에이전트를 다시 설치하는 것이 최신 버전을 가져오고 통신을 다시 시작하는 데 도움이 됩니다.

1. 컴퓨터의 서비스(services.msc)에서 Windows 게스트 에이전트 서비스가 실행되는지 여부를 확인합니다. Windows 게스트 에이전트 서비스를 다시 시작하고 Backup을 시작해 보세요.<br>
2. 서비스에서 확인할 수 없는 경우 프로그램 및 기능에서 Windows 게스트 에이전트 서비스가 설치되었는지 여부를 확인합니다.
4. 프로그램 및 기능에서 볼 수 있는 경우 Windows 게스트 에이전트를 제거합니다.
5. [최신 버전의 에이전트 MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)를 다운로드하고 설치합니다. 설치를 완료하려면 관리자 권한이 필요합니다.
6. 그런 다음 서비스에서 Windows 게스트 에이전트 서비스를 볼 수 있어야 합니다.
7. 포털에서 "지금 백업"을 클릭하여 요청 시/임시 백업을 실행해 봅니다.

또한 가상 컴퓨터가 **[시스템에 .NET 4.5를 설치](https://docs.microsoft.com/en-us/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)**했는지 확인합니다. VM 에이전트가 서비스와 통신하는 데 필요합니다.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>VM에 설치된 에이전트가 최신이 아닙니다(Linux VM의 경우).

#### <a name="solution"></a>해결 방법
Linux VM에 대부분의 에이전트 관련 또는 확장 관련 오류는 이전 VM 에이전트에 영향을 주는 문제로 인해 발생합니다. 이 문제를 해결하려면 다음과 같은 일반 지침을 수행하세요.

1. [Linux VM 에이전트 업데이트](../virtual-machines/linux/update-agent.md)의 지침을 따르세요.

 >[!NOTE]
 >배포 리포지토리를 사용할 때만 에이전트를 업데이트할 것을 *강력히 권장*합니다. GitHub에서 에이전트 코드를 직접 다운로드한 후 업데이트하는 것은 바람직하지 않습니다. 최신 에이전트를 배포할 수 없는 경우 배포 지원에 문의하여 설치 방법에 대한 지침을 얻으세요. 최신 에이전트를 확인하려면 GitHub 리포지토리의 [Microsoft Azure Linux 에이전트](https://github.com/Azure/WALinuxAgent/releases) 페이지로 이동하세요.

2. 다음 명령을 실행하여 VM에 Azure 에이전트가 실행 중인지 확인합니다. `ps -e`

 이 프로세스가 실행되고 있지 않으면 다음 명령을 사용하여 다시 시작합니다.

 * Ubuntu의 경우: `service walinuxagent start`
 * 기타 배포의 경우: `service waagent start`

3. [에이전트 자동 다시 시작을 구성합니다](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. 새 테스트 백업을 실행합니다. 오류가 계속되면 고객의 VM에서 다음 로그를 수집하십시오.

   * /var/lib/waagent/*.xml
   * /var/log/waagent.log
   * /var/log/azure/*

waagent의 자세한 로깅이 필요한 경우 다음 단계를 따르세요.

1. /etc/waagent.conf 파일에서 다음 줄을 찾습니다. **자세한 정보 로깅 사용(y|n)**
2. **Logs.Verbose** 값을 *n*에서 *y*로 변경합니다.
3. 변경 내용을 저장하고 이 섹션의 이전 단계를 수행하여 waagent를 다시 시작합니다.

### <a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>스냅숏 상태를 검색할 수 없거나 스냅숏을 만들 수 없습니다.
VM 백업은 기본 저장소 계정에 대한 스냅숏 명령 실행을 사용합니다. 저장소 계정에 액세스할 수 없거나 스냅숏 작업의 실행이 지연되기 때문에 백업이 실패할 수 있습니다.

#### <a name="solution"></a>해결 방법
다음 조건으로 인해 스냅숏 작업 오류가 발생할 수 있습니다.

| 원인 | 해결 방법 |
| --- | --- |
| VM에 구성된 SQL Server 백업이 있습니다. | 기본적으로 VM 백업은 Windows VM에서 VSS 전체 백업을 실행합니다. SQL Server 기반 서버를 실행하고 SQL Server 백업이 구성된 VM에서 스냅숏 실행이 지연될 수 있습니다.<br><br>스냅숏 문제로 인해 Backup이 실패한 경우 다음 레지스트리 키를 설정합니다.<br><br>**[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT] "USEVSSCOPYBACKUP"="TRUE"** |
| VM이 RDP에서 종료되므로 VM 상태가 잘못 보고됩니다. | RDP(원격 데스크톱 프로토콜)에서 VM을 종료하는 경우 VM 상태가 올바른지 여부를 확인하려면 포털을 확인합니다. 올바르지 않으면 VM 대시보드의 **종료** 옵션을 사용하여 포털에서 VM을 종료합니다. |
| 동일한 클라우드 서비스에서 여러 VM이 동시에 백업하도록 구성됩니다. | 동일한 클라우드 서비스에서 VM의 백업 일정을 분산하는 것이 모범 사례입니다. |
| VM이 사용량이 높은 CPU 또는 메모리에서 실행 중입니다. | VM이 사용량이 높은 CPU(90% 이상) 또는 메모리에서 실행 중인 경우 스냅숏 작업이 큐에 대기 및 지연되어 결국 시간 초과됩니다. 이 상황에서는 주문형 백업을 시도하세요. |
| VM이 DHCP에서 호스트/패브릭 주소를 가져올 수 없습니다. | IaaS VM 백업이 작동하려면 게스트 내에 DHCP를 사용하도록 설정되어야 합니다.  VM이 DHCP 응답 245에서 호스트/패브릭 주소를 가져올 수 없는 경우에는 어떠한 확장도 다운로드하거나 실행할 수 없습니다. 고정 개인 IP가 필요한 경우 플랫폼을 통해 구성해야 합니다. VM 내 DHCP 옵션은 사용 가능한 상태로 두어야 합니다. 자세한 내용은 [고정 내부 개인 IP 설정](../virtual-network/virtual-networks-reserved-private-ip.md)을 참조하세요. |

### <a name="the-backup-extension-fails-to-update-or-load"></a>백업 확장을 업데이트 또는 로드할 수 없습니다.
확장을 로드할 수 없는 경우 스냅숏을 만들 수 없기 때문에 Backup이 실패합니다.

#### <a name="solution"></a>해결 방법

**Windows 게스트의 경우:** iaasvmprovider 서비스가 사용하도록 설정되어 있는지, *자동* 시작 유형인지를 확인합니다. 서비스가 이렇게 구성되어 있지 않으면 서비스를 사용하도록 설정하여 다음 백업 성공 여부를 결정합니다.

**Linux 게스트의 경우:** VMSnapshot Linux 최신 버전(Backup에 사용되는 확장)은 1.0.91.0입니다.<br>


백업 확장을 여전히 업데이트 또는 로드할 수 없는 경우 확장을 제거하여 VMSnapshot 확장이 다시 로드되도록 할 수 있습니다. 다음 백업 시도 시 확장이 다시 로드됩니다.

확장을 제거하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com/)로 이동합니다.
2. 백업 문제가 있는 VM을 찾습니다.
3. **설정**을 클릭합니다.
4. **확장**을 클릭합니다.
5. **Vmsnapshot 확장**을 클릭합니다.
6. **제거**를 클릭합니다.

이렇게 하면 다음 백업 동안 확장을 다시 설치해야 합니다.


