---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 07780c94960c581ce69e4fdd45b346c75ab8b098
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329538"
---
### <a name="set-the-storage-account-connection"></a>Storage 계정 연결 설정

local.settings.json 파일을 열고, Storage 계정 연결 문자열인 `AzureWebJobsStorage` 값을 복사합니다. 다음 Bash 명령을 사용하여 `AZURE_STORAGE_CONNECTION_STRING` 환경 변수를 연결 문자열로 설정합니다.

```azurecli-interactive
AZURE_STORAGE_CONNECTION_STRING="<STORAGE_CONNECTION_STRING>"
```

연결 문자열이 `AZURE_STORAGE_CONNECTION_STRING` 환경 변수에 설정되면 매번 인증하지 않고도 Storage 계정에 액세스할 수 있습니다.