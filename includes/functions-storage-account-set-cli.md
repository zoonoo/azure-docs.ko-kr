---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 4ace70abe0112e0fe27d177c02bcb697746c92cc
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "78262590"
---
### <a name="set-the-storage-account-connection"></a>Storage 계정 연결 설정

local.settings.json 파일을 열고, Storage 계정 연결 문자열인 `AzureWebJobsStorage` 값을 복사합니다. 다음 Bash 명령을 사용하여 `AZURE_STORAGE_CONNECTION_STRING` 환경 변수를 연결 문자열로 설정합니다.

```bash
AZURE_STORAGE_CONNECTION_STRING="<STORAGE_CONNECTION_STRING>"
```

연결 문자열이 `AZURE_STORAGE_CONNECTION_STRING` 환경 변수에 설정되면 매번 인증하지 않고도 Storage 계정에 액세스할 수 있습니다.