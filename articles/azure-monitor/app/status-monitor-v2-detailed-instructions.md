---
title: Azure 애플리케이션 Insights 에이전트 상세 지침 | Microsoft Docs
description: Application Insights 에이전트를 시작 하는 방법에 대 한 자세한 지침입니다. 웹 사이트를 다시 배포 하지 않고 웹 사이트 성능을 모니터링 합니다. 온-프레미스, Vm 또는 Azure에서 호스트 되는 ASP.NET 웹 앱에서 작동 합니다.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 8f6134e8f8fdb9af3f578afaf0670c32a3896e01
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "81766872"
---
# <a name="application-insights-agent-formerly-named-status-monitor-v2-detailed-instructions"></a>Application Insights 에이전트 (이전의 이름이 지정 된 상태 모니터 v2): 자세한 지침

이 문서에서는 PowerShell 갤러리에 등록 하 고 ApplicationMonitor 모듈을 다운로드 하는 방법을 설명 합니다.
시작 하는 데 필요한 가장 일반적인 매개 변수는 포함 되어 있습니다.
인터넷에 액세스할 수 없는 경우에도 수동 다운로드 지침을 제공 했습니다.

## <a name="get-an-instrumentation-key"></a>계측 키 가져오기

시작 하려면 계측 키가 필요 합니다. 자세한 내용은 [Application Insights 리소스 만들기](create-new-resource.md#copy-the-instrumentation-key)를 참조 하세요.

## <a name="run-powershell-as-admin-with-an-elevated-execution-policy"></a>관리자 권한으로 실행 정책으로 PowerShell 실행

### <a name="run-as-admin"></a>관리자 권한으로 실행

PowerShell에서 컴퓨터를 변경 하려면 관리자 수준의 권한이 필요 합니다.
### <a name="execution-policy"></a>실행 정책
- 설명: 기본적으로 PowerShell 스크립트를 실행 하는 것은 사용 되지 않습니다. 현재 범위에만 RemoteSigned 스크립트를 허용 하는 것이 좋습니다.
- 참조: [실행 정책](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) 및 [set-executionpolicy](
https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6
)정보를 참조 하세요.
- 명령: `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process` .
- 선택적 매개 변수:
    - `-Force`. 확인 메시지를 무시 합니다.

**예제 오류**

```
Install-Module : The 'Install-Module' command was found in the module 'PowerShellGet', but the module could not be
loaded. For more information, run 'Import-Module PowerShellGet'.
    
Import-Module : File C:\Program Files\WindowsPowerShell\Modules\PackageManagement\1.3.1\PackageManagement.psm1 cannot
be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```


## <a name="prerequisites-for-powershell"></a>PowerShell용 필수 구성 요소

명령을 실행 하 여 PowerShell의 인스턴스를 감사 `$PSVersionTable` 합니다.
이 명령은 다음과 같은 출력을 생성 합니다.


```
Name                           Value
----                           -----
PSVersion                      5.1.17763.316
PSEdition                      Desktop
PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0...}
BuildVersion                   10.0.17763.316
CLRVersion                     4.0.30319.42000
WSManStackVersion              3.0
PSRemotingProtocolVersion      2.3
SerializationVersion           1.1.0.1
```

이러한 지침은 Windows 10을 실행 하는 컴퓨터에서 작성 및 테스트 되었으며 위에 나열 된 버전입니다.

## <a name="prerequisites-for-powershell-gallery"></a>PowerShell 갤러리에 대 한 필수 구성 요소

이러한 단계는 PowerShell 갤러리에서 모듈을 다운로드 하도록 서버를 준비 합니다.

> [!NOTE] 
> PowerShell 갤러리은 Windows 10, Windows Server 2016 및 PowerShell 6에서 지원 됩니다.
> 이전 버전에 대 한 자세한 내용은 [PowerShellGet 설치](/powershell/scripting/gallery/installing-psget)를 참조 하세요.


1. 관리자 권한으로 실행 정책을 통해 PowerShell을 관리자 권한으로 실행 합니다.
2. NuGet 패키지 공급자를 설치 합니다.
    - 설명: PowerShell 갤러리와 같은 NuGet 기반 리포지토리와 상호 작용 하려면이 공급자가 필요 합니다.
    - 참조: [install-packageprovider](https://docs.microsoft.com/powershell/module/packagemanagement/install-packageprovider?view=powershell-6).
    - 명령: `Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201` .
    - 선택적 매개 변수:
        - `-Proxy`. 요청에 대 한 프록시 서버를 지정 합니다.
        - `-Force`. 확인 메시지를 무시 합니다.
    
    NuGet이 설정 되지 않은 경우이 메시지가 표시 됩니다.
        
        NuGet provider is required to continue
        PowerShellGet requires NuGet provider version '2.8.5.201' or newer to interact with NuGet-based repositories. The NuGet
         provider must be available in 'C:\Program Files\PackageManagement\ProviderAssemblies' or
        'C:\Users\t\AppData\Local\PackageManagement\ProviderAssemblies'. You can also install the NuGet provider by running
        'Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force'. Do you want PowerShellGet to install and import
         the NuGet provider now?
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    
3. PowerShell 갤러리를 신뢰할 수 있는 리포지토리로 구성 합니다.
    - 설명: 기본적으로 PowerShell 갤러리는 신뢰할 수 없는 리포지토리입니다.
    - 참조: [set-psrepository](https://docs.microsoft.com/powershell/module/powershellget/set-psrepository?view=powershell-6).
    - 명령: `Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted` .
    - 선택적 매개 변수:
        - `-Proxy`. 요청에 대 한 프록시 서버를 지정 합니다.

    PowerShell 갤러리 신뢰할 수 없는 경우이 메시지가 표시 됩니다.

        Untrusted repository
        You are installing the modules from an untrusted repository. If you trust this repository, change its
        InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
        'PSGallery'?
        [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):

    명령을 실행 하 여이 변경 내용을 확인 하 고 모든 PSRepositories 감사를 수행할 수 있습니다 `Get-PSRepository` .

4. 최신 버전의 PowerShellGet을 설치 합니다.
    - 설명:이 모듈에는 PowerShell 갤러리에서 다른 모듈을 가져오는 데 사용 되는 도구가 포함 되어 있습니다. 버전 1.0.0.1은 Windows 10 및 Windows Server와 함께 제공 됩니다. 버전 1.6.0 이상이 필요 합니다. 설치 된 버전을 확인 하려면 `Get-Command -Module PowerShellGet` 명령을 실행 합니다.
    - 참조: [PowerShellGet을 설치](/powershell/scripting/gallery/installing-psget)합니다.
    - 명령: `Install-Module -Name PowerShellGet` .
    - 선택적 매개 변수:
        - `-Proxy`. 요청에 대 한 프록시 서버를 지정 합니다.
        - `-Force`. "이미 설치 됨" 경고를 무시 하 고 최신 버전을 설치 합니다.

    최신 버전의 PowerShellGet을 사용 하지 않는 경우이 오류가 표시 됩니다.
    
        Install-Module : A parameter cannot be found that matches parameter name 'AllowPrerelease'.
        At line:1 char:20
        Install-Module abc -AllowPrerelease
                           ~~~~~~~~~~~~~~~~
            CategoryInfo          : InvalidArgument: (:) [Install-Module], ParameterBindingException
            FullyQualifiedErrorId : NamedParameterNotFound,Install-Module
    
5. PowerShell을 다시 시작 합니다. 현재 세션에서는 새 버전을 로드할 수 없습니다. 새 PowerShell 세션에서 PowerShellGet의 최신 버전을 로드 합니다.

## <a name="download-and-install-the-module-via-powershell-gallery"></a>PowerShell 갤러리를 통해 모듈 다운로드 및 설치

이러한 단계를 수행 하면 PowerShell 갤러리에서 Az. ApplicationMonitor 모듈이 다운로드 됩니다.

1. PowerShell 갤러리에 대 한 모든 필수 조건이 충족 되는지 확인 합니다.
2. 관리자 권한으로 실행 정책을 통해 PowerShell을 관리자 권한으로 실행 합니다.
3. Az. ApplicationMonitor 모듈을 설치 합니다.
    - 참조: [Install-Module](https://docs.microsoft.com/powershell/module/powershellget/install-module?view=powershell-6).
    - 명령: `Install-Module -Name Az.ApplicationMonitor` .
    - 선택적 매개 변수:
        - `-Proxy`. 요청에 대 한 프록시 서버를 지정 합니다.
        - `-AllowPrerelease`. 알파 및 베타 릴리스를 설치할 수 있습니다.
        - `-AcceptLicense`. "라이선스 수락" 프롬프트를 무시 합니다.
        - `-Force`. "신뢰할 수 없는 리포지토리" 경고를 무시 합니다.

## <a name="download-and-install-the-module-manually-offline-option"></a>수동으로 모듈 다운로드 및 설치 (오프 라인 옵션)

어떤 이유로 든 PowerShell 모듈에 연결할 수 없는 경우에는 Az. ApplicationMonitor 모듈을 수동으로 다운로드 하 여 설치할 수 있습니다.

### <a name="manually-download-the-latest-nupkg-file"></a>최신 nupkg 파일 수동으로 다운로드

1. https://www.powershellgallery.com/packages/Az.ApplicationMonitor 로 이동합니다.
2. **버전 기록** 테이블에서 파일의 최신 버전을 선택 합니다.
3. **설치 옵션**아래에서 **수동 다운로드**를 선택 합니다.

### <a name="option-1-install-into-a-powershell-modules-directory"></a>옵션 1: PowerShell 모듈 디렉터리에 설치
Powershell 세션에서 검색할 수 있도록 수동으로 다운로드 한 PowerShell 모듈을 PowerShell 디렉터리에 설치 합니다.
자세한 내용은 [PowerShell 모듈 설치](/powershell/scripting/developer/module/installing-a-powershell-module)를 참조 하세요.


#### <a name="unzip-nupkg-as-a-zip-file-by-using-expand-archive-v1010"></a>Expand-Archive (v 1.0.1.0)를 사용 하 여 zip 파일로 nupkg 압축 풀기

- 설명: 기본 버전의 1.0.1.0 (v)에서 nupkg 파일의 압축을 풀 수 없습니다. .Zip 확장명을 사용 하 여 파일의 이름을 바꿉니다.
- 참조: [확장-보관](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6)합니다.
- 명령:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.3.0-alpha.nupkg"
    $pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
    $pathToNupkg | rename-item -newname $pathToZip
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
    ```

#### <a name="unzip-nupkg-by-using-expand-archive-v1100"></a>확장-보관을 사용 하 여 nupkg 압축 풀기 (v 1.1.0.0)

- 설명: 확장을 변경 하지 않고 nupkg 파일의 압축을 푸는 데 현재 버전의 Expand-Archive를 사용 합니다.
- 참조: [확장-보관](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6) 및 [Microsoft. PowerShell](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive/1.1.0.0).
- 명령:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToNupkg -DestinationPath $pathInstalledModule
    ```

### <a name="option-2-unzip-and-import-nupkg-manually"></a>옵션 2: 수동으로 nupkg 압축 풀기 및 가져오기
Powershell 세션에서 검색할 수 있도록 수동으로 다운로드 한 PowerShell 모듈을 PowerShell 디렉터리에 설치 합니다.
자세한 내용은 [PowerShell 모듈 설치](/powershell/scripting/developer/module/installing-a-powershell-module)를 참조 하세요.

다른 디렉터리에 모듈을 설치 하는 [경우 import-module을 사용 하](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-6)여 수동으로 모듈을 가져옵니다.

> [!IMPORTANT] 
> Dll은 상대 경로를 통해 설치 됩니다.
> 원하는 런타임 디렉터리에 패키지의 내용을 저장 하 고 액세스 권한이 읽기를 허용 하지만 쓰기를 허용 하지 않는지 확인 합니다.

1. 확장을 ".zip"으로 변경 하 고 원하는 설치 디렉터리로 패키지의 내용을 추출 합니다.
2. Az.ApplicationMonitor.psd1의 파일 경로를 찾습니다.
3. 관리자 권한으로 실행 정책을 통해 PowerShell을 관리자 권한으로 실행 합니다.
4. 명령을 사용 하 여 모듈을 로드 `Import-Module Az.ApplicationMonitor.psd1` 합니다.
    

## <a name="route-traffic-through-a-proxy"></a>프록시를 통해 트래픽 라우팅

개인 인트라넷에서 컴퓨터를 모니터링 하는 경우 프록시를 통해 HTTP 트래픽을 라우트 해야 합니다.

다운로드 하 고 설치 하는 PowerShell 명령은 PowerShell 갤러리에서 매개 변수를 지원 합니다. `-Proxy`
설치 스크립트를 작성 하는 경우 위의 지침을 검토 합니다.

Application Insights SDK는 앱의 원격 분석을 Microsoft로 전송 해야 합니다. web.config 파일에서 앱에 대 한 프록시 설정을 구성 하는 것이 좋습니다. 자세한 내용은 [APPLICATION INSIGHTS FAQ: 프록시 통과](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#proxy-passthrough)를 참조 하세요.


## <a name="enable-monitoring"></a>모니터링 사용

명령을 사용 `Enable-ApplicationInsightsMonitoring` 하 여 모니터링을 사용 하도록 설정 합니다.

이 cmdlet을 사용 하는 방법에 대 한 자세한 설명은 [API 참조](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#enable-applicationinsightsmonitoring) 를 참조 하세요.



## <a name="next-steps"></a>다음 단계

 원격 분석 보기:

- [메트릭을 탐색](../../azure-monitor/platform/metrics-charts.md) 하 여 성능 및 사용량을 모니터링 합니다.
- [이벤트와 로그를 검색](../../azure-monitor/app/diagnostic-search.md) 하 여 문제를 진단 합니다.
- 고급 쿼리를 위해 [분석을 사용](../../azure-monitor/app/analytics.md) 합니다.
- [대시보드를 만듭니다](../../azure-monitor/app/overview-dashboard.md).

 원격 분석 더 추가:

- [웹 테스트를 만들어](monitor-web-app-availability.md) 사이트가 라이브 상태로 유지되고 있는지 확인합니다.
- 웹 [클라이언트 원격 분석을 추가](../../azure-monitor/app/javascript.md) 하 여 웹 페이지 코드에서 예외를 확인 하 고 추적 호출을 사용 하도록 설정 합니다.
- 추적 및 로그 호출을 삽입할 수 있도록 [APPLICATION INSIGHTS SDK를 코드에 추가](../../azure-monitor/app/asp-net.md) 합니다.

Application Insights 에이전트를 사용 하 여 더 많은 작업 수행:

- 가이드를 사용 하 여 Application Insights 에이전트 [문제를 해결할](status-monitor-v2-troubleshoot.md) 수 있습니다.
