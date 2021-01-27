---
title: Azure Linux 에이전트 문제 해결
description: Azure Linux 에이전트의 문제를 해결 합니다.
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
ms.openlocfilehash: 62b462d8e75fc291ac599ac99dbe4fb3a74fde2b
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878700"
---
# <a name="troubleshoot-the-azure-linux-agent"></a>Azure Linux 에이전트 문제 해결

[Azure Linux 에이전트](../extensions/agent-linux.md) 를 사용 하면 vm (가상 컴퓨터)에서 IP 주소 168.63.129.16의 패브릭 컨트롤러 (vm이 호스트 되는 기본 실제 서버)와 통신할 수 있습니다.

>[!NOTE]
>이 IP 주소는 통신을 용이 하 게 하는 가상 공용 IP 주소 이며 차단 되어서는 안 됩니다. 자세한 내용은 [IP 주소 168.63.129.16?](../../virtual-network/what-is-ip-address-168-63-129-16.md)을 참조 하세요.

## <a name="before-you-begin"></a>시작하기 전에

에이전트 상태 및 버전이 아직 지원 되는지 확인 합니다. 버전 지원을 확인 하려면 [Azure의 가상 머신 에이전트에 대 한 최소 버전 지원](/troubleshoot/azure/virtual-machines/support-extensions-agent-version) 을 참조 하거나, 상태 및 버전을 확인 하는 단계는 [WALinuxAgent FAQ](https://github.com/Azure/WALinuxAgent/wiki/FAQ#what-does-goal-state-agent-mean-in-waagent---version-output) 를 참조 하세요.

## <a name="troubleshoot-a-not-ready-status"></a>준비 안 됨 상태 문제 해결

1. Azure Linux 에이전트의 서비스 상태를 확인 하 여 실행 중인지 확인 합니다. 서비스 이름은 **walinuxagent** 또는 **waagent** 일 수 있습니다.

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

   서비스가 실행 되 고 있으면 다시 시작 하 여 문제를 해결 합니다. 서비스가 중지 된 경우 서비스를 시작 하 고 몇 분 정도 기다린 후 상태를 다시 확인 합니다.

1. 자동 업데이트를 사용 하도록 설정 했는지 확인 합니다. **/Etc/waagent.conf** 에서 자동 업데이트 설정을 확인 합니다.

   ```
   AutoUpdate.Enabled=y
   ```

   Azure Linux 에이전트를 업데이트 하는 방법에 대 한 자세한 내용은 [VM에서 Azure Linux 에이전트를 업데이트](../extensions/update-linux-agent.md)하는 방법을 참조 하세요.

1. VM이 패브릭 컨트롤러에 연결할 수 있는지 확인 합니다. 168.63.129.16와 같은 도구를 사용 하 여 VM이 80, 443 및 32526 포트의에 연결할 수 있는지 여부를 테스트 합니다. VM이 정상적으로 연결 되지 않으면 VM의 로컬 방화벽에서 포트 80, 443 및 32526을 통한 아웃 바운드 통신이 열려 있는지 확인 합니다. 이 IP 주소가 차단 되 면 VM 에이전트가 예기치 않은 동작을 표시할 수 있습니다.

## <a name="advanced-troubleshooting"></a>고급 문제 해결

Azure Linux 에이전트 문제 해결에 대 한 이벤트는 **/var/log/waagent.log** 파일에 기록 됩니다.

### <a name="unable-to-connect-to-wireserver-ip-host-ip"></a>WireServer IP (호스트 IP)에 연결할 수 없습니다.

VM이 호스트 서버의 WireServer IP에 도달할 수 없는 경우 **/var/log/waagent.log** 파일에 다음 오류가 나타납니다.

```
2020-10-02T18:11:13.148998Z WARNING ExtHandler ExtHandler An error occurred while retrieving the goal state:
```

이 문제를 해결하려면:

* SSH를 사용 하 여 VM에 연결한 후 말아:에서 다음 URL에 액세스를 시도 http://168.63.129.16/?comp=versions 합니다.
* 방화벽, 프록시 또는 IP 주소 168.63.129.16에 대 한 액세스를 차단할 수 있는 다른 원본으로 인해 발생할 수 있는 문제를 확인 합니다.
* Linux IPTables 또는 타사 방화벽에서 포트 80, 443 및 32526에 대 한 액세스를 차단 하는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

Azure Linux 에이전트 문제를 추가로 해결 하려면 [Microsoft 지원에 문의 하세요](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).