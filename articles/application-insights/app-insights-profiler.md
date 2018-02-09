---
title: "Application Insights Profiler를 사용하여 Azure에서 라이브 웹앱 프로파일링 | Microsoft Docs"
description: "적은 공간의 프로파일러를 사용하여 웹 서버 코드에서 실행 부하 과다 경로를 식별합니다."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: mbullwin
ms.openlocfilehash: 5f691fb88c6764309bf012dfc65b561ec87afede
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/25/2018
---
# <a name="profile-live-azure-web-apps-with-application-insights"></a>Application Insights를 사용하여 라이브 Azure Web Apps 프로파일링

*Application Insights의 이 기능은 일반적으로 Azure App Service에서 사용할 수 있으며 Azure 계산 리소스에 대한 미리 보기에 있습니다.*

[Application Insights](app-insights-overview.md)를 사용할 때 라이브 웹 응용 프로그램의 각 메서드에서 얼마나 많은 시간이 소요되는지 알아봅니다. Application Insights 프로파일링 도구는 앱에서 제공한 실시간 요청의 세부 프로필을 표시하고 가장 많은 시간을 사용하는 *실행 부하 과다 경로*를 강조 표시합니다. 응답 시간이 다른 요청은 샘플링 기준으로 프로파일링됩니다. 응용 프로그램에 대한 오버헤드는 다양한 기술을 사용하여 최소화됩니다.

프로파일러는 현재 **Basic** 이상 서비스 계층의 Azure App Service에서 실행 중인 ASP.NET 및 ASP.NET Core 웹앱에서 작동합니다.

## <a id="installation"></a> App Services 웹앱에 대한 프로파일러 사용
응용 프로그램을 App Services에 게시했지만 Application Insights를 사용하도록 소스 코드를 변경하지 경우, Azure Portal의 App Services로 이동하고, **모니터링 | Application Insights**로 이동하여 창의 지침에 따라 웹앱을 모니터링할 Application Insights 리소스를 새로 만들거나 기존 Application Insights 리소스를 선택합니다. Profiler는 **기본** App Services 계획 이상에서만 작동합니다.

![App Services 포털에서 App Insights를 사용하도록 설정][appinsights-in-appservices]

프로젝트 소스 코드에 액세스할 수 있으면 [Application Insights를 설치](app-insights-asp-net.md)합니다. 이미 설치된 경우 최신 버전인지 확인합니다. 최신 버전을 확인하려면 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리** > **업데이트** > **모든 패키지 업데이트**를 선택합니다. 그런 다음, 앱을 배포합니다.

ASP.NET Core 응용 프로그램을 사용하려면 Microsoft.ApplicationInsights.AspNetCore Nuget 패키지 2.1.0-beta6 이상을 설치하여 프로파일러와 작동하도록 해야 합니다. 2017년 6월 27일부터 이전 버전은 지원되지 않습니다.

[Azure Portal](https://portal.azure.com)에서 웹앱에 대한 Application Insights 리소스를 엽니다. **성능** > **Application Insights Profiler 사용**을 선택합니다.

![프로파일러 사용 배너 선택][enable-profiler-banner]

또는 **Profiler** 구성을 선택하여 상태를 보고 프로파일러를 사용하거나 사용하지 않도록 설정할 수 있습니다.

![성능에서 Profiler 구성 선택][performance-blade]

Application Insights로 구성된 웹앱은 **Profiler** 구성 창에 나열됩니다. 위의 단계를 수행한 경우 프로파일러 에이전트가 이미 설치되어 있습니다. **Profiler** 구성 창에서 **프로파일러 사용**을 선택합니다.

필요한 경우 지침에 따라 프로파일러 에이전트를 설치합니다. Application Insights로 구성된 웹앱이 없는 경우 **연결된 앱 추가**를 선택합니다.

![구성 창 옵션][linked app services]

App Service 계획을 통해 호스트되는 웹앱과 달리 Azure 계산 리소스(예: Azure Virtual Machines, 가상 머신 크기 집합, Azure Service Fabric 또는 Azure Cloud Services)에서 호스트되는 응용 프로그램은 Azure에서 직접 관리되지 않습니다. 이 경우 연결할 웹앱이 없습니다. 앱에 연결하는 대신 **Profiler 사용** 단추를 선택합니다.

### <a name="enable-the-profiler-for-azure-compute-resources-preview"></a>Azure 계산 리소스에 프로파일러를 사용하도록 설정(미리 보기)

자세한 내용은 [Azure 계산 리소스에 대한 미리 보기 버전의 프로파일러](https://go.microsoft.com/fwlink/?linkid=848155)를 참조하세요.

## <a name="view-profiler-data"></a>프로파일러 데이터 보기

**응용 프로그램이 트래픽을 수신하는지 확인합니다.** 실험을 수행하는 경우 [Application Insights 성능 테스트](https://docs.microsoft.com/en-us/vsts/load-test/app-service-web-app-performance-test)를 사용하여 웹앱에 요청을 생성할 수 있습니다. Profiler를 새로 활성화한 경우 약 15분 동안 짧은 로드 테스트를 실행하면 프로파일러 추적을 얻을 수 있습니다. Profiler를 사용하도록 이미 설정되어 있는 경우에는 Profiler는 매시간 2번 임의로 실행되며 실행될 때마다 2분간 실행되는 점에 유의합니다. 샘플 프로파일러 추적을 얻을 수 있는지 확인하려면 1시간 동안 부하 테스트를 실행합니다.

응용 프로그램에서 트래픽을 수신하면 **성능** 블레이드로 이동하고 페이지의 **작업 수행** 섹션으로 이동하여 프로파일러 추적을 봅니다. **프로파일러 추적** 단추를 선택합니다.

![Application Insights 성능 창 미리 보기 프로파일러 추적][performance-blade-v2-examples]

샘플을 선택하여 요청을 실행하는 데 걸린 시간의 코드 수준 분석을 표시합니다.

![Application Insights 추적 탐색기][trace-explorer]

추적 탐색기에서 다음 정보를 보여 줍니다.

* **실행 부하 과다 경로 표시**: 가장 큰 리프 노드 또는 닫힌 것을 엽니다. 대부분의 경우에서 이 노드는 성능 병목에 인접한 상태가 됩니다.
* **레이블**: 함수 또는 이벤트의 이름입니다. 트리는 발생한 코드와 이벤트의 혼합을 보여 줍니다(예: SQL 및 HTTP 이벤트). 최상위 이벤트는 전체 요청 기간을 나타냅니다.
* **경과 시간**: 작업의 시작과 끝 사이의 시간 간격입니다.
* **시기**: 함수 또는 이벤트가 다른 함수와 관련해서 실행된 시기입니디.

## <a name="how-to-read-performance-data"></a>성능 데이터를 읽는 방법

Microsoft 서비스 프로파일러는 샘플링 메서드와 계측의 조합을 사용하여 응용 프로그램의 성능을 분석합니다. 자세한 컬렉션이 진행 중인 경우 서비스 프로파일러는 1밀리초마다 각 컴퓨터 CPU의 명령 포인터를 샘플링합니다. 각 샘플은 현재 실행 중인 스레드의 전체 호출 스택을 캡처합니다. 높은 추상화 수준과 낮은 추상화 수준 모두에서 해당 스레드가 수행한 작업에 대한 상세하고 유용한 정보를 제공합니다. 서비스 프로파일러는 또한 컨텍스트 스위칭 이벤트, TPL(작업 병렬 라이브러리) 이벤트 및 스레드 풀 이벤트와 같은 다른 이벤트를 수집하여 활동 상관 관계 및 인과 관계를 추적합니다.

타임라인 보기에 표시된 호출 스택은 샘플링 및 계측의 결과입니다. 각 샘플은 스레드의 전체 호출 스택을 캡처하므로 Microsoft .NET Framework의 코드뿐만 아니라 참조하는 다른 프레임워크의 코드를 포함합니다.

### <a id="jitnewobj"></a>개체 할당(clr!JIT\_New 또는 clr!JIT\_Newarr1)
**clr!JIT\_New** 및 **clr!JIT\_Newarr1**은 관리되는 힙에서 메모리를 할당하는 .NET Framework의 도우미 함수입니다. 개체가 할당되면 **clr!JIT\_New**가 호출됩니다. **clr!JIT\_Newarr1**은 개체 배열이 할당될 때 호출됩니다. 이 두 함수는 일반적으로 빠르며 상대적으로 시간이 적게 걸립니다. **clr!JIT\_New** 또는 **clr!JIT\_Newarr1**이 타임라인에서 상당한 양의 시간을 사용하는 경우 코드에 많은 개체가 할당되고 많은 양의 메모리가 소비될 수 있음을 나타냅니다.

### <a id="theprestub"></a>코드 로드(clr!ThePreStub)
**clr!ThePreStub**은 처음으로 실행할 코드를 준비하는 .NET Framework 내부의 도우미 함수입니다. 일반적으로 JIT(Just-In-Time) 컴파일을 포함하지만 이에 제한되지 않습니다. 각 C# 메서드의 경우 **clr!ThePreStub**은 프로세스의 수명 동안 한 번만 호출되어야 합니다.

**clr!ThePreStub**에 대해 상당한 양의 요청 시간이 사용되는 경우 요청이 해당 메서드를 실행하는 첫 번째 항목임을 나타냅니다. .NET Framework 런타임에서 해당 메서드를 로드하는 데 많은 시간이 걸립니다. 사용자가 코드에 액세스하기 전에 해당 부분을 실행하는 준비 프로세스를 사용하거나 어셈블리에서 네이티브 이미지 생성기(ngen.exe) 실행을 고려할 수 있습니다.

### <a id="lockcontention"></a>잠금 경합(clr!JITutil\_MonContention 또는 clr!JITutil\_MonEnterWorker)
**clr!JITutil\_MonContention** 또는 **clr!JITutil\_MonEnterWorker**는 현재 스레드가 잠금 해제를 기다리고 있음을 나타냅니다. 이는 일반적으로 C# **LOCK** 문을 실행하거나 **Monitor.Enter** 메서드를 호출하거나 **MethodImplOptions.Synchronized** 특성으로 메서드를 호출할 때 표시됩니다. 잠금 경합은 일반적으로 스레드 A가 잠금을 획득하고 스레드 B가 스레드 A가 잠금을 해제하기 전에 동일한 잠금을 획득하려고 하는 경우에 발생합니다.

### <a id="ngencold"></a>코드 로드([COLD])
메서드 이름에 **mscorlib.ni![COLD]System.Reflection.CustomAttribute.IsDefined**와 같이 **[COLD]**가 포함되어 있으면 .NET Framework 런타임에서 처음으로 <a href="https://msdn.microsoft.com/library/e7k32f4k.aspx">프로필 기반 최적화</a>에 의해 최적화되지 않은 코드를 실행하고 있는 것입니다. 각 메서드의 경우 프로세스의 수명 동안 한 번만 나타나야 합니다.

코드 로드에 요청에 대해 상당한 양의 시간이 사용되는 경우 요청이 메서드의 최적화되지 않은 부분을 실행하는 첫 번째 항목임을 나타냅니다. 사용자가 액세스하기 전에 코드의 해당 부분을 실행하는 준비 프로세스를 고려할 수 있습니다.

### <a id="httpclientsend"></a>HTTP 요청 보내기
**HttpClient.Send**와 같은 메서드는 코드가 HTTP 요청이 완료되기를 기다리고 있음을 나타냅니다.

### <a id="sqlcommand"></a>데이터베이스 작업
**SqlCommand.Execute**와 같은 메서드는 코드가 데이터베이스 작업이 완료되기를 기다리고 있음을 나타냅니다.

### <a id="await"></a>대기 중(AWAIT\_TIME)
**AWAIT\_TIME**은 코드가 다른 작업이 완료되기를 기다리고 있음을 나타냅니다. 이는 일반적으로 C# **AWAIT** 문과 함께 발생합니다. 코드가 C# **AWAIT**를 수행하는 경우 스레드는 스레드 풀에 대한 컨트롤을 해제 및 반환하고 **AWAIT**가 끝나기를 기다리는 것이 차단된 스레드는 없습니다. 그러나 논리적으로 **AWAIT**를 수행한 스레드는 작업이 완료되길 기다리는 것이 "차단됩니다". **AWAIT\_TIME** 문은 작업이 완료되기를 기다리는 차단된 시간을 나타냅니다.

### <a id="block"></a>차단된 시간
**BLOCKED_TIME**은 코드가 다른 리소스를 사용할 수 있을 때까지 기다리고 있음을 나타냅니다. 예를 들어, 동기화 개체를 기다리거나, 스레드를 사용할 수 있거나 요청이 완료될 때까지 기다릴 수 있습니다.

### <a id="cpu"></a>CPU 시간
CPU는 명령을 실행 중입니다.

### <a id="disk"></a>디스크 시간
응용 프로그램은 디스크 작업을 수행 중입니다.

### <a id="network"></a>네트워크 시간
응용 프로그램은 네트워크 작업을 수행 중입니다.

### <a id="when"></a>때 열
**시기** 열은 노드에 대해 수집된 INCLUSIVE 샘플이 시간에 따라 달라지는 방식의 시각화입니다. 요청의 전체 범위는 32시간 버킷으로 나뉩니다. 해당 노드에 대한 포괄적인 샘플은 이러한 32개의 버킷에 누적됩니다. 각 버킷은 막대로 표시됩니다. 막대의 높이는 크기 조정된 값을 나타냅니다. 리소스(CPU, 디스크, 스레드)를 사용하는 확실한 관계가 있는 **CPU_TIME** 또는 **BLOCKED_TIME**으로 표시된 노드의 경우 막대는 해당 버킷의 기간 동안 이러한 리소스 중 하나를 사용함을 나타냅니다. 이러한 메트릭의 경우 여러 리소스를 소비하여 100% 이상의 값을 얻을 수 있습니다. 예를 들어 간격 동안 평균적으로 두 개의 CPU를 사용하면 200%가 됩니다.

## <a name="limitations"></a>제한 사항

기본 데이터 보존 기간은 5일입니다. 하루에 수집되는 최대 데이터는 10GB입니다.

프로파일러 서비스 사용료는 청구되지 않습니다. 프로파일러 서비스를 사용하려면 웹앱이 적어도 App Service의 기본 계층에서 호스트되어야 합니다.

## <a name="overhead-and-sampling-algorithm"></a>오버헤드 및 샘플링 알고리즘

프로파일러는 프로파일러가 추적을 캡처하도록 설정된 응용 프로그램을 호스트하는 각 가상 머신에서 시간당 2분씩 임의로 실행됩니다. 프로파일러는 실행되는 동안 서버에 CPU 오버헤드를 5-15% 가중시킵니다.
응용 프로그램을 호스트하는 데 사용할 수 있는 서버가 많을수록 프로파일러가 전체 응용 프로그램 성능에 주는 영향은 감소합니다. 이는 샘플링 알고리즘으로 인해 프로파일러가 언제든지 서버의 5%에서만 실행되기 때문입니다. 프로파일러 실행으로 인한 서버 오버헤드를 상쇄하기 위해 더 많은 서버를 사용하여 웹 요청을 처리할 수 있습니다.

## <a name="disable-the-profiler"></a>프로파일러 사용 안 함
개별 App Service 인스턴스에 대해 프로파일러를 중지하거나 다시 시작하려면 **웹 작업**에서 App Service 리소스로 이동합니다. 프로파일러를 삭제하려면 **확장**으로 이동합니다.

![웹 작업에 대한 프로파일러 사용 안 함][disable-profiler-webjob]

성능 문제를 가능한 한 빨리 검색하려면 모든 웹앱에서 프로파일러를 사용하도록 설정하는 것이 좋습니다.

WebDeploy를 사용하여 웹 응용 프로그램에 변경 내용을 배포하는 경우 배포하는 동안 App_Data 폴더가 삭제되는 것을 제외하도록 확인합니다. 그렇지 않으면 다음에 Azure에 웹 응용 프로그램을 배포할 때 프로파일러 확장의 파일이 삭제됩니다.


## <a id="troubleshooting"></a>문제 해결

### <a name="too-many-active-profiling-sessions"></a>너무 많은 활성 프로파일링 세션

현재 동일한 서비스 계획으로 실행 중인 최대 4개의 Azure 웹앱 및 배포 슬롯에서 프로파일러를 사용하도록 설정할 수 있습니다. 프로파일러 웹 작업이 너무 많은 활성 프로파일링 세션을 보고하는 경우 일부 웹앱을 다른 서비스 계획으로 이동합니다.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Application Insights Profiler가 실행되고 있는지 어떻게 알 수 있나요?

프로파일러는 웹앱에서 지속형 웹 작업으로 실행됩니다. [Azure Portal](https://portal.azure.com)에서 웹앱 리소스를 열 수 있습니다. **WebJobs** 창에서 **ApplicationInsightsProfiler**의 상태를 확인합니다. 실행되지 않는 경우 **로그**를 열어 자세한 정보를 찾습니다.

### <a name="why-cant-i-find-any-stack-examples-even-though-the-profiler-is-running"></a>프로파일러가 실행 중인데도 스택 예제를 찾을 수 없는 이유는 무엇인가요?

다음 몇 가지 사항으로 확인할 수 있습니다.

* 웹앱 서비스 계획이 기본 계층 이상인지 확인합니다.
* 웹앱에서 Application Insights SDK 2.2 베타 이상이 사용하도록 설정되었는지 확인합니다.
* 웹앱에 Application Insights SDK에서 사용하는 동일한 계측 키를 사용하여 구성된 **APPINSIGHTS_INSTRUMENTATIONKEY** 설정이 있는지 확인합니다.
* 웹앱이 .NET Framework 4.6에서 실행 중인지 확인합니다.
* 웹앱이 ASP.NET Core 응용 프로그램인 경우 [필수 종속성](#aspnetcore)을 확인합니다.

프로파일러가 시작된 후 프로파일러가 여러 성능 추적을 적극적으로 수집하는 동안 짧은 준비 기간이 있습니다. 그런 다음 프로파일러는 매 시간 2분 동안 성능 추적을 수집합니다.  

### <a name="i-was-using-azure-service-profiler-what-happened-to-it"></a>Azure Service Profiler를 사용하고 있었습니다. 어떻게 된 건가요?  

Application Insights Profiler를 사용하도록 설정하면 Azure Service Profiler 에이전트는 사용하지 않도록 설정됩니다.

### <a id="double-counting"></a>병렬 스레드에서 이중 계산

경우에 따라 스택 뷰어의 총 시간 메트릭은 요청 기간 이상입니다.

병렬로 작업하는 두 개 이상의 스레드가 요청과 연결된 경우 발생할 수 있습니다. 이 경우 총 스레드 시간은 경과된 시간 이상입니다. 하나의 스레드가 다른 스레드가 완료될 때까지 대기 중일 수 있습니다. 뷰어는 이를 감지하고 필요하지 않은 대기를 생략하려고 시도하지만 중요한 정보일 수 있는 것을 생략하는 대신 너무 많은 것을 보여 주는 측면의 오류가 있습니다.  

추적에 병렬 스레드가 표시되면 요청에 중요 경로를 결정할 수 있도록 대기 중인 스레드를 결정합니다. 대부분의 경우 대기 상태로 빠르게 전환하는 스레드는 단순히 다른 스레드를 기다리고 있습니다. 다른 스레드에 집중하고 대기 중인 스레드 대기 시간을 무시합니다.

### <a id="issue-loading-trace-in-viewer"></a>프로파일링 데이터 없음

다음 몇 가지 사항으로 확인할 수 있습니다.

* 보려고 하는 데이터가 몇 주보다 오래된 경우 시간 필터를 제한하고 다시 시도합니다.
* 프록시 또는 방화벽이 https://gateway.azureserviceprofiler.net에 대한 액세스를 차단하지 않았는지 확인합니다.
* 앱에서 사용하는 Application Insights 계측 키가 프로파일링을 사용하도록 설정하는 데 사용한 Application Insights 리소스와 동일한지 확인합니다. 키는 일반적으로 ApplicationInsights.config에 있지만, web.config 또는 app.config 파일에 있을 수도 있습니다.

### <a name="error-report-in-the-profiling-viewer"></a>프로파일링 뷰어의 오류 보고서

포털에서 지원 티켓을 제출합니다. 오류 메시지의 상관 관계 ID를 포함해야 합니다.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootappdatajobs"></a>배포 오류: 디렉터리가 비어 있지 않음 'D:\\home\\site\\wwwroot\\App_Data\\jobs'

프로파일러가 사용하도록 설정된 상태에서 App Service 리소스에 웹앱을 다시 배포하는 경우 다음과 같은 메시지가 표시될 수 있습니다.

디렉터리가 비어 있지 않음 'D:\\home\\site\\wwwroot\\App_Data\\jobs'

스크립트 또는 Visual Studio Team Services 배포 파이프라인에서 웹 배포를 실행하는 경우 이 오류가 발생합니다. 솔루션은 웹 배포 작업에 다음과 같은 배포 매개 변수를 더 추가합니다.

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

이러한 매개 변수는 Application Insights Profiler에서 사용하는 폴더를 삭제하고 재배포 프로세스의 차단을 해제합니다. 현재 실행 중인 프로파일러 인스턴스에는 영향을 주지 않습니다.


## <a name="manual-installation"></a>수동 설치

프로파일러를 구성하면 웹앱의 설정에 업데이트가 이루어집니다. 사용자 환경에 필요한 경우 수동으로 업데이트를 적용할 수 있습니다. 예를 들어 응용 프로그램이 PowerApps용 App Service Environment에서 실행되는 경우가 있습니다.

1. 웹앱 제어 창에서 **설정**을 엽니다.
2. **.NET Framework 버전**을 **v4.6**으로 설정합니다.
3. **무중단**을 **사용**으로 설정합니다.
4. __APPINSIGHTS_INSTRUMENTATIONKEY__ 앱 설정을 추가하고 값을 SDK에서 사용한 동일한 계측 키로 설정합니다.
5. **고급 도구**를 엽니다.
6. **이동**을 선택하여 Kudu 웹 사이트를 엽니다.
7. Kudu 웹 사이트에서 **사이트 확장**을 선택합니다.
8. Azure Web Apps 갤러리에서 __Application Insights__를 설치합니다.
9. 웹 앱을 다시 시작합니다.

## <a id="profileondemand"></a> 수동으로 프로파일러 트리거
프로파일러를 개발할 때 앱 서비스에서 프로파일러를 테스트할 수 있도록 명령줄 인터페이스를 추가했습니다. 이 동일한 인터페이스를 사용하여 사용자는 프로파일러를 시작하는 방법을 사용자 지정할 수도 있습니다. 높은 수준에서 프로파일러는 App Service의 Kudu 시스템을 사용하여 백그라운드에서 프로파일링을 관리합니다. Application Insights 확장을 설치할 때 프로파일러를 호스팅하는 연속 웹 작업을 만듭니다. 이 동일한 기술을 사용하여 필요에 맞게 사용자 지정할 수 있는 새 웹 작업을 만듭니다.

이 섹션에서는 다음을 수행하는 방법을 설명합니다.

1.  단추를 눌러 2분 동안 프로파일러를 시작할 수 있는 웹 작업을 만듭니다.
2.  프로파일러가 실행되도록 예약할 수 있는 웹 작업을 만듭니다.
3.  프로파일러에 대한 인수를 설정합니다.


### <a name="set-up"></a>설정
먼저 웹 작업의 대시보드에 익숙해 지도록 합니다. 설정 아래에서 WebJobs 탭을 클릭합니다.

![webjobs 블레이드](./media/app-insights-profiler/webjobs-blade.png)

보시다시피 이 대시보드는 현재 사이트에 설치된 모든 웹 작업을 보여 줍니다. 실행 중인 프로파일러 작업이 있는 ApplicationInsightsProfiler2 웹 작업을 볼 수 있습니다. 여기서는 수동 및 예약된 프로파일링에 대한 새 웹 작업을 만듭니다.

먼저 필요한 이진 파일을 가져오겠습니다.

1.  먼저 kudu 사이트로 이동합니다. 개발 도구 탭 아래에서 Kudu 로고가 있는 "고급 도구" 탭을 클릭합니다. "이동"을 클릭합니다. 그러면 새 사이트로 이동되고 자동으로 로그인됩니다.
2.  다음으로 프로파일러 이진 파일을 다운로드해야 합니다. 디버그 콘솔을 통해 파일 탐색기로 이동하고 페이지의 맨 위에 있는 CMD로 이동합니다.
3.  사이트 -> wwwroot -> App_Data -> 작업-> 연속을 클릭합니다. 폴더 "ApplicationInsightsProfiler2"가 표시되어야 합니다. 폴더의 왼쪽에 있는 다운로드 아이콘을 클릭합니다. "ApplicationInsightsProfiler2.zip" 파일을 다운로드합니다.
4.  앞으로 이동하는 데 필요한 모든 파일을 다운로드합니다. 계속하기 전에 이 zip 압축 파일을 이동할 새로운 디렉터리를 만드는 것이 좋습니다.

### <a name="setting-up-the-web-job-archive"></a>웹 작업 압축 파일 설정
Azure 웹 사이트에 새 웹 작업을 추가할 때 기본적으로 run.cmd로 내부에 zip 압축 파일을 만듭니다. run.cmd는 웹 작업을 실행할 때 수행할 작업을 웹 작업 시스템에 알려줍니다. 웹 작업 문서에서 읽을 수 있는 다른 옵션이 있지만 우리의 목적을 위해 다른 것은 필요하지 않습니다.

1.  새 폴더 만들기를 시작하기 위해 "RunProfiler2Minutes"라고 이름을 지정했습니다.
2.  추출한 ApplicationInsightProfiler2 폴더에서 이 새 폴더로 파일을 복사합니다.
3.  새 run.cmd 파일을 만듭니다. (편의상 시작하기 전에 VS Code에서 이 작업 폴더를 열었음)
4.  `ApplicationInsightsProfiler.exe start --engine-mode immediate --single --immediate-profiling-duration 120` 명령을 추가하고 파일을 저장합니다.
a.  `start` 명령은 프로파일러를 시작합니다.
나.  `--engine-mode immediate`는 프로파일링을 즉시 시작하고자 함을 프로파일러에 알립니다.
다.  `--single`은 d를 실행한 다음 자동으로 중지하는 것을 의미합니다.  `--immediate-profiling-duration 120`은 프로파일러를 120초 또는 2분 동안 실행하는 것을 의미합니다.
5.  이 파일을 저장합니다.
6.  이 폴더를 보관합니다. 폴더를 마우스 오른쪽 단추로 클릭하고 전송 대상 -> 압축(ZIP) 폴더를 선택합니다. 이렇게 하면 폴더의 이름을 사용하여 .zip 파일을 만듭니다.

![프로파일러 명령 시작](./media/app-insights-profiler/start-profiler-command.png)

이제 웹 작업 .zip이 있으며 이를 사용하여 사이트에서 웹 작업을 설정할 수 있습니다.

### <a name="add-a-new-web-job"></a>새 웹 작업 추가
다음으로 사이트에서 새 웹 작업을 추가합니다. 이 예제에서는 수동 트리거된 웹 작업을 추가하는 방법을 설명합니다. 이를 수행할 수 있으면 프로세스는 거의 예약된 것과 정확하게 동일합니다. 예약된 트리거된 작업에 대해 자세히 알아볼 수 있습니다.

1.  웹 작업 대시보드로 이동합니다.
2.  도구 모음에서 추가 명령을 클릭합니다.
3.  웹 작업에 이름을 지정합니다. 쉽게 구별할 수 있도록 내 보관 파일의 이름과 일치시키고 서로 다른 버전의 run.cmd를 갖기 위해 열도록 선택했습니다.
4.  양식의 파일 업로드 부분에서 파일 열기 아이콘을 클릭하고 위에서 만든 .zip 파일을 찾습니다.
5.  형식에 대해 트리거됨을 선택합니다.
6.  트리거에 대해 수동을 선택합니다.
7.  확인을 눌러 저장합니다.

![프로파일러 명령 시작](./media/app-insights-profiler/create-webjob.png)

### <a name="run-the-profiler"></a>프로파일러 실행

수동으로 트리거할 수 있는 새 웹 작업을 만들었으므로 이제 실행을 시도할 수 있습니다.

1.  의도적으로 지정된 시간에 컴퓨터에서 실행되는 하나의 ApplicationInsightsProfiler.exe 프로세스를 가질 수 있습니다. 따라서 시작하려면 이 대시보드에서 연속 웹 작업을 비활성화해야 합니다. 행을 클릭하고 "중지"를 누릅니다. 도구 모음에서 새로 고치고 상태가 작업이 중지된 것을 확인하는지 확인합니다.
2.  추가한 새 웹 작업이 있는 행을 클릭하고 실행을 누릅니다.
3.  행을 여전히 선택한 채로 도구 모음에서 로그 명령을 클릭하면, 시작한 이 웹 작업에 대한 웹 작업 대시보드로 이동합니다. 가장 최근의 실행 및 해당 결과를 나열합니다.
4.  방금 시작한 실행을 클릭합니다.
5.  순조롭게 진행되는 경우 프로파일링을 시작했음을 확인하는 프로파일러에서 오는 일부 진단 로그가 표시되어야 합니다.

### <a name="things-to-consider"></a>고려할 사항

이 메서드는 비교적 간단하지만 몇 가지 고려할 사항이 있습니다.

1.  이는 서비스에 의해 관리되지 않으므로 웹 작업에 대한 에이전트 이진 파일을 업데이트할 방법이 없습니다. 현재 이진 파일에 대한 안정적인 다운로드 페이지가 없으므로 최신 항목을 가져올 유일한 방법은 확장을 업데이트하고 위에서 수행한 것과 같이 연속 폴더에서 가져오는 것입니다.
2.  원래 최종 사용자가 아닌 개발자가 사용하도록 설계된 명령줄 인수를 활용하기 때문에 이 인수는 나중에 변경될 수 있으므로 업그레이드할 때 주의해야 합니다. 웹 작업을 추가하고 작동하는지 테스트할 수 있으므로 그렇게 큰 문제가 되지는 않습니다. 결국 수동 프로세스 없이 이 작업을 수행할 UI를 빌드할 예정이지만 고려해야 할 사항입니다.
3.  App Services에 대한 웹 작업 기능은 웹 작업을 실행할 때 프로세스가 웹 사이트가 갖게 되는 동일한 환경 변수와 앱 설정을 갖도록 보장한다는 점에서 고유합니다. 즉 명령줄을 통해 프로파일러에 계측 키를 전달할 필요가 없습니다. 환경에서 계측 키를 선택하기만 하면 됩니다. 그러나 개발자 상자 또는 App Services 외부의 컴퓨터에서 프로파일러를 실행하려는 경우 계측 키를 제공해야 합니다. `--ikey <instrumentation-key>` 인수에서 전달하여 이를 수행할 수 있습니다. 이 값은 응용 프로그램이 사용 중인 계측 키와 일치해야 합니다. 프로파일러의 로그 출력에서 프로파일러가 시작한 ikey와 프로파일링하는 동안 해당 계측 키에서 작업을 감지했는지를 알려줍니다.
4.  수동으로 트리거된 웹 작업은 실제로 웹 후크를 통해 트리거될 수 있습니다. 대시보드에서 웹 작업을 마우스 오른쪽 단추로 클릭하고 속성을 보거나 테이블에서 웹 작업을 선택한 후 도구 모음에서 속성을 선택하여 이 url을 얻을 수 있습니다. 이에 대해 온라인에서 찾을 수 있는 많은 문서가 있으므로 더 자세히 살펴보지 않겠습니다. 하지만 이는 CI/CD 파이프라인(예: VSTS) 또는 Microsoft Flow(https://flow.microsoft.com/en-us/)와 같은 것에서 프로파일러를 트리거할 가능성을 엽니다. run.cmd(run.ps1이 될 수 있음)를 만들려는 바람에 따라 가능성은 광범위합니다.  

## <a name="next-steps"></a>다음 단계

* [Visual Studio에서 Application Insights로 작업](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[appinsights-in-appservices]:./media/app-insights-profiler/AppInsights-AppServices.png
[performance-blade]: ./media/app-insights-profiler/performance-blade.png
[performance-blade-examples]: ./media/app-insights-profiler/performance-blade-examples.png
[performance-blade-v2-examples]:./media/app-insights-profiler/performance-blade-v2-examples.png
[trace-explorer]: ./media/app-insights-profiler/trace-explorer.png
[trace-explorer-toolbar]: ./media/app-insights-profiler/trace-explorer-toolbar.png
[trace-explorer-hint-tip]: ./media/app-insights-profiler/trace-explorer-hint-tip.png
[trace-explorer-hot-path]: ./media/app-insights-profiler/trace-explorer-hot-path.png
[enable-profiler-banner]: ./media/app-insights-profiler/enable-profiler-banner.png
[disable-profiler-webjob]: ./media/app-insights-profiler/disable-profiler-webjob.png
[linked app services]: ./media/app-insights-profiler/linked-app-services.png
