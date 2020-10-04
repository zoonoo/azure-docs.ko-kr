---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 0e23e537043664929bdc1a3636de359953b66db6
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2020
ms.locfileid: "91711401"
---
| 속성 | Description |
|:--- |:---|
|**time** | 스토리지에서 요청을 수신한 UTC(협정 세계시) 시간입니다. 예: `2018/11/08 21:09:36.6900118`|
|**resourceId** | 스토리지 계정의 리소스 ID입니다. 예: `/subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/`<br>`myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/storageAccounts/blobServices/default`|
|**category** | 요청된 작업의 범주입니다. 예: `StorageRead`, `StorageWrite` 또는 `StorageDelete`.|
|**operationName** | 수행된 REST 작업의 유형입니다. <br> 전체 작업 목록은 [스토리지 분석 기록 작업 및 상태 메시지 토픽](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)을 참조하세요. |
|**operationVersion** | 요청 시 지정된 스토리지 서비스 버전입니다. 이는 **x-ms-version** 헤더의 값과 같습니다. 예: `2017-04-17`|
|**schemaVersion** | 로그의 스키마 버전입니다. 예: `1.0`|
|**statusCode** | 요청의 HTTP 상태 코드입니다. 요청이 중단되면 이 값이 `Unknown`으로 설정될 수 있습니다. <br> 예: `206` |
|**statusText** | 요청된 작업의 상태입니다.  전체 상태 메시지 목록은 [스토리지 분석 기록 작업 및 상태 메시지 토픽](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)을 참조하세요. 버전 2017-04-17 이상에서는 상태 메시지 `ClientOtherError`가 사용되지 않습니다. 그 대신 이 필드가 오류 코드를 포함합니다. 예: `SASSuccess`  |
|**durationMs** | 요청된 작업을 수행하는 데 걸린 총 시간으로 밀리초 단위입니다. 여기에는 수신 요청을 읽는 시간과 요청자에게 응답을 보내는 시간이 포함됩니다. 예: `12`|
|**callerIpAddress** | 요청자의 IP 주소(포트 번호 포함)입니다. 예: `192.100.0.102:4362` |
|**correlationId** | 여러 리소스에서 로그를 상호 연결하는 데 사용되는 ID입니다. 예: `b99ba45e-a01e-0042-4ea6-772bbb000000` |
|**location** | 스토리지 계정의 위치입니다. 예: `North Europe` |
|**protocol**|작업에 사용되는 프로토콜입니다. 예: `HTTP`, `HTTPS`, `SMB` 또는 `NFS`|
| **uri** | 요청된 URI(Uniform Resource Identifier)입니다. |