---
title: "Azure VM 백업 실패: 스냅숏 상태에 대해 VM 에이전트와 통신할 수 없음 - 스냅숏 VM 하위 작업 시간 초과 | Microsoft Docs"
description: "Azure VM 백업 실패에 대한 증상 원인 및 해결 방법은 스냅숏 상태에 대해 VM 에이전트와 통신할 수 없는 문제와 관련이 있습니다. 스냅숏 VM 하위 작업 시간 초과 오류"
services: backup
documentationcenter: 
author: genlin
manager: cfreeman
editor: 
ms.assetid: 4b02ffa4-c48e-45f6-8363-73d536be4639
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: jimpark; markgal;genli
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 87387e4f182214fa0c34a6a1358c6cc2648be906


---
# <a name="azure-vm-backup-fails-could-not-communicate-with-the-vm-agent-for-snapshot-status---snapshot-vm-sub-task-timed-out"></a>Azure VM 백업 실패: 스냅숏 상태에 대해 VM 에이전트와 통신할 수 없음 - 스냅숏 VM 하위 작업 시간 초과
## <a name="summary"></a>요약
Azure 백업에 대한 VM(가상 컴퓨터)을 등록 및 예약 후, Azure 백업 서비스는 지정 시간 스냅숏을 생성하는 VM에서 백업 확장과 통신하여 예약된 시간에 백업 작업을 시작합니다. 스냅숏이 트리거되어 백업이 실패하지 않도록 해야 하는 조건이 있습니다. 이 문서에서는 스냅숏 시간 초과 오류와 관련된 Azure VM 백업 오류 관련 문제에 대한 해결 단계를 제공합니다.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>증상
IaaS(서비스 제공 인프라) VM용 Microsoft Azure Backup이 실패하는 경우 [Azure Portal](https://portal.azure.com/)의 작업 오류 세부 정보에서 반환되는 오류 메시지는 다음과 같습니다.

**스냅숏 상태에 대해 VM 에이전트와 통신할 수 없습니다. 스냅숏 VM 하위 작업이 시간 초과되었습니다.**

## <a name="cause"></a>원인
이 오류의 일반적인 원인은 다음과 같이 네 가지가 있습니다.

* VM이 인터넷에 액세스할 수 없습니다.
* VM에 설치된 Microsoft Azure VM 에이전트가 최신이 아닙니다(Linux VM의 경우).
* 백업 확장을 업데이트 또는 로드할 수 없습니다.
* 스냅숏 상태를 검색할 수 없거나 스냅숏을 만들 수 없습니다.

## <a name="cause-1-the-vm-does-not-have-internet-access"></a>원인 1: VM이 인터넷에 액세스할 수 없습니다.
배포 요구 사항에 따라 VM이 인터넷에 연결되어 있지 않거나 Azure 인프라에 대한 액세스를 차단하는 위치에 대한 제한 사항이 있습니다.

백업 확장이 제대로 작동하려면 Azure 공용 IP 주소에 연결되어야 하는데 이는 VM의 스냅숏을 관리하도록 Azure 저장소 끝점(HTTP URL)에 명령을 보내기 때문입니다. 확장이 공용 인터넷에 액세스할 수 없는 경우 백업은 결국 실패합니다.

### <a name="solution"></a>해결 방법
이 시나리오에서 문제를 해결하려면 다음 방법 중 하나를 사용합니다.

* Azure 데이터 센터 IP 범위 허용 목록
* HTTP 트래픽을 보내는 경로 만들기

### <a name="to-whitelist-the-azure-datacenter-ip-ranges"></a>Azure 데이터 센터 IP 범위 허용 목록에 추가하려면
1. 허용 목록에 추가된 [Azure 데이터 센터 IP 목록](https://www.microsoft.com/download/details.aspx?id=41653) 을 가져옵니다.
2. New-NetRoute cmdlet을 사용하여 IP 차단을 해제합니다. 관리자 권한 PowerShell 창(관리자 권한으로 실행)의 Azure VM 내에서 이 cmdlet을 실행합니다.
3. NSG(네트워크 보안 그룹)가 있는 경우 IP에 대한 액세스를 허용하도록 NSG에 규칙을 추가합니다.

### <a name="to-create-a-path-for-http-traffic-to-flow"></a>HTTP 트래픽을 보내는 경로를 만들려면
1. 네트워크 제한이 있는 경우(예: NSG) 트래픽을 라우트하는 HTTP 프록시 서버를 배포합니다.
2. NSG(네트워크 보안 그룹)가 있는 경우 HTTP 프록시에서 인터넷에 액세스할 수 있도록 규칙을 추가합니다.

[VM 백업에 HTTP 프록시를 설정](backup-azure-vms-prepare.md#using-an-http-proxy-for-vm-backups)하는 방법에 대해 알아봅니다.

## <a name="cause-2-the-microsoft-azure-vm-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>원인 2: VM에 설치된 Microsoft Azure VM 에이전트가 최신이 아닙니다(Linux VM의 경우).
### <a name="solution"></a>해결 방법
Linux VM에 대부분의 에이전트 관련 또는 확장 관련 오류는 이전 VM 에이전트에 영향을 주는 문제로 인해 발생합니다. 일반적인 지침으로 이 문제를 해결하는 첫 번째 단계는 다음과 같습니다.

1. [최신 Azure VM 에이전트를 설치합니다](https://github.com/Azure/WALinuxAgent).
2. VM에 Azure 에이전트가 실행 중인지 확인합니다. 이렇게 하려면 ```ps -e``` 명령을 실행합니다.
   
    이 프로세스가 실행되고 있지 않으면 다음 명령을 사용하여 다시 시작합니다.
   
    Ubuntu의 경우: ```service walinuxagent start```
   
    기타 배포의 경우: ```service waagent start
   ```
3. [Configure the auto restart agent](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Run a new test backup. If the failures persist, please collect logs from the following folders for further debugging.
   
    We require the following logs from the customer’s VM:
   
   * /var/lib/waagent/*.xml
   * /var/log/waagent.log
   * /var/log/azure/*

If we require verbose logging for waagent, follow these steps to enable this:

1. In the /etc/waagent.conf file, locate the following line:
   
    Enable verbose logging (y|n)
2. Change the **Logs.Verbose** value from n to y.
3. Save the change, and then restart waagent by following the previous steps in this section.

## Cause 3: The backup extension fails to update or load
If extensions cannot be loaded, then Backup fails because a snapshot cannot be taken.

### Solution
For Windows guests:

1. Verify that the iaasvmprovider service is enabled and has a startup type of automatic.
2. If this is not the configuration, enable the service to determine whether the next backup succeeds.

For Linux guests:

The latest version of VMSnapshot Linux (extension used by backup) is 1.0.91.0

If the backup extension still fails to update or load, you can force the VMSnapshot extension to be reloaded by uninstalling the extension. The next backup attempt will reload the extension.

### To uninstall the extension
1. Go to the [Azure portal](https://portal.azure.com/).
2. Locate the particular VM that has backup problems.
3. Click **Settings**.
4. Click **Extensions**.
5. Click **Vmsnapshot Extension**.
6. Click **uninstall**.

This will cause the extension to be reinstalled during the next backup.

## Cause 4: The snapshots status cannot be retrieved or the snapshots cannot be taken
VM backup relies on issuing snapshot command to underlying storage. The backup can fail because it has no access to storage or because of a delay in snapshot task execution.

### Solution
The following conditions can cause snapshot task failure:

| Cause | Solution |
| --- | --- |
| VMs that have Microsoft SQL Server Backup configured. By default, VM Backup runs a VSS Full backup on Windows VMs. On VMs that are running SQL Server-based servers and on which SQL Server Backup is configured, snapshot execution delays may occur. |Set following registry key if you are experiencing backup failures because of snapshot issues.<br><br>[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT] "USEVSSCOPYBACKUP"="TRUE" |
| VM status is reported incorrectly because the VM is shut down in RDP. If you shut down the virtual machine in RDP, check the portal to determine whether that VM status is reflected correctly. |If it’s not, shut down the VM in the portal by using the ”Shutdown” option in the VM dashboard. |
| Many VMs from the same cloud service are configured to back up at the same time. |It’s a best practice to spread out the VMs from the same cloud service to have different backup schedules. |
| The VM is running at high CPU or memory usage. |If the VM is running at high CPU usage (more than 90 percent) or high memory usage, the snapshot task is queued and delayed and eventually times out. In this situation, try on-demand backup. |
| The VM cannot get host/fabric address from DHCP. |DHCP must be enabled inside the guest for IaaS VM Backup to work.  If the VM cannot get host/fabric address from DHCP response 245, then it cannot download ir run any extensions. If you need a static private IP, you should configure it through the platform. The DHCP option inside the VM should be left enabled. View more information about [Setting a Static Internal Private IP](../virtual-network/virtual-networks-reserved-private-ip.md). |




<!--HONumber=Nov16_HO3-->


