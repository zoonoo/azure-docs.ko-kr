---
title: Windows 가상 데스크톱 문제 진단 - Azure
description: Windows 가상 데스크톱 진단 기능을 사용하여 문제를 진단하는 방법.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: ce85fb70e1480ad285eee78fe20faa8d77b9a147
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254262"
---
# <a name="identify-and-diagnose-issues"></a>문제 식별 및 진단

Windows Virtual Desktop은 관리자가 단일 인터페이스를 통해 문제를 식별할 수 있는 진단 기능을 제공합니다. Windows 가상 데스크톱 역할은 사용자가 시스템과 상호 작용할 때마다 진단 활동을 기록합니다. 각 로그에는 트랜잭션과 관련된 Windows 가상 데스크톱 역할, 오류 메시지, 테넌트 정보 및 사용자 정보와 같은 관련 정보가 포함됩니다. 진단 활동은 최종 사용자 및 관리 작업 모두에 의해 만들어지며 세 가지 주요 버킷으로 분류할 수 있습니다.

* 피드 구독 활동: 최종 사용자는 Microsoft 원격 데스크톱 응용 프로그램을 통해 피드에 연결하려고 할 때마다 이러한 활동을 트리거합니다.
* 연결 활동: 최종 사용자는 Microsoft 원격 데스크톱 응용 프로그램을 통해 데스크톱 또는 RemoteApp에 연결하려고 할 때마다 이러한 활동을 트리거합니다.
* 관리 활동: 관리자는 호스트 풀 만들기, 앱 그룹에 사용자 할당, 역할 할당 만들기 와 같은 관리 작업을 수행할 때마다 이러한 작업을 트리거합니다.
  
진단 역할 서비스 자체가 Windows 가상 데스크톱의 일부이므로 Windows 가상 데스크톱에 도달하지 않는 연결은 진단 결과에 표시되지 않습니다. Windows 가상 데스크톱 연결 문제는 최종 사용자가 네트워크 연결 문제가 발생할 때 발생할 수 있습니다.

시작하려면 아직 사용하지 않은 경우 PowerShell 세션에서 사용할 [Windows 가상 데스크톱 PowerShell 모듈을 다운로드하여 가져옵니다.](/powershell/windows-virtual-desktop/overview/) 그런 후, 다음 cmdlet을 실행하여 계정에 로그인합니다.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="diagnose-issues-with-powershell"></a>PowerShell에서 문제 진단

Windows 가상 데스크톱 진단은 하나의 PowerShell cmdlet만 사용하지만 문제를 좁히고 격리하는 데 도움이 되는 많은 선택적 매개 변수를 포함합니다. 다음 섹션에서는 문제를 진단하기 위해 실행할 수 있는 cmdlet을 나열합니다. 대부분의 필터를 함께 적용할 수 있습니다. 와 같은 `<tenantName>`괄호에 나열된 값은 상황에 적용되는 값으로 바꿔야 합니다.

>[!IMPORTANT]
>진단 기능은 단일 사용자 문제 해결을 위한 것입니다. PowerShell을 사용하는 모든 쿼리에는 *-UserName* 또는 *-ActivityID* 매개 변수가 포함되어야 합니다. 모니터링 기능을 사용하십시오. 진단 데이터를 작업 영역으로 보내는 방법에 대한 자세한 내용은 [진단 기능에 로그 분석 사용을](diagnostics-log-analytics.md) 참조하십시오. 

### <a name="filter-diagnostic-activities-by-user"></a>사용자별 진단 활동 필터링

**-UserName** 매개 변수는 다음 예제 cmdlet에 표시된 대로 지정된 사용자가 시작한 진단 활동 목록을 반환합니다.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN>
```

**-UserName** 매개 변수는 다른 선택적 필터링 매개 변수와 결합할 수도 있습니다.

### <a name="filter-diagnostic-activities-by-time"></a>시간별 진단 활동 필터링

반환된 진단 활동 목록을 **-StartTime** 및 **-EndTime** 매개 변수로 필터링할 수 있습니다. **-StartTime** 매개 변수는 다음 예제와 같이 특정 날짜부터 시작되는 진단 활동 목록을 반환합니다.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -StartTime "08/01/2018"
```

**-EndTime** 매개 변수는 **-StartTime** 매개 변수가 있는 cmdlet에 추가하여 결과를 받을 특정 기간을 지정할 수 있습니다. 다음 예제 cmdlet은 8월 1일과 8월 10일 사이의 진단 활동 목록을 반환합니다.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -StartTime "08/01/2018" -EndTime "08/10/2018"
```

**-StartTime** 및 **-EndTime** 매개 변수를 다른 선택적 필터링 매개 변수와 결합할 수도 있습니다.

### <a name="filter-diagnostic-activities-by-activity-type"></a>활동 유형별로 진단 활동 필터링

**-ActivityType** 매개 변수를 사용 하 여 활동 유형별로 진단 활동을 필터링할 수도 있습니다. 다음 cmdlet은 최종 사용자 연결 목록을 반환합니다.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -ActivityType Connection
```

다음 cmdlet은 관리자 관리 작업 목록을 반환합니다.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Management
```

**Get-RdsDiagnosticActivities** cmdlet은 현재 피드를 활동 유형으로 지정하는 것을 지원하지 않습니다.

### <a name="filter-diagnostic-activities-by-outcome"></a>결과별로 진단 활동 필터링

**-Result** 매개 변수를 사용 하 여 결과 의해 반환 된 진단 활동 목록을 필터링할 수 있습니다. 다음 예제 cmdlet 성공적인 진단 활동 목록을 반환 합니다.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -Outcome Success
```

다음 예제 cmdlet 실패 한 진단 활동의 목록을 반환 합니다.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Failure
```

**-result** 매개 변수는 다른 선택적 필터링 매개 변수와 결합할 수도 있습니다.

### <a name="retrieve-a-specific-diagnostic-activity-by-activity-id"></a>활동 ID별로 특정 진단 활동 검색

다음 예제 cmdlet에 표시된 것처럼 **-ActivityId** 매개 변수가 있는 경우 특정 진단 활동을 반환합니다.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityIdGuid>
```

### <a name="view-error-messages-for-a-failed-activity-by-activity-id"></a>활동 ID로 실패한 활동에 대한 오류 메시지 보기

실패한 활동에 대한 오류 메시지를 보려면 **-Detailed** 매개 변수를 사용하여 cmdlet을 실행해야 합니다. **선택-개체** cmdlet을 실행 하 여 오류 목록을 볼 수 있습니다.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantname> -ActivityId <ActivityGuid> -Detailed | Select-Object -ExpandProperty Errors
```

### <a name="retrieve-detailed-diagnostic-activities"></a>자세한 진단 활동 검색

**-details** 매개 변수는 반환된 각 진단 활동에 대한 추가 세부 정보를 제공합니다. 각 활동의 형식은 활동 유형에 따라 다릅니다. **-detailed** 매개 변수는 다음 예제와 같이 **Get-RdsDiagnosticActivities** 쿼리에 추가할 수 있습니다.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityGuid> -Detailed
```

## <a name="common-error-scenarios"></a>일반적인 오류 시나리오

오류 시나리오는 서비스 내부와 Windows 가상 데스크톱 외부에서 분류됩니다.

* 내부 문제: 테넌트 관리자가 완화할 수 없고 지원 문제로 해결해야 하는 시나리오를 지정합니다. [Windows 가상 데스크톱 기술 커뮤니티를](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)통해 피드백을 제공할 때는 활동 ID와 문제가 발생한 시기의 대략적인 기간을 포함합니다.
* 외부 문제: 시스템 관리자가 완화할 수 있는 시나리오와 관련이 있습니다. 이들은 윈도우 가상 데스크톱에 외부.

다음 표에는 관리자가 실행할 수 있는 일반적인 오류가 나열되어 있습니다.

>[!NOTE]
>이 목록에는 가장 일반적인 오류가 포함되며 일반 흐름에 따라 업데이트됩니다. 최신 정보를 확인하려면 적어도 한 달에 한 번이 문서를 다시 확인하십시오.

### <a name="external-management-error-codes"></a>외부 관리 오류 코드

|숫자 코드|오류 코드|제안된 솔루션|
|---|---|---|
|3|무단 액세스|관리 PowerShell cmdlet을 실행하려고 시도한 사용자에게는 해당 작업을 수행할 권한이 없거나 사용자 이름을 잘못 입력했습니다.|
|1000|테넌트NotFound|입력한 테넌트 이름이 기존 테넌트와 일치하지 않습니다. 오타에 대한 테넌트 이름을 검토하고 다시 시도하십시오.|
|1006|테넌트수제거해시세션호스트풀|테넌트가 개체를 포함하는 한 테넌트를 삭제할 수 없습니다. 먼저 세션 호스트 풀을 삭제한 다음 다시 시도하십시오.|
|2000|호스트풀찾을수 없습니다|입력한 호스트 풀 이름이 기존 호스트 풀과 일치하지 않습니다. 오타에 대한 호스트 풀 이름을 검토하고 다시 시도하십시오.|
|2005|호스트풀수제거해시응용그룹|객체가 포함된 호스트 풀은 삭제할 수 없습니다. 먼저 호스트 풀의 모든 앱 그룹을 제거합니다.|
|2004|호스트풀수제거해시세션호스트|세션 호스트 풀을 삭제하기 전에 모든 세션 호스트를 먼저 제거합니다.|
|5001|세션호스트NotFound|쿼리한 세션 호스트가 오프라인 일 수 있습니다. 호스트 풀의 상태를 확인합니다.|
|5008|세션호스트유저세션존재 |의도한 관리 활동을 실행하기 전에 세션 호스트의 모든 사용자를 로그아웃해야 합니다.|
|6000|앱그룹NotFound|입력한 앱 그룹 이름이 기존 앱 그룹과 일치하지 않습니다. 오타에 대한 앱 그룹 이름을 검토하고 다시 시도하십시오.|
|6022|리모트앱발견|입력한 RemoteApp 이름이 RemoteApps와 일치하지 않습니다. 오타에 대한 RemoteApp 이름을 검토하고 다시 시도하십시오.|
|6010|게시된 항목존재|게시하려는 리소스의 이름은 이미 존재하는 리소스와 동일합니다. 리소스 이름을 변경하고 다시 시도하십시오.|
|7002|이름Not유효화이트스페이스|이름에 공백을 사용하지 마십시오.|
|8000|잘못된 권한 부여 역할 범위|입력한 역할 이름이 기존 역할 이름과 일치하지 않습니다. 오타의 역할 이름을 검토하고 다시 시도하십시오. |
|8001|UserNotFound |입력한 사용자 이름이 기존 사용자 이름과 일치하지 않습니다. 오타이름을 검토하고 다시 시도하십시오.|
|8005|사용자NotFoundInAD |입력한 사용자 이름이 기존 사용자 이름과 일치하지 않습니다. 오타이름을 검토하고 다시 시도하십시오.|
|8008|테넌트동의 필수|테넌트에 대한 동의를 제공하려면 [여기의](tenant-setup-azure-active-directory.md#grant-permissions-to-windows-virtual-desktop) 지침에 따라.|

### <a name="external-connection-error-codes"></a>외부 연결 오류 코드

|숫자 코드|오류 코드|제안된 솔루션|
|---|---|---|
|-2147467259|연결실패Ad신뢰할수있는관계실패|세션 호스트가 Active Directory에 올바르게 조인되지 않았습니다.|
|-2146233088|연결실패유사용자하스유효세션부르드쉬비언|세션 호스트를 사용할 수 없기 때문에 연결이 실패했습니다. 세션 호스트의 상태를 확인합니다.|
|-2146233088|연결 실패한 클라이언트연결|이 오류가 자주 표시되면 사용자의 컴퓨터가 네트워크에 연결되어 있는지 확인합니다.|
|-2146233088|연결실패노헬시Rdsh사용 가능|호스트 사용자가 연결하려고 시도한 세션이 정상이 아닙니다. 가상 컴퓨터를 디버깅합니다.|
|-2146233088|연결실패사용자무단승인|사용자에게 게시된 앱 또는 데스크톱에 액세스할 수 있는 권한이 없습니다. 관리자가 게시된 리소스를 제거한 후 오류가 나타날 수 있습니다. 사용자에게 원격 데스크톱 응용 프로그램에서 피드를 새로 고치도록 요청합니다.|
|2|FileNotFound|사용자가 액세스하려고 시도한 응용 프로그램이 잘못 설치되었거나 잘못된 경로로 설정되었습니다.|
|3|InvalidCredentials|사용자가 입력한 사용자 이름 또는 암호가 기존 사용자 이름 또는 암호와 일치하지 않습니다. 오타에 대한 자격 증명을 검토하고 다시 시도하십시오.|
|8|연결 끊김|클라이언트와 게이트웨이 또는 서버 간의 연결이 끊어졌다. 예기치 않게 발생하지 않는 한 작업이 필요하지 않습니다.|
|14|예기치 않은 네트워크 연결 끊기|네트워크에 대한 연결이 끊어졌다. 사용자에게 다시 연결하도록 요청합니다.|
|24|역연결 실패|호스트 가상 시스템에는 RD 게이트웨이에 대한 직접적인 가시선이 없습니다. 게이트웨이 IP 주소를 확인할 수 있는지 확인합니다.|

## <a name="next-steps"></a>다음 단계

Windows 가상 데스크톱 내의 역할에 대해 자세히 알아보려면 [Windows 가상 데스크톱 환경을](environment-setup.md)참조하십시오.

Windows 가상 데스크톱에 사용할 수 있는 PowerShell cmdlet 목록을 보려면 [PowerShell 참조를](/powershell/windows-virtual-desktop/overview)참조하십시오.
