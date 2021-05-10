---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 5e99c6e795729426964ec1f89f5f8c904e330227
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99493395"
---
`context.bindings`에서 `msg` 출력 바인딩 개체를 사용하여 큐 메시지를 만드는 코드를 추가합니다. `context.res` 문 앞에 이 코드를 추가합니다.

:::code language="javascript" range="5-7" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/index.js":::

이 시점에서 함수는 다음과 같이 표시됩니다.

:::code language="javascript" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/index.js":::