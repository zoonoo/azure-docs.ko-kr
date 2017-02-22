---
title: "Azure Functions에 대한 모범 사례 | Microsoft Docs"
description: "Azure Functions에 대한 모범 사례 및 패턴에 알아봅니다."
services: functions
documentationcenter: na
author: wesmc7777
manager: erikre
editor: 
tags: 
keywords: "Azure Functions, 패턴, 모범 사례, 함수, 이벤트 처리, webhook, 동적 계산, 서버가 없는 아키텍처"
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/09/2016
ms.author: wesmc
translationtype: Human Translation
ms.sourcegitcommit: 182e28e37eb56c547e28524f2a3e13f042238cb4
ms.openlocfilehash: c638bf42b9adf906f195d77268637d056f7b00a9

---

# <a name="best-practices-for-azure-functions"></a>Azure Functions에 대한 모범 사례

##<a name="overview"></a>개요

이 문서에서는 함수 앱 구현을 고려하는 사용자를 위해 여러 모범 사례를 모았습니다. Azure 함수 앱이 Azure App Service라는 것을 염두에 두어야 합니다. 그래야 이러한 모범 사례가 적용됩니다.


## <a name="avoid-large-long-running-functions"></a>큰 장기 실행 함수를 방지

큰 장기 실행 함수는 예기치 않은 시간 초과 문제를 발생시킬 수 있습니다. 함수는 많은 Node.js 종속성으로 인해 커질 수 있습니다. 이러한 종속성을 가져오면 로드 시간이 높아져 예기치 않은 제한 시간으로 이어질 수 있습니다. Node.js 종속성은 사용자 코드에서 여러 `require()` 문에 의해 명백하게 로드될 수 있습니다. 또한 자체적으로 내부 종속성을 갖는 사용자 코드에 의해 로드되는 하나의 모듈에 따라 암시적일 수 있습니다.  

큰 함수를 더 작은 함수 집합으로 리팩터링할 때마다 함께 작동하고 빠른 응답을 반환합니다. 예를 들어 webhook 또는 HTTP 트리거 함수는 특정 시간 제한 내의 승인 응답이 필요할 수 있습니다. HTTP 트리거 페이로드를 큐 트리거 함수에 의해 처리되도록 큐에 전달할 수 있습니다. 이 방법은 실제 작업을 연기하고 즉각적인 응답을 반환할 수 있습니다. webhook의 경우 즉각적인 응답을 요구하는 것이 일반적입니다.


## <a name="cross-function-communication"></a>함수 통신을 교차.

여러 기능을 통합할 때 함수 통신 교차를 위해 저장소 큐를 사용하는 것이 일반적으로 가장 좋은 방법입니다.  주요 이유는 저장소 큐는 더 저렴하고 프로비전하는 것이 훨씬 쉽습니다. 

저장소 큐에 있는 개별 메시지 크기는 64KB로 제한됩니다. 함수 간에 더 큰 메시지를 전달해야 하는 경우 Azure Service Bus 큐를 사용하여 최대 256KB의 메시지 크기를 지원할 수 있습니다.

Service Bus 토픽은 메시지를 처리하기 전에 필터링해야 하는 경우에 유용합니다.

이벤트 허브는 고용량 통신을 지원하는 데 유용합니다.



## <a name="write-functions-to-be-stateless"></a>상태 비저장 함수 작성 

함수는 가능하면 상태 비저장이며 idempotent여야 합니다. 필요한 모든 상태 정보를 사용자 데이터에 연결합니다. 예를 들어 처리할 주문에 연결된 `state` 멤버가 있을 수 있습니다. 함수는 주문을 해당 상태에 따라 처리하며 함수 자체는 비저장 상태로 남아 있을 수 있습니다. 

Idempotent 함수는 특히 타이머 트리거 사용이 권장됩니다. 예를 들어 반드시 하루에 한 번 실행해야 하는 내용이 있는 경우 하루 동안 언제든지 같은 결과로 실행할 수 있도록 작성합니다. 함수는 특정 날짜에 대한 작업이 없는 경우 종료될 수 있습니다. 또한 이전 실행이 완료되지 못한 경우 다음 실행은 중단되었던 부분으로 돌아가야 합니다.


## <a name="write-defensive-functions"></a>방어적 함수 작성.

함수에는 언제든지 예외가 발생할 수 있다고 가정합니다. 다음 실행 동안 이전 실패 지점에서 계속할 수 있는 기능을 넣어 함수를 설계합니다. 다음과 같은 작업이 필요한 시나리오를 고려하세요.

1. Db의 10,000개 행에 대해 쿼리합니다.
2. 앞으로 처리할 각 행에 대한 큐 메시지를 만듭니다.
 
시스템의 복잡성에 따라 잘못 작동되는 관련된 다운스트림 서비스, 네트워킹 작동 중단 또는 할당량 제한 초과 등이 있을 수 있습니다. 이러한 항목은 모두 언제든지 함수에 영향을 줄 수 있습니다. 함수가 이에 대비할 수 있도록 설계해야 합니다.

이러한 항목 중 5,000개를 처리를 위해 큐에 삽입한 후 오류가 발생한다면 코드는 어떻게 반응할까요? 사용자가 완료한 집합에서 항목을 추적합니다. 다른 방법으로 다음 번에 해당 항목을 삽입할 수도 있습니다. 이는 작업 흐름에 심각한 영향을 미칠 수 있습니다. 

큐 항목을 이미 처리한 경우 함수는 수행되지 않습니다.

Azure Functions 플랫폼에서 사용하는 구성 요소를 위해 이미 제공된 방어 수단을 활용하세요. 예를 들어 [Azure Storage 큐 트리거](functions-bindings-storage-queue.md#trigger)를 위한 설명서에서 **포이즌 큐 메시지 처리**를 참조하세요.
 



## <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>동일한 함수 앱에서 테스트와 프로덕션 코드의 혼합 금지.

함수 앱 공유 리소스 내에서 작용합니다. 예를 들어 메모리는 공유됩니다. 프로덕션 환경에서 함수 앱을 사용하는 경우 테스트 관련 함수 및 리소스를 추가하지 마세요. 프로덕션 코드를 실행하는 동안 예기치 않은 오버 헤드가 발생할 수 있습니다.

프로덕션 함수 앱에서 로드하는 것에 주의하세요. 메모리는 앱에서 각 함수가 사용하는 것의 평균으로 계산됩니다.

여러.Net 함수에서 참조되는 공유 어셈블리가 있는 경우 일반적인 공유 폴더에 저장합니다. 다음 예제와 비슷한 문을 사용하여 어셈블리를 참조합니다. 

    #r "..\Shared\MyAssembly.dll". 

그렇지 않으면 함수 간 다르게 작동하는 동일한 바이너리의 여러 테스트 버전이 실수로 배포되기 쉽습니다.

프로덕션 코드에 너무 많은 로깅을 사용하지 마세요. 부정적인 영향을 미칩니다.



## <a name="use-async-code-but-avoid-taskresult"></a>비동기 코드는 사용, Task.Result는 방지

비동기 프로그래밍은 권장되는 최상의 방법입니다. 단, 항상 `Task.Result` 속성 참조는 피하세요. 기본적으로 이 방법은 다른 스레드의 잠금에 대한 사용 대기를 수행합니다. 잠금을 유지하면 교착 상태가 발생할 가능성이 있습니다.




## <a name="next-steps"></a>다음 단계
자세한 내용은 다음 리소스를 참조하세요.

* [Azure Functions 개발자 참조](functions-reference.md)
* [Azure Functions C# 개발자 참조](functions-reference-csharp.md)
* [Azure Functions F# 개발자 참조](functions-reference-fsharp.md)
* [Azure Functions NodeJS 개발자 참조](functions-reference-node.md)




<!--HONumber=Feb17_HO2-->


