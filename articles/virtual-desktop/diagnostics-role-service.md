---
title: Windows Virtual Desktop 진단 문제 - Azure
description: Windows Virtual Desktop 진단 기능을 사용하여 문제를 진단하는 방법.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: a985ce4f93b04e4065b5189b2a406b54729720c3
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "88005088"
---
# <a name="identify-and-diagnose-windows-virtual-desktop-issues"></a>Windows 가상 데스크톱 문제 식별 및 진단

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Windows Virtual Desktop 개체를 통해 Windows Virtual Desktop에 적용됩니다. Azure Resource Manager 개체 없이 Windows Virtual Desktop(클래식)을 사용하는 경우 [이 문서](./virtual-desktop-fall-2019/diagnostics-role-service-2019.md)를 참조하세요.

Windows Virtual Desktop은 관리자가 단일 인터페이스를 통해 문제를 식별할 수 있도록 지원하는 진단 기능을 제공합니다. Windows 가상 데스크톱의 진단 기능에 대해 자세히 알아보려면 [진단 기능에 Log Analytics 사용](diagnostics-log-analytics.md)을 참조 하세요.

진단 역할 서비스 자체가 Windows Virtual Desktop의 일부이므로 Windows Virtual Desktop에 도달하지 않는 연결은 진단 결과에 표시되지 않습니다. Windows Virtual Desktop 연결 문제는 최종 사용자가 네트워크 연결 문제를 경험할 때 발생할 수 있습니다.

## <a name="common-error-scenarios"></a>일반적인 오류 시나리오

오류 시나리오는 서비스 내부 오류와 Windows Virtual Desktop 외부 오류로 분류됩니다.

* 내부 문제: 고객이 완화할 수 없고 지원 문제를 해결 해야 하는 시나리오를 지정 합니다. [Windows 가상 데스크톱 기술 커뮤니티](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)를 통해 피드백을 제공할 때 문제가 발생 했을 때의 상관 관계 ID와 대략적인 시간 프레임을 포함 합니다.
* 외부 문제: 고객이 완화할 수 있는 시나리오와 관련이 있습니다. 이러한 문제는 Windows Virtual Desktop 외부적인 오류입니다.

다음 표에는 관리자가 직면할 수 있는 일반적인 오류가 나와 있습니다.

>[!NOTE]
>이 목록은 가장 일반적인 오류를 포함하며, 정기적으로 업데이트됩니다. 최신 정보를 살펴보려면 한 달에 한 번 이상 이 문서를 확인하세요.

## <a name="management-errors"></a>관리 오류

|오류 메시지|추천 솔루션|
|---|---|
|등록 키를 만들지 못했습니다. |등록 토큰을 만들 수 없습니다. 더 짧은 만료 시간 (1 시간에서 1 개월 사이)으로 다시 만들어 보세요. |
|등록 키를 삭제 하지 못했습니다.|등록 토큰을 삭제할 수 없습니다. 다시 삭제 해 보세요. 여전히 작동 하지 않는 경우 PowerShell을 사용 하 여 토큰이 여전히 있는지 확인 합니다. 있는 경우 PowerShell을 사용 하 여 삭제 합니다.|
|세션 호스트 드레이닝 모드를 변경 하지 못했습니다. |VM에서 드레이닝 모드를 변경할 수 없습니다. VM 상태를 확인 합니다. VM을 사용할 수 없는 경우 드레이닝 모드를 변경할 수 없습니다.|
|사용자 세션의 연결을 끊지 못했습니다. |VM에서 사용자의 연결을 끊을 수 없습니다. VM 상태를 확인 합니다. VM을 사용할 수 없는 경우 사용자 세션의 연결을 끊을 수 없습니다. VM을 사용할 수 있는 경우 사용자 세션 상태를 확인 하 여 연결이 끊어졌는지 확인 합니다. |
|세션 호스트 내의 모든 사용자를 로그 오프 하지 못했습니다. |사용자를 VM 외부로 로그인 할 수 없습니다. VM 상태를 확인 합니다. 사용할 수 없는 경우 사용자를 로그 아웃할 수 없습니다. 사용자 세션 상태를 확인 하 여 이미 로그 아웃 했는지 확인 합니다. PowerShell을 사용 하 여 강제로 로그 아웃할 수 있습니다. |
|응용 프로그램 그룹에서 사용자의 할당을 취소 하지 못했습니다.|사용자에 대 한 앱 그룹의 게시를 취소할 수 없습니다. Azure AD에서 사용자를 사용할 수 있는지 확인 합니다. 사용자가 앱 그룹이 게시 된 사용자 그룹에 속해 있는지 확인 하십시오. |
|사용 가능한 위치를 검색 하는 동안 오류가 발생 했습니다. |호스트 풀 만들기 마법사에서 사용 되는 VM의 위치를 확인 합니다. 해당 위치에서 이미지를 사용할 수 없는 경우 해당 위치에 이미지를 추가 하거나 다른 VM 위치를 선택 합니다. |

### <a name="external-connection-error-codes"></a>외부 연결 오류 코드

|숫자 코드|오류 코드|추천 솔루션|
|---|---|---|
|-2147467259|ConnectionFailedAdTrustedRelationshipFailure|세션 호스트가 Active Directory에 올바르게 가입되어 있지 않습니다.|
|-2146233088|ConnectionFailedUserHasValidSessionButRdshIsUnhealthy|세션 호스트를 사용할 수 없기 때문에 연결이 실패했습니다. 세션 호스트의 상태를 확인하세요.|
|-2146233088|ConnectionFailedClientDisconnect|이 오류가 자주 발생하면 사용자의 컴퓨터가 네트워크에 연결되어 있는지 확인하세요.|
|-2146233088|ConnectionFailedNoHealthyRdshAvailable|호스트 사용자가 연결하려는 세션이 정상이 아닙니다. 가상 머신을 디버그하세요.|
|-2146233088|ConnectionFailedUserNotAuthorized|사용자가 게시된 앱 도는 데스크톱에 액세스할 권한이 없습니다. 이 오류는 관리자가 게시된 리소스를 제거한 경우 발생할 수 있습니다. 사용자에게 원격 데스크톱 애플리케이션에서 피드 새로 고침을 요청하세요.|
|2|FileNotFound|사용자가 액세스하려는 애플리케이션이 잘못 설치되었거나 잘못된 경로로 설정되었습니다.<br><br>사용자에 게 활성 세션이 있는 동안 새 앱을 게시 하면 사용자가이 앱에 액세스할 수 없습니다. 사용자가 앱에 액세스할 수 있으려면 먼저 세션을 종료 하 고 다시 시작 해야 합니다. |
|3|InvalidCredentials|사용자가 입력한 사용자 이름 또는 암호가 기존 사용자 이름 또는 암호와 일치하지 않습니다. 자격 증명에 오타가 있는지 확인한 후 다시 시도하세요.|
|8|ConnectionBroken|클라이언트와 게이트웨이 또는 서버 사이의 연결이 끊어졌습니다. 예기치 않게 발생한 경우가 아닌 이상 조치가 필요하지 않습니다.|
|14|UnexpectedNetworkDisconnect|네트워크 연결이 끊어졌습니다. 사용자에게 다시 연결하라고 요청하세요.|
|24|ReverseConnectFailed|호스트 가상 머신에 RD 게이트웨이에 대한 직접적인 시야가 없습니다. 게이트웨이 IP 주소를 확인할 수 있는지 확인하세요.|

## <a name="next-steps"></a>다음 단계

Windows Virtual Desktop의 역할에 대해 자세히 알아보려면 [Windows Virtual Desktop 환경](environment-setup.md)을 참조하세요.

Windows Virtual Desktop에 대해 사용할 수 있는 PowerShell cmdlet 목록을 보려면 [PowerShell 참조](/powershell/windows-virtual-desktop/overview)를 참조하세요.
