---
title: Azure Linux 게스트 에이전트 문제 해결
description: Azure Linux 게스트 에이전트가 작동 하지 않는 문제 해결
services: virtual-machines-linux
ms.service: virtual-machines-linux
author: axelg
manager: dcscontentpm
editor: ''
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/17/2020
ms.author: axelg
ms.openlocfilehash: 9e3b376cfaf5379acaf92713c42509471200d066
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95550001"
---
# <a name="troubleshooting-azure-linux-guest-agent"></a>Azure Linux 게스트 에이전트 문제 해결

[Azure Linux 게스트 에이전트](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) 는 VM (가상 머신) 에이전트입니다. IP 주소 168.63.129.16에서 VM이 패브릭 컨트롤러 (VM이 호스트 되는 기본 실제 서버)와 통신할 수 있습니다. 이 IP 주소는 통신을 용이 하 게 하는 가상 공용 IP 주소입니다. 자세한 내용은 [IP 주소 168.63.129.16?](../../virtual-network/what-is-ip-address-168-63-129-16.md)을 참조 하세요.

## <a name="checking-agent-status-and-version"></a>에이전트 상태 및 버전 확인

https://github.com/Azure/WALinuxAgent/wiki/FAQ#what-does-goal-state-agent-mean-in-waagent---version-output을 참조하십시오.

## <a name="troubleshooting-vm-agent-that-is-in-not-ready-status"></a>준비 안 됨 상태의 VM 에이전트 문제 해결

### <a name="step-1-check-whether-the-azure-linux-guest-agent-service-is-running"></a>1 단계 Azure Linux 게스트 에이전트 서비스가 실행 되 고 있는지 확인

배포판에 따라 서비스 이름은 walinuxagent 또는 waagent 일 수 있습니다.

```
root@nam-u18:/home/nam# service walinuxagent status
● walinuxagent.service - Azure Linux Agent
   Loaded: loaded (/lib/systemd/system/walinuxagent.service; enabled; vendor preset: enabled)
   Active: active (running) since Thu 2020-10-08 17:10:29 UTC; 3min 9s ago
 Main PID: 1036 (python3)
    Tasks: 4 (limit: 4915)
   CGroup: /system.slice/walinuxagent.service
           ├─1036 /usr/bin/python3 -u /usr/sbin/waagent -daemon
           └─1156 python3 -u bin/WALinuxAgent-2.2.51-py2.7.egg -run-exthandlers

Oct 08 17:10:33 nam-u18 python3[1036]: 2020-10-08T17:10:33.129375Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.OSTCExtensions.VMAccessForLinux-1.5.10, path: /sys/fs/cgroup/memory/sys
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.189020Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Target handler state: enabled [incarnation 2]
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.197932Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] [Enable] current handler state is: enabled
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.212316Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Update settings file: 0.settings
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.224062Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Enable extension [bin/run-command-shim enable]
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.236993Z INFO ExtHandler ExtHandler Started extension in unit 'Microsoft.CPlat.Core.RunCommandLinux_1.0.1_db014406-294a-49ed-b112-c7912a86ae9e
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.263572Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.CPlat.Core.RunCommandLinux-1.0.1, path: /sys/fs/cgroup/cpu,cpuacct/syst
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.280691Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.CPlat.Core.RunCommandLinux-1.0.1, path: /sys/fs/cgroup/memory/system.sl
Oct 08 17:10:37 nam-u18 python3[1036]: 2020-10-08T17:10:37.349090Z INFO ExtHandler ExtHandler ProcessGoalState completed [incarnation 2; 4496 ms]
Oct 08 17:10:37 nam-u18 python3[1036]: 2020-10-08T17:10:37.365590Z INFO ExtHandler ExtHandler [HEARTBEAT] Agent WALinuxAgent-2.2.51 is running as the goal state agent [DEBUG HeartbeatCounter: 1;Heartb
root@nam-u18:/home/nam#
```


서비스를 볼 수 있고 실행 중인 경우에는 서비스를 다시 시작 하 여 문제가 해결 되었는지 확인 합니다. 서비스가 중지 된 경우 서비스를 시작 하 고 몇 분 정도 기다립니다. 그런 다음 **에이전트 상태가** **준비** 로 보고 되는지 확인 합니다. 이러한 문제를 추가로 해결 하려면 [Microsoft 지원](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)에 문의 하세요.

### <a name="step-2-check-whether-auto-update-is-enabled"></a>2 단계 자동 업데이트가 사용 되는지 여부 확인

/Etc/waagent.conf에서 다음 설정을 확인 합니다.

```
AutoUpdate.Enabled=y
```

Azure Linux 에이전트를 업데이트 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요. https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent 
    

### <a name="step-3-check-whether-the-vm-can-connect-to-the-fabric-controller"></a>3 단계 VM이 패브릭 컨트롤러에 연결할 수 있는지 확인 합니다.

168.63.129.16와 같은 도구를 사용 하 여 VM이 80, 32526 및 443 포트의에 연결할 수 있는지 여부를 테스트 합니다. VM이 정상적으로 연결 되지 않으면 VM의 로컬 방화벽에서 포트 80, 443 및 32526을 통한 아웃 바운드 통신이 열려 있는지 확인 합니다. 이 IP 주소가 차단되는 경우 VM 에이전트는 다양한 시나리오에서 예기치 않은 동작을 표시할 수 있습니다.

## <a name="advanced-troubleshooting"></a>고급 문제 해결

Azure Linux 게스트 에이전트 문제 해결에 대 한 이벤트는 다음 로그 파일에 기록 됩니다.

- /var/log/waagent.log


  
### <a name="unable-to-connect-to-wireserver-ip-host-ip"></a>WireServer IP (호스트 IP)에 연결할 수 없습니다. 

/Var/log/waagent.log에서 다음 오류 항목을 확인할 수 있습니다.

```
2020-10-02T18:11:13.148998Z WARNING ExtHandler ExtHandler An error occurred while retrieving the goal state:
```

**분석**

VM이 호스트 서버의 WireServer IP에 도달할 수 없습니다.

**해결 방법**

1. WireServer IP에 연결할 수 없기 때문에 SSH를 사용 하 여 VM에 연결한 후 말아에서 다음 URL에 액세스를 시도 합니다. http://168.63.129.16/?comp=versions 
1. 방화벽, 프록시 또는 IP 주소 168.63.129.16에 대 한 액세스를 차단할 수 있는 다른 원본으로 인해 발생할 수 있는 문제를 확인 합니다.
1. Linux IPTables 또는 타사 방화벽에서 포트 80, 443 및 32526에 대 한 액세스를 차단 하는지 확인 합니다. 이 주소를 차단 하지 않아야 하는 이유에 대 한 자세한 내용은 [IP 주소 168.63.129.16?](../../virtual-network/what-is-ip-address-168-63-129-16.md)을 참조 하세요.


## <a name="next-steps"></a>다음 단계

"Windows Azure 게스트 에이전트가 작동 하지 않습니다." 문제를 추가로 해결 하려면 [Microsoft 지원에 문의 하세요](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
