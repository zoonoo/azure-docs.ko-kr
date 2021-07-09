---
author: baanders
description: Azure Digital Twins용 파일 포함 - 인스턴스 관리 방법
ms.service: digital-twins
ms.topic: include
ms.date: 11/11/2020
ms.author: baanders
ms.openlocfilehash: 1f66101fc1231be2e5ce36dc348b1ca850113867
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107303767"
---
이 문서에서는 [Azure Digital Twins .NET(C#) **SDK**](/dotnet/api/overview/azure/digitaltwins/management)를 사용하여 다양한 관리 작업을 완료하는 방법을 중점적으로 설명합니다. [*방법: Azure Digital Twins API 및 SDK 사용*](../articles/digital-twins/how-to-use-apis-sdks.md)에 설명된 다른 언어 SDK를 사용하여 이와 동일한 관리 호출을 만들 수도 있습니다.

> [!TIP] 
> 모든 SDK 메서드는 동기 및 비동기 버전으로 제공됩니다. 페이징 호출의 경우 비동기 메서드는 `AsyncPageable<T>`을 반환하고 동기 버전은 `Pageable<T>`을 반환합니다.

또 다른 관리 옵션은 Postman과 같은 REST 클라이언트를 통해 이 토픽 영역에 대한 Azure Digital Twins [**REST API**](/rest/api/azure-digitaltwins/)를 직접 호출하는 것입니다. 이 작업을 수행하는 방법에 대한 지침은 [*방법: Postman을 사용하여 요청 만들기*](../articles/digital-twins/how-to-use-postman.md)를 참조하세요.

마지막으로, Azure Digital Twins **CLI** 를 사용하여 동일한 관리 작업을 완료할 수 있습니다. CLI를 사용하는 방법에 대한 자세한 내용은 [*방법: Azure Digital Twins CLI 사용*](../articles/digital-twins/how-to-use-cli.md)을 참조하세요.