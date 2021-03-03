---
title: Azure Notification Hubs 진단 로그 | Microsoft Docs
description: 이 문서에서는 Azure Notification Hubs에 사용할 수 있는 모든 운영 및 진단 로그에 대 한 개요를 제공 합니다.
author: brannon
ms.author: brjones
ms.service: notification-hubs
ms.topic: article
ms.date: 01/29/2021
ms.openlocfilehash: b98a04a70062461cec603bea83052c4f1224819e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101736239"
---
# <a name="enable-diagnostics-logs-for-notification-hubs"></a>Notification Hubs에 대 한 진단 로그 사용

Azure Notification Hubs 네임 스페이스 사용을 시작 하는 경우 네임 스페이스를 생성, 삭제 또는 액세스 하는 방법과 시기를 모니터링할 수 있습니다. 이 문서에서는 사용 가능한 모든 운영 및 진단 로그에 대 한 개요를 제공 합니다.

Azure Notification Hubs는 현재 Azure Notification Hubs 네임 스페이스에서 수행 되는 *관리 작업* 을 캡처하는 활동 및 작업 로그를 지원 합니다.

## <a name="diagnostic-logs-schema"></a>진단 로그 스키마

모든 로그는 다음 두 위치에서 JavaScript Object Notation (JSON) 형식으로 저장 됩니다.

- **Azureactivity**: Azure Portal 또는 Azure Resource Manager 템플릿 배포를 통해 네임 스페이스에 대해 수행 되는 작업 및 작업의 로그를 표시 합니다.
- **Azurediagnostics**: API를 사용 하거나 언어 SDK의 관리 클라이언트를 통해 네임 스페이스에 대해 수행 되는 작업 및 작업의 로그를 표시 합니다.

진단 로그 JSON 문자열에는 다음 표에 나열 된 요소가 포함 됩니다.

| 속성 | Description |
| ------- | ------- |
| time | 로그의 UTC 타임 스탬프 |
| resourceId | Azure 리소스에 대 한 상대 경로 |
| operationName | 관리 작업의 이름입니다. |
| category | 로그 범주. 유효한 값: `OperationalLogs` |
| callerIdentity | 관리 작업을 시작한 호출자의 id |
| resultType | 관리 작업의 상태입니다. 유효한 값: `Succeeded` 또는 `Failed` |
| resultDescription | 관리 작업에 대 한 설명 |
| correlationId | 관리 작업의 상관 관계 ID (지정 된 경우) |
| callerIpAddress | 호출자 IP 주소입니다. Azure Portal에서 시작 된 호출의 경우 비어 있습니다. |

작업 로그 JSON 문자열 예제는 다음과 같습니다.

```json
{
    "operationName": "Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action",
    "resourceId": "/SUBSCRIPTIONS/2CAC2A14-BA6B-46A6-BCE8-2D9781A41BA2/RESOURCEGROUPS/SAMPLES/PROVIDERS/MICROSOFT.NOTIFICATIONHUBS/NAMESPACES/SAMPLE-NS",
    "time": "1/1/2021 5:16:32 AM +00:00",
    "category": "OperationalLogs",
    "resultType": "Succeeded",
    "resultDescription": "Gets Hub Authorization Rules",
    "correlationId": "00000000-0000-0000-0000-000000000000",
    "callerIdentity": "{ \"identityType\": \"Portal\", \"identity\": \"\" }"
}
```

`callerIdentity`필드는 비워 두거나 다음 형식 중 하나를 사용 하는 JSON 문자열을 사용할 수 있습니다.

Azure Portal에서 시작 되는 호출의 경우 `identity` 필드가 비어 있습니다. 로그를 활동 로그와 상호 연결 하 여 로그인 한 사용자를 확인할 수 있습니다.
```json
{
    "identityType": "Portal",
    "identity": ""
}
```

Azure Resource Manager를 통해 수행 된 호출의 경우에는 `identity` 로그인 한 사용자의 사용자 이름이 필드에 포함 됩니다.
```json
{
   "identityType": "Username",
   "identity": "test@foo.com"
}
```

REST API Notification Hubs에 대 한 호출의 경우 `identity` 이 필드에는 SharedAccessSignature 토큰을 생성 하는 데 사용 되는 액세스 정책의 이름이 포함 됩니다.
```json
{
   "identityType": "KeyName",
   "identity": "SharedAccessRootKey2"
}
```

## <a name="events-and-operations-captured-in-operational-logs"></a>작업 로그에 캡처되는 이벤트 및 작업

작업 로그는 Azure Notification Hubs 네임 스페이스에서 수행 되는 모든 관리 작업을 캡처합니다. Azure Notification Hubs에서 수행 되는 대량의 데이터 작업으로 인해 데이터 작업이 캡처되지 않습니다.

다음 관리 작업은 작업 로그에 캡처됩니다. 

| Scope | 작업 이름 | 작업 설명 |
| :-- | :-- | :-- |
| 네임스페이스 | Microsoft.NotificationHubs/Namespaces/authorizationRules/action | 권한 부여 규칙 나열 |
| 네임스페이스 | Microsoft.NotificationHubs/Namespaces/authorizationRules/delete | 권한 부여 규칙 삭제 |
| 네임스페이스 | Microsoft.NotificationHubs/Namespaces/authorizationRules/listkeys/action | 키를 나열합니다. |
| 네임스페이스 | Microsoft.NotificationHubs/Namespaces/authorizationRules/read | 권한 부여 규칙 가져오기 |
| 네임스페이스 | Microsoft.NotificationHubs/Namespaces/authorizationRules/regenerateKeys/action | 키 다시 생성 |
| 네임스페이스 | Microsoft.NotificationHubs/Namespaces/authorizationRules/write | 권한 부여 규칙 만들기 또는 업데이트 |
| 네임스페이스 | Microsoft. NotificationHubs/네임 스페이스/삭제 | 네임스페이스 삭제 |
| 네임스페이스 | Microsoft.NotificationHubs/Namespaces/read | 네임 스페이스 가져오기 |
| 네임스페이스 | Microsoft.NotificationHubs/Namespaces/write | 네임 스페이스 만들기 또는 업데이트 |
| 알림 허브 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action | 권한 부여 규칙 나열 |
| 알림 허브 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/delete | 권한 부여 규칙 삭제 |
| 알림 허브 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/listkeys/action | 키를 나열합니다. |
| 알림 허브 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/read | 권한 부여 규칙 읽기 |
| 알림 허브 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action | 키 다시 생성 |
| 알림 허브 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/write | 권한 부여 규칙 만들기 또는 업데이트 |
| 알림 허브 | Microsoft. NotificationHubs/네임 스페이스/NotificationHubs/delete | 알림 허브 삭제 |
| 알림 허브 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/action | PNS 자격 증명 만들기, 업데이트 또는 가져오기 |
| 알림 허브 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/read | 알림 허브 가져오기 |
| 알림 허브 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/write | 알림 허브 만들기 또는 업데이트 |

## <a name="enable-operational-logs"></a>작업 로그 사용

작업 로그는 기본적으로 사용 되지 않습니다. 로그를 사용 하도록 설정 하려면 다음을 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에서 Azure Notification Hubs 네임 스페이스로 이동한 후 **모니터링** 아래에서 **진단 설정** 을 선택 합니다.

   !["진단 설정" 링크](./media/notification-hubs-diagnostic-logs/image-1.png)

1. **진단 설정** 창에서 **진단 설정 추가** 를 선택 합니다.  

   !["진단 설정 추가" 링크](./media/notification-hubs-diagnostic-logs/image-2.png)

1. 다음을 수행 하 여 진단 설정을 구성 합니다.

   a. **이름** 상자에 진단 설정의 이름을 입력합니다.  

   b. 진단 로그에 다음 세 가지 대상 중 하나를 선택합니다.  
   - **Log Analytics 작업 영역에 보내기** 를 선택 하는 경우 진단이 전송 될 Log Analytics 인스턴스를 지정 해야 합니다.  
   - **저장소 계정에 보관** 을 선택 하는 경우 진단 로그가 저장 되는 저장소 계정을 구성 해야 합니다.  
   - **이벤트 허브로 스트림** 을 선택 하는 경우 진단 로그를 스트리밍할 이벤트 허브를 구성 해야 합니다.

   다. **OperationalLogs** 확인란을 선택 합니다.

    !["진단 설정" 창](./media/notification-hubs-diagnostic-logs/image-3.png)

1. **저장** 을 선택합니다.

새 설정은 약 10분 후에 적용됩니다. 구성된 보관 대상의 **진단 로그** 창에 로그가 표시됩니다.

## <a name="next-steps"></a>다음 단계

진단 설정 구성에 대 한 자세한 내용은 다음을 참조 하세요.
* [Azure 진단 로그에 대 한 개요](../azure-monitor/essentials/platform-logs-overview.md)입니다.

Azure Notification Hubs에 대 한 자세한 내용은 다음을 참조 하세요.
* [Azure Notification Hubs란 무엇인가요?](notification-hubs-push-notification-overview.md)