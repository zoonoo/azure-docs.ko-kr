---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 894a89126d1ee3ed909134f3e0dd914166568654
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90606497"
---
큐는 [Azure Portal](../articles/storage/queues/storage-quickstart-queues-portal.md) 또는 [Microsoft Azure Storage Explorer](https://storageexplorer.com/)에서 볼 수 있습니다. 또한 다음 단계에 설명된 대로 Azure CLI에서 큐를 볼 수도 있습니다.

1. 함수 프로젝트의 *local.setting.json* 파일을 열고, 연결 문자열 값을 복사합니다. 터미널 또는 명령 창에서 다음 명령을 실행하여 `AZURE_STORAGE_CONNECTION_STRING`이라는 환경 변수를 만들고, `<MY_CONNECTION_STRING>` 대신 특정 연결 문자열을 붙여넣습니다. (이 환경 변수는 `--connection-string` 인수를 사용하여 연결 문자열을 각 후속 명령에 제공할 필요가 없음을 의미합니다.)

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    export AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<MY_CONNECTION_STRING>"
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/cmd)
    
    ```azurecli
    set AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    ---
    
1. (선택 사항) [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) 명령을 사용하여 계정의 Storage 큐를 봅니다. 이 명령의 출력에는 함수에서 첫 번째 메시지를 해당 큐에 쓸 때 만들어진 `outqueue`라는 이름의 큐가 포함되어야 합니다.
    
    ```azurecli
    az storage queue list --output tsv
    ```

1. [`az storage message get`](/cli/azure/storage/message#az-storage-message-get) 명령을 사용하여 이 큐의 메시지를 읽습니다. 이 메시지는 이전에 함수를 테스트할 때 사용한 이름이어야 합니다. 명령은 큐에서 첫 번째 메시지를 읽고 제거합니다. 

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/cmd)
    
    ```azurecli
    az storage message get --queue-name outqueue -o tsv --query [].{Message:content} > %TEMP%out.b64 && certutil -decode -f %TEMP%out.b64 %TEMP%out.txt > NUL && type %TEMP%out.txt && del %TEMP%out.b64 %TEMP%out.txt /q
    ```

    이 스크립트는 certutil을 사용하여 로컬 임시 파일에서 base64로 인코딩된 메시지 컬렉션을 디코딩합니다. 출력이 없는 경우 오류가 발생하면 `> NUL`을 스크립트에서 제거하여 certutil 출력 억제를 중지해봅니다. 
    
    ---
    
    메시지 본문은 [Base64로 인코딩](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#encoding)되어 저장되기 때문에 메시지를 표시하기 전에 먼저 디코딩해야 합니다. `az storage message get`을 실행하면 메시지가 큐에서 제거됩니다. `outqueue`에 메시지가 하나만 있는 경우 이 명령을 두 번째로 실행하면 메시지가 검색되는 대신 오류가 발생합니다.
