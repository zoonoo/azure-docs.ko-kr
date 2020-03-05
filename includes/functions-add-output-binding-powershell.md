---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/19/2020
ms.author: glenga
ms.openlocfilehash: 753a3d16792e0dbaffe318cd188ea3f0d40bd5cb
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78191059"
---
`Push-OutputBinding` cmdlet을 사용하여 `msg` 출력 바인딩을 통해 큐에 텍스트를 작성하는 코드를 추가합니다. `if` 문에서 확인 상태를 설정하기 전에 이 코드를 추가합니다.

:::code language="powershell" range="18-19" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/run.ps1":::

이 시점에서 함수는 다음과 같이 표시됩니다.

:::code language="powershell" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/run.ps1":::
