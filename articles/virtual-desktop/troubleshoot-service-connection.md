---
title: 서비스 연결 문제 해결 Azure Virtual Desktop - Azure
description: Azure Virtual Desktop 테넌트 환경에서 서비스 연결을 설정하는 동안 발생하는 문제를 해결하는 방법입니다.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 10/15/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: cce3d219158111d147849e45fb0990f41e2423ab
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111756746"
---
# <a name="azure-virtual-desktop-service-connections"></a>Azure Virtual Desktop 서비스 연결

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Azure Virtual Desktop 개체를 통해 Azure Virtual Desktop에 적용됩니다. Azure Resource Manager 개체 없이 Azure Virtual Desktop(클래식)을 사용하는 경우 [이 문서](./virtual-desktop-fall-2019/troubleshoot-service-connection-2019.md)를 참조하세요.

이 문서를 사용하여 Azure Virtual Desktop 클라이언트 연결 문제를 해결합니다.

## <a name="provide-feedback"></a>피드백 제공

[Azure Virtual Desktop 기술 커뮤니티](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)에서 제품 팀 및 기타 활성 커뮤니티 멤버와 함께 Azure Virtual Desktop Service에 대해 피드백을 제공하고 논의할 수 있습니다.

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>사용자가 연결했지만 아무것도 표시되지 않음(피드 없음)

사용자는 원격 데스크톱 클라이언트를 시작하고 인증할 수 있지만 웹 검색 피드에 아이콘이 표시되지 않습니다.

1. 다음 명령줄을 사용하여 문제를 보고하는 사용자가 애플리케이션 그룹에 할당되었는지 확인합니다.

     ```powershell
     Get-AzRoleAssignment -SignInName <userupn>
     ```

2. 사용자가 올바른 자격 증명을 사용하여 로그인하고 있는지 확인합니다.

3. 웹 클라이언트를 사용하는 경우 캐시된 자격 증명 문제가 없는지 확인합니다.

4. 사용자가 Azure AD(Active Directory) 사용자 그룹의 일부인 경우에는 사용자 그룹이 배포 그룹이 아닌 보안 그룹인지 확인합니다. Azure Virtual Desktop은 Azure AD 배포 그룹을 지원하지 않습니다.

## <a name="user-loses-existing-feed-and-no-remote-resource-is-displayed-no-feed"></a>사용자가 기존 피드를 잃고 원격 리소스가 표시되지 않음(피드 없음)

이 오류는 일반적으로 사용자가 Azure AD 테넌트 간에 구독을 이동한 후에 나타납니다. 따라서 서비스는 여전히 이전 Azure AD 테넌트에 연결되어 있으므로 사용자 할당을 추적하지 못합니다.

이 문제를 해결하려면 사용자를 앱 그룹에 다시 할당하기만 하면 됩니다.

이는 CSP 공급자가 구독을 만든 다음, 고객에게 전송한 경우에도 발생할 수 있습니다. 이 문제를 해결하려면 리소스 공급자를 다시 등록합니다.

1. Azure Portal에 로그인합니다.
2. **구독** 으로 이동한 다음, 구독을 선택합니다.
3. 페이지 왼쪽에 있는 메뉴에서 **리소스 공급자** 를 선택합니다.
4. **Microsoft.DesktopVirtualization** 을 찾아 선택한 다음, **다시 등록** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

- Azure Virtual Desktop 및 에스컬레이션 트랙 문제 해결에 대한 개요는 [문제 해결 개요, 피드백 및 지원](troubleshoot-set-up-overview.md)을 참조하세요.
- Azure Virtual Desktop 환경에서 Azure Virtual Desktop 환경 및 호스트 풀을 만드는 데 발생하는 문제를 해결하려면 [환경 및 호스트 풀 만들기](troubleshoot-set-up-issues.md)를 참조하세요.
- Azure Virtual Desktop에서 VM(가상 머신)을 구성하는 동안 문제를 해결하려면 [세션 호스트 가상 머신 구성](troubleshoot-vm-configuration.md)을 참조하세요.
- Azure Virtual Desktop 에이전트 또는 세션 연결과 관련된 문제를 해결하려면 [일반적인 Azure Virtual Desktop 에이전트 문제 해결](troubleshoot-agent.md)을 참조하세요.
- Azure Virtual Desktop과 함께 PowerShell을 사용할 때 발생하는 문제를 해결하려면 [Azure Virtual Desktop PowerShell](troubleshoot-powershell.md)을 참조하세요.
- 문제 해결 자습서를 진행하려면 [자습서: Resource Manager 템플릿 배포 문제 해결](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)을 참조하세요.
