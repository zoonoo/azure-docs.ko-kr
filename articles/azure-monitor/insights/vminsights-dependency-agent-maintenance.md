---
title: VM 종속성 에이전트에 대한 Azure 모니터를 업그레이드하는 방법
description: 이 문서에서는 명령줄, 설치 마법사 및 기타 메서드를 사용하여 VM 종속성 에이전트에 대한 Azure 모니터를 업그레이드하는 방법에 대해 설명합니다.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: c55bee9880c4134f2e304a7fc5176225477fe5f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480762"
---
# <a name="how-to-upgrade-the-azure-monitor-for-vms-dependency-agent"></a>VM 종속성 에이전트에 대한 Azure 모니터를 업그레이드하는 방법

VM 종속성 에이전트에 대한 Azure 모니터를 초기 배포한 후 버그 수정 또는 새로운 기능 또는 기능에 대한 지원이 포함된 업데이트가 릴리스됩니다.  이 문서에서는 사용 가능한 방법과 수동으로 또는 자동화를 통해 업그레이드를 수행하는 방법을 이해하는 데 도움이 됩니다.

## <a name="upgrade-options"></a>업그레이드 옵션 

Windows 및 Linux용 종속성 에이전트는 컴퓨터가 실행 중인 배포 시나리오 및 환경에 따라 수동으로 또는 자동으로 최신 릴리스로 업그레이드할 수 있습니다. 에이전트를 업그레이드하는 데 다음 방법을 사용할 수 있습니다.

|Environment |설치 방법 |업그레이드 방법 |
|------------|--------------------|---------------|
|Azure VM | [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) 및 [Linux용](../../virtual-machines/extensions/agent-dependency-linux.md) 종속성 에이전트 VM 확장 | 속성 *autoUpgradeMinorVersion을* **false로**설정하여 옵트아웃하도록 Azure 리소스 관리자 템플릿을 구성하지 않는 한 에이전트는 기본적으로 자동으로 업그레이드됩니다. 자동 업그레이드가 비활성화된 부 버전의 업그레이드와 주 버전 업그레이드는 확장을 제거하고 다시 설치하는 동일한 방법을 따릅니다. |
| 사용자 지정 Azure VM 이미지 | Windows/Linux용 종속성 에이전트의 수동 설치 | Windows 설치 관리자 패키지 또는 Linux 자체 추출 및 설치 가능한 셸 스크립트 번들을 실행하는 명령줄에서 에이전트의 최신 버전으로 VM을 업데이트해야 합니다.|
| 비Azure VM | Windows/Linux용 종속성 에이전트의 수동 설치 | Windows 설치 관리자 패키지 또는 Linux 자체 추출 및 설치 가능한 셸 스크립트 번들을 실행하는 명령줄에서 에이전트의 최신 버전으로 VM을 업데이트해야 합니다. |

## <a name="upgrade-windows-agent"></a>Windows 에이전트 업그레이드 

Windows VM의 에이전트를 종속성 에이전트 VM 확장을 사용하여 설치되지 않은 최신 버전으로 업데이트하려면 명령 프롬프트, 스크립트 또는 기타 자동화 솔루션에서 실행하거나 InstallDependencyAgent-Windows.exe 설치 마법사를 사용하여 실행합니다.  

여기에서 최신 버전의 Windows 에이전트를 다운로드할 수 [있습니다.](https://aka.ms/dependencyagentwindows)

### <a name="using-the-setup-wizard"></a>설정 마법사 사용

1. 관리 권한이 있는 계정으로 컴퓨터에 로그인합니다.

2. **설치를 실행의존에이전트-Windows.exe** 설치 마법사를 시작합니다.
   
3. **종속성 에이전트 설치** 마법사를 따라 종속성 에이전트의 이전 버전을 제거한 다음 최신 버전을 설치합니다.


### <a name="from-the-command-line"></a>명령줄에서

1. 관리 권한이 있는 계정으로 컴퓨터에 로그인합니다.

2. 다음 명령을 실행합니다.

    ```dos
    InstallDependencyAgent-Windows.exe /S /RebootMode=manual
    ```

    이 `/RebootMode=manual` 매개 변수는 일부 프로세스가 이전 버전의 파일을 사용하고 있고 잠금이 있는 경우 업그레이드가 자동으로 컴퓨터를 재부팅하지 못하게 합니다. 

3. 업그레이드가 성공적으로 시작됐는지 `install.log` 확인하려면 자세한 설정 정보를 확인하십시오. 로그 디렉터리는 *%Programfiles%\Microsoft Dependency Agent\logs*입니다.

## <a name="upgrade-linux-agent"></a>리눅스 에이전트 업그레이드 

Linux의 종속성 에이전트의 이전 버전에서 업그레이드가 지원되고 새 설치와 동일한 명령에 따라 수행됩니다.

여기에서 최신 버전의 Windows 에이전트를 다운로드할 수 [있습니다.](https://aka.ms/dependencyagentlinux)

1. 관리 권한이 있는 계정으로 컴퓨터에 로그인합니다.

2. 다음 명령을 루트로`sh InstallDependencyAgent-Linux64.bin -s`실행합니다. 

Dependency Agent를 시작하지 못하는 경우 로그에서 자세한 오류 정보를 확인합니다. Linux 에이전트에서 로그 디렉터리 */var/opt/microsoft/종속성 에이전트/로그입니다.* 

## <a name="next-steps"></a>다음 단계

시간 동안 VM 모니터링을 중지하거나 VM에 대한 Azure 모니터를 완전히 제거하려면 [VM에 대한 Azure 모니터의 모니터링 을 사용하지 않도록](vminsights-optout.md)설정합니다.
