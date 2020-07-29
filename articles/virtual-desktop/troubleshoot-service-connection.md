---
title: 서비스 연결 문제 해결 Windows Virtual Desktop - Azure
description: Windows Virtual Desktop 테넌트 환경에서 클라이언트 연결을 설정할 때 발생하는 문제를 해결하는 방법입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 06/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 875f39653c5240e3a1b571b531eb2bb08c4811d0
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87289845"
---
# <a name="windows-virtual-desktop-service-connections"></a>Windows Virtual Desktop 서비스 연결

>[!IMPORTANT]
>이 콘텐츠는 windows 가상 데스크톱 개체가 Azure Resource Manager windows 가상 데스크톱에 적용 됩니다. Azure Resource Manager 개체 없이 Windows 가상 데스크톱 (클래식)을 사용 하는 경우 [이 문서](./virtual-desktop-fall-2019/troubleshoot-service-connection-2019.md)를 참조 하세요.

이 문서를 사용하여 Windows Virtual Desktop 클라이언트 연결 문제를 해결합니다.

## <a name="provide-feedback"></a>피드백 제공

[Windows Virtual Desktop 기술 커뮤니티](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)에서 제품 팀 및 기타 활성 커뮤니티 멤버와 Windows Virtual Desktop Service에 대해 피드백을 제공하고 논의할 수 있습니다.

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>사용자가 연결했지만 아무것도 표시되지 않음(피드 없음)

사용자는 원격 데스크톱 클라이언트를 시작하고 인증할 수 있지만 웹 검색 피드에 아이콘이 표시되지 않습니다.

1. 다음 명령줄을 사용하여 문제를 보고하는 사용자가 애플리케이션 그룹에 할당되었는지 확인합니다.

     ```powershell
     Get-AzRoleAssignment -SignInName <userupn>
     ```

2. 사용자가 올바른 자격 증명을 사용하여 로그인하고 있는지 확인합니다.

3. 웹 클라이언트를 사용하는 경우 캐시된 자격 증명 문제가 없는지 확인합니다.

4. 사용자가 AD (Azure Active Directory) 사용자 그룹의 일부인 경우에는 사용자 그룹이 메일 그룹 대신 보안 그룹 인지 확인 합니다. Windows 가상 데스크톱은 Azure AD 메일 그룹을 지원 하지 않습니다.

## <a name="next-steps"></a>다음 단계

- Windows Virtual Desktop 및 에스컬레이션 트랙 문제 해결에 대한 개요는 [문제 해결 개요, 피드백 및 지원](troubleshoot-set-up-overview.md)을 참조하세요.
- Windows Virtual Desktop 환경에서 Windows Virtual Desktop 환경 및 호스트 풀을 만드는 데 발생하는 문제를 해결하려면 [환경 및 호스트 풀 만들기](troubleshoot-set-up-issues.md)를 참조하세요.
- Windows Virtual Desktop에서 VM(가상 머신)을 구성하면서 생기는 문제를 해결하려면 [세션 호스트 가상 머신 구성](troubleshoot-vm-configuration.md)을 참조하세요.
- Windows Virtual Desktop과 함께 PowerShell을 사용할 때 발생하는 문제를 해결하려면 [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)을 참조하세요.
- 문제 해결 자습서를 진행하려면 [자습서: Resource Manager 템플릿 배포 문제 해결](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)을 참조하세요.
