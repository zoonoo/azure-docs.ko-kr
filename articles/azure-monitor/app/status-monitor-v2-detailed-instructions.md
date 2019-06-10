---
title: Azure 상태 모니터 v2 상세 지침 | Microsoft Docs
description: 상태 모니터 v2를 사용 하 여 시작 하는 것에 대 한 자세한 지침입니다. 웹 사이트를 다시 배포 하지 않고 웹 사이트 성능을 모니터링 합니다. 온-프레미스 또는 Azure Vm에서 호스트 하는 ASP.NET 웹 앱에서 작동 합니다.
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: d0960c749d74903acc778c0f21d5c49f380195ae
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734191"
---
# <a name="status-monitor-v2-detailed-instructions"></a>상태 모니터 v2: 자세한 지침

이 문서에서는 설명 하는 방법을 다운로드 ApplicationMonitor 모듈을 PowerShell 갤러리에 등록 합니다.
시작 해야 하는 가장 일반적인 매개 변수를 설명 합니다.
또한 인터넷 액세스 권한이 없는 경우 수동 지침을 포함 합니다.

> [!IMPORTANT]
> 상태 모니터 v2는 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공 하 고 프로덕션 워크 로드에 대 한 권장 하지 않습니다. 일부 기능은 지원 되지 않는, 및 일부 기능이 제한 될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="get-an-instrumentation-key"></a>계측 키 가져오기

시작 하려면 계측 키를 해야 합니다. 자세한 내용은 [Application Insights 리소스 만들기](create-new-resource.md#copy-the-instrumentation-key)합니다.

## <a name="run-powershell-as-admin-with-an-elevated-execution-policy"></a>상승 된 실행 정책을 사용 하 여 PowerShell을 관리자로 실행 합니다.

**관리자로 실행**

PowerShell에는 컴퓨터를 변경 하려면 관리자 수준 권한이 필요 합니다.

**실행 정책**
- 설명: PowerShell 스크립트를 실행 합니다. 기본적으로 비활성화 됩니다. 현재 범위에만 RemoteSigned 스크립트를 허용 하는 것이 좋습니다.
- 참조: [실행 정책에 대 한](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) 하 고 [Set-executionpolicy](
https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6
)합니다.
- 명령: `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process`합니다.
- 선택적 매개 변수:
    - `-Force`. 확인 프롬프트를 무시합니다.

**오류의 예제**

```
Install-Module : The 'Install-Module' command was found in the module 'PowerShellGet', but the module could not be
loaded. For more information, run 'Import-Module PowerShellGet'.
    
Import-Module : File C:\Program Files\WindowsPowerShell\Modules\PackageManagement\1.3.1\PackageManagement.psm1 cannot
be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```


## <a name="prerequisites-for-powershell"></a>PowerShell용 필수 구성 요소

PowerShell의 인스턴스를 실행 하 여 감사를 `$PSVersionTable` 명령입니다.
이 명령은 다음 출력을 생성합니다.


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

이러한 지침은 작성 되 고 Windows 10 및 위에 나열 된 버전을 실행 하는 컴퓨터에서 테스트 합니다.

## <a name="prerequisites-for-powershell-gallery"></a>PowerShell 갤러리에 대 한 필수 구성 요소

이러한 단계는 PowerShell 갤러리에서 모듈을 다운로드 하 여 서버를 준비 합니다.

> [!NOTE] 
> PowerShell 갤러리는 Windows 10, Windows Server 2016 및 PowerShell 6에서 지원 됩니다.
> 이전 버전에 대 한 정보를 참조 하세요 [PowerShellGet 설치](https://docs.microsoft.com/powershell/gallery/installing-psget)합니다.


1. 관리자 권한 실행 정책을 사용 하 여 관리자로 PowerShell을 실행 합니다.
2. NuGet 패키지 공급자를 설치 합니다.
    - 설명: 이 공급자를 PowerShell 갤러리와 같은 NuGet 기반 리포지토리와 상호 작용 해야 합니다.
    - 참조: [Install-PackageProvider](https://docs.microsoft.com/powershell/module/packagemanagement/install-packageprovider?view=powershell-6).
    - 명령: `Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201`합니다.
    - 선택적 매개 변수:
        - `-Proxy`. 요청에 대 한 프록시 서버를 지정합니다.
        - `-Force`. 확인 프롬프트를 무시합니다.
    
    NuGet 설정 하지 않은 경우이 메시지가 됩니다.
        
        NuGet provider is required to continue
        PowerShellGet requires NuGet provider version '2.8.5.201' or newer to interact with NuGet-based repositories. The NuGet
         provider must be available in 'C:\Program Files\PackageManagement\ProviderAssemblies' or
        'C:\Users\t\AppData\Local\PackageManagement\ProviderAssemblies'. You can also install the NuGet provider by running
        'Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force'. Do you want PowerShellGet to install and import
         the NuGet provider now?
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    
3. PowerShell 갤러리를 신뢰할 수 있는 리포지토리로 구성 합니다.
    - 설명: 기본적으로 PowerShell 갤러리는 신뢰할 수 없는 리포지토리입니다.
    - 참조: [Set-PSRepository](https://docs.microsoft.com/powershell/module/powershellget/set-psrepository?view=powershell-6).
    - 명령: `Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted`합니다.
    - 선택적 매개 변수:
        - `-Proxy`. 요청에 대 한 프록시 서버를 지정합니다.

    PowerShell 갤러리를 신뢰할 수 없는 경우이 메시지가 됩니다.

        Untrusted repository
        You are installing the modules from an untrusted repository. If you trust this repository, change its
        InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
        'PSGallery'?
        [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):

    이 변경 내용을 확인 하 수 실행 하 여 모든 PSRepositories 감사는 `Get-PSRepository` 명령입니다.

4. 최신 버전의 PowerShellGet 설치 합니다.
    - 설명: 이 모듈을 PowerShell 갤러리에서 다른 모듈을 가져오는 데 사용 하는 도구를 포함 합니다. Windows 10 및 Windows Server 버전 1.0.0.1 제공 됩니다. 버전 1.6.0 이상이 필요 합니다. 설치 된 버전을 확인 하려면 실행을 `Get-Command -Module PowerShellGet` 명령입니다.
    - 참조: [PowerShellGet 설치](https://docs.microsoft.com/powershell/gallery/installing-psget)합니다.
    - 명령: `Install-Module -Name PowerShellGet`합니다.
    - 선택적 매개 변수:
        - `-Proxy`. 요청에 대 한 프록시 서버를 지정합니다.
        - `-Force`. "이미 설치 된" 경고를 무시 하 고 최신 버전을 설치 합니다.

    최신 버전의 PowerShellGet 사용 하지 않는 경우이 오류가 표시 됩니다.
    
        Install-Module : A parameter cannot be found that matches parameter name 'AllowPrerelease'.
        At line:1 char:20
        Install-Module abc -AllowPrerelease
                           ~~~~~~~~~~~~~~~~
            CategoryInfo          : InvalidArgument: (:) [Install-Module], ParameterBindingException
            FullyQualifiedErrorId : NamedParameterNotFound,Install-Module
    
5. PowerShell을 다시 시작 합니다. 현재 세션에서 새 버전을 로드할 수 없습니다. 새 PowerShell 세션에는 최신 버전의 PowerShellGet 로드 됩니다.

## <a name="download-and-install-the-module-via-powershell-gallery"></a>PowerShell 갤러리를 통해 모듈 다운로드 및 설치

이러한 단계는 PowerShell 갤러리에서 Az.ApplicationMonitor 모듈을 다운로드 합니다.

1. PowerShell 갤러리에 대 한 모든 필수 구성 요소가 충족 되는지 확인 합니다.
2. 관리자 권한 실행 정책을 사용 하 여 관리자로 PowerShell을 실행 합니다.
3. Az.ApplicationMonitor 모듈을 설치 합니다.
    - 참조: [Install-module](https://docs.microsoft.com/powershell/module/powershellget/install-module?view=powershell-6)합니다.
    - 명령: `Install-Module -Name Az.ApplicationMonitor`합니다.
    - 선택적 매개 변수:
        - `-Proxy`. 요청에 대 한 프록시 서버를 지정합니다.
        - `-AllowPrerelease`. 알파 및 베타 버전의 설치를 허용 합니다.
        - `-AcceptLicense`. "라이선스가 허용" 라는 메시지를 무시
        - `-Force`. "신뢰할 수 없는 리포지토리의" 경고를 무시합니다.

## <a name="download-and-install-the-module-manually-offline-option"></a>수동으로 다운로드 및 설치 된 모듈 (오프 라인 옵션)

어떤 이유로 든 PowerShell 모듈에 연결할 수 없으면, 수동으로 다운로드 하 Az.ApplicationMonitor 모듈을 설치 합니다.

### <a name="manually-download-the-latest-nupkg-file"></a>수동으로 최신 nupkg 파일 다운로드

1. https://www.powershellgallery.com/packages/Az.ApplicationMonitor로 이동합니다.
2. 파일의 최신 버전을 선택 합니다 **버전 기록** 테이블입니다.
3. 아래 **설치 옵션**를 선택 **수동 다운로드**합니다.

### <a name="option-1-install-into-a-powershell-modules-directory"></a>옵션 1: PowerShell 모듈 디렉터리에 설치
PowerShell 세션에서 검색할 수 있도록 PowerShell 디렉터리에 수동으로 다운로드 한 PowerShell 모듈을 설치 합니다.
자세한 내용은 [PowerShell 모듈 설치](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module)합니다.


#### <a name="unzip-nupkg-as-a-zip-file-by-using-expand-archive-v1010"></a>Nupkg를 zip 파일로 압축을 풉니다 Expand-archive (v1.0.1.0)를 사용 하 여

- 설명: Microsoft.PowerShell.Archive (v1.0.1.0)의 기본 버전은 nupkg 파일을 압축을 풉니다 수 없습니다. .Zip 확장명을 사용 하 여 파일을 이름을 바꿉니다.
- 참조: [확장 보관](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6)합니다.
- 명령:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.3.0-alpha.nupkg"
    $pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
    $pathToNupkg | rename-item -newname $pathToZip
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
    ```

#### <a name="unzip-nupkg-by-using-expand-archive-v1100"></a>Nupkg을 Expand-archive (v1.1.0.0)를 사용 하 여 압축을 풉니다

- 설명: Expand-archive의 최신 버전의 확장을 변경 하지 않고 nupkg 파일 압축을 사용 합니다.
- 참조: [확장 보관](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6) 하 고 [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive/1.1.0.0)합니다.
- 명령:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToNupkg -DestinationPath $pathInstalledModule
    ```

### <a name="option-2-unzip-and-import-nupkg-manually"></a>옵션 2: 압축을 풀고 nupkg를 수동으로 가져오려면
PowerShell 세션에서 검색할 수 있도록 PowerShell 디렉터리에 수동으로 다운로드 한 PowerShell 모듈을 설치 합니다.
자세한 내용은 [PowerShell 모듈 설치](https://docs.mircrosoft.com/powershell/developer/module/installing-a-powershell-module)합니다.

다른 디렉터리에 모듈을 설치 하는 경우 수동으로 모듈을 사용 하 여 가져올 [Import-module](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-6)합니다.

> [!IMPORTANT] 
> Dll은 상대 경로 통해 설치 됩니다.
> 패키지의 내용을 원하는 런타임 디렉터리에 저장 하 고 액세스 권한을 읽기 쓰기는 제외를 허용 하는지 확인 합니다.

1. 확장명 ".zip"로 변경 하 고 원하는 설치 디렉터리에 패키지 내용의 압축을 풉니다.
2. Az.ApplicationMonitor.psd1의 파일 경로를 찾습니다.
3. 관리자 권한 실행 정책을 사용 하 여 관리자로 PowerShell을 실행 합니다.
4. 사용 하 여 모듈을 로드 합니다 `Import-Module Az.ApplicationMonitor.psd1` 명령입니다.
    

## <a name="route-traffic-through-a-proxy"></a>프록시를 통한 트래픽 라우팅

사설 인트라넷에서 컴퓨터를 모니터링 하는 경우에 프록시를 통해 HTTP 트래픽을 라우팅하는 것이 해야 합니다.

다운로드 하 여 PowerShell 갤러리에서 Az.ApplicationMonitor를 설치 하기 위한 PowerShell 명령은 지원를 `-Proxy` 매개 변수입니다.
설치 스크립트를 작성 하는 경우 위의 지침을 검토 하세요.

Application Insights SDK는 microsoft 앱의 원격 분석을 전송 해야 합니다. Web.config 파일에서 앱에 대 한 프록시를 구성 하는 것이 좋습니다. 자세한 내용은 참조 하세요. [Application Insights FAQ: 프록시 통과](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#proxy-passthrough)합니다.


## <a name="enable-monitoring"></a>모니터링 사용

사용 된 `Enable-ApplicationInsightsMonitoring` 모니터링을 사용 하도록 명령 합니다.

참조 된 [API 참조](status-monitor-v2-api-enable-monitoring.md) 에 대 한 자세한 설명은이 cmdlet을 사용 하는 방법입니다.



## <a name="next-steps"></a>다음 단계

 원격 분석 보기:

- [메트릭 탐색](../../azure-monitor/app/metrics-explorer.md) 성능 모니터링을 사용 합니다.
- [검색 이벤트 및 로그](../../azure-monitor/app/diagnostic-search.md) 문제를 진단 합니다.
- [Analytics를 사용 하 여](../../azure-monitor/app/analytics.md) 자세한 고급 쿼리 합니다.
- [대시보드를 만들](../../azure-monitor/app/overview-dashboard.md)합니다.

 원격 분석 더 추가:

- [웹 테스트를 만들어](monitor-web-app-availability.md) 사이트가 라이브 상태로 유지 되도록 합니다.
- [웹 클라이언트 원격 분석 추가](../../azure-monitor/app/javascript.md) 웹 페이지 코드에서 예외를 확인 하 고 추적 호출을 사용 하도록 설정 합니다.
- [코드에 Application Insights SDK 추가](../../azure-monitor/app/asp-net.md) 추적을 삽입 하 고 호출을 기록할 수 있도록 합니다.

상태 모니터 v2 사용 하 여 더 수행 합니다.

- 가이드를 사용 하 여 [해결](status-monitor-v2-troubleshoot.md) v2 상태 모니터입니다.
