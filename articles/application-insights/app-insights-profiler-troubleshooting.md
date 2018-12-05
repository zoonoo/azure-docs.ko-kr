---
title: Azure Application Insights Profiler 관련 문제 해결 | Microsoft Docs
description: 이 페이지에는 Application Insights Profiler를 사용하도록 설정하거나 사용하는 데 문제가 있는 개발자에게 도움을 주기 위한 문제 해결 단계 및 정보가 나와 있습니다.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: fa6e70fe58e5066fcf308425a4c0d104c072a756
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52164306"
---
# <a name="troubleshoot-problems-enabling-or-viewing-application-insights-profiler"></a>Application Insights Profiler를 사용하도록 설정하거나 볼 때 발생하는 문제 해결

## <a id="troubleshooting"></a>일반적인 문제 해결

### <a name="profiles-are-only-uploaded-if-there-are-requests-to-your-application-while-the-profiler-is-running"></a>Profiler가 실행되는 동안 응용 프로그램에 대한 요청이 있을 때만 업로드됨
Application Insights Profiler는 매시간 2분 동안 또는 **Application Insights Profiler 구성** 페이지에서 [**지금 프로파일링**](app-insights-profiler-settings.md?toc=/azure/azure-monitor/toc.json) 단추를 누른 경우에만 Profiler 데이터를 수집합니다. 하지만 프로파일링 데이터는 Profiler가 실행되는 동안 발생한 요청에 첨부할 수 있을 때만 업로드됩니다. 

Profiler는 Application Insights 리소스에 추적 메시지 및 사용자 지정 이벤트를 씁니다. 해당 이벤트를 사용하여 Profiler가 실행되는 방법을 확인할 수 있습니다. Profiler가 실행되고 있으며 추적을 캡처하고 있다고 생각되지만 성능 페이지에 표시되지 않으면 Profiler가 실행되는 방식을 확인할 수 있습니다.

1. Profiler가 Application Insights 리소스에 보낸 추적 메시지 및 사용자 지정 이벤트를 검색합니다. 이러한 검색 문자열을 사용하여 관련 데이터를 찾을 수 있습니다.

    ```
    stopprofiler OR startprofiler OR upload OR ServiceProfilerSample
    ```
    아래 스크린샷에는 두 개의 다른 AI 리소스를 사용한 두 가지 검색 예제가 나와 있습니다. 
    
    * 왼쪽 예제는 Profiler를 실행하는 동안 요청을 가져오지 않는 응용 프로그램에 대한 것입니다. 활동이 없으므로 업로드가 취소되었다는 메시지를 볼 수 있습니다. 

    * 오른쪽 예제에서는 Profiler가 실행되는 동안 발생한 요청을 검색했을 때 Profiler가 사용자 지정 이벤트를 시작하고 전송했다는 것을 확인할 수 있습니다. ServiceProfilerSample 사용자 지정 이벤트가 표시되지 않는 경우 Profiler가 요청에 추적을 첨부한 것이며, Application Insights 성능 페이지에서 추적을 볼 수 있습니다.

    * 원격 분석이 전혀 표시되지 않으면 Profiler가 실행되고 있지 않은 것입니다. 아래에서 이 문서의 특정 앱 형식에 대한 문제 해결 섹션을 참조해야 할 수도 있습니다.  

     ![Profiler 원격 분석 검색][profiler-search-telemetry]

1. Profiler가 실행되는 기간 동안 요청이 있는 경우 Profiler가 사용하도록 설정된 응용 프로그램의 부분에서 요청이 처리되는지 확인합니다. 경우에 따라 응용 프로그램이 여러 구성 요소로 이루어져 있지만 Profiler가 전체가 아닌 일부 구성 요소에 대해서만 사용하도록 설정되어 있을 수 있습니다. Application Insights Profiler 구성 페이지에 추적이 업로드된 구성 요소가 표시됩니다.

### <a name="other-things-to-check"></a>기타 확인해야 할 사항:
* 앱이 .NET Framework 4.6에서 실행되고 있는지 확인합니다.
* 웹앱이 ASP.NET Core 응용 프로그램인 경우 ASP.NET Core 2.0 이상을 실행해야 합니다.
* 보려고 하는 데이터가 몇 주보다 오래된 경우 시간 필터를 제한하고 다시 시도합니다. 추적은 7일 후에 삭제됩니다.
* 프록시 또는 방화벽이 https://gateway.azureserviceprofiler.net에 대한 액세스를 차단하지 않도록 합니다.

### <a id="double-counting"></a>병렬 스레드에서 이중 계산

경우에 따라 스택 뷰어의 총 시간 메트릭은 요청 기간 이상입니다.

두 개 이상의 스레드가 요청과 연결되고 병렬로 적용되는 경우 이 상황이 발생할 수 있습니다. 이 경우 총 스레드 시간은 경과된 시간 이상입니다. 하나의 스레드가 다른 스레드가 완료될 때까지 대기 중일 수 있습니다. 뷰어는 이러한 상황을 감지하고 필요하지 않은 대기를 생략하려고 시도하지만 중요한 정보일 수 있는 것을 생략하는 대신 너무 많은 정보를 표시하는 측면의 오류가 있습니다.

추적에 병렬 스레드가 표시되면 요청에 중요 경로를 결정할 수 있도록 대기 중인 스레드를 결정합니다. 대부분의 경우 대기 상태로 빠르게 전환하는 스레드는 단순히 다른 스레드를 기다리고 있습니다. 다른 스레드에 집중하고 대기 중인 스레드 대기 시간을 무시합니다.

### <a name="error-report-in-the-profile-viewer"></a>프로파일링 뷰어의 오류 보고서
포털에서 지원 티켓을 제출합니다. 오류 메시지의 상관 관계 ID를 포함해야 합니다.

## <a name="troubleshooting-profiler-on-app-services"></a>App Service에서 Profiler 문제 해결
### <a name="for-the-profiler-to-work-properly"></a>프로파일러가 제대로 작동하려면:
* 웹앱 서비스 계획이 기본 계층 이상이어야 합니다.
* 웹앱에서 Application Insights를 사용할 수 있어야 합니다.
* 웹앱에 Application Insights SDK에서 사용하는 동일한 계측 키를 사용하여 구성된 **APPINSIGHTS_INSTRUMENTATIONKEY** 설정이 있어야 합니다.
* 웹앱에 **APPINSIGHTS_PROFILERFEATURE_VERSION** 앱 설정이 정의되어 있고 1.0.0으로 설정되어 있어야 합니다.
* 웹앱에 **DiagnosticServices_EXTENSION_VERSION** 앱 설정이 정의되어 있어야 하며 값을 ~3으로 설정해야 합니다.
* **ApplicationInsightsProfiler3** 웹 작업이 실행되고 있어야 합니다. [Kudu](https://blogs.msdn.microsoft.com/cdndevs/2015/04/01/the-kudu-debug-console-azure-websites-best-kept-secret/)로 이동한 후 도구 메뉴에서 **WebJobs 대시보드**를 열어 웹 작업을 확인할 수 있습니다. 아래 스크린샷에서 볼 수 있는 것처럼 ApplicationInsightsProfiler2 링크를 클릭하면 로그를 포함하여 웹 작업에 대한 세부 정보를 볼 수 있습니다.

    웹 작업 세부 정보를 보기 위해 클릭해야 하는 링크는 다음과 같습니다. 

    ![profiler-webjob]

    세부 정보를 표시하는 페이지는 다음과 같습니다. Profiler가 작동하지 않는 이유를 파악할 수 없는 경우 로그를 다운로드한 후 담당 팀으로 보낼 수 있습니다.
    
    ![profiler-webjob-log]

### <a name="manual-installation"></a>수동 설치

Profiler를 구성하면 웹앱의 설정에 업데이트가 이루어집니다. 사용자 환경에 필요한 경우 수동으로 업데이트를 적용할 수 있습니다. 예제에서 응용 프로그램이 PowerApps의 Web Apps 환경에서 실행될 수 있습니다.

1. **Web App 제어** 창에서 **설정**을 엽니다.
1. **.NET Framework 버전**을 **v4.6**으로 설정합니다.
1. **무중단**을 **사용**으로 설정합니다.
1. **APPINSIGHTS_INSTRUMENTATIONKEY** 앱 설정을 추가하고 값을 SDK에서 사용한 동일한 계측 키로 설정합니다.
1. **APPINSIGHTS_PROFILERFEATURE_VERSION** 앱 설정을 추가하고 해당 값을 1.0.0으로 설정합니다.
1. **DiagnosticServices_EXTENSION_VERSION** 앱 설정을 추가하고, 값을 ~3으로 설정합니다.

### <a name="too-many-active-profiling-sessions"></a>너무 많은 활성 프로파일링 세션

현재 동일한 서비스 계획으로 실행 중인 최대 4개의 Azure 웹앱 및 배포 슬롯에서 Profiler를 사용하도록 설정할 수 있습니다. 하나의 앱 서비스 계획에서 실행되는 것보다 더 많은 웹앱이 있는 경우 프로파일러에 의해 throw되는 Microsoft.ServiceProfiler.Exceptions.TooManyETWSessionException이 표시될 수 있습니다. 프로파일러는 각 웹앱에 대해 개별적으로 실행되고 각 앱에 대한 ETW 세션을 시작하려고 합니다. 그러나 한 번에 활성화될 수 있는 ETW 세션 수에 제한이 있습니다. Profiler 웹 작업이 너무 많은 활성 프로파일링 세션을 보고하는 경우 일부 웹앱을 다른 서비스 계획으로 이동합니다.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootappdatajobs"></a>배포 오류: 디렉터리가 비어 있지 않음 'D:\\home\\site\\wwwroot\\App_Data\\jobs'

Profiler를 사용하는 Web Apps 리소스에 웹앱을 다시 배포하는 경우 다음과 같은 메시지가 표시될 수 있습니다.

*디렉터리가 비어 있지 않음 'D:\\home\\site\\wwwroot\\App_Data\\jobs'*

스크립트 또는 Azure DevOps 배포 파이프라인에서 웹 배포를 실행하는 경우 이 오류가 발생합니다. 솔루션은 웹 배포 작업에 다음과 같은 배포 매개 변수를 더 추가합니다.

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

이러한 매개 변수는 Application Insights Profiler에서 사용하는 폴더를 삭제하고 재배포 프로세스의 차단을 해제합니다. 현재 실행 중인 Profiler 인스턴스에는 영향을 주지 않습니다.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Application Insights Profiler가 실행되고 있는지 어떻게 알 수 있나요?

Profiler는 웹앱에서 지속형 웹 작업으로 실행됩니다. [Azure Portal](https://portal.azure.com)에서 웹앱 리소스를 열 수 있습니다. **WebJobs** 창에서 **ApplicationInsightsProfiler**의 상태를 확인합니다. 실행되지 않는 경우 **로그**를 열어 자세한 정보를 찾습니다.

## <a name="troubleshooting-problems-with-profiler-and-wad"></a>Profiler 및 WAD 문제 해결

WAD에서 Profiler가 올바르게 구성되어 있는지 확인하기 위해 검토할 세 가지 사항이 있습니다. 먼저 배포된 WAD 구성의 내용이 예상과 일치하는지 확인해야 합니다. 둘째, WAD가 Profiler 명령줄에 적절한 iKey 전달하는지 확인해야 합니다. 셋째, Profiler 로그 파일을 확인하여 Profiler가 실행되지만 오류가 발생하고 있는지를 알아볼 수 있습니다. 

WAD를 구성하는 데 사용한 설정을 확인하려면 VM에 로그인하고 다음 위치에서 로그 파일을 열어야 합니다. 
```
c:\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.logs  
```
해당 파일에서 "WadCfg" 문자열을 검색하면 WAD를 구성하기 위해 VM에 전달된 설정을 확인할 수 있습니다. Profiler 싱크에서 사용하는 iKey가 올바른지 확인할 수 있습니다.

둘째, Profiler를 시작하는 데 사용되는 명령줄을 확인할 수 있습니다. 다음 파일에는 Profiler를 시작하는 데 사용되는 인수가 포함되어 있습니다.
```
D:\ProgramData\ApplicationInsightsProfiler\config.json
```
Profiler 명령줄의 ikey가 올바른지 확인합니다. 

셋째, 위의 config.json 파일에 있는 경로를 사용하여, Profiler 로그 파일을 확인합니다. 이 로그 파일은 Profiler가 사용 중인 설정과 Profiler의 상태 및 오류 메시지를 나타내는 디버그 정보를 표시합니다. 응용 프로그램이 요청을 수신하는 동안 Profiler가 실행되는 경우 “iKey에서 활동이 검색되었습니다.” 메시지가 표시됩니다. 추적을 업로드하는 동안 “추적 업로드를 시작합니다.” 메시지가 표시됩니다. 


[profiler-search-telemetry]:./media/app-insights-profiler/Profiler-Search-Telemetry.png
[profiler-webjob]:./media/app-insights-profiler/Profiler-webjob.png
[profiler-webjob-log]:./media/app-insights-profiler/Profiler-webjob-log.png








