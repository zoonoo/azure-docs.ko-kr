---
title: Application Insights Profiler를 사용하여 Azure에서 프로덕션 애플리케이션 프로파일링 | Microsoft Docs
description: 적은 공간의 프로파일러를 사용하여 웹 서버 코드에서 실행 부하 과다 경로를 식별합니다.
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: c07b325f3de6cd2cf3aaa436736786d2cdc42881
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58498131"
---
# <a name="profile-production-applications-in-azure-with-application-insights"></a>Application Insights를 사용하여 Azure에서 프로덕션 애플리케이션 프로파일링
## <a name="enable-application-insights-profiler-for-your-application"></a>애플리케이션에 대해 Application Insights Profiler 사용

Azure Application Insights Profiler는 Azure의 프로덕션 환경에서 실행 중인 애플리케이션의 성능을 추적합니다. Profiler는 사용자에게 부정적인 영향을 주지 않으면서 데이터를 대규모로 자동으로 캡처합니다. Profiler는 특정 웹 요청을 처리할 때 시간이 가장 오래 걸리는 “핫” 코드 경로를 식별할 수 있도록 합니다. 

Profiler는 다음과 같은 Azure 서비스에 배포된 .NET 애플리케이션에서 작동합니다. 각 서비스 형식에 맞게 Profiler를 사용하도록 설정하기 위한 특정 지침은 아래 링크에 나와 있습니다.

* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines 및 Virtual Machine Scale Sets](profiler-vm.md?toc=/azure/azure-monitor/toc.json)
* [**미리 보기** ASP.NET Core Azure Linux 웹 앱](profiler-aspnetcore-linux.md?toc=/azure/azure-monitor/toc.json) 

Profiler를 사용하도록 설정해도 추적이 표시되지 않으면 [문제 해결 가이드](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)를 확인하세요.

## <a name="view-profiler-data"></a>Profiler 데이터 보기

Profiler가 추적을 업로드하도록 하기 위해 애플리케이션은 요청을 적극적으로 처리해야 합니다. 실험을 수행하는 경우 [Application Insights 성능 테스트](https://docs.microsoft.com/vsts/load-test/app-service-web-app-performance-test)를 사용하여 웹앱에 요청을 생성할 수 있습니다. Profiler를 새로 사용하도록 설정한 경우 짧은 부하 테스트를 실행할 수 있습니다. 부하 테스트를 실행하는 동안 [**Profiler 설정** 창](profiler-settings.md#profiler-settings-pane)에서 **지금 프로파일링** 단추를 선택합니다. Profiler는 실행되면 1시간 간격으로 2분 동안 무작위로 프로파일링을 수행합니다. 애플리케이션이 지속적인 요청 스트림을 처리하는 경우 Profiler는 매시간 추적을 업로드합니다.

애플리케이션이 트래픽을 수신하고 Profiler가 추적을 업로드하면 사용자가 확인할 수 있는 추적이 생성됩니다. 이 프로세스는 최대 5~10분이 걸릴 수 있습니다. 추적을 보려면 **성능** 창에서 **작업 수행**을 선택한 후 **Profiler 추적** 단추를 선택합니다.

![Application Insights 성능 창 미리 보기 Profiler 추적][performance-blade]

샘플을 선택하여 요청을 실행하는 데 걸린 시간의 코드 수준 분석을 표시합니다.

![Application Insights 추적 탐색기][trace-explorer]

추적 탐색기에서 다음 정보를 표시합니다.

* **실행 부하 과다 경로 표시**: 가장 큰 리프 노드 또는 최소한 닫힌 것을 엽니다. 대부분의 경우에서 이 노드는 성능 병목에 인접한 상태가 됩니다.
* **레이블**: 함수 또는 이벤트의 이름입니다. 트리는 발생한 코드와 이벤트의 혼합을 보여 줍니다(예: SQL 및 HTTP 이벤트). 최상위 이벤트는 전체 요청 기간을 나타냅니다.
* **경과 시간**: 작업의 시작과 끝 간의 시간 간격입니다.
* **시기**: 함수 또는 이벤트가 다른 함수와 관련해서 실행된 시기입니다.

## <a name="how-to-read-performance-data"></a>성능 데이터를 읽는 방법

Microsoft 서비스 프로파일러는 샘플링 메서드와 계측의 조합을 사용하여 애플리케이션의 성능을 분석합니다. 자세한 컬렉션이 진행 중인 경우 서비스 프로파일러는 1밀리초마다 각 컴퓨터 CPU의 명령 포인터를 샘플링합니다. 각 샘플은 현재 실행 중인 스레드의 전체 호출 스택을 캡처합니다. 높은 추상화 수준과 낮은 추상화 수준 모두에서 해당 스레드가 수행한 작업에 대한 상세한 정보를 제공합니다. 서비스 프로파일러는 또한 컨텍스트 스위칭 이벤트, TPL(작업 병렬 라이브러리) 이벤트 및 스레드 풀 이벤트와 같은 다른 이벤트를 수집하여 활동 상관 관계 및 인과 관계를 추적합니다.

타임라인 보기에 표시된 호출 스택은 샘플링 및 계측의 결과입니다. 각 샘플은 스레드의 전체 호출 스택을 캡처하므로 Microsoft .NET Framework의 코드뿐만 아니라 참조하는 다른 프레임워크의 코드를 포함합니다.

### <a id="jitnewobj"></a>개체 할당(clr!JIT\_New 또는 clr!JIT\_Newarr1)

**clr!JIT\_New** 및 **clr!JIT\_Newarr1**은 관리되는 힙에서 메모리를 할당하는 .NET Framework의 도우미 함수입니다. 개체가 할당되면 **clr!JIT\_New**가 호출됩니다. **clr!JIT\_Newarr1**은 개체 배열이 할당될 때 호출됩니다. 이 두 함수는 일반적으로 빠르며 상대적으로 시간이 적게 걸립니다. 타임라인에서 **clr!JIT\_New** 또는 **clr!JIT\_Newarr1**에 많은 시간이 소요되면 코드는 많은 개체를 할당하고 많은 양의 메모리를 사용하고 있는 것일 수 있습니다.

### <a id="theprestub"></a>코드 로드(clr!ThePreStub)

**clr!ThePreStub**은 처음으로 실행할 코드를 준비하는 .NET Framework 내부의 도우미 함수입니다. 이 실행은 일반적으로 JIT(Just-In-Time) 컴파일을 포함하지만 이에 제한되지 않습니다. 각 C# 메서드의 경우 **clr!ThePreStub**은 프로세스 동안 한 번만 호출되어야 합니다.

특정 요청에 대해 **clr!ThePreStub**이 오래 걸릴 경우 해당 요청에 해당 메서드를 실행하는 첫 번째 항목인 것입니다. .NET Framework 런타임에서 첫 번째 메서드를 로드하는 데 많은 시간이 걸립니다. 사용자가 코드에 액세스하기 전에 해당 부분을 실행하는 준비 프로세스를 사용하거나 어셈블리에서 네이티브 이미지 생성기(ngen.exe) 실행을 고려할 수 있습니다.

### <a id="lockcontention"></a>잠금 경합(clr!JITutil\_MonContention 또는 clr!JITutil\_MonEnterWorker)

**clr!JITutil\_MonContention** 또는 **clr!JITutil\_MonEnterWorker**는 현재 스레드가 잠금 해제를 기다리고 있음을 나타냅니다. 이는 종종 C# **LOCK** 문을 실행하거나 **Monitor.Enter** 메서드를 호출하거나 **MethodImplOptions.Synchronized** 특성으로 메서드를 호출할 때 표시됩니다. 잠금 경합은 일반적으로 스레드 _A_가 잠금을 획득하고 스레드 _B_가 스레드 _A_가 잠금을 해제하기 전에 동일한 잠금을 획득하려고 하는 경우에 발생합니다.

### <a id="ngencold"></a>코드 로드([COLD])

메서드 이름에 **mscorlib.ni![COLD]System.Reflection.CustomAttribute.IsDefined**와 같이 **[COLD]** 가 포함되어 있으면 .NET Framework 런타임에서 처음으로 [프로필 기반 최적화](/cpp/build/profile-guided-optimizations)에 의해 최적화되지 않은 코드를 실행하고 있는 것입니다. 각 메서드의 경우 프로세스 동안 한 번만 표시되어야 합니다.

특정 요청에 대해 코드 로드가 상당히 오래 걸릴 경우 해당 요청이 메서드의 최적화되지 않은 부분을 실행하는 첫 번째 항목인 것입니다. 사용자가 액세스하기 전에 코드의 해당 부분을 실행하는 준비 프로세스를 고려할 수 있습니다.

### <a id="httpclientsend"></a>HTTP 요청 보내기

**HttpClient.Send**와 같은 메서드는 코드가 HTTP 요청이 완료되기를 기다리고 있음을 나타냅니다.

### <a id="sqlcommand"></a>데이터베이스 작업

**SqlCommand.Execute**와 같은 메서드는 코드가 데이터베이스 작업이 완료되기를 기다리고 있음을 나타냅니다.

### <a id="await"></a>대기 중(AWAIT\_TIME)

**AWAIT\_TIME**은 코드가 다른 작업이 완료되기를 기다리고 있음을 나타냅니다. 이러한 지연은 일반적으로 C# **AWAIT** 문과 함께 발생합니다. 코드가 C# **AWAIT**를 수행하는 경우 스레드는 스레드 풀에 대한 컨트롤을 해제 및 반환하고 **AWAIT**가 끝나기를 기다리는 것이 차단된 스레드는 없습니다. 그러나 논리적으로 **AWAIT**를 수행한 스레드는 작업이 완료되길 기다리는 것이 "차단"됩니다. **AWAIT\_TIME** 문은 작업이 완료되기를 기다리는 차단된 시간을 나타냅니다.

### <a id="block"></a>차단된 시간

**BLOCKED_TIME**은 코드가 다른 리소스를 사용할 수 있을 때까지 기다리고 있음을 나타냅니다. 예를 들어, 동기화 개체를 기다리거나, 스레드를 사용할 수 있거나 요청이 완료될 때까지 기다릴 수 있습니다.

### <a name="unmanaged-async"></a>관리되지 않는 비동기

.NET framework는 ETW 이벤트를 내보냅니다 및 스레드에서 비동기 호출을 추적할 수 있도록 스레드 간 작업 id를 전달 합니다. 비관리 코드 (네이티브 코드용) 및 비동기 코드의 일부 이전 스타일은 없으므로 이러한 이벤트와 동작 id 프로파일러는 스레드 및 스레드에서 실행 하는 함수는 알 수 없습니다. 호출 스택의 ' 관리 되지 않는 Async' 이라고 합니다. ETW 파일을 다운로드 하는 경우에 사용할 수 있습니다 [PerfView](https://github.com/Microsoft/perfview/blob/master/documentation/Downloading.md) 벌어지는 상황에 대 한 자세한 정보를 가져오려고 합니다.

### <a id="cpu"></a>CPU 시간

CPU는 명령을 실행 중입니다.

### <a id="disk"></a>디스크 시간

애플리케이션은 디스크 작업을 수행 중입니다.

### <a id="network"></a>네트워크 시간

애플리케이션은 네트워크 작업을 수행 중입니다.

### <a id="when"></a>때 열

**시기** 열은 노드에 대해 수집된 INCLUSIVE 샘플이 시간에 따라 달라지는 방식의 시각화입니다. 요청의 전체 범위는 32시간 버킷으로 나뉩니다. 해당 노드에 대한 포괄적인 샘플은 이러한 32개의 버킷에 누적됩니다. 각 버킷은 막대로 표시됩니다. 막대의 높이는 크기 조정된 값을 나타냅니다. **CPU_TIME** 또는 **BLOCKED_TIME**으로 표시된 노드 또는 리소스(CPU, 디스크, 스레드)를 사용하는 확실한 관계가 있는 노드의 경우 막대는 버킷 동안 해당 리소스 중 하나를 사용함을 나타냅니다. 이러한 메트릭의 경우 여러 리소스를 소비하여 100% 이상의 값을 얻을 수 있습니다. 예를 들어 간격 동안 평균적으로 두 개의 CPU를 사용하는 경우 200%가 됩니다.

## <a name="limitations"></a>제한 사항

기본 데이터 보존 기간은 5일입니다. 하루에 수집되는 최대 데이터는 10GB입니다.

Profiler 서비스 사용료는 청구되지 않습니다. 이를 사용하려면 적어도 Azure App Service의 Web Apps 기능 기본 계층에 웹앱을 호스트해야 합니다.

## <a name="overhead-and-sampling-algorithm"></a>오버헤드 및 샘플링 알고리즘

Profiler는 Profiler가 추적을 캡처하도록 설정된 애플리케이션을 호스트하는 각 가상 머신에서 시간당 2분씩 임의로 실행됩니다. Profiler를 실행하는 경우 서버에 5%~15% CPU 오버헤드를 추가하게 됩니다.

## <a name="next-steps"></a>다음 단계
Azure 애플리케이션에 대해 Application Insights Profiler를 사용하도록 설정합니다. 또한 다음을 참조하세요.
* [App Services](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines 및 Virtual Machine Scale Sets](profiler-vm.md?toc=/azure/azure-monitor/toc.json)


[performance-blade]: ./media/profiler-overview/performance-blade-v2-examples.png
[trace-explorer]: ./media/profiler-overview/trace-explorer.png
