---
title: 서비스 연결 문제 해결 Windows Virtual Desktop - Azure
description: Windows Virtual Desktop 테넌트 환경에서 클라이언트 연결을 설정할 때 발생하는 문제를 해결하는 방법입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 05/20/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 7bf05fe039de2ab9e25495f9e2652fde8fac34e1
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83747706"
---
# <a name="windows-virtual-desktop-service-connections"></a>Windows Virtual Desktop 서비스 연결

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Windows Virtual Desktop 개체를 사용하여 2020년 봄 업데이트에 적용됩니다. Azure Resource Manager 개체 없이 Windows Virtual Desktop 2019년 가을 릴리스를 사용하는 경우 [이 문서](./virtual-desktop-fall-2019/troubleshoot-service-connection-2019.md)를 참조하세요.
>
> Windows Virtual Desktop 2020 봄 업데이트는 현재 공개 미리 보기로 제공됩니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서를 사용하여 Windows Virtual Desktop 클라이언트 연결 문제를 해결합니다.

## <a name="provide-feedback"></a>피드백 제공

[Windows Virtual Desktop 기술 커뮤니티](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)에서 제품 팀 및 기타 활성 커뮤니티 멤버와 Windows Virtual Desktop Service에 대해 피드백을 제공하고 논의할 수 있습니다.

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>사용자가 연결했지만 아무것도 표시되지 않음(피드 없음)

사용자는 원격 데스크톱 클라이언트를 시작하고 인증할 수 있지만 웹 검색 피드에 아이콘이 표시되지 않습니다.

다음 명령줄을 사용하여 문제를 보고하는 사용자가 애플리케이션 그룹에 할당되었는지 확인합니다.

```PowerShell
Get-AzRoleAssignment -SignInName <userupn>
```

사용자가 올바른 자격 증명을 사용하여 로그인하고 있는지 확인합니다.

웹 클라이언트를 사용하는 경우 캐시된 자격 증명 문제가 없는지 확인합니다.

## <a name="next-steps"></a>다음 단계

- Windows Virtual Desktop 및 에스컬레이션 트랙 문제 해결에 대한 개요는 [문제 해결 개요, 피드백 및 지원](troubleshoot-set-up-overview.md)을 참조하세요.
- Windows Virtual Desktop 환경에서 Windows Virtual Desktop 환경 및 호스트 풀을 만드는 데 발생하는 문제를 해결하려면 [환경 및 호스트 풀 만들기](troubleshoot-set-up-issues.md)를 참조하세요.
- Windows Virtual Desktop에서 VM(가상 머신)을 구성하면서 생기는 문제를 해결하려면 [세션 호스트 가상 머신 구성](troubleshoot-vm-configuration.md)을 참조하세요.
- Windows Virtual Desktop과 함께 PowerShell을 사용할 때 발생하는 문제를 해결하려면 [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)을 참조하세요.
- 문제 해결 자습서를 진행하려면 [자습서: Resource Manager 템플릿 배포 문제 해결](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)을 참조하세요.
