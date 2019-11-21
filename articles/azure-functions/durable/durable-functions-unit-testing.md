---
title: Azure Durable Functions 단위 테스트
description: Durable Functions를 단위 테스트하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: 86733f8b5b80799bad3e52c643ed27465dfc7641
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231219"
---
# <a name="durable-functions-unit-testing"></a>Durable Functions 단위 테스트

단위 테스트는 최신 소프트웨어 개발 방법의 중요한 부분입니다. 단위 테스트는 비즈니스 논리 동작을 확인하고 향후 중요하지 않은 변경 내용을 도입하지 못하게 방지합니다. Durable Functions는 복잡성이 쉽게 증가할 수 있으므로 단위 테스트를 도입하면 변경 내용을 방지하는 데 도움이 됩니다. The following sections explain how to unit test the three function types - Orchestration client, orchestrator, and activity functions.

> [!NOTE]
> This article provides guidance for unit testing for Durable Functions apps targeting Durable Functions 1.x. It has not yet been updated to account for changes introduced in Durable Functions 2.x. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

## <a name="prerequisites"></a>전제 조건

이 문서의 예제를 살펴보려면 다음과 같은 개념과 프레임워크에 대한 지식이 필요합니다.

* 단위 테스트

* 지속성 함수

* [xUnit](https://xunit.github.io/) - 프레임워크 테스트

* [moq](https://github.com/moq/moq4) - 모의 프레임워크 만들기

## <a name="base-classes-for-mocking"></a>모의 동작에 대한 기본 클래스

Mocking is supported via three abstract classes in Durable Functions 1.x:

* `DurableOrchestrationClientBase`

* `DurableOrchestrationContextBase`

* `DurableActivityContextBase`

These classes are base classes for `DurableOrchestrationClient`, `DurableOrchestrationContext`, and `DurableActivityContext` that define Orchestration Client, Orchestrator, and Activity methods. 모의 동작은 기본 클래스 메서드에 대한 예상 동작을 설명하므로 단위 테스트로 비즈니스 논리를 확인할 수 있습니다. 오케스트레이션 클라이언트 및 오케스트레이터에서 비즈니스 논리를 단위 테스트하는 2단계 워크플로가 있습니다.

1. Use the base classes instead of the concrete implementation when defining orchestration client and orchestrator function signatures.
2. 단위 테스트에서 모의 기본 클래스 동작을 만들어서 비즈니스 논리를 확인합니다.

자세한 내용은 오케스트레이션 클라이언트 바인딩 및 오케스트레이터 트리거 바인딩을 사용하는 함수를 테스트하는 방법에 대한 다음 그래프를 참조하세요.

## <a name="unit-testing-trigger-functions"></a>트리거 함수 단위 테스트

이 섹션에서는 단위 테스트로 새 오케스트레이션을 시작하는 다음 HTTP 트리거 함수의 논리를 확인합니다.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

단위 테스트 작업에서는 응답 페이로드에 제공되는 `Retry-After` 헤더의 값을 확인합니다. So the unit test will mock some of `DurableOrchestrationClientBase` methods to ensure predictable behavior.

First, a mock of the base class is required, `DurableOrchestrationClientBase`. The mock can be a new class that implements `DurableOrchestrationClientBase`. 그러나 [moq](https://github.com/moq/moq4) 같은 모의 프레임워크를 사용하면 프로세스가 간단해집니다.

```csharp
    // Mock DurableOrchestrationClientBase
    var durableOrchestrationClientBaseMock = new Mock<DurableOrchestrationClientBase>();
```

그런 후 잘 알려진 인스턴스 ID를 반환하는 모의 `StartNewAsync` 메서드를 만듭니다.

```csharp
    // Mock StartNewAsync method
    durableOrchestrationClientBaseMock.
        Setup(x => x.StartNewAsync(functionName, It.IsAny<object>())).
        ReturnsAsync(instanceId);
```

다음으로 항상 빈 HTTP 200 응답을 반환하는 모의 `CreateCheckStatusResponse`를 만듭니다.

```csharp
    // Mock CreateCheckStatusResponse method
    durableOrchestrationClientBaseMock
        .Setup(x => x.CreateCheckStatusResponse(It.IsAny<HttpRequestMessage>(), instanceId))
        .Returns(new HttpResponseMessage
        {
            StatusCode = HttpStatusCode.OK,
            Content = new StringContent(string.Empty),
            Headers =
            {
                RetryAfter = new RetryConditionHeaderValue(TimeSpan.FromSeconds(10))
            }
        });
```

모의 `ILogger`도 만듭니다.

```csharp
    // Mock ILogger
    var loggerMock = new Mock<ILogger>();
```  

이제 `Run` 메서드는 단위 테스트에서 호출됩니다.

```csharp
    // Call Orchestration trigger function
    var result = await HttpStart.Run(
        new HttpRequestMessage()
        {
            Content = new StringContent("{}", Encoding.UTF8, "application/json"),
            RequestUri = new Uri("http://localhost:7071/orchestrators/E1_HelloSequence"),
        },
        durableOrchestrationClientBaseMock.Object,
        functionName,
        loggerMock.Object);
 ```

 마지막 단계는 출력을 예상 값과 비교하는 것입니다.

```csharp
    // Validate that output is not null
    Assert.NotNull(result.Headers.RetryAfter);

    // Validate output's Retry-After header value
    Assert.Equal(TimeSpan.FromSeconds(10), result.Headers.RetryAfter.Delta);
```

모든 단계를 결합하면 단위 테스트가 다음과 같은 코드를 갖게 됩니다.

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HttpStartTests.cs)]

## <a name="unit-testing-orchestrator-functions"></a>오케스트레이터 함수 단위 테스트

오케스트레이터 함수는 일반적으로 훨씬 많은 비즈니스 논리를 갖고 있기 때문에 훨씬 흥미로운 단위 테스트 대상입니다.

이 섹션에서는 단위 테스트로 `E1_HelloSequence` 오케스트레이터 함수 출력의 유효성을 검사합니다.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

단위 테스트 코드는 가장 먼저 모의 동작을 만듭니다.

```csharp
    var durableOrchestrationContextMock = new Mock<DurableOrchestrationContextBase>();
```

그런 후 모의 작업 메서드 호출을 만듭니다.

```csharp
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Tokyo")).ReturnsAsync("Hello Tokyo!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Seattle")).ReturnsAsync("Hello Seattle!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "London")).ReturnsAsync("Hello London!");
```

다음으로 단위 테스트에서 `HelloSequence.Run` 메서드를 호출합니다.

```csharp
    var result = await HelloSequence.Run(durableOrchestrationContextMock.Object);
```

마지막으로 출력의 유효성을 검사합니다.

```csharp
    Assert.Equal(3, result.Count);
    Assert.Equal("Hello Tokyo!", result[0]);
    Assert.Equal("Hello Seattle!", result[1]);
    Assert.Equal("Hello London!", result[2]);
```

모든 단계를 결합하면 단위 테스트가 다음과 같은 코드를 갖게 됩니다.

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceOrchestratorTests.cs)]

## <a name="unit-testing-activity-functions"></a>작업 함수 단위 테스트

작업 함수는 비 내구성 함수와 동일한 방식으로 단위 테스트할 수 있습니다.

이 섹션에서는 단위 테스트로 `E1_SayHello` 작업 함수 동작의 유효성을 검사합니다.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

그리고 단위 테스트에서 출력의 형식을 확인합니다. The unit tests can use the parameter types directly or mock `DurableActivityContextBase` class:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceActivityTests.cs)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [xUnit에 대해 자세히 알아보기](https://xunit.github.io/docs/getting-started-dotnet-core)
> 
> [moq에 대해 자세히 알아보기 ](https://github.com/Moq/moq4/wiki/Quickstart)
