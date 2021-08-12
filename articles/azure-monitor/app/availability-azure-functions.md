---
title: Azure Functions를 사용하여 사용자 지정 가용성 테스트 만들기 및 실행
description: 이 문서에서는 TimerTrigger 함수에 지정된 구성에 따라 주기적으로 실행되는 TrackAvailability()를 사용하여 Azure 함수를 만드는 방법을 설명합니다. 이 테스트의 결과는 Application Insights 리소스로 전송되며, 해당 리소스에서 가용성 결과 데이터를 쿼리하고 경고할 수 있습니다. 사용자 지정된 테스트를 통해 포털 UI를 사용하여 가능한 것보다 복잡한 가용성 테스트를 작성하거나, Azure VNET 내부에서 앱을 모니터링하거나, 엔드포인트 주소를 변경하거나, 지역에서 사용할 수 없는 경우 가용성 테스트를 만들 수 있습니다.
ms.topic: conceptual
ms.date: 05/06/2021
ms.openlocfilehash: 891f69bd42dc228e01e7b341630e7f37522f9828
ms.sourcegitcommit: c1b0d0b61ef7635d008954a0d247a2c94c1a876f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2021
ms.locfileid: "109628190"
---
# <a name="create-and-run-custom-availability-tests-using-azure-functions"></a>Azure Functions를 사용하여 사용자 지정 가용성 테스트 만들기 및 실행

이 문서에서는 고유한 비즈니스 논리를 포함하는 TimerTrigger 함수에 지정된 구성에 따라 주기적으로 실행되는 TrackAvailability()를 사용하여 Azure 함수를 만드는 방법을 설명합니다. 이 테스트의 결과는 Application Insights 리소스로 전송되며, 해당 리소스에서 가용성 결과 데이터를 쿼리하고 경고할 수 있습니다. 그러면 포털에서 [가용성 모니터링](./monitor-web-app-availability.md)을 통해 수행할 수 있는 것과 유사한 사용자 지정된 테스트를 만들 수 있습니다. 사용자 지정된 테스트를 통해 포털 UI를 사용하여 가능한 것보다 복잡한 가용성 테스트를 작성하거나, Azure VNET 내부에서 앱을 모니터링하거나, 엔드포인트 주소를 변경하거나, 지역에서 사용할 수 없는 기능이라 하더라도 가용성 테스트를 만들 수 있습니다.

> [!NOTE]
> 이 예제는 Azure Function 내에서 TrackAvailability() API 호출이 작동하는 방식에 대한 메커니즘을 보여 주기 위해서만 설계되었습니다. 이를 전체 기능을 갖춘 가용성 테스트로 전환하는 데 필요한 기본 HTTP 테스트 코드/비즈니스 논리를 작성하는 방법에 대해서는 설명하지 않습니다. 기본적으로 이 예제를 진행하는 경우 기본 가용성 HTTP GET 테스트를 만듭니다.

## <a name="create-a-timer-trigger-function"></a>타이머 트리거 함수 만들기

1. Azure Functions 리소스를 만듭니다.
    - Application Insights 리소스가 이미 있는 경우:
        - 기본적으로 Azure Functions는 Application Insights 리소스를 만들지만 이미 만든 리소스 중 하나를 사용하려는 경우 만드는 도중에 지정해야 합니다.
        - 다음 수정과 함께 [Azure Functions 리소스 만들기](../../azure-functions/functions-create-scheduled-function.md#create-a-function-app) 방법에 대한 지침을 따릅니다.
            - **모니터링** 탭에서 Application Insights 드롭다운 상자를 선택한 다음, 리소스의 이름을 입력하거나 선택합니다.
                :::image type="content" source="media/availability-azure-functions/app-insights-resource.png" alt-text="모니터링 탭에서 기존 Application Insights 리소스를 선택합니다.":::
    - 타이머 트리거 함수에서 사용할 Application Insights 리소스가 아직 생성되지 않은 경우
        - 기본적으로 Azure Functions 애플리케이션을 만들 때 Application Insights 리소스를 만들게 됩니다. [Azure Functions 리소스 만들기](../../azure-functions/functions-create-scheduled-function.md#create-a-function-app) 방법에 대한 지침을 따릅니다.
    > [!NOTE]
    > 소비, 프리미엄 또는 App Service 요금제에서 함수를 호스트할 수 있습니다. V-Net에서 테스트하거나 퍼블릭이 아닌 엔드포인트를 테스트하는 경우에는 소비 대신 프리미엄 요금제를 사용해야 합니다. **호스팅** 탭에서 요금제를 선택합니다.
2. 타이머 트리거 함수를 만듭니다.
    1. 함수 앱에서 **함수** 탭을 선택합니다.
    1. **추가** 를 선택하고 함수 추가 탭에서 다음 구성을 선택합니다.
        1. 개발 환경: *포털에서 개발*
        1. 템플릿 선택: *타이머 트리거*
    1. **추가** 를 선택하여 타이머 트리거 함수를 만듭니다.

    :::image type="content" source="media/availability-azure-functions/add-function.png" alt-text="함수 앱에 타이머 트리거 함수를 추가하는 방법의 스크린샷" lightbox="media/availability-azure-functions/add-function.png":::

## <a name="add-and-edit-code-in-the-app-service-editor"></a>App Service 편집기에서 코드 추가 및 편집

배포된 함수 앱으로 이동하고 *개발 도구* 에서 **App Service 편집기** 탭을 선택합니다.

새 파일을 만들려면 타이머 트리거 함수(예: "TimerTrigger1")를 마우스 오른쪽 단추로 클릭하고 **새 파일** 을 선택합니다. 그런 다음, 파일 이름을 입력하고 enter 키를 누릅니다.

1. "function.proj"라는 파일을 만들고 다음 코드를 붙여넣습니다.

    ```xml
    <Project Sdk="Microsoft.NET.Sdk"> 
        <PropertyGroup> 
            <TargetFramework>netstandard2.0</TargetFramework> 
        </PropertyGroup> 
        <ItemGroup> 
            <PackageReference Include="Microsoft.ApplicationInsights" Version="2.15.0" /> <!-- Ensure you’re using the latest version --> 
        </ItemGroup> 
    </Project> 
    
    ```

     :::image type="content" source="media/availability-azure-functions/function-proj.png" alt-text="App Service 편집기의 function.proj의 스크린샷" lightbox="media/availability-azure-functions/function-proj.png":::

2. "runAvailabilityTest.csx"라는 새 파일을 만들고 다음 코드를 붙여넣습니다.

    ```csharp
    using System.Net.Http; 
    
    public async static Task RunAvailabilityTestAsync(ILogger log) 
    { 
        using (var httpClient = new HttpClient()) 
        { 
            // TODO: Replace with your business logic 
            await httpClient.GetStringAsync("https://www.bing.com/"); 
        } 
    } 
    ```

3. 아래 코드를 run.csx 파일에 복사합니다. 그러면 기존 코드가 바뀝니다.

    ```csharp
    #load "runAvailabilityTest.csx" 
    
    using System; 
    
    using System.Diagnostics; 
    
    using Microsoft.ApplicationInsights; 
    
    using Microsoft.ApplicationInsights.Channel; 
    
    using Microsoft.ApplicationInsights.DataContracts; 
    
    using Microsoft.ApplicationInsights.Extensibility; 
    
    private static TelemetryClient telemetryClient; 
    
    // ============================================================= 
    
    // ****************** DO NOT MODIFY THIS FILE ****************** 
    
    // Business logic must be implemented in RunAvailabilityTestAsync function in runAvailabilityTest.csx 
    
    // If this file does not exist, please add it first 
    
    // ============================================================= 
    
    public async static Task Run(TimerInfo myTimer, ILogger log, ExecutionContext executionContext) 
    
    { 
        if (telemetryClient == null) 
        { 
            // Initializing a telemetry configuration for Application Insights based on connection string 
    
            var telemetryConfiguration = new TelemetryConfiguration(); 
            telemetryConfiguration.ConnectionString = Environment.GetEnvironmentVariable("APPLICATIONINSIGHTS_CONNECTION_STRING"); 
            telemetryConfiguration.TelemetryChannel = new InMemoryChannel(); 
            telemetryClient = new TelemetryClient(telemetryConfiguration); 
        } 
    
        string testName = executionContext.FunctionName; 
        string location = Environment.GetEnvironmentVariable("REGION_NAME"); 
        var availability = new AvailabilityTelemetry 
        { 
            Name = testName, 
    
            RunLocation = location, 
    
            Success = false, 
        }; 
    
        availability.Context.Operation.ParentId = Activity.Current.SpanId.ToString(); 
        availability.Context.Operation.Id = Activity.Current.RootId; 
        var stopwatch = new Stopwatch(); 
        stopwatch.Start(); 
    
        try 
        { 
            using (var activity = new Activity("AvailabilityContext")) 
            { 
                activity.Start(); 
                availability.Id = Activity.Current.SpanId.ToString(); 
                // Run business logic 
                await RunAvailabilityTestAsync(log); 
            } 
            availability.Success = true; 
        } 
    
        catch (Exception ex) 
        { 
            availability.Message = ex.Message; 
            throw; 
        } 
    
        finally 
        { 
            stopwatch.Stop(); 
            availability.Duration = stopwatch.Elapsed; 
            availability.Timestamp = DateTimeOffset.UtcNow; 
            telemetryClient.TrackAvailability(availability); 
            telemetryClient.Flush(); 
        } 
    } 
    
    ```

## <a name="check-availability"></a>중복 확인

모든 것이 작동하는지 확인하기 위해 Application Insights 리소스의 가용성 탭에서 그래프를 살펴볼 수 있습니다.

> [!NOTE]
> TrackAvailability()로 만든 테스트는 테스트 이름 옆에 **CUSTOM** 이 표시됩니다.

 :::image type="content" source="media/availability-azure-functions/availability-custom.png" alt-text="성공 결과가 표시된 가용성 탭" lightbox="media/availability-azure-functions/availability-custom.png":::

엔드투엔드 트랜잭션 세부 정보를 보려면 상세 검색 아래에서 **성공** 또는 **실패** 를 선택하고 샘플을 선택합니다. 그래프에서 데이터 요소를 선택하여 엔드투엔드 트랜잭션 세부 정보를 가져올 수도 있습니다.

:::image type="content" source="media/availability-azure-functions/sample.png" alt-text="샘플 가용성 테스트 선택":::

:::image type="content" source="media/availability-azure-functions/end-to-end.png" alt-text="엔드투엔드 트랜잭션 세부 정보" lightbox="media/availability-azure-functions/end-to-end.png":::

## <a name="query-in-logs-analytics"></a>로그(분석)에서 쿼리

로그(분석)를 사용하여 가용성 결과, 종속성 등을 볼 수 있습니다. 로그에 대한 자세한 내용을 보려면 [로그 쿼리 개요](../logs/log-query-overview.md)를 참조하세요.

:::image type="content" source="media/availability-azure-functions/availabilityresults.png" alt-text="가용성 결과" lightbox="media/availability-azure-functions/availabilityresults.png":::

:::image type="content" source="media/availability-azure-functions/dependencies.png" alt-text="종속성이 50개로 제한된 새 쿼리 탭을 보여 주는 스크린샷" lightbox="media/availability-azure-functions/dependencies.png":::

## <a name="next-steps"></a>다음 단계

- [애플리케이션 맵](./app-map.md)
- [트랜잭션 진단](./transaction-diagnostics.md)
