---
title: Azure Virtual Desktop(클래식)용 진단 도구 배포 - Azure
description: Azure Virtual Desktop(클래식)용 진단 UX 도구를 배포하는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 12/15/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 92876524f036126f39a2e4e5db8eb5dbd660cee9
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111754784"
---
# <a name="deploy-the-azure-virtual-desktop-classic-diagnostics-tool"></a>Azure Virtual Desktop(클래식) 진단 도구 배포

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Azure Virtual Desktop 개체를 지원하지 않는 Azure Virtual Desktop(클래식)에 적용됩니다.

Azure Virtual Desktop용 진단 도구로 다음 작업을 수행할 수 있습니다.

- 일주일 동안 단일 사용자에 대한 진단 작업(관리, 연결 또는 피드)을 찾습니다.
- Log Analytics 작업 영역에서 연결 작업에 대한 세션 호스트 정보를 수집합니다.
- 특정 호스트에 대한 VM(가상 머신) 성능 정보를 검토합니다.
- 세션 호스트에 로그인한 사용자를 확인합니다.
- 특정 세션 호스트의 활성 사용자에게 메시지를 보냅니다.
- 세션 호스트에서 사용자를 로그아웃합니다.

## <a name="prerequisites"></a>필수 구성 요소

도구에 대한 Azure Resource Manager 템플릿을 배포하려면 먼저 Azure Active Directory 앱 등록과 Log Analytics 작업 영역을 만들어야 합니다. 이를 수행하려면 사용자 또는 관리자에게 다음 권한이 필요합니다.

- Azure 구독의 소유자
- Azure 구독에서 리소스를 만드는 권한
- Azure AD 앱을 만드는 권한
- RDS 소유자 또는 기여자 권한

또한 시작하기 전에 다음 2가지 PowerShell 모듈을 설치해야 합니다.

- [Azure PowerShell 모듈](/powershell/azure/install-az-ps)
- [Azure AD 모듈](/powershell/azure/active-directory/install-adv2)

로그인할 때 구독 ID가 준비되었는지 확인합니다.

준비가 끝난 후 Azure AD 앱 등록을 만들 수 있습니다.

## <a name="create-an-azure-active-directory-app-registration"></a>Azure Active Directory 앱 등록 만들기

이 섹션에서는 PowerShell을 사용하여 서비스 주체가 있는 Azure Active Directory 앱을 만들고 이에 대한 API 권한을 얻는 방법을 보여줍니다.

>[!NOTE]
>Azure Virtual Desktop, Log Analytics 및 Microsoft Graph API 권한인 API 권한이 Azure Active Directory 애플리케이션에 추가됩니다.

1. 관리자 권한으로 PowerShell을 엽니다.
2. 진단 도구에 사용하려는 Azure 구독에 대한 소유자 또는 기여자 권한이 있는 계정으로 Azure에 로그인합니다.
   ```powershell
   Login-AzAccount
   ```
3. 동일한 계정으로 Azure AD에 로그인합니다.
   ```powershell
   Connect-AzureAD
   ```
4. [RDS 템플릿 GitHub 리포지토리](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts)로 이동하고 PowerShell에서 **CreateADAppRegistrationforDiagnostics.ps1** 스크립트를 실행합니다.
5.  스크립트에서 앱 이름을 요청하면 고유한 앱 이름을 입력합니다.


스크립트가 성공적으로 실행되면 출력에 다음과 같은 항목이 표시됩니다.

-  이제 앱에 서비스 주체 역할 할당이 있음을 확인하는 메시지
-  진단 도구를 배포할 때 필요한 클라이언트 ID와 클라이언트 암호 키

앱을 등록했으므로 이제 Log Analytics 작업 영역을 구성해야 합니다.

## <a name="configure-your-log-analytics-workspace"></a>Log Analytics 작업 영역 구성

최상의 환경을 위해 원격 세션에서 사용자 환경에 대한 정보를 얻을 수 있는 다음 성능 카운터를 사용하여 Log Analytics 작업 영역을 구성하는 것이 좋습니다. 권장 임계값이 있는 권장 카운터 목록은 [Windows 성능 카운터 임계값](deploy-diagnostics.md#windows-performance-counter-thresholds)을 참조하세요.

### <a name="create-an-azure-log-analytics-workspace-using-powershell"></a>PowerShell을 사용하여 Azure Log Analytics 작업 영역 만들기

PowerShell 스크립트를 실행하여 Log Analytics 작업 영역을 만들고 권장 Windows 성능 카운터를 구성하여 사용자 환경 및 앱 성능을 모니터링할 수 있습니다.

>[!NOTE]
>사용하려는 PowerShell 스크립트 없이 만든 기존 Log Analytics 작업 영역이 이미 있는 경우 [Azure Portal에서 스크립트 결과 유효성 검사](#validate-the-script-results-in-the-azure-portal)로 건너뜁니다.

PowerShell 스크립트를 실행하려면 다음을 수행합니다.

1.  관리자 권한으로 PowerShell을 엽니다.
2.  [RDS 템플릿 GitHub 리포지토리](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts)로 이동하고 PowerShell에서 **CreateLogAnalyticsWorkspaceforDiagnostics.ps1** 스크립트를 실행합니다.
3. 매개 변수에 대해 다음 값을 입력합니다.

    - **ResourceGroupName** 에 대해 리소스 그룹의 이름을 입력합니다.
    - **LogAnalyticsWorkspaceName** 에 대해 Log Analytics 작업 영역의 고유 이름을 입력합니다.
    - **위치** 에 대해 사용 중인 Azure 지역을 입력합니다.
    - Azure Portal의 **구독** 아래에서 찾을 수 있는 **Azure 구독 ID** 를 입력합니다.

4. 위임된 관리자 액세스 권한이 있는 사용자의 자격 증명을 입력합니다.
5. 같은 사용자의 자격 증명을 사용하여 Azure Portal에 로그인합니다.
6. 나중을 위해 LogAnalyticsWorkspace ID를 적어 두거나 기억합니다.
7. PowerShell 스크립트를 사용하여 Log Analytics 작업 영역을 설정하는 경우 성능 카운터가 이미 구성되어 있어야 하며 [Azure Portal에서 스크립트 결과 유효성 검사](#validate-the-script-results-in-the-azure-portal)로 건너뛸 수 있습니다. 그렇지 않으면 다음 섹션을 계속 진행합니다.

### <a name="configure-windows-performance-counters-in-your-existing-log-analytics-workspace"></a>기존 Log Analytics 작업 영역에서 Windows 성능 카운터 구성

이 섹션은 이전 섹션에서 PowerShell 스크립트 없이 만든 기존 Azure Log Analytics 작업 영역을 사용하려는 사용자를 대상으로 합니다. 스크립트를 사용하지 않은 경우 권장 Windows 성능 카운터를 수동으로 구성해야 합니다.

권장 성능 카운터를 수동으로 구성하는 방법은 다음과 같습니다.

1. 인터넷 브라우저를 열고 관리자 계정을 사용하여 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 다음으로 **Log Analytics 작업 영역** 으로 이동하여 구성된 Windows 성능 카운터를 검토합니다.
3. **설정** 섹션에서 **고급 설정** 을 선택합니다.
4. 그런 다음, **데이터** > **Windows 성능 카운터** 로 이동하여 다음 카운터를 추가합니다.

    -   LogicalDisk(\*)\\%Free Space
    -   LogicalDisk(C:)\\Avg. Disk Queue Length
    -   Memory(\*)\\Available Mbytes
    -   Processor Information(\*)\\Processor Time
    -   User Input Delay per Session(\*)\\Max Input Delay

[Azure Monitor의 Windows 및 Linux 성능 데이터 원본](../../azure-monitor/agents/data-sources-performance-counters.md)에서 성능 카운터에 대해 자세히 알아봅니다.

>[!NOTE]
>구성하는 추가 카운터는 진단 도구 자체에 표시되지 않습니다. 진단 도구에 표시되게 하려면 도구의 구성 파일을 구성해야 합니다. 고급 관리를 사용하여 이를 수행하는 방법에 대한 지침은 나중에 GitHub에서 확인할 수 있습니다.

## <a name="validate-the-script-results-in-the-azure-portal"></a>Azure Portal 스크립트 결과의 유효성 검사

진단 도구를 계속 배포하기 전에 Azure Active Directory 애플리케이션에 API 권한이 있고 Log Analytics 작업 영역에 미리 구성된 Windows 성능 카운터가 있는지 확인하는 것이 좋습니다.

### <a name="review-your-app-registration"></a>앱 등록 검토

앱 등록에 API 권한이 있는지 확인하려면 다음을 수행합니다.

1. 브라우저를 열고 관리자 계정으로 [Azure Portal](https://portal.azure.com/)에 연결합니다.
2. **Azure Active Directory** 로 이동합니다.
3. **앱 등록** 으로 이동하고 **모든 애플리케이션** 을 선택합니다.
4. [Azure Active Directory 앱 등록 만들기](deploy-diagnostics.md#create-an-azure-active-directory-app-registration)의 5단계에서 입력한 것과 동일한 앱 이름으로 Azure AD 앱 등록을 찾습니다.

### <a name="review-your-log-analytics-workspace"></a>Log Analytics 작업 영역 검토

Log Analytics 작업 영역에 미리 구성된 Windows 성능 카운터가 있는지 확인하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에서 **Log Analytics 작업 영역** 으로 이동하여 구성된 Windows 성능 카운터를 검토합니다.
2. **설정** 아래에서 **고급 설정** 을 선택합니다.
3. 그런 다음, **데이터** > **Windows 성능 카운터** 로 이동합니다.
4. 다음 카운터가 미리 구성되어 있는지 확인합니다.

   - LogicalDisk(\*)\\%Free Space: 디스크에서 사용 가능한 총 공간 중 여유 공간을 백분율로 표시합니다.
   - LogicalDisk(C:)\\Avg. Disk Queue Length: C 드라이브에 대한 디스크 전송 요청 길이입니다. 값은 짧은 시간 동안 2를 초과하지 않아야 합니다.
   - Memory(\*)\\Available Mbytes: 시스템에 사용할 수 있는 메모리(MB)입니다.
   - Processor Information(\*)\\Processor Time: 프로세서가 비유휴 스레드를 실행하는 데 걸린 경과된 시간의 백분율입니다.
   - User Input Delay per Session(\*)\\Max Input Delay

### <a name="connect-to-vms-in-your-log-analytics-workspace"></a>Log Analytics 작업 영역에서 VM에 연결

VM의 상태를 보려면 Log Analytics 연결을 사용하도록 설정해야 합니다. VM을 연결하려면 다음 단계를 수행합니다.

1. 브라우저를 열고 관리자 계정으로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Log Analytics 작업 영역으로 이동합니다.
3. 왼쪽 패널의 작업 영역 데이터 원본에서 **가상 머신** 을 선택합니다.
4. 연결하려는 VM의 이름을 선택합니다.
5. **연결** 을 선택합니다.

## <a name="deploy-the-diagnostics-tool"></a>진단 도구 배포

진단 도구용 Azure 리소스 관리 템플릿을 배포하려면 다음을 수행합니다.

1.  [GitHub Azure RDS 템플릿 페이지](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy)로 이동합니다.
2.  템플릿을 Azure에 배포하고 템플릿의 지침을 따릅니다. 다음과 같은 정보를 사용할 수 있어야 합니다.

    -   Client-Id
    -   Client-Secret
    -   Log Analytics 작업 영역 ID

3.  입력 매개 변수가 제공되면 사용 약관에 동의한 다음, **구매** 를 선택합니다.

배포에는 2~3분 정도 걸립니다. 성공적으로 배포한 후 리소스 그룹으로 이동하여 웹앱 및 App Service 요금제 리소스가 있는지 확인합니다.

그 후에는 리디렉션 URI를 설정해야 합니다.

### <a name="set-the-redirect-uri"></a>리디렉션 URI 설정

리디렉션 URI를 설정하려면 다음을 수행합니다.

1.  [Azure Portal](https://portal.azure.com/)에서 **App Services** 으로 이동하고 만든 애플리케이션을 찾습니다.
2.  개요 페이지로 이동하고 이 페이지에서 찾은 URL을 복사합니다.
3.  **앱 등록** 으로 이동하고 배포하려는 앱을 선택합니다.
4.  왼쪽 패널의 관리 섹션에서 **인증** 을 선택합니다.
5.  원하는 리디렉션 URI를 **리디렉션 URI** 텍스트 상자에 입력한 다음, 메뉴의 왼쪽 위 모서리에서 **저장** 을 선택합니다.
6. 형식 아래의 드롭다운 메뉴에서 **웹** 을 선택합니다.
7. 앱 개요 페이지에서 URL을 입력하고 끝에 **/security/signin-callback** 을 추가합니다. 예: `https://<yourappname>.azurewebsites.net/security/signin-callback`.

   > [!div class="mx-imgBorder"]
   > ![리디렉션 URI 페이지](../media/redirect-uri-page.png)

8. 이제 Azure 리소스를 이동하고, 템플릿에서 제공한 이름을 가진 Azure App Services 리소스를 선택하고, 해당 리소스와 연결된 URL로 이동합니다. 예를 들어 템플릿에서 사용한 앱 이름이 `contosoapp45`이면 연결된 URL은 <http://contoso.azurewebsites.net>입니다.
9. 적절한 Azure Active Directory 사용자 계정을 사용하여 로그인합니다.
10.   **수락** 을 선택합니다.

## <a name="distribute-the-diagnostics-tool"></a>진단 도구 배포

사용자가 진단 도구를 사용할 수 있도록 설정하기 전에 사용자에게 다음 권한이 있는지 확인합니다.

- 사용자에게 로그 분석에 대한 읽기 권한이 필요합니다. 자세한 내용은 [Azure Monitor로 역할, 권한 및 보안 시작하기](../../azure-monitor/roles-permissions-security.md)를 참조하세요.
-  또한 사용자에게 Azure Virtual Desktop 테넌트(RDS 리더 역할)에 대한 읽기 권한이 필요합니다. 자세한 내용은 [Azure Virtual Desktop에서 위임된 액세스](delegated-access-virtual-desktop-2019.md)를 참조하세요.

또한 사용자에게 다음 정보를 제공해야 합니다.

- 앱의 URL
- 액세스할 수 있는 테넌트 그룹 개별 테넌트의 이름

## <a name="use-the-diagnostics-tool"></a>진단 도구 사용

조직에서 받은 정보를 사용하여 계정에 로그인한 후 활동을 쿼리하려는 사용자에 대해 UPN을 준비합니다. 검색은 지난 주 내에 발생한 지정된 활동 형식 아래의 모든 활동을 제공합니다.

### <a name="how-to-read-activity-search-results"></a>활동 검색 결과를 읽는 방법

활동은 타임스탬프별로 정렬되며 최신 활동이 먼저 표시됩니다. 결과가 오류를 반환하는 경우 먼저 서비스 오류인지 확인합니다. 서비스 오류의 경우 문제를 디버그하는 데 도움이 되는 활동 정보가 포함된 지원 티켓을 만듭니다. 다른 모든 오류 형식은 일반적으로 사용자 또는 관리자가 해결할 수 있습니다. 가장 일반적인 오류 시나리오와 그 해결 방법에 대한 자세한 내용은 [문제 식별 및 진단](diagnostics-role-service-2019.md#common-error-scenarios)을 참조하세요.

>[!NOTE]
>연결된 문서에서는 서비스 오류를 "외부 오류"라고 합니다. 이는 PowerShell 참조를 업데이트할 때 변경될 예정입니다.

연결 작업에 1개보다 많은 오류가 있을 수 있습니다. 작업 형식을 확장하여 사용자에게 발생한 다른 오류를 볼 수 있습니다. 오류 코드의 이름을 선택하여 대화 상자를 열고 이에 대한 자세한 정보를 봅니다.

### <a name="investigate-the-session-host"></a>세션 호스트 조사

검색 결과에서 정보를 보려는 세션 호스트를 찾아 선택합니다.

세션 호스트 상태를 분석할 수 있습니다.

- 미리 정의된 임계값에 따라 Log Analytics가 쿼리하는 세션 호스트 상태 정보를 검색할 수 있습니다.
- 활동이 없거나 세션 호스트가 Log Analytics에 연결되어 있지 않으면 정보를 사용할 수 없습니다.

세션 호스트에서 사용자와 상호 작용할 수도 있습니다.

- 로그아웃할 수 있습니다. 또는 로그인한 사용자에게 메시지를 보낼 수 있습니다.
- 처음에 검색한 사용자가 기본적으로 선택되지만 추가 사용자를 선택하여 메시지를 보내거나 한 번에 여러 사용자를 로그아웃할 수도 있습니다.

### <a name="windows-performance-counter-thresholds"></a>Windows 성능 카운터 임계값

- LogicalDisk(\*)\\%Free Space:

    - 논리 디스크에서 사용 가능한 총 공간의 백분율을 표시합니다.
    - 임계값: 20% 미만이면 비정상으로 표시됩니다.

- LogicalDisk(C:)\\Avg. Disk Queue Length:

    - 스토리지 시스템 상태를 나타냅니다.
    - 임계값: 5보다 높으면 비정상으로 표시됩니다.

- Memory(\*)\\Available Mbytes:

    - 시스템에 사용 가능한 메모리입니다.
    - 임계값: 500메가바이트 미만이면 비정상으로 표시됩니다.

- Processor Information(\*)\\Processor Time:

    - 임계값: 80%보다 높으면 비정상으로 표시됩니다.

- [User Input Delay per Session(\*)\\Max Input Delay](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/):

    - 임계값: 2000ms보다 높으면 비정상으로 표시됩니다.

## <a name="next-steps"></a>다음 단계

- [Log Analytics에서 진단 사용](diagnostics-log-analytics-2019.md)에서 활동 로그를 모니터링하는 방법에 알아봅니다.
- [문제 식별 및 진단](diagnostics-role-service-2019.md)에서 일반적인 오류 시나리오 및 해결 방법에 알아봅니다.