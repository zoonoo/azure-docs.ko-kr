---
title: Azure Site Recovery를 사용하여 재해 복구를 위한 Azure VM 확장 문제 해결
description: Azure Site Recovery를 사용하여 재해 복구를 위한 Azure VM 확장 관련 문제를 해결합니다.
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.openlocfilehash: c1915d108bf9465d3e5b8d6a55053b583ee4f580
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96009707"
---
# <a name="troubleshoot-azure-vm-extension-issues"></a>Azure VM 확장 문제 해결

이 문서에서는 VM 에이전트 및 확장과 관련된 Azure Site Recovery 오류를 해결하는 데 유용한 문제 해결 단계를 제공합니다.

## <a name="low-system-resources"></a>시스템 리소스 부족

이 문제는 시스템에 사용 가능한 메모리가 부족하여 모바일 서비스 설치에 메모리를 할당할 수 없는 경우에 발생합니다. 설치를 계속하고 성공적으로 완료하려면 충분한 메모리를 확보해야 합니다.

## <a name="azure-site-recovery-extension-time-out"></a>Azure Site Recovery 확장 시간 제한  

오류 메시지: “확장 작업이 시작될 때까지 추적하는 동안 작업 실행 시간이 초과되었습니다.”<br>
오류 코드: “151076”

 Azure Site Recovery에서는 보호 작업 사용 과정의 일부로 가상 머신에 확장을 설치합니다. 다음 조건 중 하나라도 충족되지 않을 경우 보호 기능이 트리거되지 않고 작업이 실패할 수 있습니다. 다음 문제 해결 단계를 완료한 후, 작업을 다시 시도하세요.

- [에이전트가 VM에 설치되어 있지만 응답하지 않습니다(Windows VM의 경우).](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [VM에 설치된 에이전트가 최신이 아닙니다(Linux VM의 경우).](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
- [Site Recovery 확장을 업데이트 또는 로드하지 못합니다.](#the-site-recovery-extension-fails-to-update-or-load)

오류 메시지: “이전 Site Recovery 확장 작업이 예상보다 더 오래 걸립니다.”<br>
오류 코드: “150066”

- [에이전트가 VM에 설치되어 있지만 응답하지 않습니다(Windows VM의 경우).](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [VM에 설치된 에이전트가 최신이 아닙니다(Linux VM의 경우).](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
- [Site Recovery 확장 상태가 잘못되었습니다.](#the-site-recovery-extension-fails-to-update-or-load)

## <a name="protection-fails-because-the-vm-agent-is-unresponsive"></a>VM 에이전트가 응답하지 않으므로 보호 기능이 실패합니다.

오류 메시지: “확장 작업이 시작될 때까지 추적하는 동안 작업 실행 시간이 초과되었습니다.”<br>
오류 코드: “151099”

이 오류는 가상 머신의 Azure 게스트 에이전트가 준비 상태가 아닌 경우에 발생할 수 있습니다.

[Azure Portal](https://portal.azure.com/)에서 Azure 게스트 에이전트의 상태를 확인할 수 있습니다. 보호하려는 가상 머신으로 이동하여 **VM** > **설정** > **속성** > **에이전트 상태** 를 확인합니다. 대부분의 경우 가상 머신을 다시 부팅한 후 에이전트는 준비 상태가 됩니다. 그러나 다시 부팅할 수 없거나 여전히 문제가 발생하는 경우 다음 문제 해결 단계를 완료합니다.

- [에이전트가 VM에 설치되어 있지만 응답하지 않습니다(Windows VM의 경우).](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [VM에 설치된 에이전트가 최신이 아닙니다(Linux VM의 경우).](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)


오류 메시지: “확장 작업이 시작될 때까지 추적하는 동안 작업 실행 시간이 초과되었습니다.”<br>
오류 코드: “151095”

이 오류는 Linux 컴퓨터의 에이전트 버전이 만료된 경우에 발생합니다. 다음 문제 해결 단계를 완료합니다.

- [VM에 설치된 에이전트가 최신이 아닙니다(Linux VM의 경우).](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)  

## <a name="causes-and-solutions"></a>원인 및 해결 방법

### <a name="the-agent-is-installed-in-the-vm-but-its-unresponsive-for-windows-vms"></a><a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>에이전트가 VM에 설치되어 있지만 응답하지 않습니다(Windows VM의 경우).

#### <a name="solution"></a>솔루션
VM 에이전트가 손상되었거나 서비스가 중지되었습니다. VM 에이전트를 다시 설치하면 최신 버전을 가져올 수 있습니다. 또한 서비스와의 통신을 다시 시작하는 데도 도움이 됩니다.

1. VM 서비스(services.msc)에서 Microsoft Azure 게스트 에이전트 서비스가 실행 중인지 확인합니다. Windows Azure 게스트 에이전트 서비스를 다시 시작합니다.    
1. Windows Azure 게스트 에이전트 서비스가 서비스에 표시되지 않는 경우 제어판을 엽니다. **프로그램 및 기능** 으로 이동하여 Windows 게스트 에이전트 서비스가 설치되어 있는지 확인합니다.
1. Microsoft Azure 게스트 에이전트가 **프로그램 및 기능** 에 표시되면 Windows Azure 게스트 에이전트를 제거합니다.
1. [최신 버전의 에이전트 MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)를 다운로드하고 설치합니다. 설치를 완료하려면 관리자 권한이 있어야 합니다.
1. Windows Azure 게스트 에이전트 서비스가 서비스에 표시되는지 확인합니다.
1. 보호 작업을 다시 시작합니다.

또는 VM에서 [Microsoft .NET 4.5가 설치되어 있는지](/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) 확인합니다. 서비스와 통신하려면 VM 에이전트용 .NET 4.5가 필요합니다.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>VM에 설치된 에이전트가 최신이 아닙니다(Linux VM의 경우).

#### <a name="solution"></a>솔루션
Linux VM에 대부분의 에이전트 관련 또는 확장 관련 오류는 이전 VM 에이전트에 영향을 주는 문제로 인해 발생합니다. 이 문제를 해결하려면 다음과 같은 일반 지침을 수행하세요.

1. [Linux VM 에이전트 업데이트](../virtual-machines/extensions/update-linux-agent.md)의 지침을 따르세요.

   > [!NOTE]
   > 배포 리포지토리를 사용할 때만 에이전트를 업데이트할 것을 *강력히 권장* 합니다. GitHub에서 에이전트 코드를 직접 다운로드하고 업데이트하는 것은 권장하지 않습니다. 최신 에이전트를 배포할 수 없는 경우 배포 고객 지원팀에 문의하여 설치 방법에 대한 지침을 얻으세요. 최신 에이전트를 확인하려면 GitHub 리포지토리의 [Microsoft Azure Linux 에이전트](https://github.com/Azure/WALinuxAgent/releases) 페이지로 이동하세요.

1. `ps -e` 명령을 실행하여 VM에 Azure 에이전트가 실행 중인지 확인합니다.

   이 프로세스가 실행되고 있지 않으면 다음 명령을 사용하여 다시 시작합니다.

   - Ubuntu의 경우: `service walinuxagent start`
   - 기타 배포의 경우: `service waagent start`

1. [에이전트 자동 다시 시작을 구성합니다](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
1. 가상 머신의 보호를 사용하도록 설정합니다.

### <a name="the-site-recovery-extension-fails-to-update-or-load"></a>Site Recovery 확장을 업데이트 또는 로드하지 못함

확장 상태는 "Empty", "NotReady" 또는 "Transitioning"으로 표시됩니다.

#### <a name="solution"></a>솔루션

확장을 제거하고 작업을 다시 시작합니다.

확장을 제거하려면

1. [Azure Portal](https://portal.azure.com/)에서 백업 실패가 발생하는 VM으로 이동합니다.
1. **설정** 을 선택합니다.
1. **확장** 을 섡택합니다.
1. **Site Recovery 확장** 을 선택합니다.
1. **제거** 를 선택합니다.

VMSnapshot 확장이 Azure Portal에 표시되지 않는 경우, Linux VM은 [Azure Linux 에이전트를 업데이트합니다](../virtual-machines/extensions/update-linux-agent.md). 그 후 보호를 실행합니다.

이 단계를 완료하면 보호 기능을 사용하는 동안 확장이 다시 설치됩니다.
