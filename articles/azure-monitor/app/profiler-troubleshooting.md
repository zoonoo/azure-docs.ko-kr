---
title: Azure 애플리케이션 Insights Profiler를 사용 하 여 문제 해결
description: 이 문서에서는 개발자가 Application Insights Profiler를 사용 하 고 사용 하는 데 도움이 되는 문제 해결 단계 및 정보를 제공
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 622a83c6d91bf2a30c2844e3279d6fd4b89d429f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102213796"
---
# <a name="troubleshoot-problems-enabling-or-viewing-application-insights-profiler"></a>Application Insights Profiler를 사용하도록 설정하거나 볼 때 발생하는 문제 해결

## <a name="general-troubleshooting"></a><a id="troubleshooting"></a>일반 문제 해결

### <a name="make-sure-youre-using-the-appropriate-profiler-endpoint"></a>적절 한 프로파일러 끝점을 사용 하 고 있는지 확인 합니다.

현재는 끝점을 수정 해야 하는 유일한 지역은 [Azure Government](https://docs.microsoft.com/azure/azure-government/compare-azure-government-global-azure#application-insights) 및 [Azure 중국](https://docs.microsoft.com/azure/china/resources-developer-guide)입니다.

|앱 설정    | 미국 정부 클라우드 | 중국 클라우드 |   
|---------------|---------------------|-------------|
|ApplicationInsightsProfilerEndpoint         | `https://profiler.monitor.azure.us`    | `https://profiler.monitor.azure.cn` |
|ApplicationInsightsEndpoint | `https://dc.applicationinsights.us` | `https://dc.applicationinsights.azure.cn` |

### <a name="profiles-are-uploaded-only-if-there-are-requests-to-your-application-while-profiler-is-running"></a>Profiler가 실행되는 동안 애플리케이션에 대한 요청이 있을 때만 프로필에 업로드됨

Azure 애플리케이션 Insights Profiler는 매시간 2 분 동안 데이터를 수집 합니다. **Application Insights Profiler 구성** 창에서 **지금 프로필** 단추를 선택 하면 데이터를 수집할 수도 있습니다.

> [!NOTE]
> 프로 파일링 데이터는 프로파일러가 실행 되는 동안 발생 한 요청에 연결할 수 있는 경우에만 업로드 됩니다. 

Profiler는 Application Insights 리소스에 추적 메시지 및 사용자 지정 이벤트를 씁니다. 이러한 이벤트를 사용 하 여 프로파일러가 실행 되는 방법을 확인할 수 있습니다.

1. Profiler가 Application Insights 리소스에 보낸 추적 메시지 및 사용자 지정 이벤트를 검색합니다. 이러한 검색 문자열을 사용하여 관련 데이터를 찾을 수 있습니다.

    ```
    stopprofiler OR startprofiler OR upload OR ServiceProfilerSample
    ```
    다음 이미지에는 두 AI 리소스에서 수행한 검색의 두 가지 예가 나와 있습니다. 
    
   * 왼쪽 예의 경우 Profiler가 실행되는 동안 애플리케이션이 요청을 수신하지 않습니다. 그리고 활동이 없어서 업로드가 취소되었음을 설명하는 메시지가 표시됩니다. 

   * 오른쪽 예의 경우에는 Profiler가 시작되었으며, Profiler가 실행되는 동안 발생한 요청이 검색되면 사용자 지정 이벤트가 전송되었습니다. `ServiceProfilerSample`사용자 지정 이벤트가 표시 되 면 프로필이 캡처되고 **Application Insights 성능** 창에서 해당 프로필을 사용할 수 있음을 의미 합니다.

     레코드가 표시 되지 않으면 프로파일러가 실행 되지 않습니다. 이러한 문제를 해결하려면 이 문서 뒷부분에서 사용 중인 특정 앱 유형에 해당하는 문제 해결 섹션을 참조하세요.  

     ![Profiler 원격 분석 검색][profiler-search-telemetry]

### <a name="other-things-to-check"></a>기타 확인해야 할 사항
* 앱이 .NET Framework 4.6에서 실행 중인지 확인합니다.
* 웹앱이 ASP.NET Core 애플리케이션인 경우 ASP.NET Core 2.0 이상을 실행해야 합니다.
* 확인하려는 데이터가 생성된 지 2주 이상 지난 경우에는 시간 필터의 범위를 제한한 후에 다시 시도해 봅니다. 추적은 7일 후에 삭제됩니다.
* 프록시 또는 방화벽이 액세스를 차단 하지 않았는지 확인 https://gateway.azureserviceprofiler.net 합니다.
* 프로파일러는 무료 또는 공유 app service 계획에서 지원 되지 않습니다. 이러한 계획 중 하나를 사용 하는 경우 기본 계획 중 하나로 확장을 시도 하 고 프로파일러가 작업을 시작 해야 합니다.

### <a name="double-counting-in-parallel-threads"></a><a id="double-counting"></a>병렬 스레드에서 이중 계산

경우에 따라 스택 뷰어의 총 시간 메트릭은 요청 기간 이상입니다.

이 상황은 둘 이상의 병렬 스레드가 요청과 연결 된 경우 발생할 수 있습니다. 이 경우 총 스레드 시간은 경과된 시간 이상입니다.

하나의 스레드가 다른 스레드가 완료될 때까지 대기 중일 수 있습니다. 뷰어는 이러한 상황의 검색을 시도하여 불필요한 대기를 생략합니다. 그런데 이 과정에서 중요할 수 있는 정보를 생략할 위험을 방지하기 위해 너무 많은 정보가 표시되는 문제가 발생합니다.

추적에 병렬 스레드가 표시 되 면 요청에 대 한 실행 부하 과다 경로를 식별할 수 있도록 대기 중인 스레드를 확인 합니다.

대기 상태로 빠르게 전환되는 스레드는 다른 스레드를 기다리는 중일 뿐인 경우가 많습니다. 다른 스레드에 집중하고 대기 중인 스레드 대기 시간을 무시합니다.

### <a name="error-report-in-the-profile-viewer"></a>프로파일링 뷰어의 오류 보고서
포털에서 지원 티켓을 제출합니다. 오류 메시지의 상관 관계 ID를 포함해야 합니다.

## <a name="troubleshoot-profiler-on-azure-app-service"></a>Azure App Service에서 Profiler 문제 해결

Profiler가 제대로 작동하도록 하려면 다음 조건을 충족해야 합니다.
* 웹앱 서비스 계획이 기본 계층 이상이어야 합니다.
* 웹앱에서 Application Insights를 사용할 수 있어야 합니다.
* 웹 앱에는 다음과 같은 앱 설정이 있어야 합니다.

    |앱 설정    | 값    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | Application Insights 리소스용 iKey    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~3 |


* **ApplicationInsightsProfiler3** 웹 작업이 실행되고 있어야 합니다. 웹 작업을 확인하려면 다음 단계를 수행합니다.
   1. [Kudu](/archive/blogs/cdndevs/the-kudu-debug-console-azure-websites-best-kept-secret)로 이동합니다.
   1. **도구** 메뉴에서 **웹 작업 대시보드** 를 선택합니다.  
      **웹 작업** 창이 열립니다. 
   
      ![스크린샷에는 작업의 이름, 상태 및 마지막 실행 시간이 표시 되는 WebJobs 창이 표시 됩니다.][profiler-webjob]   
   
   1. 로그를 포함 하 여 webjob의 세부 정보를 보려면 **ApplicationInsightsProfiler3** 링크를 선택 합니다.  
     **지속적인 웹 작업 세부 정보** 창이 열립니다.

      ![연속 WebJob 세부 정보 창이 표시 됩니다.][profiler-webjob-log]

프로파일러가 작동 하지 않는 경우 로그를 다운로드 하 여 팀에 보내 도움을 받을 수 있습니다 serviceprofilerhelp@microsoft.com .

### <a name="check-the-diagnostic-services-site-extension-status-page"></a>진단 서비스 사이트 확장의 상태 페이지를 확인 합니다.
포털이 포털의 [Application Insights 창을](profiler.md) 통해 사용 하도록 설정 된 경우 진단 서비스 사이트 확장에서 프로파일러를 사용 하도록 설정 했습니다.

> [!NOTE]
> Application Insights Profiler의 코드 없는 설치는 .NET Core 지원 정책을 따릅니다.
> 지원 되는 런타임에 대 한 자세한 내용은 [.Net Core 지원 정책](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)을 참조 하세요.

다음 url로 이동 하 여이 확장의 상태 페이지를 확인할 수 있습니다. `https://{site-name}.scm.azurewebsites.net/DiagnosticServices`

> [!NOTE]
> 상태 페이지 링크의 도메인은 클라우드에 따라 달라 집니다.
이 도메인은 App Service에 대 한 Kudu 관리 사이트와 동일 합니다.

이 상태 페이지는 프로파일러 및 Snapshot Collector 에이전트의 설치 상태를 표시 합니다. 예기치 않은 오류가 발생 한 경우 표시 되 고이를 해결 하는 방법을 보여 줍니다.

App Service에 대 한 Kudu 관리 사이트를 사용 하 여이 상태 페이지의 기준 url을 가져올 수 있습니다.
1. Azure Portal에서 App Service 애플리케이션을 엽니다.
2. **고급 도구** 를 선택 하거나 **Kudu** 를 검색 합니다.
3. **이동** 을 선택합니다.
4. Kudu 관리 사이트에 있는 경우 URL에서 **다음을 추가 `/DiagnosticServices` 하 고 enter 키를 누릅니다**.
 다음과 같이 종료 됩니다. `https://<kudu-url>/DiagnosticServices`

아래와 유사한 상태 페이지가 표시 됩니다. ![ 진단 서비스 상태 페이지](./media/diagnostic-services-site-extension/status-page.png)
    
### <a name="manual-installation"></a>수동 설치

Profiler를 구성하면 웹앱의 설정에 업데이트가 이루어집니다. 사용 중인 환경에 필요한 경우 수동으로 업데이트를 적용할 수 있습니다. 예제에서 애플리케이션이 PowerApps의 Web Apps 환경에서 실행될 수 있습니다. 수동으로 업데이트를 적용 하려면:

1. **웹 앱 제어** 창에서 **설정** 을 엽니다.

1. **.NET Framework 버전** 을 **v 4.6** 으로 설정 합니다.

1. **무중단** 을 **사용** 으로 설정합니다.
1. 다음 앱 설정을 만듭니다.

    |앱 설정    | 값    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | Application Insights 리소스용 iKey    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~3 |

### <a name="too-many-active-profiling-sessions"></a>너무 많은 활성 프로파일링 세션

동일한 서비스 계획에서 실행 되는 최대 4 개의 Web Apps에서 프로파일러를 사용 하도록 설정할 수 있습니다. 4 개 이상이 면 프로파일러가 *TooManyETWSessionException* 을 throw 할 수 있습니다. 이를 해결 하려면 일부 웹 앱을 다른 서비스 계획으로 이동 합니다.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootapp_datajobs"></a>배포 오류: 디렉터리가 비어 있지 않음 'D:\\home\\site\\wwwroot\\App_Data\\jobs'

Profiler를 사용하는 Web Apps 리소스에 웹앱을 다시 배포하는 경우 다음과 같은 메시지가 표시될 수 있습니다.

*디렉터리가 비어 있지 않음 ' d: \\ home \\ site \\ wwwroot \\ App_Data \\ job '*

스크립트 또는 Azure Pipelines에서 웹 배포를 실행 하는 경우이 오류가 발생 합니다. 해결 방법은 웹 배포 작업에 다음 배포 매개 변수를 추가 하는 것입니다.

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

이러한 매개 변수는 Application Insights Profiler에서 사용하는 폴더를 삭제하고 재배포 프로세스의 차단을 해제합니다. 현재 실행 중인 Profiler 인스턴스에는 영향을 주지 않습니다.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Application Insights Profiler가 실행되고 있는지 어떻게 알 수 있나요?

Profiler는 웹앱에서 지속적인 웹 작업으로 실행됩니다. [Azure Portal](https://portal.azure.com)에서 웹 앱 리소스를 열 수 있습니다. **WebJobs** 창에서 **ApplicationInsightsProfiler** 의 상태를 확인합니다. 실행되지 않는 경우 **로그** 를 열어 자세한 정보를 찾습니다.

## <a name="troubleshoot-vms-and-cloud-services"></a>Vm 및 Cloud Services 문제 해결

>**Cloud Services에 대 한 WAD에서 제공 되는 프로파일러의 버그가 수정 되었습니다.** Cloud Services에 대 한 최신 버전의 WAD (1.12.2.0)는 최신 버전의 App Insights SDK와 함께 작동 합니다. 클라우드 서비스 호스트는 WAD를 자동으로 업그레이드 하지만 즉시 실행 되지 않습니다. 강제로 업그레이드 하려면 서비스를 다시 배포 하거나 노드를 다시 부팅 하면 됩니다.

Azure 진단에서 프로파일러가 올바르게 구성 되었는지 확인 하려면 다음 단계를 수행 합니다. 
1. 배포 된 Azure 진단 구성 내용이 필요한 지 확인 합니다. 

1. 다음으로 Azure Diagnostics가 Profiler 명령줄에서 적절한 iKey를 전달하는지 확인합니다. 

1. 셋째로는 Profiler 로그 파일에서 Profiler가 실행되었으나 오류가 반환되었는지 여부를 확인합니다. 

Azure Diagnostics를 구성하는 데 사용된 설정을 확인하려면 다음 단계를 수행합니다.

1. VM (가상 컴퓨터)에 로그인 한 다음이 위치에서 로그 파일을 엽니다. 컴퓨터에서 플러그 인 버전이 최신 버전이 될 수 있습니다.
    
    Vm의 경우:
    ```
    c:\WindowsAzure\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log
    ```
    
    Cloud Services의 경우:
    ```
    c:\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log  
    ```

1. 해당 파일에서 **WadCfg** 문자열을 검색하면 Azure Diagnostics를 구성하기 위해 VM에 전달된 설정을 확인할 수 있습니다. Profiler 싱크에서 사용하는 iKey가 올바른지 확인할 수 있습니다.

1. Profiler를 시작하는 데 사용되는 명령줄을 확인합니다. 프로파일러를 시작 하는 데 사용 되는 인수는 다음 파일에 있습니다. 드라이브는 c: 또는 d: 이며 디렉터리를 숨길 수도 있습니다.

    Vm의 경우:
    ```
    C:\ProgramData\ApplicationInsightsProfiler\config.json
    ```
    
    Cloud Services:
    ```
    D:\ProgramData\ApplicationInsightsProfiler\config.json
    ```

1. Profiler 명령줄의 ikey가 올바른지 확인합니다. 

1. 파일 *의 이전config.js* 에 있는 경로를 사용 하 여 **BootstrapN** 라는 프로파일러 로그 파일을 확인 합니다. 이 파일에는 Profiler가 사용하는 설정을 나타내는 디버그 정보가 표시됩니다. 또한 Profiler의 상태 및 오류 메시지도 표시됩니다.  

    Vm의 경우 파일은 다음과 같습니다.
    ```
    C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.17.0.6\ApplicationInsightsProfiler
    ```

    Cloud Services의 경우:
    ```
    C:\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.17.0.6\ApplicationInsightsProfiler
    ```

    응용 프로그램에서 요청을 수신 하는 동안 프로파일러가 실행 중인 경우 *iKey에서 검색 된 작업* 인 다음 메시지가 표시 됩니다. 

    추적을 업로드 하는 동안 다음 메시지가 표시 됩니다. *추적 업로드를 시작* 합니다. 


## <a name="edit-network-proxy-or-firewall-rules"></a>네트워크 프록시 또는 방화벽 규칙 편집

응용 프로그램이 프록시 또는 방화벽을 통해 인터넷에 연결 하는 경우 프로파일러 서비스와 통신 하도록 규칙을 업데이트 해야 할 수 있습니다.

Application Insights Profiler에서 사용 하는 Ip는 Azure Monitor 서비스 태그에 포함 됩니다. 자세한 내용은 [서비스 태그 설명서](../../virtual-network/service-tags-overview.md)를 참조 하세요.


[profiler-search-telemetry]:./media/profiler-troubleshooting/Profiler-Search-Telemetry.png
[profiler-webjob]:./media/profiler-troubleshooting/Profiler-webjob.png
[profiler-webjob-log]:./media/profiler-troubleshooting/Profiler-webjob-log.png