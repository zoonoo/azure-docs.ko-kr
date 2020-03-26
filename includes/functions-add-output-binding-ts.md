---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/11/2020
ms.author: glenga
ms.openlocfilehash: dab5f0f24fa1f36b611eb79329336832d8a4b3cb
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2020
ms.locfileid: "78191060"
---
`msg`에서 `context.bindings` 출력 바인딩 개체를 사용하여 큐 메시지를 만드는 코드를 추가합니다. `context.res` 문 앞에 이 코드를 추가합니다.

:::code language="typescript" range="10" source="~/functions-docs-typescript/functions-add-output-binding-storage-queue-cli/HttpExample/index.ts":::

이 시점에서 함수는 다음과 같이 표시됩니다.

:::code language="typescript" source="~/functions-docs-typescript/functions-add-output-binding-storage-queue-cli/HttpExample/index.ts":::