---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 629de079f7cc7d95d10f8ff951a47b8b8fc62dad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77474136"
---
Azure 함수에서 발생하는 오류는 다음 의 출처에서 비롯될 수 있습니다.

- 기본 제공 Azure Functions [트리거 및 바인딩 사용](..\articles\azure-functions\functions-triggers-bindings.md)
- 기본 Azure 서비스의 API 에 대한 호출
- REST 끝점으로 의 호출
- 클라이언트 라이브러리, 패키지 또는 타사 API에 대한 호출

데이터 손실이나 누락된 메시지를 방지하려면 견고한 오류 처리 방법을 따르는 것이 중요합니다. 권장오류 처리 방법은 다음과 같은 작업을 포함합니다.

- [Application Insights 사용](../articles/azure-functions/functions-monitoring.md)
- [구조화 된 오류 처리 사용](#use-structured-error-handling)
- [이뎀포테니시 디자인](../articles/azure-functions/functions-idempotent.md)
- [재시도 정책 구현(해당하는](../articles/azure-functions/functions-reliable-event-processing.md) 경우)

### <a name="use-structured-error-handling"></a>구조화 된 오류 처리 사용

오류를 캡처하고 게시하는 것은 응용 프로그램의 상태를 모니터링하는 데 매우 중요합니다. 모든 함수 코드의 최상위 수준에는 try/catch 블록이 포함되어야 합니다. catch 블록에서 오류를 캡처하고 게시할 수 있습니다.

### <a name="retry-support"></a>재시도 지원

다음 트리거에는 기본 제공 재시도 지원이 있습니다.

* [Azure Blob 스토리지](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Azure 큐 저장소](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Azure Service Bus(큐/토픽)](../articles/azure-functions/functions-bindings-service-bus.md)

기본적으로 이러한 트리거는 요청을 최대 5회까지 다시 시도합니다. 다섯 번째 다시 시도 후 Azure 큐 저장소 및 Azure Service Bus 트리거 모두 [포이즌 큐에](..\articles\azure-functions\functions-bindings-storage-queue-trigger.md#poison-messages)메시지를 작성합니다.

다른 트리거 또는 바인딩 형식에 대해 다시 시도 정책을 수동으로 구현해야 합니다. 수동 구현에는 [포이즌 메시지 큐에](..\articles\azure-functions\functions-bindings-storage-blob-trigger.md#poison-blobs)오류 정보를 작성하는 것이 포함될 수 있습니다. 포이즌 큐에 편지를 쓰면 나중에 작업을 다시 시도할 수 있습니다. 이 방식은 Blob Storage 트리거에 사용되는 방식과 동일합니다.
