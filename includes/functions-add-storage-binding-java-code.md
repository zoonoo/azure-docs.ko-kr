---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/18/2020
ms.author: glenga
ms.openlocfilehash: f4f109a6da0cb75555f9aadfa2fff31b8aa3107d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "80673449"
---
이제 새 `msg` 매개 변수를 사용하여 함수 코드에서 출력 바인딩에 쓸 수 있습니다. 성공 응답 앞에 다음 코드 줄을 추가하여 `name` 값을 `msg` 출력 바인딩에 추가합니다.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="33":::

출력 바인딩을 사용하면 인증하거나, 큐 참조를 가져오거나, 데이터를 쓰는 데 Azure Storage SDK 코드를 사용할 필요가 없습니다. Functions 런타임 및 큐 출력 바인딩이 이러한 작업을 알아서 처리합니다.

이제 `run` 메서드는 다음 예제와 같이 표시됩니다.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="16-37":::