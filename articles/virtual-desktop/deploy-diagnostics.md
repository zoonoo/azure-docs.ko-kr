---
title: Windows 가상 데스크톱 - Azure용 진단 도구 배포
description: Windows 가상 데스크톱용 진단 UX 도구를 배포하는 방법.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4eb63fe4bd8f8a8b0961aa6a7fccb8de9b7c2f16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123400"
---
# <a name="deploy-the-diagnostics-tool"></a>진단 도구 배포

>[!IMPORTANT]
>2020년 3월 16일부터 서비스에 대한 수요 증가로 인해 사용자 경험에 영향을 주는 진단 쿼리가 일시적으로 비활성화되었습니다. 이렇게 하면 해당 쿼리가 작동하기 때문에 도구가 작동을 중지합니다. 진단 쿼리를 다시 사용할 수 있는 경우 이 문서를 업데이트하겠습니다.
>
>그때까지는 지속적인 모니터링을 위해 [로그 애널리틱스를 사용하는](diagnostics-log-analytics.md) 것이 좋습니다.

다음은 Windows 가상 데스크톱용 진단 도구가 수행할 수 있는 사항입니다.

- 1주일 동안 단일 사용자에 대한 진단 활동(관리, 연결 또는 피드)을 찾습니다.
- Log Analytics 작업 영역에서 연결 활동에 대한 세션 호스트 정보를 수집합니다.
- 특정 호스트에 대한 VM(가상 시스템) 성능 세부 정보를 검토합니다.
- 세션 호스트에 로그인한 사용자를 확인합니다.
- 특정 세션 호스트에서 활성 사용자에게 메시지를 보냅니다.
- 세션 호스트에서 사용자를 서명합니다.

## <a name="prerequisites"></a>사전 요구 사항

도구에 대한 Azure 리소스 관리자 템플릿을 배포하려면 Azure Active Directory 앱 등록 및 로그 분석 작업 영역을 만들어야 합니다. 귀하 또는 관리자는 다음과 같은 권한이 필요합니다.

- Azure 구독의 소유자
- Azure 구독에서 리소스를 만들 수 있는 권한
- Azure AD 앱을 만들 수 있는 권한
- RDS 소유자 또는 기여자 권리

또한 시작하기 전에 다음 두 개의 PowerShell 모듈을 설치해야 합니다.

- [Azure PowerShell 모듈](/powershell/azure/install-az-ps?view=azps-2.4.0/)
- [Azure 광고 모듈](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0/)

로그인할 때 구독 ID가 준비되어 있는지 확인합니다.

모든 순서대로 모든 것을 정리한 후 Azure AD 앱 등록을 만들 수 있습니다.

## <a name="create-an-azure-active-directory-app-registration"></a>Azure Active Directory 앱 등록 만들기

이 섹션에서는 PowerShell을 사용하여 서비스 주체가 있는 Azure Active Directory 앱을 만들고 API 권한을 얻는 방법을 보여 주며, 이에 대한 API 권한을 얻을 수 있습니다.

>[!NOTE]
>API 권한은 Windows 가상 데스크톱, 로그 분석 및 Microsoft 그래프 API 사용 권한이 Azure Active Directory 응용 프로그램에 추가됩니다.

1. 관리자 권한으로 PowerShell을 엽니다.
2. 진단 도구에 사용할 Azure 구독에 대한 소유자 또는 기여자 권한이 있는 계정으로 Azure에 로그인합니다.
   ```powershell
   Login-AzAccount
   ```
3. 동일한 계정으로 Azure AD에 로그인합니다.
   ```powershell
   Connect-AzureAD
   ```
4. [RDS 템플릿 GitHub 리포지토리로](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts) 이동하여 PowerShell에서 **CreateADApp 등록을 위한 진단.ps1 스크립트를** 실행합니다.
5.  스크립트에서 앱 이름을 묻는 메시지가 지정되면 고유한 앱 이름을 입력합니다.


스크립트가 성공적으로 실행되면 출력에 다음과 같은 사항이 표시됩니다.

-  이제 앱에 서비스 주체 역할 할당이 있음을 확인하는 메시지입니다.
-  진단 도구를 배포할 때 필요한 클라이언트 ID 및 클라이언트 보안 키입니다.

이제 앱을 등록했기 때문에 Log Analytics 작업 영역을 구성할 차례입니다.

## <a name="configure-your-log-analytics-workspace"></a>로그 분석 작업 영역 구성

최상의 환경을 위해 원격 세션에서 사용자 환경의 설명표를 파생할 수 있는 다음 성능 카운터로 Log Analytics 작업 영역을 구성하는 것이 좋습니다. 권장 임계값이 있는 권장 카운터 목록은 [Windows 성능 카운터 임계값을](deploy-diagnostics.md#windows-performance-counter-thresholds)참조하십시오.

### <a name="create-an-azure-log-analytics-workspace-using-powershell"></a>PowerShell을 사용하여 Azure 로그 분석 작업 영역 만들기

PowerShell 스크립트를 실행하여 Log Analytics 작업 영역을 만들고 권장Windows 성능 카운터를 구성하여 사용자 환경 및 앱 성능을 모니터링할 수 있습니다.

>[!NOTE]
>사용하려는 PowerShell 스크립트 없이 만든 기존 Log Analytics 작업 영역이 이미 있는 경우 건너뛰고 [Azure 포털에서 스크립트 결과 유효성 검사합니다.](#validate-the-script-results-in-the-azure-portal)

PowerShell 스크립트를 실행하려면 다음을 수행하십시오.

1.  관리자 권한으로 PowerShell을 엽니다.
2.  [RDS 템플릿 GitHub 리포지토리로](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts) 이동하여 PowerShell에서 **CreateLogAnalyticsWorkspaceforDiagnostics.ps1 스크립트를** 실행합니다.
3. 매개 변수에 대해 다음 값을 입력합니다.

    - **ResourceGroupName의**경우 리소스 그룹의 이름을 입력합니다.
    - **LogAnalyticsWorkspaceName의**경우 로그 분석 작업 영역에 대한 고유한 이름을 입력합니다.
    - **위치의**경우 사용 중인 Azure 영역을 입력합니다.
    - 구독 아래의 Azure 포털에서 찾을 수 있는 **Azure 구독 ID를** **입력합니다.**

4. 위임된 관리자 액세스 권한이 있는 사용자의 자격 증명을 입력합니다.
5. 동일한 사용자의 자격 증명으로 Azure 포털에 로그인합니다.
6. 나중에 LogAnalyticsWorkspace ID를 적거나 외우십시오.
7. PowerShell 스크립트를 사용하여 Log Analytics 작업 영역을 설정한 경우 성능 카운터가 이미 구성되어야 하며 Azure [포털에서 스크립트 결과 유효성 검사를](#validate-the-script-results-in-the-azure-portal)건너뛸 수 있습니다. 그렇지 않으면 다음 섹션으로 계속합니다.

### <a name="configure-windows-performance-counters-in-your-existing-log-analytics-workspace"></a>기존 로그 분석 작업 영역에서 Windows 성능 카운터 구성

이 섹션은 이전 섹션의 PowerShell 스크립트 없이 만든 기존 Azure Log Analytics 작업 영역을 사용하려는 사용자를 위한 섹션입니다. 스크립트를 사용하지 않은 경우 권장 Windows 성능 카운터를 수동으로 구성해야 합니다.

권장 성능 카운터를 수동으로 구성하는 방법은 다음과 같습니다.

1. 인터넷 브라우저를 열고 관리 계정으로 [Azure 포털에](https://portal.azure.com/) 로그인합니다.
2. 다음으로 **로그 분석 작업 영역으로** 이동하여 구성된 Windows 성능 카운터를 검토합니다.
3. **설정** 섹션에서 고급 **설정을**선택합니다.
4. 그런 다음 **데이터** > **Windows 성능 카운터로** 이동하여 다음 카운터를 추가합니다.

    -   논리 디스크()\*\\%여유 공간
    -   논리 디스크(C:)\\평균 디스크 큐 길이
    -   \*메모리()\\사용 가능한 M바이트
    -   프로세서 정보()\*\\프로세서 시간
    -   세션당 사용자 입력\*\\지연() 최대 입력 지연

[Azure 모니터의 Windows 및 Linux 성능 데이터 원본의](/azure/azure-monitor/platform/data-sources-performance-counters)성능 카운터에 대해 자세히 알아봅니다.

>[!NOTE]
>구성한 추가 카운터는 진단 도구 자체에 표시되지 않습니다. 진단 도구에 표시하려면 도구의 구성 파일을 구성해야 합니다. 고급 관리를 사용하여 이 작업을 수행하는 방법에 대한 지침은 나중에 GitHub에서 사용할 수 있습니다.

## <a name="validate-the-script-results-in-the-azure-portal"></a>Azure 포털에서 스크립트 결과 유효성 검사

진단 도구를 계속 배포하기 전에 Azure Active Directory 응용 프로그램에 API 권한이 있고 Log Analytics 작업 영역에 미리 구성된 Windows 성능 카운터가 있는지 확인하는 것이 좋습니다.

### <a name="review-your-app-registration"></a>앱 등록 검토

앱 등록에 API 권한이 있는지 확인하려면 다음을 수행하십시오.

1. 브라우저를 열고 관리 계정으로 [Azure 포털에](https://portal.azure.com/) 연결합니다.
2. **Azure Active Directory**로 이동합니다.
3. 앱 **등록으로** 이동하여 **모든 응용 프로그램을 선택합니다.**
4. Azure Active Directory 앱 등록 만들기의 5단계에서 입력한 것과 동일한 앱 이름으로 [Azure AD 앱 등록을](deploy-diagnostics.md#create-an-azure-active-directory-app-registration)찾습니다.

### <a name="review-your-log-analytics-workspace"></a>로그 분석 작업 영역 검토

Log Analytics 작업 영역에 미리 구성된 Windows 성능 카운터가 있는지 확인하려면 다음을 수행하십시오.

1. Azure [포털에서](https://portal.azure.com/) **로그 분석 작업 영역으로** 이동하여 구성된 Windows 성능 카운터를 검토합니다.
2. **설정에서** **고급 설정을**선택합니다.
3. 그런 다음 **데이터** > **Windows 성능 카운터로 이동합니다.**
4. 다음 카운터가 미리 구성되어 있는지 확인합니다.

   - \*LogicDisk()\\%사용 가능한 공간: 디스크에서 사용할 수 있는 총 공간의 사용 가능한 공간의 양을 백분율로 표시합니다.
   - 논리 디스크(C:)\\평균 디스크 큐 길이: C 드라이브에 대한 디스크 전송 요청 의 길이입니다. 이 값은 짧은 기간 동안 2를 초과해서는 안 됩니다.
   - \*메모리()\\사용 가능한 Mbytes: 시스템에 사용할 수 있는 메모리(메가바이트)입니다.
   - 프로세서 정보()\*\\프로세서 시간: 프로세서가 비유휴 스레드를 실행하는 데 소비하는 경과 시간의 백분율입니다.
   - 세션당 사용자 입력\*\\지연() 최대 입력 지연

### <a name="connect-to-vms-in-your-log-analytics-workspace"></a>로그 분석 작업 영역의 VM에 연결

VM의 상태를 보려면 로그 분석 연결을 사용하도록 설정해야 합니다. 다음 단계에 따라 VM을 연결합니다.

1. 브라우저를 열고 관리 계정으로 [Azure 포털에](https://portal.azure.com/) 로그인합니다.
2. 로그 분석 작업 영역으로 이동합니다.
3. 왼쪽 패널에서 작업 영역 데이터 원본 에서 **가상 컴퓨터를**선택합니다.
4. 연결할 VM의 이름을 선택합니다.
5. **연결**을 선택합니다.

## <a name="deploy-the-diagnostics-tool"></a>진단 도구 배포

진단 도구에 대한 Azure 리소스 관리 템플릿을 배포하려면 다음을 수행합니다.

1.   [GitHub Azure RDS-템플릿 페이지로](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy)이동합니다.
2.  템플릿을 Azure에 배포하고 템플릿의 지침을 따릅니다. 다음 정보를 사용할 수 있는지 확인합니다.

    -   클라이언트 ID
    -   클라이언트 시크릿
    -   Log Analytics 작업 영역 ID

3.  입력 매개 변수가 제공되면 이용 약관에 동의한 다음 **구입을**선택합니다.

배포에는 2~3분이 소요됩니다. 성공적으로 배포한 후 리소스 그룹으로 이동하여 웹 앱 및 앱 서비스 계획 리소스가 있는지 확인합니다.

그런 다음 리디렉션 URI를 설정해야 합니다.

### <a name="set-the-redirect-uri"></a>리디렉션 URI 설정

리디렉션 URI를 설정하려면 다음을 수행하십시오.

1.  Azure [포털에서](https://portal.azure.com/)앱 **서비스로** 이동하여 만든 응용 프로그램을 찾습니다.
2.  개요 페이지로 이동하여 찾은 URL을 복사합니다.
3.  앱 **등록으로** 이동하여 배포할 앱을 선택합니다.
4.  왼쪽 패널에서 관리 섹션에서 **인증을**선택합니다.
5.  원하는 URI 리디렉션 URI 를 **URI** 리디렉션 텍스트 상자에 입력한 다음 메뉴의 왼쪽 상단 모서리에 **저장을** 선택합니다.
6. 유형 아래의 드롭다운 메뉴에서 **웹을** 선택합니다.
7. 앱 개요 페이지에서 URL을 입력하고 **/security/signin-callback을** 끝에 추가합니다. 예: `https://<yourappname>.azurewebsites.net/security/signin-callback`

   ![리디렉션 URI 페이지](media/redirect-uri-page.png)

8. 이제 Azure 리소스로 이동하여 템플릿에 제공한 이름으로 Azure App Services 리소스를 선택하고 연결된 URL로 이동합니다. 예를 들어 템플릿에 사용한 앱 이름이 `contosoapp45`'연결된 URL'인 <https://contosoapp45.azurewebsites.net>경우).
9. 적절한 Azure Active Directory 사용자 계정을 사용하여 로그인합니다.
10.   **수락**을 선택합니다.

## <a name="distribute-the-diagnostics-tool"></a>진단 도구 배포

사용자가 진단 도구를 사용할 수 있도록 하기 전에 사용자에게 다음 권한이 있는지 확인합니다.

- 사용자는 로그 분석을 위해 읽기 액세스가 필요합니다. 자세한 내용은 [Azure Monitor를 사용 하 여 역할, 사용 권한 및 보안 시작](/azure/azure-monitor/platform/roles-permissions-security)을 참조 합니다.
-  또한 사용자는 Windows 가상 데스크톱 테넌트(RDS Reader 역할)에 대한 읽기 액세스가 필요합니다. 자세한 내용은 [Windows 가상 데스크톱의 위임된 액세스를](delegated-access-virtual-desktop.md)참조하십시오.

또한 사용자에게 다음과 같은 정보를 제공해야 합니다.

- 앱의 URL
- 액세스할 수 있는 테넌트 그룹 개별 테넌트의 이름입니다.

## <a name="use-the-diagnostics-tool"></a>진단 도구 사용

조직에서 받은 정보를 사용하여 계정에 로그인한 후 활동을 쿼리하려는 사용자에 대해 UPN을 준비하십시오. 검색은 지난 주 에 발생한 지정된 활동 유형에서 모든 활동을 제공합니다.

### <a name="how-to-read-activity-search-results"></a>활동 검색 결과를 읽는 방법

활동은 타임스탬프별로 정렬되며 최신 활동이 먼저 표시됩니다. 결과가 오류를 반환하는 경우 먼저 서비스 오류인지 확인합니다. 서비스 오류의 경우 문제 해결에 도움이 되는 활동 정보가 있는 지원 티켓을 만듭니다. 다른 모든 오류 유형은 일반적으로 사용자 또는 관리자가 해결할 수 있습니다. 가장 일반적인 오류 시나리오 목록과 이를 해결하는 방법은 [문제 식별 및 진단을](diagnostics-role-service.md#common-error-scenarios)참조하십시오.

>[!NOTE]
>서비스 오류는 연결된 설명서에서 "외부 오류"라고 합니다. PowerShell 참조를 업데이트할 때 변경됩니다.

연결 활동에 두 개 이상의 오류가 있을 수 있습니다. 활동 유형을 확장하여 사용자가 건너온 다른 오류를 볼 수 있습니다. 오류 코드 이름을 선택하여 대화 상자를 열어 자세한 정보를 확인합니다.

### <a name="investigate-the-session-host"></a>세션 호스트 조사 

검색 결과에서 정보를 원하는 세션 호스트를 찾아 선택합니다.

세션 호스트 상태를 분석할 수 있습니다.

- 미리 정의된 임계값에 따라 Log Analytics가 쿼리하는 세션 호스트 상태 정보를 검색할 수 있습니다.
- 활동이 없거나 세션 호스트가 Log Analytics에 연결되어 있지 않으면 정보를 사용할 수 없습니다.

세션 호스트의 사용자와 상호 작용할 수도 있습니다.

- 로그아웃하거나 로그인한 사용자에게 메시지를 보낼 수 있습니다.
- 원래 검색한 사용자는 기본적으로 선택되지만 추가 사용자를 선택하여 메시지를 보내거나 한 번에 여러 사용자를 로그아웃할 수도 있습니다.

### <a name="windows-performance-counter-thresholds"></a>Windows 성능 카운터 임계값

- 논리 디스크()\*\\%여유 공간:

    - 사용 가능한 총 사용 가능한 공간의 백분율을 사용 가능한 논리 디스크에 표시합니다.
    - 임계값: 20% 미만은 비정상으로 표시됩니다.

- 논리 디스크(C:)\\평균 디스크 큐 길이:

    - 저장소 시스템 조건을 나타냅니다.
    - 임계값: 5보다 높은 경우 비정상으로 표시됩니다.

- \*메모리()\\사용 가능한 M바이트:

    - 시스템에 사용할 수 있는 메모리입니다.
    - 임계값: 500MB 미만이 비정상으로 표시됩니다.

- 프로세서 정보()\*\\프로세서 시간:

    - 임계값: 80% 이상이 비정상으로 표시됩니다.

- [세션당 사용자 입력\*\\지연() 최대 입력 지연](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/):

    - 임계값: 2000ms보다 높은 값은 비정상으로 표시됩니다.

## <a name="next-steps"></a>다음 단계

- 로그 분석을 사용하여 [진단 사용에서](diagnostics-log-analytics.md)활동 로그를 모니터링하는 방법에 대해 알아봅니다.
- 일반적인 오류 시나리오및 문제 식별 [및 진단에서](diagnostics-role-service.md)문제를 해결하는 방법에 대해 읽어보십시오.
