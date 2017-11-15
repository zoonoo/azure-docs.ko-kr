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
ms.openlocfilehash: e66dc2af18785c6c8e83815129c8bca5b877d25b
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2017
---
# <a name="profile-live-azure-web-apps-with-application-insights"></a>Application Insights를 사용하여 라이브 Azure Web Apps 프로파일링

*Application Insights의 이 기능은 일반적으로 Azure App Service에서 사용할 수 있으며 Azure 계산 리소스에 대한 미리 보기에 있습니다.*

[Application Insights Profiler](app-insights-overview.md)를 사용하여 라이브 웹 응용 프로그램의 각 메서드에서 얼마나 많은 시간이 소요되는지 알아봅니다. Application Insights 프로파일링 도구는 앱에서 제공한 실시간 요청의 세부 프로필을 표시하고 가장 많은 시간을 사용하는 *실행 부하 과다 경로*를 강조 표시합니다. 프로파일러는 응답 시간이 다른 예제를 자동으로 선택한 다음 다양한 기술을 사용하여 오버헤드를 최소화합니다.

프로파일러는 현재 최소한 기본 서비스 계층의 Azure App Service에서 실행 중인 ASP.NET 웹앱에 대해 작동합니다.

## <a id="installation"></a> 프로파일러 사용

코드에서 [Application Insights를 설치합니다](app-insights-asp-net.md). 이미 설치된 경우 최신 버전인지 확인합니다. 최신 버전을 확인하려면 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리** > **업데이트** > **모든 패키지 업데이트**를 선택합니다. 그런 다음 앱을 다시 배포합니다.

*ASP.NET Core를 사용 중입니까? [자세한 정보](#aspnetcore)를 참조하세요.*

[Azure Portal](https://portal.azure.com)에서 웹앱에 대한 Application Insights 리소스를 엽니다. **성능** > **Application Insights Profiler 사용**을 선택합니다.

![프로파일러 사용 배너 선택][enable-profiler-banner]

또는 **구성**을 선택하여 상태를 보고 프로파일러를 사용하거나 사용하지 않도록 설정할 수 있습니다.

![성능에서 구성 선택][performance-blade]

Application Insights로 구성된 웹앱은 **구성**에 나열됩니다. 필요한 경우 지침에 따라 프로파일러 에이전트를 설치합니다. Application Insights로 구성된 웹앱이 없는 경우 **연결된 앱 추가**를 선택합니다.

연결된 모든 웹앱의 프로파일러를 제어하려면 **구성** 창에서 **Profiler 사용** 또는 **Profiler 사용 안 함**을 선택합니다.

![구성 창 옵션][linked app services]

App Service 계획을 통해 호스트되는 웹앱과 달리 Azure 계산 리소스(예: Azure Virtual Machines, 가상 컴퓨터 크기 집합, Azure Service Fabric 또는 Azure Cloud Services)에서 호스트되는 응용 프로그램은 Azure에서 직접 관리되지 않습니다. 이 경우 연결할 웹앱이 없습니다. 앱에 연결하는 대신 **Profiler 사용** 단추를 선택합니다.

## <a name="disable-the-profiler"></a>프로파일러 사용 안 함
개별 App Service 인스턴스에 대해 프로파일러를 중지하거나 다시 시작하려면 **웹 작업**에서 App Service 리소스로 이동합니다. 프로파일러를 삭제하려면 **확장**으로 이동합니다.

![웹 작업에 대한 프로파일러 사용 안 함][disable-profiler-webjob]

성능 문제를 가능한 한 빨리 검색하려면 모든 웹앱에서 프로파일러를 사용하도록 설정하는 것이 좋습니다.

WebDeploy를 사용하여 웹 응용 프로그램에 변경 내용을 배포하는 경우 배포하는 동안 App_Data 폴더가 삭제되는 것을 제외하도록 확인합니다. 그렇지 않으면 다음에 Azure에 웹 응용 프로그램을 배포할 때 프로파일러 확장의 파일이 삭제됩니다.

### <a name="using-profiler-with-azure-vms-and-azure-compute-resources-preview"></a>Azure VM 및 Azure 계산 리소스에서 프로파일러 사용(미리 보기)

[런타임 시 Azure App Service에 대해 Application Insights를 사용하도록 설정](app-insights-azure-web-apps.md#run-time-instrumentation-with-application-insights)하면 Application Insights Profiler를 자동으로 사용할 수 있습니다. 리소스에 대해 Application Insights를 이미 사용하도록 설정한 경우 구성 마법사를 사용하여 최신 버전으로 업데이트해야 할 수 있습니다.

자세한 내용은 [Azure 계산 리소스에 대한 미리 보기 버전의 프로파일러](https://go.microsoft.com/fwlink/?linkid=848155)를 참조하세요.


## <a name="limitations"></a>제한 사항

기본 데이터 보존 기간은 5일입니다. 하루에 수집되는 최대 데이터는 10GB입니다.

프로파일러 서비스 사용료는 청구되지 않습니다. 프로파일러 서비스를 사용하려면 웹앱이 적어도 App Service의 기본 계층에서 호스트되어야 합니다.

## <a name="overhead-and-sampling-algorithm"></a>오버헤드 및 샘플링 알고리즘

프로파일러는 프로파일러가 추적을 캡처하도록 설정된 응용 프로그램을 호스트하는 각 가상 컴퓨터에서 시간당 2분씩 임의로 실행됩니다. 프로파일러는 실행되는 동안 서버에 CPU 오버헤드를 5-15% 가중시킵니다.
응용 프로그램을 호스트하는 데 사용할 수 있는 서버가 많을수록 프로파일러가 전체 응용 프로그램 성능에 주는 영향은 감소합니다. 이는 샘플링 알고리즘으로 인해 프로파일러가 언제든지 서버의 5%에서만 실행되기 때문입니다. 프로파일러 실행으로 인한 서버 오버헤드를 상쇄하기 위해 더 많은 서버를 사용하여 웹 요청을 처리할 수 있습니다.


## <a name="view-profiler-data"></a>프로파일러 데이터 보기

**성능** 창으로 이동한 다음 작업 목록에서 아래로 스크롤합니다.

![Application Insights 성능 창 예제 열][performance-blade-examples]

작업 테이블에는 다음과 같은 열이 있습니다.

* **수**: **수** 창의 시간 범위에서 이러한 요청 수입니다.
* **중앙값**: 앱이 요청에 응답하는 데 걸리는 일반적인 시간입니다. 모든 응답의 절반은 이 값보다 더 빨랐습니다.
* **95 백분위수**: 응답의 95%는 이 값보다 더 빨랐습니다. 이 값이 중앙값과 전혀 다른 경우 앱에 간헐적 문제가 있을 수 있습니다. (또는 캐시와 같은 디자인 기능으로 설명될 수 있습니다.)
* **프로파일러 추적**: 아이콘은 프로파일러가 이 작업에 대한 스택 추적을 캡처했음을 나타냅니다.

**보기**를 선택하여 추적 탐색기를 엽니다. 탐색기는 응답 시간에 따라 분류된 프로파일러가 캡처한 몇 가지 샘플을 보여 줍니다.

**미리 보기 성능** 창을 사용하는 경우 페이지의 **작업 수행** 섹션으로 이동하여 프로파일러 추적을 확인합니다. **프로파일러 추적** 단추를 선택합니다.

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
**clr!JIT\_New** 및 **clr!JIT\_Newarr1**은 관리되는 힙에서 메모리를 할당하는 .NET Framework의 도우미 함수입니다. 개체가 할당되면 **clr!JIT\_New**가 호출됩니다. **clr!JIT\_Newarr1**은 개체 배열이 할당될 때 호출됩니다. 이 두 함수는 일반적으로 매우 빠르며 상대적으로 적은 양의 시간을 사용합니다. **clr!JIT\_New** 또는 **clr!JIT\_Newarr1**이 타임라인에서 상당한 양의 시간을 사용하는 경우 코드에 많은 개체가 할당되고 많은 양의 메모리가 소비될 수 있음을 나타냅니다.

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

## <a id="aspnetcore"></a>ASP.NET Core 지원

ASP.NET Core 응용 프로그램을 사용하려면 Microsoft.ApplicationInsights.AspNetCore Nuget 패키지 2.1.0-beta6 이상을 설치하여 프로파일러와 작동하도록 해야 합니다. 2017년 6월 27일부터 이전 버전은 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

* [Visual Studio에서 Application Insights로 작업](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

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
