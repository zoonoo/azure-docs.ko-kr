---
title: Azure 상태 모니터 v2 상세 지침 | Microsoft Docs
description: 상태 모니터 v2를 사용 하 여 시작 하는 것에 대 한 자세한 지침입니다. 웹 사이트를 다시 배포 하지 않고 웹 사이트 성능을 모니터링 합니다. VM 또는 Azure의 온-프레미스에서 호스트되는 ASP.NET 웹앱으로 작업합니다.
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
ms.openlocfilehash: 6eca2b47c2362f34415db8b4f335f3089babc58b
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66255871"
---
# <a name="status-monitor-v2-detailed-instructions"></a>상태 모니터 v2 상세 지침

이 세부 정보를 어떻게 문서 다운로드 ApplicationMonitor 모듈을 PowerShell 갤러리에 등록 합니다. 시작 하는 데 필요한 가장 일반적인 매개 변수를 문서화 했습니다.
수동 지침은 인터넷 액세스를 사용할 수 없는 하는 경우에 포함 했습니다.

> [!IMPORTANT]
> 상태 모니터 v2는 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 참조 하세요. [추가 사용 약관에 대 한 Microsoft Azure 미리 보기](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="instrumentation-key"></a>계측 키

시작 하려면 계측 키가 있어야 합니다. 자세한 내용은 [Application Insights 리소스 만들기](create-new-resource.md#copy-the-instrumentation-key)합니다.

## <a name="run-powershell-as-administrator-with-an-elevated-execution-policy"></a>관리자 권한 실행 정책 사용 하 여 관리자 권한으로 PowerShell을 실행 합니다.

**관리자 권한으로 실행**: 
- 설명: PowerShell에는 컴퓨터를 변경 하려면 관리자 수준 권한이 필요 합니다.

**실행 정책을**:
- 설명: 기본적으로 PowerShell 스크립트를 실행할 수 없게 됩니다. 현재 범위에 RemoteSigned 스크립트를 허용 하는 것이 좋습니다.
- 참조: [실행 정책에 대 한](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) 고 [Set-executionpolicy](
https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6
)
- Cmd: `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process`
- 선택적 매개 변수:
    - `-Force` 이 확인 프롬프트를 건너뜁니다.

**예제 오류:**

```
Install-Module : The 'Install-Module' command was found in the module 'PowerShellGet', but the module could not be
loaded. For more information, run 'Import-Module PowerShellGet'.
    
Import-Module : File C:\Program Files\WindowsPowerShell\Modules\PackageManagement\1.3.1\PackageManagement.psm1 cannot
be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```


## <a name="prerequisites-for-powershell"></a>PowerShell용 필수 구성 요소

현재 사용 중인 PowerShell 명령을 실행 하 여 감사: `$PSVersionTable`합니다.
명령은 다음 출력을 생성 합니다.


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

이러한 지침은 작성 되 고 위에 나열 된 버전을 사용 하 여 Windows 10 컴퓨터에서 테스트 합니다.

## <a name="prerequisites-for-powershell-gallery"></a>PowerShell 갤러리에 대 한 필수 구성 요소

이러한 단계는 PowerShell 갤러리에서 모듈을 다운로드 하 여 서버를 준비 합니다.

> [!NOTE] 
> Windows 10, Windows Server 2016 및 PowerShell 6에 PowerShell 갤러리에 대 한 지원이 포함 됩니다. 이전 버전의 경우이 문서를 검토 합니다. [PowerShellGet 설치](https://docs.microsoft.com/powershell/gallery/installing-psget)


1. 관리자 권한 실행 정책을 사용 하 여 관리자 권한으로 PowerShell을 실행 합니다.
2. NuGet 패키지 공급자 
    - 설명: 이 공급자 powershell 갤러리와 같은 NuGet 기반 리포지토리와 상호 작용 해야 합니다.
    - 참조: [Install-PackageProvider](https://docs.microsoft.com/powershell/module/packagemanagement/install-packageprovider?view=powershell-6)
    - Cmd: `Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201`
    - 선택적 매개 변수:
        - `-Proxy` 요청에 대 한 프록시 서버를 지정합니다.
        - `-Force` 이 확인 프롬프트를 건너뜁니다. 
    
    NuGet 설정 하지 않은 경우이 메시지가 됩니다.
        
        NuGet provider is required to continue
        PowerShellGet requires NuGet provider version '2.8.5.201' or newer to interact with NuGet-based repositories. The NuGet
         provider must be available in 'C:\Program Files\PackageManagement\ProviderAssemblies' or
        'C:\Users\t\AppData\Local\PackageManagement\ProviderAssemblies'. You can also install the NuGet provider by running
        'Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force'. Do you want PowerShellGet to install and import
         the NuGet provider now?
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    
3. 신뢰할 수 있는 리포지토리
    - 설명: 기본적으로 powershell 갤러리는 신뢰할 수 없는 리포지토리입니다.
    - 참조: [Set-PSRepository](https://docs.microsoft.com/powershell/module/powershellget/set-psrepository?view=powershell-6)
    - Cmd: `Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted`
    - 선택적 매개 변수:
        - `-Proxy` 요청에 대 한 프록시 서버를 지정합니다.

    PowerShell 갤러리를 신뢰할 수 없는 경우이 메시지가 됩니다.

        Untrusted repository
        You are installing the modules from an untrusted repository. If you trust this repository, change its
        InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
        'PSGallery'?
        [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):

    - 이 변경 내용을 확인 하 고 cmd를 실행 하 여 모든 PSRepositories를 감사할 수 있습니다. `Get-PSRepository`

4. PowerShellGet 버전 
    - 설명: 이 모듈을 PowerShell 갤러리에서 다른 모듈을 가져오는 데 사용 하는 도구를 포함 합니다. Windows 10 및 Windows Server를 사용 하 여 v1.0.0.1 제공 됩니다. 필요한 최소 버전은 v1.6.0입니다. 명령을 실행 하 여 설치 된 버전을 감사 합니다. `Get-Command -Module PowerShellGet`
    - 참조: [PowerShellGet 설치](https://docs.microsoft.com/powershell/gallery/installing-psget)
    - Cmd: `Install-Module -Name PowerShellGet`
    - 선택적 매개 변수:
        - `-Proxy` 요청에 대 한 프록시 서버를 지정합니다.
        - `-Force` "이미 설치 된" 경고는 무시 되 고 최신 버전 설치 합니다.

    최신 버전의 PowerShellGet 사용 하지 않는 경우이 오류가 표시 됩니다.
    
        Install-Module : A parameter cannot be found that matches parameter name 'AllowPrerelease'.
        At line:1 char:20
        Install-Module abc -AllowPrerelease
                           ~~~~~~~~~~~~~~~~
            CategoryInfo          : InvalidArgument: (:) [Install-Module], ParameterBindingException
            FullyQualifiedErrorId : NamedParameterNotFound,Install-Module
    
5. PowerShell을 다시 시작 합니다. 현재 세션에서 새 버전을 로드 하는 것이 불가능 합니다. 새 Powershell 세션에 로드에 최신 PowerShellGet을 갖게 됩니다.

## <a name="download--install-via-powershell-gallery"></a>다운로드 및 PowerShell 갤러리를 통해 설치

이러한 단계는 PowerShell 갤러리에서 Az.ApplicationMonitor 모듈을 다운로드 합니다.

1. PowerShell 갤러리에 대 한 필수 구성 요소가 필요 합니다.
2. 관리자 권한 실행 정책을 사용 하 여 관리자 권한으로 PowerShell을 실행 합니다.
3. Az.ApplicationMonitor 모듈 설치
    - 참조: [Install-Module](https://docs.microsoft.com/powershell/module/powershellget/install-module?view=powershell-6)
    - Cmd: `Install-Module -Name Az.ApplicationMonitor`
    - 선택적 매개 변수:
        - `-Proxy` 요청에 대 한 프록시 서버를 지정합니다.
        - `-AllowPrerelease` 그러면 알파 및 베타 릴리스를 설치 합니다.
        - `-AcceptLicense` 이 "사용권에 동의" 프롬프트를 건너뜁니다.
        - `-Force` 이 "신뢰할 수 없는 리포지토리의" 경고 무시

## <a name="download--install-manually-offline-option"></a>다운로드 및 설치를 수동으로 (오프 라인 옵션)

어떤 이유로 든 PowerShell 모듈에 연결할 수 없으면, 있습니다 수 수동으로 다운로드 하 여 Az.ApplicationMonitor 모듈을 설치 합니다.

### <a name="manually-download-the-latest-nupkg"></a>최신 nupkg를 수동으로 다운로드

1. 로 이동 합니다. https://www.powershellgallery.com/packages/Az.ApplicationMonitor
2. 버전 기록에서 최신 버전을 선택 합니다.
3. "설치 옵션"을 찾아 "수동 다운로드"를 선택 합니다.

### <a name="option-1-install-into-powershell-modules-directory"></a>옵션 1: PowerShell modules 디렉터리에 설치
PowerShell 세션에서 검색 가능한 사용할 수 있도록 PowerShell 디렉터리에 수동으로 다운로드 한 PowerShell 모듈을 설치 합니다.
자세한 내용은 다음을 참조하세요. [PowerShell 모듈 설치](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module)


#### <a name="unzip-nupkg-as-zip-using-expand-archive-v1010"></a>Nupkg Expand-archive (v1.0.1.0)를 사용 하 여 zip으로 압축 해제

- 설명: Microsoft.PowerShell.Archive (v1.0.1.0)의 기본 버전은 nupkg 파일을 압축을 풉니다 수 없습니다. ".Zip" 확장명을 사용 하 여 파일을 이름을 바꿉니다.
- 참조: [Expand-Archive](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6)
- Cmd: 

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
    $pathToNupkg | rename-item -newname $pathToZip
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
    ```

#### <a name="unzip-nupkg-using-expand-archive-v1100"></a>Nupkg을 Expand-archive (v1.1.0.0)를 사용 하 여 압축을 풉니다.

- 설명: Expand-archive의 최신 버전의 확장 이름을 바꾸지 않고가 압축을 사용 합니다. 
- 참조: [확장 보관](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6) 고 [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive/1.1.0.0)
- Cmd:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToNupkg -DestinationPath $pathInstalledModule
    ```

### <a name="option-2-unzip-and-import-manually"></a>옵션 2: 압축을 풀고 수동으로 가져오기
PowerShell 세션에서 검색 가능한 사용할 수 있도록 PowerShell 디렉터리에 수동으로 다운로드 한 PowerShell 모듈을 설치 합니다.
자세한 내용은 다음을 참조하세요. [PowerShell 모듈 설치](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module)

를 다른 디렉터리에 설치 하는 경우 수동으로 가져와야 사용 하 여 모듈 [모듈 가져오기](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-6)

> [!IMPORTANT] 
> Dll 상대 경로 통해 설치 됩니다. 이 패키지 내용의 압축을 의도 한 런타임 디렉터리에 저장 하 고 액세스 권한을 읽기 쓰기는 제외를 허용 하는지 확인 합니다.

1. 확장명 ".zip"로 변경 하 고 원하는 설치 디렉터리에 패키지 내용의 압축을 풉니다.
2. "Az.ApplicationMonitor.psd1"에 파일 경로를 찾습니다.
3. 관리자 권한 실행 정책을 사용 하 여 관리자 권한으로 PowerShell을 실행 합니다. 
4. 명령을 통해 모듈을 로드: `Import-Module Az.ApplicationMonitor.psd1`합니다.
    

## <a name="proxy"></a>Proxy

사설 인트라넷에서 컴퓨터를 모니터링 하는 경우 프록시를 통해 http 트래픽을 라우팅하는 데 필요한 됩니다.

다운로드 하 여 PowerShell 갤러리에서는 Az.ApplicationMonitor를 설치 하기 위한 PowerShell 명령은 지원지 않습니다는 `-Proxy` 매개 변수입니다.
경우 위의 지침을 검토 하는 설치 스크립트를 작성 합니다.

Application Insights SDK는 microsoft 응용 프로그램의 원격 분석을 전송 해야 합니다. Web.config 파일에 응용 프로그램에 대 한 프록시 설정을 구성 하는 것이 좋습니다. 참조 [Application Insights FAQ: 프록시 통과](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#proxy-passthrough) 자세한 내용은 합니다.


## <a name="enable-monitoring"></a>모니터링 사용 

Cmd: `Enable-ApplicationInsightsMonitoring`

검토 우리의 [API 참조](status-monitor-v2-api-enable-monitoring.md) 에 대 한 자세한 설명은이 cmdlet을 사용 하는 방법입니다. 



## <a name="next-steps"></a>다음 단계

 원격 분석 보기:

- [메트릭을 탐색하여](../../azure-monitor/app/metrics-explorer.md) 성능 및 사용량을 모니터링합니다.
- [검색 이벤트 및 로그](../../azure-monitor/app/diagnostic-search.md) 문제 진단
- [분석](../../azure-monitor/app/analytics.md)을 통해 고급 쿼리를 수행합니다.
- [대시보드를 만듭니다](../../azure-monitor/app/overview-dashboard.md).

 원격 분석 더 추가:

- [웹 테스트를 만들어](monitor-web-app-availability.md) 사이트가 라이브 상태로 유지 되도록 합니다.
- [웹 클라이언트 원격 분석 추가](../../azure-monitor/app/javascript.md) 웹 페이지 코드에서 예외를 확인 하 고 추적 호출을 삽입할 수 있도록 합니다.
- [코드에 Application Insights SDK 추가](../../azure-monitor/app/asp-net.md) 추적을 삽입 하 고 호출을 기록할 수 있도록

상태 모니터 v2 사용 하 여 더 수행 합니다.

- 가이드를 사용 하 여 [문제 해결](status-monitor-v2-troubleshoot.md) v2 상태 모니터입니다.
