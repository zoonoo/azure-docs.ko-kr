---
title: Azure 사이트 복구를 사용 하 고 재해 복구에 대 한 Azure VM 확장 문제 해결
description: Azure 사이트 복구를 사용 하 고 재해 복구에 대 한 Azure VM 확장 문제를 해결 합니다.
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.openlocfilehash: a780a42179a0bacf0e4a12ba1e75ae84943539b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190717"
---
# <a name="troubleshoot-azure-vm-extension-issues"></a>Azure VM 확장 문제 해결

이 문서에서는 VM 에이전트 및 확장과 관련된 Azure 사이트 복구 오류를 해결하는 데 도움이 되는 문제 해결 단계를 제공합니다.


## <a name="azure-site-recovery-extension-time-out"></a>Azure 사이트 복구 확장 시간 지정 시간  

오류 메시지: “확장 작업이 시작될 때까지 추적하는 동안 작업 실행 시간이 초과되었습니다.”<br>
오류 코드: “151076”

 Azure 사이트 복구는 활성화 보호 작업의 일부로 가상 시스템에 확장을 설치했습니다. 다음 조건 중 하나에서 보호가 트리거되지 않고 작업이 실패할 수 있습니다. 다음 문제 해결 단계를 완료한 후, 작업을 다시 시도하세요.

- [에이전트가 VM에 설치되어 있지만 응답하지 않습니다(Windows VM의 경우).](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [VM에 설치된 에이전트가 최신이 아닙니다(Linux VM의 경우).](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
- [Site Recovery 확장을 업데이트 또는 로드하지 못합니다.](#the-site-recovery-extension-fails-to-update-or-load)

오류 메시지: "이전 사이트 복구 확장 작업이 예상보다 많은 시간이 걸리고 있습니다."<br>
오류 코드: “150066”

- [에이전트가 VM에 설치되어 있지만 응답하지 않습니다(Windows VM의 경우).](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [VM에 설치된 에이전트가 최신이 아닙니다(Linux VM의 경우).](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
- [Site Recovery 확장 상태가 잘못되었습니다.](#the-site-recovery-extension-fails-to-update-or-load)

## <a name="protection-fails-because-the-vm-agent-is-unresponsive"></a>VM 에이전트가 응답하지 않으므로 보호 기능이 실패합니다.

오류 메시지: “확장 작업이 시작될 때까지 추적하는 동안 작업 실행 시간이 초과되었습니다.”<br>
오류 코드: “151099”

이 오류는 가상 컴퓨터의 Azure 게스트 에이전트가 준비 된 상태에 없는 경우에 발생할 수 있습니다.

[Azure 포털에서](https://portal.azure.com/)Azure 게스트 에이전트의 상태를 확인할 수 있습니다. 보호하려는 가상 컴퓨터로 이동하여 **VM** > **설정** > **속성** > 에이전트**상태의**상태를 확인합니다. 대부분의 경우 가상 컴퓨터를 다시 부팅한 후 에이전트의 상태가 준비됩니다. 그러나 재부팅할 수 없거나 여전히 문제가 발생한 경우 다음 문제 해결 단계를 완료하십시오.

- [에이전트가 VM에 설치되어 있지만 응답하지 않습니다(Windows VM의 경우).](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [VM에 설치된 에이전트가 최신이 아닙니다(Linux VM의 경우).](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)


오류 메시지: “확장 작업이 시작될 때까지 추적하는 동안 작업 실행 시간이 초과되었습니다.”<br>
오류 코드: “151095”

이 오류는 Linux 컴퓨터의 에이전트 버전이 오래된 경우에 발생합니다. 다음 문제 해결 단계를 완료합니다.

- [VM에 설치된 에이전트가 최신이 아닙니다(Linux VM의 경우).](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)  

## <a name="causes-and-solutions"></a>원인 및 해결 방법

### <a name="the-agent-is-installed-in-the-vm-but-its-unresponsive-for-windows-vms"></a><a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>에이전트가 VM에 설치되어 있지만 응답하지 않습니다(Windows VM의 경우).

#### <a name="solution"></a>해결 방법
VM 에이전트가 손상되었거나 서비스가 중지되었습니다. VM 에이전트를 다시 설치하면 최신 버전을 가져올 수 있습니다. 또한 서비스와의 통신을 다시 시작하는 데도 도움이 됩니다.

1. VM 서비스(services.msc)에서 Microsoft Azure 게스트 에이전트 서비스가 실행 중인지 확인합니다. Windows Azure 게스트 에이전트 서비스를 다시 시작합니다.    
1. 서비스에서 Windows Azure 게스트 에이전트 서비스가 표시되지 않으면 제어판을 엽니다. 프로그램 **및 기능으로** 이동하여 Windows 게스트 에이전트 서비스가 설치되어 있는지 확인합니다.
1. Microsoft Azure 게스트 에이전트가 **프로그램 및 기능**에 표시되면 Windows Azure 게스트 에이전트를 제거합니다.
1. [최신 버전의 에이전트 MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)를 다운로드하고 설치합니다. 설치를 완료하려면 관리자 권한이 필요합니다.
1. Windows Azure 게스트 에이전트 서비스가 서비스에 나타나는지 확인합니다.
1. 보호 작업을 다시 시작합니다.

또는 VM에서 [Microsoft .NET 4.5가 설치되어 있는지](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) 확인합니다. VM 에이전트가 서비스와 통신하려면 .NET 4.5가 필요합니다.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>VM에 설치된 에이전트가 최신이 아닙니다(Linux VM의 경우).

#### <a name="solution"></a>해결 방법
Linux VM에 대부분의 에이전트 관련 또는 확장 관련 오류는 이전 VM 에이전트에 영향을 주는 문제로 인해 발생합니다. 이 문제를 해결하려면 다음과 같은 일반 지침을 수행하세요.

1. [Linux VM 에이전트 업데이트](../virtual-machines/linux/update-agent.md)의 지침을 따르세요.

   > [!NOTE]
   > 배포 리포지토리를 사용할 때만 에이전트를 업데이트할 것을 *강력히 권장*합니다. GitHub에서 에이전트 코드를 직접 다운로드하고 업데이트하는 것은 권장되지 않습니다. 배포의 최신 에이전트를 사용할 수 없는 경우 배포 지원에 문의하여 배포 를 설치하는 방법에 대한 지침을 확인합니다. 최신 에이전트를 확인하려면 GitHub 리포지토리의 [Microsoft Azure Linux 에이전트](https://github.com/Azure/WALinuxAgent/releases) 페이지로 이동하세요.

1. `ps -e` 명령을 실행하여 VM에 Azure 에이전트가 실행 중인지 확인합니다.

   이 프로세스가 실행되고 있지 않으면 다음 명령을 사용하여 다시 시작합니다.

   - Ubuntu의 경우: `service walinuxagent start`
   - 기타 배포의 경우: `service waagent start`

1. [자동 다시 시작 에이전트를 구성합니다.](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash)
1. 가상 머신의 보호를 사용하도록 설정합니다.

### <a name="the-site-recovery-extension-fails-to-update-or-load"></a>Site Recovery 확장을 업데이트 또는 로드하지 못함

확장 상태가 "비어 있음", "준비 되지 않음" 또는 "전환"으로 표시됩니다.

#### <a name="solution"></a>해결 방법

확장을 제거하고 작업을 다시 시작합니다.

확장을 제거하려면

1. Azure [포털에서](https://portal.azure.com/)백업 오류가 발생한 VM으로 이동합니다.
1. **설정**을 선택합니다.
1. **확장**을 섡택합니다.
1. **Site Recovery 확장**을 선택합니다.
1. **제거**를 선택합니다.

Linux VM의 경우 VMSnapshot 확장이 Azure 포털에 표시되지 않으면 [Azure Linux 에이전트를 업데이트합니다.](../virtual-machines/linux/update-agent.md) 그런 다음 보호를 실행합니다.

이 단계를 완료하면 보호 중에 확장이 다시 설치됩니다.
