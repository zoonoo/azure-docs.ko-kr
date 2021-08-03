---
title: Azure Virtual Desktop 진단 문제 - Azure
description: Azure Virtual Desktop 진단 기능을 사용하여 문제를 진단하는 방법.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 09/21/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: de140f83c4f00d92379b1ff0b70f627234480295
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111757880"
---
# <a name="identify-and-diagnose-azure-virtual-desktop-issues"></a>Azure Virtual Desktop 문제 식별 및 진단

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Azure Virtual Desktop 개체를 통해 Azure Virtual Desktop에 적용됩니다. Azure Resource Manager 개체 없이 Azure Virtual Desktop(클래식)을 사용하는 경우 [이 문서](./virtual-desktop-fall-2019/diagnostics-role-service-2019.md)를 참조하세요.

Azure Virtual Desktop은 관리자가 단일 인터페이스를 통해 문제를 식별할 수 있도록 지원하는 진단 기능을 제공합니다. Azure Virtual Desktop의 진단 기능에 대한 자세한 내용은 [진단 기능에 Log Analytics 사용](diagnostics-log-analytics.md)을 참조하세요.

진단 역할 서비스 자체가 Azure Virtual Desktop의 일부이므로 Azure Virtual Desktop에 도달하지 않는 연결은 진단 결과에 표시되지 않습니다. Azure Virtual Desktop 연결 문제는 최종 사용자가 네트워크 연결 문제를 경험할 때 발생할 수 있습니다.

## <a name="common-error-scenarios"></a>일반적인 오류 시나리오

WVDErrors 테이블은 모든 활동 형식에서 오류를 추적합니다. "ServiceError"라는 열에는 "True" 또는 "False"로 표시되는 추가 플래그가 제공됩니다. 이 플래그는 오류가 서비스와 관련되어 있는지 여부를 알려줍니다.

* 값이 "True"이면 서비스 팀에서 이 문제를 이미 조사했을 수 있습니다. 이 문제는 사용자 환경에 영향을 주며 많은 자주 발생하는 경우 Azure Virtual Desktop에 대한 지원 티켓을 제출하는 것이 좋습니다.
* 값이 "False"이면 직접 수정할 수 있는 잘못된 구성일 수 있습니다. 오류 메시지는 시작할 위치에 대한 단서를 제공할 수 있습니다.

다음 표에는 관리자가 직면할 수 있는 일반적인 오류가 나와 있습니다.

>[!NOTE]
>이 목록은 가장 일반적인 오류를 포함하며, 정기적으로 업데이트됩니다. 최신 정보를 살펴보려면 한 달에 한 번 이상 이 문서를 확인하세요.

## <a name="management-errors"></a>관리 오류

|오류 메시지|추천 솔루션|
|---|---|
|등록 키 생성 실패 |등록 토큰을 만들 수 없습니다. 더 짧은 만료 시간(1시간에서 1개월 사이)으로 다시 만들어 보세요. |
|등록 키 삭제 실패|등록 토큰을 삭제할 수 없습니다. 다시 삭제해 보세요. 여전히 작동하지 않는 경우 PowerShell을 사용하여 토큰이 아직 있는지 확인합니다. 있는 경우 PowerShell을 사용하여 삭제합니다.|
|세션 호스트 드레이닝 모드를 변경하지 못함 |VM에서 드레이닝 모드를 변경할 수 없습니다. VM 상태를 확인합니다. VM을 사용할 수 없는 경우 드레이닝 모드를 변경할 수 없습니다.|
|사용자 세션의 연결을 끊지 못함 |VM에서 사용자의 연결을 끊을 수 없습니다. VM 상태를 확인합니다. VM을 사용할 수 없는 경우 사용자 세션의 연결을 끊을 수 없습니다. VM을 사용할 수 있는 경우 사용자 세션 상태를 확인하여 연결이 끊어졌는지 확인합니다. |
|세션 호스트 내의 모든 사용자를 로그오프하지 못함 |사용자를 VM에서 로그아웃할 수 없습니다. VM 상태를 확인합니다. 사용할 수 없는 경우 사용자를 로그아웃할 수 없습니다. 사용자 세션 상태를 확인하여 이미 로그아웃되었는지 확인합니다. PowerShell을 사용하여 강제로 로그아웃할 수 있습니다. |
|애플리케이션 그룹에서 사용자의 할당을 취소하지 못함|사용자에 대한 앱 그룹의 게시를 취소할 수 없습니다. Azure AD에서 사용자가 사용할 수 있는지 확인합니다. 사용자가 앱 그룹이 게시된 사용자 그룹에 일부인지 확인합니다. |
|사용 가능한 위치를 검색하는 동안 오류가 발생했습니다. |호스트 풀 만들기 마법사에서 사용되는 VM의 위치를 확인합니다. 해당 위치에서 이미지를 사용할 수 없는 경우 해당 위치에 이미지를 추가하거나 다른 VM 위치를 선택합니다. |

### <a name="connection-error-codes"></a>연결 오류 코드

|숫자 코드|오류 코드|추천 솔루션|
|---|---|---|
|-2147467259|ConnectionFailedAdTrustedRelationshipFailure|세션 호스트가 Active Directory에 올바르게 가입되어 있지 않습니다.|
|-2146233088|ConnectionFailedUserHasValidSessionButRdshIsUnhealthy|세션 호스트를 사용할 수 없기 때문에 연결이 실패했습니다. 세션 호스트의 상태를 확인하세요.|
|-2146233088|ConnectionFailedClientDisconnect|이 오류가 자주 발생하면 사용자의 컴퓨터가 네트워크에 연결되어 있는지 확인하세요.|
|-2146233088|ConnectionFailedNoHealthyRdshAvailable|호스트 사용자가 연결하려는 세션이 정상이 아닙니다. 가상 머신을 디버그하세요.|
|-2146233088|ConnectionFailedUserNotAuthorized|사용자가 게시된 앱 도는 데스크톱에 액세스할 권한이 없습니다. 이 오류는 관리자가 게시된 리소스를 제거한 경우 발생할 수 있습니다. 사용자에게 원격 데스크톱 애플리케이션에서 피드 새로 고침을 요청하세요.|
|2|FileNotFound|사용자가 액세스하려는 애플리케이션이 잘못 설치되었거나 잘못된 경로로 설정되었습니다.<br><br>사용자에게 활성 세션이 있는 동안 새 앱을 게시하면 사용자가 이 앱에 액세스할 수 없습니다. 사용자가 앱에 액세스하려면 먼저 세션을 종료하고 다시 시작해야 합니다. |
|3|InvalidCredentials|사용자가 입력한 사용자 이름 또는 암호가 기존 사용자 이름 또는 암호와 일치하지 않습니다. 자격 증명에 오타가 있는지 확인한 후 다시 시도하세요.|
|8|ConnectionBroken|클라이언트와 게이트웨이 또는 서버 사이의 연결이 끊어졌습니다. 예기치 않게 발생한 경우가 아닌 이상 조치가 필요하지 않습니다.|
|14|UnexpectedNetworkDisconnect|네트워크 연결이 끊어졌습니다. 사용자에게 다시 연결하라고 요청하세요.|
|24|ReverseConnectFailed|호스트 가상 머신에 RD 게이트웨이에 대한 직접적인 시야가 없습니다. 게이트웨이 IP 주소를 확인할 수 있는지 확인하세요.|

## <a name="error-cant-add-user-assignments-to-an-app-group"></a>오류: 앱 그룹에 사용자 할당을 추가할 수 없음

앱 그룹에 사용자를 할당한 후 Azure Portal에 "세션 종료" 또는 "인증 문제 발생 - 확장 Microsoft_Azure_WVD"라는 경고가 표시됩니다. 그러면 할당 페이지가 로드되지 않고, 그 후에 페이지는 Azure Portal(예: Azure Monitor, Log Analytics, Service Health 등) 전체에서 로드가 중지됩니다.

**원인:** 조건부 액세스 정책에 문제가 있습니다. Azure Portal은 SharePoint Online에 종속된 Microsoft Graph에 대한 토큰을 가져오려고 합니다. 고객에게는 사용자가 데이터 스토리지에 액세스하는 데 필요한 사용 약관에 동의해야 하는 "Microsoft Office 365 데이터 스토리지 사용 약관"이라는 조건부 액세스 정책이 있습니다. 그러나 아직 로그인하지 않았으므로 Azure Portal에서 토큰을 가져올 수 없습니다.

**해결 방법:** 관리자는 Azure Portal에 로그인하기 전에 먼저 SharePoint에 로그인하고 사용 약관에 동의해야 합니다. 그 후에는 평상시와 같이 Azure Portal에 로그인할 수 있어야 합니다.

## <a name="next-steps"></a>다음 단계

Azure Virtual Desktop 내의 역할에 대해 자세히 알아보려면 [Azure Virtual Desktop 환경](environment-setup.md)을 참조하세요.

Azure Virtual Desktop에 사용할 수 있는 PowerShell cmdlet 목록을 보려면 [PowerShell 참조](/powershell/windows-virtual-desktop/overview)를 참조하세요.
