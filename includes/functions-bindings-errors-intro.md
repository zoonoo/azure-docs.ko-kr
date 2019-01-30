---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: c1784111cd2fc2c93b67510f310b9e513cf2b86e
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52978775"
---
Azure Functions [트리거 및 바인딩](../articles/azure-functions/functions-triggers-bindings.md)은 다양한 Azure 서비스와 통신합니다. 이러한 서비스와 통합하는 경우 기본 Azure 서비스의 API에서 발생하는 오류가 있을 수 있습니다. REST 또는 클라이언트 라이브러리를 사용하여 함수 코드에서 다른 서비스와 통신을 시도하는 경우에도 오류가 발생할 수 있습니다. 데이터 손실을 방지하고 함수의 올바른 작동을 보장하려면 두 가지 원본 중 한 곳에서 오류를 처리하는 것이 중요합니다.

다음 트리거에는 기본 제공 재시도 지원이 있습니다.

* [Azure Blob 저장소](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Azure Queue 저장소](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Azure Service Bus(큐/토픽)](../articles/azure-functions/functions-bindings-service-bus.md)

기본적으로 이러한 트리거는 최대 5번까지 재시도됩니다. 5번째 재시도 후에 이러한 트리거는 특별 [포이즌 큐](../articles/azure-functions/functions-bindings-storage-queue.md#trigger---poison-messages)에 메시지를 씁니다.

다른 함수 트리거는 함수 실행 중 오류가 발생할 때 재시도가 기본 제공되지 않습니다. 함수에 오류가 발생하는 경우 트리거 정보가 손실되지 않도록 하려면, 함수 코드에서 try-catch 블록을 사용하여 오류를 캐치하는 것이 좋습니다. 오류가 발생하면 트리거에 의해 함수로 전달된 정보를 특별 "포이즌" 메시지 큐에 씁니다. 이 방식은 [Blob Storage 트리거](../articles/azure-functions/functions-bindings-storage-blob.md#trigger---poison-blobs)에 사용되는 방식과 동일합니다.

이런 방식으로, 오류로 인해 손실될 수 있는 트리거 이벤트를 캡처하고 저장된 정보를 사용하여 포이즌 큐의 메시지를 처리하는 다른 함수를 사용하여 나중에 트리거 이벤트를 다시 시도할 수 있습니다.  
