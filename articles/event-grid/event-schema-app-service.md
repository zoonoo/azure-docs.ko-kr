---
title: Event Grid 원본으로 Azure App Service
description: 이 문서에서는 Event Grid 이벤트 원본으로 Azure App Service를 사용 하는 방법을 설명 합니다. 스키마와 자습서 및 방법 문서에 대 한 링크를 제공 합니다.
services: event-grid
author: jasonfreeberg
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: jafreebe
ms.openlocfilehash: 19cb7d7cfdb5c5ae61aba0f75d06476b40bdd6d7
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83116937"
---
# <a name="azure-app-service-as-an-event-grid-source"></a>Event Grid 원본으로 Azure App Service

이 문서에서는 Azure App Service 이벤트에 대 한 속성 및 스키마를 제공 합니다. 이벤트 스키마에 대한 소개는 [Azure Event Grid 이벤트 스키마](event-schema.md)를 참조하세요. 또한 이벤트 원본으로 Azure App Service를 사용 하는 빠른 시작 및 자습서의 목록을 제공 합니다.

## <a name="event-grid-event-schema"></a>Event Grid 이벤트 스키마

### <a name="available-event-types"></a>사용할 수 있는 이벤트 유형

Azure App Service는 다음과 같은 이벤트 유형을 내보냅니다.

|    이벤트 유형                                             |    설명                                                     |
|-----------------------------------------------------------|--------------------------------------------------------------------|
|    Microsoft. 웹/사이트. BackupOperationStarted             |    백업이 시작 될 때 트리거됨                             |
|    Microsoft. 웹/사이트. BackupOperationCompleted           |    백업이 완료 되었을 때 트리거됨                           |
|    Microsoft. 웹/사이트. BackupOperationFailed              |    백업이 실패 한 경우 트리거됨                              |
|    Microsoft. 웹/사이트. RestoreOperationStarted            |    백업에서 복원이 시작 될 때 트리거됩니다.        |
|    Microsoft. 웹/사이트. RestoreOperationCompleted          |    백업에서 복원이 완료 되 면 트리거됩니다.      |
|    Microsoft. 웹/사이트. RestoreOperationFailed             |    백업에서 복원이 실패 한 경우 트리거됨         |
|    Microsoft. 웹/사이트. SlotSwapStarted                    |    슬롯 교환이 시작 될 때 트리거됩니다.                          |
|    Microsoft. 웹/사이트. SlotSwapCompleted                  |    슬롯 교환이 완료 되 면 트리거됩니다.                      |
|    Microsoft. 웹/사이트. SlotSwapFailed                     |    슬롯 교환이 실패 한 경우 트리거됨                           |
|    Microsoft. 웹/사이트. SlotSwapWithPreviewStarted         |    미리 보기가 있는 슬롯 교환이 시작 될 때 트리거됩니다.           |
|    Microsoft. 웹/사이트. SlotSwapWithPreviewCancelled       |    미리 보기가 있는 슬롯 교환이 취소 되 면 트리거됩니다.    |
|    Microsoft. 웹/사이트. AppUpdated. 다시 시작 됨               |    사이트가 다시 시작 되 면 트리거됩니다.                      |
|    Microsoft. 웹/사이트. AppUpdated. 중지 됨                 |    사이트가 중지 될 때 트리거됨                          |
|    Microsoft. 웹/사이트. AppUpdated. ChangedAppSettings      |    사이트의 앱 설정이 변경 될 때 트리거됩니다.             |
|    Microsoft 웹/serverfarms. Appservice설계도 업데이트 됨        |    App Service 계획이 업데이트 될 때 트리거됩니다.                 |

### <a name="the-contents-of-an-event-response"></a>이벤트 응답의 내용

이벤트가 트리거될 때 Event Grid 서비스는 해당 이벤트에 대 한 데이터를 구독 끝점으로 보냅니다.
이 섹션에는 각 이벤트에 대 한 데이터가 어떻게 표시 되는지 예가 포함 되어 있습니다. 각 이벤트에는 다음과 같은 최상위 수준 데이터가 있습니다.

|     속성          |     유형     |     설명                                                                                                                                |
|-----------------------|--------------|------------------------------------------------------------------------------------------------------------------------------------------------|
|    토픽              |    문자열    |    이벤트 원본에 대 한 전체 리소스 경로입니다. 이 필드는 쓸 수 없습니다. Event Grid는 이 값을 제공합니다.                                      |
|    subject            |    문자열    |    이벤트 주체에 대 한 게시자 정의 경로입니다.                                                                                              |
|    eventType          |    문자열    |    이 이벤트 소스에 대해 등록 된 이벤트 유형 중 하나입니다.                                                                                  |
|    eventTime          |    문자열    |    공급자의 UTC 시간을 기준으로 이벤트가 생성 된 시간입니다.                                                                         |
|    id                 |    문자열    |    이벤트에 대한 고유 식별자입니다.                                                                                                            |
|    데이터               |    개체    |    Blob Storage 이벤트 데이터입니다.                                                                                                                    |
|    dataVersion        |    문자열    |    데이터 개체의 스키마 버전입니다. 게시자가 스키마 버전을 정의합니다.                                                          |
|    metadataVersion    |    문자열    |    이벤트 메타 데이터의 스키마 버전입니다. Event Grid는 최상위 속성의 스키마를 정의합니다. Event Grid에서이 값을 제공 합니다.    |

#### <a name="backupoperationstarted-backupoperationcompleted-backupoperationfailed"></a>BackupOperationStarted, BackupOperationCompleted, BackupOperationFailed

```js
{
    id:'7c5d6de5-eb70-4de2-b788-c52a544e68b8',
    subject:'/Microsoft.Web/sites/<site-name>',
    eventType:'Microsoft.Web.SlotSwapStarted',
    eventTime:'2020-01-28T18:26:51.7194887Z',
    data: {
        "appEventTypeDetail": { "action": "Started" },
        "siteName": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "None"
    }
    topic:'/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion:'1',
    metaDataVersion:'1'
}
```

데이터 개체에는 다음 속성이 포함 되어 있습니다.

|    속성                |    유형      |    설명                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    개체    |    앱에 대 한 작업 세부 정보                                                                                       |
|    action                  |    문자열    |    작업 동작의 유형입니다.                                                                                   |
|    이름                    |    문자열    |    이 이벤트가 발생 한 웹 사이트의 이름입니다.                                                                          |
|    clientRequestId         |    문자열    |    이 이벤트를 트리거한 사이트 API 작업에 대해 app service에서 생성 한 클라이언트 요청 id입니다.         |
|    correlationRequestId    |    문자열    |    이 이벤트를 트리거한 사이트 API 작업에 대해 app service에서 생성 한 상관 관계 요청 id입니다.    |
|    requestId               |    문자열    |    이 이벤트를 트리거한 사이트 API 작업에 대해 app service에서 생성 된 요청 id입니다.                |
|    address                 |    문자열    |    이 작업의 HTTP 요청 URL                                                                                |
|    verb                    |    문자열    |    이 작업의 HTTP 동사입니다.                                                                                       |

#### <a name="restoreoperationstarted-restoreoperationcompleted-restoreoperationfailed"></a>RestoreOperationStarted, RestoreOperationCompleted, RestoreOperationFailed

```js
{
    id:'7c5d6de5-eb70-4de2-b788-c52a544e68b8',
    subject:'/Microsoft.Web/sites/<site-name>',
    eventType:'Microsoft.Web.RestoreOperationStarted,
    eventTime:'2020-01-28T18:26:51.7194887Z',
    data: {
        "appEventTypeDetail": { 
            "action": "Started" 
        },
        "siteName": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "POST"
    }
    topic:'/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion:'1',
    metaDataVersion:'1'
}
```

데이터 개체에는 다음 속성이 포함 되어 있습니다.

|    속성                |    유형      |    설명                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    개체    |    앱에 대 한 작업 세부 정보                                                                                       |
|    action                  |    문자열    |    작업 동작의 유형입니다.                                                                                   |
|    이름                    |    문자열    |    이 이벤트가 발생 한 웹 사이트의 이름입니다.                                                                          |
|    clientRequestId         |    문자열    |    이 이벤트를 트리거한 사이트 API 작업에 대해 app service에서 생성 한 클라이언트 요청 id입니다.         |
|    correlationRequestId    |    문자열    |    이 이벤트를 트리거한 사이트 API 작업에 대해 app service에서 생성 한 상관 관계 요청 id입니다.    |
|    requestId               |    문자열    |    이 이벤트를 트리거한 사이트 API 작업에 대해 app service에서 생성 된 요청 id입니다.                |
|    address                 |    문자열    |    이 작업의 HTTP 요청 URL                                                                                |
|    verb                    |    문자열    |    이 작업의 HTTP 동사입니다.                                                                                       |

#### <a name="slotswapstarted-slotswapcompleted-slotswapfailed"></a>SlotSwapStarted, SlotSwapCompleted, SlotSwapFailed

```js
{
    id:'7c5d6de5-eb70-4de2-b788-c52a544e68b8',
    subject:'/Microsoft.Web/sites/<site-name>',
    eventType:'Microsoft.Web.SlotSwapStarted',
    eventTime:'2020-01-28T18:26:51.7194887Z',
    data:{
        appEventTypeDetail:null,
        siteName:'<site-name>',
        clientRequestId:'922f4841-20d9-4dd6-8c5b-23f0d85e5592',
        correlationRequestId:'9ac46505-2b8a-4e06-834c-05ffbe2e8c3a',
        requestId:'765117aa-eaf8-4bd2-a644-1dbf69c7b0fd',
        address: '/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production',
        verb:'POST'
        sourceSlot: "staging",
        targetSlot: "production"
    },
    topic:'/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion:'1',
    metaDataVersion:'1'
}
```

데이터 개체에는 다음 속성이 포함 되어 있습니다.

|    속성                |    유형      |    설명                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    개체    |    앱에 대 한 작업 세부 정보                                                                                       |
|    action                  |    문자열    |    작업 동작의 유형입니다.                                                                                   |
|    이름                    |    문자열    |    이 이벤트가 발생 한 웹 사이트의 이름입니다.                                                                          |
|    clientRequestId         |    문자열    |    이 이벤트를 트리거한 사이트 API 작업에 대해 app service에서 생성 한 클라이언트 요청 id입니다.         |
|    correlationRequestId    |    문자열    |    이 이벤트를 트리거한 사이트 API 작업에 대해 app service에서 생성 한 상관 관계 요청 id입니다.    |
|    requestId               |    문자열    |    이 이벤트를 트리거한 사이트 API 작업에 대해 app service에서 생성 된 요청 id입니다.                |
|    address                 |    문자열    |    이 작업의 HTTP 요청 URL                                                                                |
|    verb                    |    문자열    |    이 작업의 HTTP 동사입니다.                                                                                       |
|    Source로트              |    문자열    |    교환 원본 슬롯                                                                                       |

#### <a name="slotswapwithpreviewstarted-slotswapwithpreviewcancelled"></a>SlotSwapWithPreviewStarted, SlotSwapWithPreviewCancelled

```js
{
    id:'7c5d6de5-eb70-4de2-b788-c52a544e68b8',
    subject:'/Microsoft.Web/sites/<site-name>',
    eventType:'Microsoft.Web.SlotSwapWithPreviewStarted',
    eventTime:'2020-01-28T18:26:51.7194887Z',
    data:{
        appEventTypeDetail:null,
        siteName:'<site-name >',
        clientRequestId:'922f4841-20d9-4dd6-8c5b-23f0d85e5592',
        correlationRequestId:'9ac46505-2b8a-4e06-834c-05ffbe2e8c3a',
        requestId:'765117aa-eaf8-4bd2-a644-1dbf69c7b0fd',
        address: '/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production',
        verb:'POST'
        sourceSlot: "staging",
        targetSlot: "production"
    },
    topic:'/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion:'1',
    metaDataVersion:'1'
}
```

데이터 개체에는 다음 속성이 포함 되어 있습니다.

|    속성                |    유형      |    설명                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    개체    |    앱에 대 한 작업 세부 정보                                                                                       |
|    action                  |    문자열    |    작업 동작의 유형입니다.                                                                                   |
|    이름                    |    문자열    |    이 이벤트가 발생 한 웹 사이트의 이름입니다.                                                                          |
|    clientRequestId         |    문자열    |    이 이벤트를 트리거한 사이트 API 작업에 대해 app service에서 생성 한 클라이언트 요청 id입니다.         |
|    correlationRequestId    |    문자열    |    이 이벤트를 트리거한 사이트 API 작업에 대해 app service에서 생성 한 상관 관계 요청 id입니다.    |
|    requestId               |    문자열    |    이 이벤트를 트리거한 사이트 API 작업에 대해 app service에서 생성 된 요청 id입니다.                |
|    address                 |    문자열    |    이 작업의 HTTP 요청 URL                                                                                |
|    verb                    |    문자열    |    이 작업의 HTTP 동사입니다.                                                                                       |

#### <a name="appupdatedrestarted-appupdatedstopped-appupdatedchangedappsettings"></a>AppUpdated. 다시 시작 됨, AppUpdated. 중지 됨, AppUpdated. ChangedAppSettings

```js
{
    id: 'b74ea56b-2a3f-4de5-a5d7-38e60c81cf23',
    subject: '/Microsoft.Web/sites/<site-name>',
    eventType: 'Microsoft.Web.AppUpdated',
    eventTime: '2020-01-28T18:22:30.2760952Z',
    data: {
        appEventTypeDetail: {
            action: 'Stopped'
        },
        siteName: '<site-name>',
        clientRequestId: '64a5e0aa-7cee-4ff1-9093-b9197b820014',
        correlationRequestId: '25bb36a5-8f6c-4f04-b615-e9a0ee045756',
        requestId: 'f2e8eb3f-b190-42de-b99e-6acefe587374',
        address: '/websystems/WebSites/web/subscriptions/<id>/webspaces/ <webspace>/sites/<site-name>/stop',
        verb: 'POST'
    },
    topic: '/subscriptions/<id>/resourceGroups/<group>/providers/ Microsoft.Web/sites/<site-name>',
    dataVersion: '1',
    metaDataVersion: '1'
}
```

데이터 개체의 속성은 다음과 같습니다.

|    속성                |    유형      |    설명                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    개체    |    앱에 대 한 작업 세부 정보                                                                                       |
|    action                  |    문자열    |    작업 동작의 유형입니다.                                                                                   |
|    이름                    |    문자열    |    이 이벤트가 발생 한 웹 사이트의 이름입니다.                                                                          |
|    clientRequestId         |    문자열    |    이 이벤트를 트리거한 사이트 API 작업에 대해 app service에서 생성 한 클라이언트 요청 id입니다.         |
|    correlationRequestId    |    문자열    |    이 이벤트를 트리거한 사이트 API 작업에 대해 app service에서 생성 한 상관 관계 요청 id입니다.    |
|    requestId               |    문자열    |    이 이벤트를 트리거한 사이트 API 작업에 대해 app service에서 생성 된 요청 id입니다.                |
|    address                 |    문자열    |    이 작업의 HTTP 요청 URL                                                                                |
|    verb                    |    문자열    |    이 작업의 HTTP 동사입니다.                                                                                       |

#### <a name="serverfarmsappserviceplanupdated"></a>Serverfarms. Appservice계획 업데이트 됨

```js
{
   "id":"56501672-9150-40e1-893a-18420c7fdbf7",
   "subject":"/Microsoft.Web/serverfarms/<plan-name>",
   "eventType":"Microsoft.Web.AppServicePlanUpdated",
   "eventTime":"2020-01-28T18:22:23.5516004Z",
   "data":{
        "serverFarmEventTypeDetail":{
            "stampKind":"Public",
            "action":"Updated",
            "status":"Started"
        },
        "serverFarmId":"0",
        "sku":{
            "name":"P1v2",
            "tier":"PremiumV2",
            "size":"P1v2",
            "family":"Pv2",
            "capacity":1
        },
        "clientRequestId":"8f880321-a991-45c7-b743-6ff63fe4c004",
        "correlationRequestId":"1995c3be-ba7f-4ccf-94af-516df637ec8a",
        "requestId":"b973a8e6-6949-4783-b44c-ac778be831bb",
        "address":"/websystems/WebSites/serverfarms/subscriptions/<id>/webspaces/<webspace-id>/serverfarms/<plan-name>/async",
        "verb":"PUT"
   },
   "topic":"/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/ serverfarms/<serverfarm-name>",
   "dataVersion":"1",
   "metaDataVersion":"1"
}
```

데이터 개체의 속성은 다음과 같습니다.

|    속성                         |    유형      |    설명                                                                                                       |
|-------------------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    Appservice설계도 Eventtypedetail    |    개체    |    App service 계획에 대 한 작업 세부 정보                                                                          |
|    stampKind                        |    문자열    |    앱 서비스 계획이 인 환경의 종류                                                                     |
|    action                           |    문자열    |    App service 계획에 대 한 작업 유형                                                                            |
|    상태                           |    문자열    |    App service 계획에 대 한 작업의 상태입니다.                                                                   |
|    sku                              |    개체    |    app service 계획의 sku                                                                                       |
|    이름                             |    문자열    |    app service 계획의 이름                                                                                      |
|    계층                             |    문자열    |    app service 계획의 계층                                                                                      |
|    Size                             |    문자열    |    app service 계획의 크기                                                                                      |
|    패밀리                           |    문자열    |    앱 서비스 계획 제품군                                                                                        |
|    용량                         |    문자열    |    app service 계획의 용량                                                                                      |
|    action                           |    문자열    |    작업 동작의 유형입니다.                                                                                   |
|    이름                             |    문자열    |    이 이벤트가 발생 한 웹 사이트의 이름입니다.                                                                          |
|    clientRequestId                  |    문자열    |    이 이벤트를 트리거한 사이트 API 작업에 대해 app service에서 생성 한 클라이언트 요청 id입니다.         |
|    correlationRequestId             |    문자열    |    이 이벤트를 트리거한 사이트 API 작업에 대해 app service에서 생성 한 상관 관계 요청 id입니다.    |
|    requestId                        |    문자열    |    이 이벤트를 트리거한 사이트 API 작업에 대해 app service에서 생성 된 요청 id입니다.                |
|    address                          |    문자열    |    이 작업의 HTTP 요청 URL                                                                                |
|    verb                             |    문자열    |    이 작업의 HTTP 동사입니다.                                                                                       |
