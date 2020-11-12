---
author: baanders
description: Azure Digital Twins에 대 한 포함 파일-인스턴스를 관리 하는 방법
ms.service: digital-twins
ms.topic: include
ms.date: 11/11/2020
ms.author: baanders
ms.openlocfilehash: 47de00780fb259e7476c16dea9b7c63bc512a211
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94533895"
---
이 문서에서는 [Azure Digital Twins .net (c #) **SDK**](/dotnet/api/overview/azure/digitaltwins/management?view=azure-dotnet&preserve-view=true)를 사용 하 여 다양 한 관리 작업을 완료 하는 방법을 중점적으로 설명 합니다. [*방법: Azure Digital Twins api 및 Sdk 사용*](../articles/digital-twins/how-to-use-apis-sdks.md)에 설명 된 다른 언어 sdk를 사용 하 여 동일한 관리 호출을 만들 수도 있습니다.

> [!TIP] 
> 모든 SDK 메서드는 동기 및 비동기 버전으로 제공 됩니다. 페이징 호출의 경우 비동기 메서드는 `AsyncPageable<T>` 동기 버전이 반환 되는 동안를 반환 합니다 `Pageable<T>` .

또 다른 관리 옵션은이 토픽 영역에 대 한 Azure Digital Twins [**REST api**](/rest/api/azure-digitaltwins/) 를 직접 호출 하는 것입니다.

마지막으로, Azure Digital Twins **CLI** 를 사용 하 여 동일한 관리 작업을 완료할 수 있습니다. CLI를 사용 하는 방법에 대 한 자세한 내용은 [*방법: Azure Digital Twins CLI 사용*](../articles/digital-twins/how-to-use-cli.md)을 참조 하세요.