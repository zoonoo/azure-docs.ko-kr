---
title: Windows 가상 데스크톱 진단 문제-Azure
description: Windows 가상 데스크톱 진단 기능을 사용 하 여 문제를 진단 하는 방법입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: cffc6393ef6f5c1a33be615d9d5d4b8729ab711f
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611860"
---
# <a name="identify-and-diagnose-issues"></a>문제 식별 및 진단

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Windows 가상 데스크톱 개체를 사용 하 여 스프링 2020 업데이트에 적용 됩니다. Azure Resource Manager 개체 없이 Windows 가상 데스크톱 2019 릴리스를 사용 하는 경우 [이 문서](./virtual-desktop-fall-2019/diagnostics-role-service-2019.md)를 참조 하세요.
>
> Windows 가상 데스크톱 스프링 2020 업데이트는 현재 공개 미리 보기로 제공 됩니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에 사용 하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Windows 가상 데스크톱은 관리자가 단일 인터페이스를 통해 문제를 식별할 수 있도록 하는 진단 기능을 제공 합니다. Windows 가상 데스크톱의 진단 기능에 대해 자세히 알아보려면 [진단 기능에 Log Analytics 사용](diagnostics-log-analytics.md)을 참조 하세요.
  
진단 역할 서비스 자체는 Windows 가상 데스크톱의 일부 이기 때문에 Windows 가상 데스크톱에 연결 되지 않은 연결은 진단 결과에 표시 되지 않습니다. Windows 가상 데스크톱 연결 문제는 최종 사용자에 게 네트워크 연결 문제가 발생 하는 경우에 발생할 수 있습니다.

## <a name="common-error-scenarios"></a>일반적인 오류 시나리오

오류 시나리오는 서비스 내부와 Windows 가상 데스크톱 외부에서 분류 됩니다.

* 내부 문제: 고객이 완화할 수 없고 지원 문제를 해결 해야 하는 시나리오를 지정 합니다. [Windows 가상 데스크톱 기술 커뮤니티](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)를 통해 피드백을 제공할 때 문제가 발생 했을 때의 상관 관계 ID와 대략적인 시간 프레임을 포함 합니다.
* 외부 문제: 고객이 완화할 수 있는 시나리오와 관련이 있습니다. 이러한 가상 데스크톱은 Windows 가상 데스크톱의 외부에 있습니다.

다음 표에는 관리자가 실행할 수 있는 일반적인 오류가 정리 되어 있습니다.

>[!NOTE]
>이 목록에는 가장 일반적인 오류가 포함 되며 일반 흐름에서 업데이트 됩니다. 최신 정보를 확인 하려면이 문서를 한 달에 한 번 이상 확인 해야 합니다.

## <a name="management-errors"></a>관리 오류

|오류 메시지|제안 된 솔루션|
|---|---|
|등록 키를 만들지 못했습니다. |등록 토큰을 만들 수 없습니다. 더 짧은 만료 시간 (1 시간에서 1 개월 사이)으로 다시 만들어 보세요. |
|등록 키를 삭제 하지 못했습니다.|등록 토큰을 삭제할 수 없습니다. 다시 삭제 해 보세요. 여전히 작동 하지 않는 경우 PowerShell을 사용 하 여 토큰이 여전히 있는지 확인 합니다. 있는 경우 PowerShell을 사용 하 여 삭제 합니다.|
|세션 호스트 드레이닝 모드를 변경 하지 못했습니다. |VM에서 드레이닝 모드를 변경할 수 없습니다. VM 상태를 확인 합니다. VM을 사용할 수 없는 경우 드레이닝 모드를 변경할 수 없습니다.|
|사용자 세션의 연결을 끊지 못했습니다. |VM에서 사용자의 연결을 끊을 수 없습니다. VM 상태를 확인 합니다. VM을 사용할 수 없는 경우 사용자 세션의 연결을 끊을 수 없습니다. VM을 사용할 수 있는 경우 사용자 세션 상태를 확인 하 여 연결이 끊어졌는지 확인 합니다. |
|세션 호스트 내의 모든 사용자를 로그 오프 하지 못했습니다. |사용자를 VM 외부로 로그인 할 수 없습니다. VM 상태를 확인 합니다. 사용할 수 없는 경우 사용자를 로그 아웃할 수 없습니다. 사용자 세션 상태를 확인 하 여 이미 로그 아웃 했는지 확인 합니다. PowerShell을 사용 하 여 강제로 로그 아웃할 수 있습니다. |
|응용 프로그램 그룹에서 사용자의 할당을 취소 하지 못했습니다.|사용자에 대 한 앱 그룹의 게시를 취소할 수 없습니다. Azure AD에서 사용자를 사용할 수 있는지 확인 합니다. 사용자가 앱 그룹이 게시 된 사용자 그룹에 속해 있는지 확인 하십시오. |
|사용 가능한 위치를 검색 하는 동안 오류가 발생 했습니다. |호스트 풀 만들기 마법사에서 사용 되는 VM의 위치를 확인 합니다. 해당 위치에서 이미지를 사용할 수 없는 경우 해당 위치에 이미지를 추가 하거나 다른 VM 위치를 선택 합니다. |

### <a name="external-connection-error-codes"></a>외부 연결 오류 코드

|숫자 코드|오류 코드|제안 된 솔루션|
|---|---|---|
|-2147467259|Connectionfailedad를 연결 하지 못했습니다.|세션 호스트가 Active Directory에 올바르게 조인 되어 있지 않습니다.|
|-2146233088|Connectionfaileduserhas유효한 Sessionsrdshis비정상|세션 호스트를 사용할 수 없기 때문에 연결에 실패 했습니다. 세션 호스트의 상태를 확인 합니다.|
|-2146233088|ConnectionFailedClientDisconnect|이 오류가 자주 발생 하면 사용자의 컴퓨터가 네트워크에 연결 되어 있는지 확인 하십시오.|
|-2146233088|ConnectionFailedNoHealthyRdshAvailable|호스트 사용자가 연결 하려고 시도한 세션이 정상이 아닙니다. 가상 컴퓨터를 디버그 합니다.|
|-2146233088|ConnectionFailedUserNotAuthorized 있음|사용자에 게 게시 된 앱 또는 데스크톱에 액세스할 수 있는 권한이 없습니다. 관리자가 게시 된 리소스를 제거한 후에이 오류가 나타날 수 있습니다. 사용자에 게 원격 데스크톱 응용 프로그램에서 피드를 새로 고치도록 요청 합니다.|
|2|FileNotFound|사용자가 액세스 하려고 시도한 응용 프로그램이 잘못 설치 되었거나 잘못 된 경로로 설정 되었습니다.<br><br>사용자에 게 활성 세션이 있는 동안 새 앱을 게시 하면 사용자가이 앱에 액세스할 수 없습니다. 사용자가 앱에 액세스할 수 있으려면 먼저 세션을 종료 하 고 다시 시작 해야 합니다. |
|3|InvalidCredentials|사용자가 입력 한 사용자 이름 또는 암호가 기존 사용자 이름 또는 암호와 일치 하지 않습니다. 오타가 있는지 확인 하 고 다시 시도 하세요.|
|8|ConnectionBroken|클라이언트와 게이트웨이 또는 서버 간 연결이 끊어졌습니다. 예기치 않게 발생 하지 않는 한 작업이 필요 하지 않습니다.|
|14|UnexpectedNetworkDisconnect|네트워크에 대 한 연결을 삭제 했습니다. 사용자에 게 다시 연결 하도록 요청 합니다.|
|24|ReverseConnectFailed|호스트 가상 컴퓨터는 RD 게이트웨이에 대 한 직접적인 시야를 갖지 않습니다. 게이트웨이 IP 주소를 확인할 수 있는지 확인 합니다.|

## <a name="next-steps"></a>다음 단계

Windows 가상 데스크톱의 역할에 대 한 자세한 내용은 [Windows 가상 데스크톱 환경](environment-setup.md)을 참조 하세요.

Windows 가상 데스크톱에 사용할 수 있는 PowerShell cmdlet의 목록을 보려면 [powershell 참조](/powershell/windows-virtual-desktop/overview)를 참조 하세요.
