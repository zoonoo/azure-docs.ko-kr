---
title: Azure 응용 프로그램 인사이트 에이전트 자세한 지침 | 마이크로 소프트 문서
description: 응용 프로그램 인사이트 에이전트를 시작하기 위한 자세한 지침입니다. 웹 사이트를 다시 배포하지 않고 웹 사이트 성능을 모니터링합니다. 온-프레미스, VM 또는 Azure에서 호스팅되는 ASP.NET 웹 앱과 함께 작동합니다.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 8f6134e8f8fdb9af3f578afaf0670c32a3896e01
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81766872"
---
# <a name="application-insights-agent-formerly-named-status-monitor-v2-detailed-instructions"></a>응용 프로그램 인사이트 에이전트(이전 이름 상태 모니터 v2): 자세한 지침

이 문서에서는 PowerShell 갤러리에 온보로 등록하고 ApplicationMonitor 모듈을 다운로드하는 방법을 설명합니다.
시작하는 데 필요한 가장 일반적인 매개 변수가 포함되어 있습니다.
또한 인터넷에 액세스할 수 없는 경우를 대비하여 수동 다운로드 지침을 제공했습니다.

## <a name="get-an-instrumentation-key"></a>계측 키 받기

시작하려면 계측 키가 필요합니다. 자세한 내용은 [응용 프로그램 인사이트 만들기 리소스를](create-new-resource.md#copy-the-instrumentation-key)참조하십시오.

## <a name="run-powershell-as-admin-with-an-elevated-execution-policy"></a>높은 실행 정책을 통해 PowerShell을 관리자로 실행

### <a name="run-as-admin"></a>관리자로 실행

PowerShell은 컴퓨터를 변경하려면 관리자 수준의 권한이 필요합니다.
### <a name="execution-policy"></a>실행 정책
- 설명: 기본적으로 PowerShell 스크립트 실행이 비활성화됩니다. 현재 범위에 대해서만 RemoteSigned 스크립트를 허용하는 것이 좋습니다.
- 참조: 실행 정책 및 [실행 정책 설정](
https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6
)에 [대해.](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6)
- 명령: `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process`.
- 선택적 매개 변수:
    - `-Force`. 확인 프롬프트를 무시합니다.

**예제 오류**

```
Install-Module : The 'Install-Module' command was found in the module 'PowerShellGet', but the module could not be
loaded. For more information, run 'Import-Module PowerShellGet'.
    
Import-Module : File C:\Program Files\WindowsPowerShell\Modules\PackageManagement\1.3.1\PackageManagement.psm1 cannot
be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```


## <a name="prerequisites-for-powershell"></a>PowerShell용 필수 구성 요소

명령을 실행하여 PowerShell 인스턴스를 `$PSVersionTable` 감사합니다.
이 명령은 다음과 같은 출력을 생성합니다.


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

이러한 지침은 Windows 10을 실행하는 컴퓨터와 위에 나열된 버전에서 작성및 테스트되었습니다.

## <a name="prerequisites-for-powershell-gallery"></a>파워쉘 갤러리의 필수 구성 조건

이 단계는 서버가 PowerShell 갤러리에서 모듈을 다운로드할 수 있도록 준비합니다.

> [!NOTE] 
> PowerShell 갤러리는 윈도우에서 지원 됩니다 10, 윈도우 서버 2016, 그리고 파워 쉘 6.
> 이전 버전에 대한 자세한 내용은 [PowerShellGet 설치를](/powershell/scripting/gallery/installing-psget)참조하십시오.


1. PowerShell을 높은 실행 정책을 통해 관리자로 실행합니다.
2. NuGet 패키지 공급자를 설치합니다.
    - 설명: PowerShell 갤러리와 같은 NuGet 기반 리포지토리와 상호 작용하려면 이 공급자가 필요합니다.
    - 참조: [설치 패키지공급자](https://docs.microsoft.com/powershell/module/packagemanagement/install-packageprovider?view=powershell-6).
    - 명령: `Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201`.
    - 선택적 매개 변수:
        - `-Proxy`. 요청에 대한 프록시 서버를 지정합니다.
        - `-Force`. 확인 프롬프트를 무시합니다.
    
    NuGet이 설정되지 않은 경우 이 프롬프트가 표시됩니다.
        
        NuGet provider is required to continue
        PowerShellGet requires NuGet provider version '2.8.5.201' or newer to interact with NuGet-based repositories. The NuGet
         provider must be available in 'C:\Program Files\PackageManagement\ProviderAssemblies' or
        'C:\Users\t\AppData\Local\PackageManagement\ProviderAssemblies'. You can also install the NuGet provider by running
        'Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force'. Do you want PowerShellGet to install and import
         the NuGet provider now?
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    
3. PowerShell 갤러리를 신뢰할 수 있는 리포지토리로 구성합니다.
    - 설명: 기본적으로 PowerShell 갤러리는 신뢰할 수 없는 리포지토리입니다.
    - 참조: [설정-PSRepository](https://docs.microsoft.com/powershell/module/powershellget/set-psrepository?view=powershell-6).
    - 명령: `Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted`.
    - 선택적 매개 변수:
        - `-Proxy`. 요청에 대한 프록시 서버를 지정합니다.

    PowerShell 갤러리를 신뢰할 수 없는 경우 이 프롬프트를 받게 됩니다.

        Untrusted repository
        You are installing the modules from an untrusted repository. If you trust this repository, change its
        InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
        'PSGallery'?
        [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):

    이 변경 을 확인하고 명령을 실행하여 모든 PSRepositories를 감사 할 `Get-PSRepository` 수 있습니다.

4. 최신 버전의 PowerShellGet을 설치합니다.
    - 설명: 이 모듈에는 PowerShell 갤러리에서 다른 모듈을 얻는 데 사용되는 도구가 포함되어 있습니다. 버전 1.0.0.1 은 Windows 10 및 Windows 서버와 함께 제공됩니다. 버전 1.6.0 이상이 필요합니다. 설치된 버전을 확인하려면 명령을 `Get-Command -Module PowerShellGet` 실행합니다.
    - 참조: [PowerShellGet 설치](/powershell/scripting/gallery/installing-psget).
    - 명령: `Install-Module -Name PowerShellGet`.
    - 선택적 매개 변수:
        - `-Proxy`. 요청에 대한 프록시 서버를 지정합니다.
        - `-Force`. "이미 설치된" 경고를 우회하고 최신 버전을 설치합니다.

    최신 버전의 PowerShellGet을 사용하지 않는 경우 이 오류가 발생합니다.
    
        Install-Module : A parameter cannot be found that matches parameter name 'AllowPrerelease'.
        At line:1 char:20
        Install-Module abc -AllowPrerelease
                           ~~~~~~~~~~~~~~~~
            CategoryInfo          : InvalidArgument: (:) [Install-Module], ParameterBindingException
            FullyQualifiedErrorId : NamedParameterNotFound,Install-Module
    
5. 파워쉘을 다시 시작합니다. 현재 세션에서 새 버전을 로드할 수 없습니다. 새 PowerShell 세션은 최신 버전의 PowerShellGet을 로드합니다.

## <a name="download-and-install-the-module-via-powershell-gallery"></a>PowerShell 갤러리를 통해 모듈 다운로드 및 설치

이 단계는 PowerShell 갤러리에서 Az.ApplicationMonitor 모듈을 다운로드합니다.

1. PowerShell 갤러리의 모든 필수 구성 조건이 충족되었는지 확인합니다.
2. PowerShell을 높은 실행 정책을 통해 관리자로 실행합니다.
3. Az.ApplicationMonitor 모듈을 설치합니다.
    - 참조: [설치 모듈](https://docs.microsoft.com/powershell/module/powershellget/install-module?view=powershell-6).
    - 명령: `Install-Module -Name Az.ApplicationMonitor`.
    - 선택적 매개 변수:
        - `-Proxy`. 요청에 대한 프록시 서버를 지정합니다.
        - `-AllowPrerelease`. 알파 및 베타 릴리스를 설치할 수 있습니다.
        - `-AcceptLicense`. "라이센스 수락" 프롬프트를 무시합니다.
        - `-Force`. "신뢰할 수 없는 리포지토리" 경고를 무시합니다.

## <a name="download-and-install-the-module-manually-offline-option"></a>모듈을 수동으로 다운로드및 설치(오프라인 옵션)

어떤 이유로든 PowerShell 모듈에 연결할 수 없는 경우 Az.ApplicationMonitor 모듈을 수동으로 다운로드하여 설치할 수 있습니다.

### <a name="manually-download-the-latest-nupkg-file"></a>수동으로 최신 nupkg 파일을 다운로드

1. [https://editor.swagger.io](https://www.powershellgallery.com/packages/Az.ApplicationMonitor ) 로 이동합니다.
2. **버전 기록** 테이블에서 파일의 최신 버전을 선택합니다.
3. **설치 옵션에서** **수동 다운로드를 선택합니다.**

### <a name="option-1-install-into-a-powershell-modules-directory"></a>옵션 1: PowerShell 모듈 디렉토리에 설치
수동으로 다운로드한 PowerShell 모듈을 PowerShell 디렉토리에 설치하여 PowerShell 세션에서 검색할 수 있습니다.
자세한 내용은 [PowerShell 모듈 설치를](/powershell/scripting/developer/module/installing-a-powershell-module)참조하십시오.


#### <a name="unzip-nupkg-as-a-zip-file-by-using-expand-archive-v1010"></a>확장 아카이브 (v1.0.1.0)를 사용하여 zip 파일로 압축 nupkg을 해제

- 설명: Microsoft.PowerShell.Archive(v1.0.1.0)의 기본 버전은 nupkg 파일의 압축을 해제할 수 없습니다. .zip 확장명으로 파일 이름을 바꿉니다.
- 참조: [확장 아카이브.](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6)
- 명령:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.3.0-alpha.nupkg"
    $pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
    $pathToNupkg | rename-item -newname $pathToZip
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
    ```

#### <a name="unzip-nupkg-by-using-expand-archive-v1100"></a>확장 아카이브(v1.1.0.0)를 사용하여 압축 해제

- 설명: 확장/확장/확장 내용을 변경하지 않고 nupkg 파일의 압축을 풀려면 확장 아카이브의 현재 버전을 사용합니다.
- 참조: [확장-아카이브](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6) 및 [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive/1.1.0.0).
- 명령:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToNupkg -DestinationPath $pathInstalledModule
    ```

### <a name="option-2-unzip-and-import-nupkg-manually"></a>옵션 2: 수동으로 압축 해제 및 가져오기 nupkg
수동으로 다운로드한 PowerShell 모듈을 PowerShell 디렉토리에 설치하여 PowerShell 세션에서 검색할 수 있습니다.
자세한 내용은 [PowerShell 모듈 설치를](/powershell/scripting/developer/module/installing-a-powershell-module)참조하십시오.

모듈을 다른 디렉터리에 설치하는 경우 [가져오기 모듈을](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-6)사용하여 모듈을 수동으로 가져옵니다.

> [!IMPORTANT] 
> DLL은 상대 경로를 통해 설치됩니다.
> 패키지의 내용을 의도한 런타임 디렉터리에 저장하고 액세스 권한이 읽기를 허용하지만 작성할 수 없는지 확인합니다.

1. 확장을 ".zip"으로 변경하고 패키지 내용을 의도한 설치 디렉토리로 추출합니다.
2. Az.ApplicationMonitor.psd1의 파일 경로를 찾습니다.
3. PowerShell을 높은 실행 정책을 통해 관리자로 실행합니다.
4. 명령을 사용하여 모듈을 `Import-Module Az.ApplicationMonitor.psd1` 로드합니다.
    

## <a name="route-traffic-through-a-proxy"></a>프록시를 통한 트래픽 경로

개인 인트라넷에서 컴퓨터를 모니터링할 때 프록시를 통해 HTTP 트래픽을 라우팅해야 합니다.

PowerShell 명령은 A.Application모니터를 다운로드하여 설치하도록 명령합니다. `-Proxy`
설치 스크립트를 작성할 때 위의 지침을 검토합니다.

응용 프로그램 인사이트 SDK는 앱의 원격 분석을 Microsoft로 보내야 합니다. web.config 파일에서 앱에 대한 프록시 설정을 구성하는 것이 좋습니다. 자세한 내용은 [응용 프로그램 인사이트 FAQ: 프록시 통과](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#proxy-passthrough)를 참조하십시오.


## <a name="enable-monitoring"></a>모니터링 사용

`Enable-ApplicationInsightsMonitoring` 명령을 사용하여 모니터링을 활성화합니다.

이 cmdlet을 사용하는 방법에 대한 자세한 설명은 [API 참조를](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#enable-applicationinsightsmonitoring) 참조하십시오.



## <a name="next-steps"></a>다음 단계

 원격 분석 보기:

- [메트릭을 탐색하여](../../azure-monitor/platform/metrics-charts.md) 성능 및 사용량을 모니터링합니다.
- [이벤트 및 로그를 검색하여](../../azure-monitor/app/diagnostic-search.md) 문제를 진단합니다.
- 고급 쿼리에 [애널리틱스를 사용합니다.](../../azure-monitor/app/analytics.md)
- [대시보드를 만듭니다.](../../azure-monitor/app/overview-dashboard.md)

 원격 분석 더 추가:

- [웹 테스트를 만들어](monitor-web-app-availability.md) 사이트가 라이브 상태로 유지되고 있는지 확인합니다.
- [웹 클라이언트 원격 분석을 추가하여](../../azure-monitor/app/javascript.md) 웹 페이지 코드의 예외를 확인하고 추적 호출을 사용하도록 설정합니다.
- 추적 및 로그 호출을 삽입할 수 있도록 [코드에 응용 프로그램 인사이트 SDK를 추가합니다.](../../azure-monitor/app/asp-net.md)

애플리케이션 인사이트 에이전트로 더 많은 작업을 수행합니다.

- 가이드를 사용하여 애플리케이션 인사이트 에이전트 [문제를 해결하세요.](status-monitor-v2-troubleshoot.md)
