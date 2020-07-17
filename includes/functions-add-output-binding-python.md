---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 8bdd8b9d900cc50fdeb34ff7d233ac4d7e17a45c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2020
ms.locfileid: "78191058"
---
다음 코드와 일치하도록 *HttpExample\\\_\_init\_\_.py*를 업데이트하여 `msg` 매개 변수를 함수 정의에 추가하고 `if name:` 문 아래에 `msg.set(name)`을 추가합니다.

:::code language="python" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

`msg` 매개 변수는 [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest)의 인스턴스입니다. `set` 메서드는 문자열 메시지를 큐에 씁니다. 이 경우 URL 쿼리 문자열의 함수에 전달된 이름입니다.
