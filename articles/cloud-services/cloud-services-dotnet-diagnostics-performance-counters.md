---
title: "Azure 진단에서 성능 카운터 사용 | Microsoft Docs"
description: "Azure 클라우드 서비스 또는 가상 컴퓨터에서 성능 카운터를 사용하여 병목을 찾고 성능을 조정합니다."
services: cloud-services
documentationcenter: .net
author: rboucher
manager: jwhit
editor: tysonn
ms.assetid: fc4c61e9-d49d-4ed9-a32c-b91cdf851909
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/29/2016
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 9cf1faabe3ea12af0ee5fd8a825975e30947b03a
ms.openlocfilehash: 55623820a74b5226471d642e9b960480f25b4390


---
# <a name="create-and-use-performance-counters-in-an-azure-application"></a>Azure 응용 프로그램에서 성능 카운터 만들기 및 사용
이 문서에서는 성능 카운터의 이점 및 Azure 응용 프로그램에 성능 카운터를 배치하는 방법을 설명합니다. 데이터를 수집하고 병목을 찾고 시스템 및 응용 프로그램 성능 튜닝에 사용할 수 있습니다.

Windows Server, IIS 및 ASP.NET에서 사용할 수 있는 성능 카운터를 수집하여 Azure 웹 역할, 작업자 역할 및 가상 컴퓨터의 상태를 확인하는 데 사용할 수도 있습니다. 사용자 지정 성능 카운터를 만들고 사용할 수도 있습니다.  

성능 카운터 데이터를 다음과 같이 검사할 수 있습니다.

1. 원격 데스크톱을 사용하여 액세스된 성능 모니터 도구와 응용 프로그램 호스트에서 직접
2. Azure 관리 팩을 사용하여 System Center Operations Manager를 통해
3. Azure 저장소로 전송되는 진단 데이터에 액세스하는 다른 모니터링 도구를 통해. 자세한 내용은 [Azure 저장소에서 진단 데이터 저장 및 보기](https://msdn.microsoft.com/library/azure/hh411534.aspx) 를 참조하세요.  

[Azure 클래식 포털](http://manage.azure.com/)에서의 응용 프로그램 성능 모니터링에 대한 내용은 [클라우드 서비스를 모니터링하는 방법](https://www.azure.com/manage/services/cloud-services/how-to-monitor-a-cloud-service/)을 참조하세요.

문제를 해결하고 Azure 응용 프로그램을 최적화하기 위한 로깅 및 추적 전략 만들기와 진단 및 기타 기술 사용에 대한 더 많은 세부 지침은 [Azure 응용 프로그램 개발 문제 해결 모범 사례](https://msdn.microsoft.com/library/azure/hh771389.aspx)(영문)를 참조하십시오.

## <a name="enable-performance-counter-monitoring"></a>성능 카운터 모니터링 사용
성능 카운터는 기본적으로 사용할 수 없습니다. 응용 프로그램 또는 시작 작업은 각 역할 인스턴스에 대해 모니터링하려는 특정 성능 카운터를 포함하도록 기본 진단 에이전트 구성을 수정해야 합니다.

### <a name="performance-counters-available-for-microsoft-azure"></a>Microsoft Azure에 사용할 수 있는 성능 카운터
Azure는 Windows Server, IIS 및 ASP.NET 스택에 사용할 수 있는 성능 카운터의 하위 집합을 제공합니다. 다음 표에서 Azure 응용 프로그램에 대한 특정 관심의 성능 카운터 중 일부를 나열합니다.

| 카운터 범주: 개체(인스턴스) | 카운터 이름 | 참조 |
| --- | --- | --- |
| .NET CLR Exceptions(*Global*) |# Exceps Thrown / sec |예외 성능 카운터 |
| .NET CLR Memory(*Global*) |% Time in GC |메모리 성능 카운터 |
| ASP.NET |응용 프로그램 다시 시작 |ASP.NET용 성능 카운터 |
| ASP.NET |요청 실행 시간 |ASP.NET용 성능 카운터 |
| ASP.NET |요청이 끊김 |ASP.NET용 성능 카운터 |
| ASP.NET |작업자 프로세스 다시 시작 |ASP.NET용 성능 카운터 |
| ASP.NET Applications(**Total**) |총 요청 수 |ASP.NET용 성능 카운터 |
| ASP.NET Applications(**Total**) |Requests/Sec |ASP.NET용 성능 카운터 |
| ASP.NET v4.0.30319 |요청 실행 시간 |ASP.NET용 성능 카운터 |
| ASP.NET v4.0.30319 |요청 대기 시간 |ASP.NET용 성능 카운터 |
| ASP.NET v4.0.30319 |현재 요청 |ASP.NET용 성능 카운터 |
| ASP.NET v4.0.30319 |대기 중인 요청 |ASP.NET용 성능 카운터 |
| ASP.NET v4.0.30319 |거부된 요청 |ASP.NET용 성능 카운터 |
| 메모리 |Available MBytes |메모리 성능 카운터 |
| 메모리 |커밋된 바이트 |메모리 성능 카운터 |
| Processor(_Total) |% Processor Time |ASP.NET용 성능 카운터 |
| TCPv4 |연결 오류 |TCP Object |
| TCPv4 |Connections Established |TCP Object |
| TCPv4 |Connections Reset |TCP Object |
| TCPv4 |Segments Sent/sec |TCP Object |
| Network Interface(*) |Bytes Received/sec |Network Interface Object |
| Network Interface(*) |Bytes Sent/sec |Network Interface Object |
| Network Interface(Microsoft Virtual Machine Bus Network Adapter _2) |Bytes Received/sec |Network Interface Object |
| Network Interface(Microsoft Virtual Machine Bus Network Adapter _2) |Bytes Sent/sec |Network Interface Object |
| Network Interface(Microsoft Virtual Machine Bus Network Adapter _2) |Bytes Total/sec |Network Interface Object |

## <a name="create-and-add-custom-performance-counters-to-your-application"></a>응용 프로그램에 사용자 지정 성능 카운터 만들기 및 추가
Azure는 웹 역할 및 작업자 역할에 대한 사용자 지정 성능 카운터 만들기 및 수정에 대해 지원합니다. 카운터는 응용 프로그램 관련 동작을 추적하고 모니터링하는 데 사용될 수도 있습니다. 승격된 권한으로 시작 작업, 웹 역할 또는 작업자 역할에서 사용자 지정 성능 카운터 범주 및 지정자를 만들고 삭제할 수 있습니다.

> [!NOTE]
> 사용자 지정 성능 카운터를 변경하는 코드에는 실행할 승격된 권한이 있어야 합니다. 코드가 웹 역할 또는 작업자 역할에 있는 경우 역할은 올바르게 초기화하기 위해 ServiceDefinition.csdef 파일에 <Runtime executionContext="elevated" /> 태그를 포함해야 합니다.
> 
> 

진단 에이전트를 사용하여 Azure 저장소에 사용자 지정 성능 카운터 데이터를 보낼 수 있습니다.

표준 성능 카운터 데이터는 Azure 프로세스에 의해 생성됩니다. 웹 역할 또는 작업자 역할 응용 프로그램에서 사용자 지정 성능 카운터 데이터를 만들어야 합니다. 사용자 지정 성능 카운터에 저장할 수 있는 데이터의 형식에 대한 정보는 [성능 카운터 형식](https://msdn.microsoft.com/library/z573042h.aspx) 을 참조하세요. 웹 역할에서 사용자 지정 성능 카운터 데이터를 만들고 설정하는 예제는 [PerformanceCounters 샘플](http://code.msdn.microsoft.com/azure/) 을 참조하세요.

## <a name="store-and-view-performance-counter-data"></a>성능 카운터 데이터 저장 및 보기
Azure는 기타 진단 정보와 함께 성능 카운터 데이터를 캐시합니다. 이 데이터는 성능 모니터와 같은 도구를 보기 위해 원격 데스크톱 액세스를 사용하여 역할 인스턴스가 실행되는 동안 원격 모니터링에 사용할 수 있습니다. 역할 인스턴스 외부의 데이터를 유지하려면 진단 에이전트가 Azure 저장소에 데이터를 전송해야 합니다. 캐시된 성능 카운터 데이터의 크기 제한은 진단 에이전트에서 구성될 수 있거나 모든 진단 데이터에 대한 공유 제한의 일부가 되도록 구성될 수도 있습니다. 버퍼 크기 설정에 대한 자세한 내용은 [OverallQuotaInMB](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitorconfiguration.overallquotainmb.aspx) 및 [DirectoriesBufferConfiguration](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.directoriesbufferconfiguration.aspx)을 참조하세요. 저장소 계정에 데이터를 전송하도록 진단 에이전트 설정에 대한 개요는 [Azure 저장소에서 진단 데이터 저장 및 보기](https://msdn.microsoft.com/library/azure/hh411534.aspx) 를 참조하세요.

구성된 각 성능 카운터 인스턴스는 지정된 샘플링 속도로 기록되고 샘플링된 데이터는 예약된 전송 요청 또는 주문형 전송 요청으로 저장소 계정에 전송됩니다. 자동 전송은 매 분마다 예약할 수 있습니다. 진단 에이전트에 의해 전송된 성능 카운터 데이터는 저장소 계정의 테이블, WADPerformanceCountersTable에 저장됩니다. 표준 Azure 저장소 API 메서드를 사용하여 이 테이블에 액세스 및 쿼리할 수 있습니다. WADPerformanceCountersTable 테이블에서 성능 카운터 데이터 쿼리 및 표시의 예제는 [Microsoft Azure PerformanceCounters 샘플](http://code.msdn.microsoft.com/Windows-Azure-PerformanceCo-7d80ebf9) 을 참조하세요.

> [!NOTE]
> 진단 에이전트 전송 빈도 및 큐 대기 시간에 따라 저장소 계정의 가장 최근 성능 카운터 데이터는 몇 분 정도 지연될 수 있습니다.
> 
> 

## <a name="enable-performance-counters-using-diagnostics-configuration-file"></a>진단 구성 파일을 사용하여 성능 카운터를 사용하도록 설정
다음 절차를 사용하여 Azure 응용 프로그램에서 성능 카운터를 사용합니다.

## <a name="prerequisites"></a>필수 조건
이 섹션은 진단 모니터를 응용 프로그램으로 가져왔으며 진단 구성 파일을 Visual Studio 솔루션에 추가했다고 가정합니다(SDK 2.4 이하에서 diagnostics.wadcfg 또는 SDK 2.5 이상에서 diagnostics.wadcfgx). 자세한 내용은 [Azure 클라우드 서비스 및 Virtual Machine에서 진단 사용](cloud-services-dotnet-diagnostics.md))의 1, 2단계를 참조하세요.

## <a name="step-1-collect-and-store-data-from-performance-counters"></a>1단계: 성능 카운터에서 데이터 수집 및 저장
진단 파일을 Visual Studio 솔루션에 추가하면 Azure 응용 프로그램에서 성능 카운터 데이터의 수집 및 저장을 구성할 수 있습니다. 이렇게 하려면 성능 카운터를 진단 파일에 추가하면 됩니다. 성능 카운터를 포함한 진단 데이터는 인스턴스에서 먼저 수집됩니다. 그런 다음 그 데이터는 Azure 테이블 서비스의 WADPerformanceCountersTable 테이블에 보관되므로 응용 프로그램에서 저장소 계정도 지정해야 합니다. 계산 에뮬레이터에서 응용 프로그램을 로컬로 테스트하는 중이면 진단 데이터도 저장소 에뮬레이터에 로컬로 저장할 수 있습니다. 진단 데이터를 저장하기 전에 먼저 [Azure 클래식 포털](http://manage.windowsazure.com/) 로 이동하여 저장소 계정을 만들어야 합니다. 외부 대역폭 비용을 지불하지 않고 대기 시간을 줄이기 위해 Azure 응용 프로그램과 동일한 지리적 위치에 저장소 계정을 두는 것이 가장 좋습니다.

### <a name="add-performance-counters-to-the-diagnostics-file"></a>진단 파일에 성능 카운터 추가
사용할 수 있는 카운터가 많습니다. 다음 예제는 웹 및 작업자 역할 모니터링에 권장되는 일부 성능 카운터를 보여 줍니다.

진단 파일(SDK 2.4 이하에서 diagnostics.wadcfg 또는 SDK 2.5 이상에서 diagnostics.wadcfgx)을 열고 다음을 DiagnosticMonitorConfiguration 요소에 추가합니다.

```xml
<PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
    <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT30S" />

    <!-- Use the Process(w3wp) category counters in a web role -->

    <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\% Processor Time" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\Private Bytes" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\Thread Count" sampleRate="PT30S" />

    <!-- Use the Process(WaWorkerHost) category counters in a worker role.
        <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\% Processor Time" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\Private Bytes" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\Thread Count" sampleRate="PT30S" />
    -->

    <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Interop(_Global_)\# of marshalling" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Loading(_Global_)\% Time Loading" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\.NET CLR LocksAndThreads(_Global_)\Contention Rate / sec" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Memory(_Global_)\# Bytes in all Heaps" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Networking(_Global_)\Connections Established" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Remoting(_Global_)\Remote Calls/sec" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Jit(_Global_)\% Time in Jit" sampleRate="PT30S" />
</PerformanceCounters>
```

bufferQuotaInMB 특성은 데이터 수집 형식(Azure 로그, IIS 로그 등)에 사용할 수 있는 파일 시스템 저장소의 최대량을 지정합니다. 기본값은 0입니다. 할당량에 도달하면 새 데이터가 추가될 때 가장 오래된 데이터가 삭제됩니다. 모든 bufferQuotaInMB 속성의 합계는 OverallQuotaInMB 특성 값보다 커야 합니다. 진단 데이터를 수집하는 데 필요한 저장소 양을 결정하는 자세한 논의는 [Azure 응용 프로그램 개발 문제 해결 모범 사례](https://msdn.microsoft.com/library/windowsazure/hh771389.aspx)(영문)의 WAD 설정 섹션을 참조하십시오.

scheduledTransferPeriod 특성은 데이터의 예약 전송 사이의 간격을 지정합니다(가장 가까운 시간(분)으로 반올림됨). 다음 예제에서는 PT30M(30분)으로 설정됩니다. 전송 간격을 1분처럼 작은 값으로 설정하면 프로덕션에서 응용 프로그램의 성능에 악영향을 주지만 테스트할 때 진단 작업을 빠르게 보는 데는 유용할 수 있습니다. 예약 전송 간격은 인스턴스에서 진단 데이터를 덮어쓰지 않을 정도로 작고 응용 프로그램의 성능에 영향을 주지 않을 정도로 커야 합니다.

counterSpecifier 특성은 수집할 성능 카운터를 지정합니다.sampleRate 특성은 성능 카운터가 샘플링되어야 하는 속도를 지정합니다(이 경우 30초).

수집할 성능 카운터를 추가했으면 변경 내용을 진단 파일에 저장합니다. 그 다음에 진단 데이터를 보관할 저장소 계정을 지정해야 합니다.

### <a name="specify-the-storage-account"></a>저장소 계정 지정
진단 정보를 Azure 저장소 계정에 보관하려면 서비스 구성(ServiceConfiguration.cscfg) 파일에서 연결 문자열을 지정해야 합니다.

Azure SDK 2.5에서는 저장소 계정이 diagnostics.wadcfgx 파일에서 지정될 수 있습니다.

> [!NOTE]
> 이러한 지침은 Azure SDK 2.4 이하에만 적용됩니다. Azure SDK 2.5에서는 저장소 계정이 diagnostics.wadcfgx 파일에서 지정될 수 있습니다.
> 
> 

연결 문자열을 설정하려면

1. 원하는 텍스트 편집기를 사용하여 ServiceConfiguration.Cloud.cscfg 파일을 열고 저장소에 대한 연결 문자열을 설정합니다. *AccountName* 및 *AccountKey* 값은 저장소 계정 대시보드의 Azure 클래식 포털에서 키 관리 아래에 있습니다.
  
    ```xml
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<name>;AccountKey=<key>"/>
    </ConfigurationSettings>
    ```
2. ServiceConfiguration.Cloud.cscfg 파일을 저장합니다.
3. ServiceConfiguration.Local.cscfg 파일을 열고 UseDevelopmentStorage가 true로 설정되어 있는지 확인합니다.
   
    ```xml
    <ConfigurationSettings>
      <Settingname="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true"/>
    </ConfigurationSettings>
    ```
   연결 문자열이 설정되면 응용 프로그램이 배포될 때 응용 프로그램에서 진단 데이터를 저장소 계정에 보관합니다.
4. 프로젝트를 저장하고 빌드한 다음 응용 프로그램을 배포합니다.

## <a name="step-2-optional-create-custom-performance-counters"></a>2단계: (옵션) 사용자 지정 성능 카운터 만들기
미리 정의한 성능 카운터 외에도 웹 또는 작업자 역할을 모니터링하기 위해 사용자 지정 성능 카운터를 추가할 수 있습니다. 사용자 지정 성능 카운터는 응용 프로그램별 동작을 추적하고 모니터링하는 데 사용할 수 있으며, 관리자 권한으로 시작 작업, 웹 역할 또는 작업자 역할에서 만들거나 삭제할 수 있습니다.

Azure 진단 에이전트는 시작 1분 후 .wadcfg 파일에서 성능 카운터 구성을 새로 고칩니다.  OnStart 메서드에서 사용자 지정 성능 카운터를 만드는 경우 시작 작업은 실행하는 데 1분 이상이 걸리며 Azure 진단 에이전트가 이를 로드하려는 경우 사용자 지정 성능 카운터는 만들어지지 않습니다.  이 시나리오에서는 Azure 진단이 사용자 지정 성능 카운터를 제외한 모든 진단 데이터를 올바르게 캡처하는 것을 확인합니다.  이 문제를 해결하려면 시작 작업에서 성능 카운터를 만들거나 성능 카운터를 만든 후 시작 작업 중 일부를 OnStart 메서드로 이동합니다.

다음 단계를 수행하여 "\MyCustomCounterCategory\MyButton1Counter"라는 간단한 사용자 지정 성능 카운터를 만듭니다.

1. 응용 프로그램의 서비스 정의 파일(CSDEF)을 엽니다.
2. Runtime 요소를 WebRole 또는 WorkerRole 요소에 추가하여 관리자 권한으로 실행할 수 있도록 합니다.
   
    ```xml
    <runtime executioncontext="elevated"/>
    ```
3. 파일을 저장합니다.
4. 진단 파일(SDK 2.4 이하에서 diagnostics.wadcfg 또는 SDK 2.5 이상에서 diagnostics.wadcfgx)을 열고 다음을 DiagnosticMonitorConfiguration에 추가 
   
    ```xml
    <PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
      <PerformanceCounterConfiguration counterSpecifier="\MyCustomCounterCategory\MyButton1Counter" sampleRate="PT30S"/>
    </PerformanceCounters>
    ```
5. 파일을 저장합니다.
6. base.OnStart를 호출하기 전에 역할의 OnStart 메서드에 사용자 지정 성능 카운터 범주를 만듭니다. 다음 C# 예제는 사용자 지정 범주가 없는 경우 만듭니다.
   
    ```csharp
    public override bool OnStart()
    {
      if (!PerformanceCounterCategory.Exists("MyCustomCounterCategory"))
      {
         CounterCreationDataCollection counterCollection = new CounterCreationDataCollection();
   
         // add a counter tracking user button1 clicks
         CounterCreationData operationTotal1 = new CounterCreationData();
         operationTotal1.CounterName = "MyButton1Counter";
         operationTotal1.CounterHelp = "My Custom Counter for Button1";
         operationTotal1.CounterType = PerformanceCounterType.NumberOfItems32;
         counterCollection.Add(operationTotal1);
   
         PerformanceCounterCategory.Create(
           "MyCustomCounterCategory",
           "My Custom Counter Category",
           PerformanceCounterCategoryType.SingleInstance, counterCollection);
   
         Trace.WriteLine("Custom counter category created.");
      }
      else {
        Trace.WriteLine("Custom counter category already exists.");
      }
   
    return base.OnStart();
    }
    ```
7. 응용 프로그램 내에서 카운터를 업데이트합니다. 다음 예제는 Button1_Click 이벤트에서 사용자 지정 성능 카운터를 업데이트합니다.
   
    ```csharp
    protected void Button1_Click(object sender, EventArgs e)
    {
      PerformanceCounter button1Counter = new PerformanceCounter(
        "MyCustomCounterCategory",
        "MyButton1Counter",
        string.Empty,
        false);
      button1Counter.Increment();
      this.Button1.Text = "Button 1 count: " +
        button1Counter.RawValue.ToString();
    }
    ```
8. 파일을 저장합니다.  

Azure 진단 모니터가 사용자 지정 성능 카운터 데이터를 수집합니다.

## <a name="step-3-query-performance-counter-data"></a>3단계: 성능 카운터 데이터 쿼리
응용 프로그램이 배포되고 진단 모니터가 실행되면 성능 카운터를 수집하여 해당 데이터를 Azure 저장소에 보관하기 시작합니다. Visual Studio의 서버 탐색기, [Azure Storage 탐색기](http://azurestorageexplorer.codeplex.com/) 또는 Cerebrata의 [Azure Diagnostics Manager](http://www.cerebrata.com/Products/AzureDiagnosticsManager/Default.aspx)와 같은 도구를 사용하여 WADPerformanceCountersTable 테이블의 성능 카운터 데이터를 확인합니다. 또한 [C#](../storage/storage-dotnet-how-to-use-tables.md),  [Java](../storage/storage-java-how-to-use-table-storage.md),  [Node.js](../storage/storage-nodejs-how-to-use-table-storage.md), [Python](../storage/storage-python-how-to-use-table-storage.md), [Ruby](../storage/storage-ruby-how-to-use-table-storage.md) 또는 [PHP](../storage/storage-php-how-to-use-table-storage.md)를 사용하여 Table service를 프로그래밍 방식으로 쿼리할 수도 있습니다.

다음 C# 예제는 WADPerformanceCountersTable 테이블에 대한 간단한 쿼리를 보여 주고 진단 데이터를 CSV 파일에 저장합니다. 성능 카운터를 CSV 파일에 저장하고 나면 Microsoft Excel 또는 다른 도구의 그래픽 기능을 사용하여 데이터를 시각화할 수 있습니다. Azure SDK for .NET 2012년 10월 이상 버전에 포함되어 있는 Microsoft.WindowsAzure.Storage.dll에 참조를 추가해야 합니다. 어셈블리는 %Program %Program Files%\Microsoft SDKs\Microsoft Azure.NET SDK\version-num\ref\ 디렉터리에 설치됩니다.

```csharp
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Table;
...

// Get the connection string. When using Microsoft Azure Cloud Services, it is recommended
// you store your connection string using the Microsoft Azure service configuration
// system (*.csdef and *.cscfg files). You can you use the CloudConfigurationManager type
// to retrieve your storage connection string.  If you're not using Cloud Services, it's
// recommended that you store the connection string in your web.config or app.config file.
// Use the ConfigurationManager type to retrieve your storage connection string.

string connectionString = Microsoft.WindowsAzure.CloudConfigurationManager.GetSetting("StorageConnectionString");
//string connectionString = System.Configuration.ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString;

// Get a reference to the storage account using the connection string.  You can also use the development
// storage account (Storage Emulator) for local debugging.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
//CloudStorageAccount storageAccount = CloudStorageAccount.DevelopmentStorageAccount;

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "WADPerformanceCountersTable" table.
CloudTable table = tableClient.GetTableReference("WADPerformanceCountersTable");

// Create the table query, filter on a specific CounterName, DeploymentId and RoleInstance.
TableQuery<PerformanceCountersEntity> query = new TableQuery<PerformanceCountersEntity>()
  .Where(
    TableQuery.CombineFilters(
      TableQuery.GenerateFilterCondition("CounterName", QueryComparisons.Equal, @"\Processor(_Total)\% Processor Time"),
      TableOperators.And,
      TableQuery.CombineFilters(
      TableQuery.GenerateFilterCondition("DeploymentId", QueryComparisons.Equal, "ec26b7a1720447e1bcdeefc41c4892a3"),
      TableOperators.And,
      TableQuery.GenerateFilterCondition("RoleInstance", QueryComparisons.Equal, "WebRole1_IN_0")
    )
  )
);

// Execute the table query.
IEnumerable<PerformanceCountersEntity> result = table.ExecuteQuery(query);

// Process the query results and build a CSV file.
StringBuilder sb = new StringBuilder("TimeStamp,EventTickCount,DeploymentId,Role,RoleInstance,CounterName,CounterValue\n");

foreach (PerformanceCountersEntity entity in result)
{
  sb.Append(entity.Timestamp + "," + entity.EventTickCount + "," + entity.DeploymentId + ","
    + entity.Role + "," + entity.RoleInstance + "," + entity.CounterName + "," + entity.CounterValue+"\n");
}

StreamWriter sw = File.CreateText(@"C:\temp\PerfCounters.csv");
sw.Write(sb.ToString());
sw.Close();
```

엔터티는 **TableEntity**에서 파생된 사용자 지정 클래스를 사용하여 C# 개체에 매핑됩니다. 다음 코드는 **WADPerformanceCountersTable** 테이블의 성능 카운터를 나타내는 엔터티 클래스를 정의합니다.

```csharp
public class PerformanceCountersEntity : TableEntity
{
  public long EventTickCount { get; set; }
  public string DeploymentId { get; set; }
  public string Role { get; set; }
  public string RoleInstance { get; set; }
  public string CounterName { get; set; }
  public double CounterValue { get; set; }
}
```

## <a name="next-steps"></a>다음 단계
[Azure 진단에 대한 추가 문서 보기](../azure-diagnostics.md)



<!--HONumber=Nov16_HO3-->


