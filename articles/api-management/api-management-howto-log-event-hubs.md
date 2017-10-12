---
title: "Azure API 관리에서 Azure 이벤트 허브에 이벤트를 기록하는 방법 | Microsoft Docs"
description: "Azure API 관리에서 Azure 이벤트 허브에 이벤트를 기록하는 방법 배우기"
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 88f6507d-7460-4eb2-bffd-76025b73f8c4
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: a310236179677046ec49930b07cfdffdadc37974
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Azure API 관리에서 Azure 이벤트 허브에 이벤트를 기록하는 방법
Azure 이벤트 허브는 초당 수백만 개의 이벤트를 수집할 수 있는 확장성이 뛰어난 데이터 수집 서비스이므로 연결된 장치와 응용 프로그램이 생성하는 대량의 데이터를 처리하고 분석할 수 있습니다. 이벤트 허브는 이벤트 파이프라인에 대한 "현관"의 역할을 하고 데이터가 이벤트 허브에 수집되면 실시간 분석 공급자 또는 일괄 처리/저장소 어댑터를 사용하여 변환 및 저장될 수 있습니다. 이벤트 허브는 이러한 이벤트를 소비하는 데에서 이벤트 스트림의 프로덕션을 분리하므로 이벤트 소비자가 자신의 개인 일정에 이벤트를 액세스할 수 있습니다.

이 문서는 [이벤트 허브와 Azure API 관리 통합](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) 동영상과 함께 제공되며 Azure 이벤트 허브를 사용하여 API 관리 이벤트를 기록하는 방법을 설명합니다.

## <a name="create-an-azure-event-hub"></a>Azure 이벤트 허브 만들기
이벤트 허브를 만들려면 [Azure 클래식 포털](https://manage.windowsazure.com)에 로그인하여 **새로 만들기**->**App Services**->**Service Bus**->**이벤트 허브**->**빠른 생성**을 차례로 클릭합니다. 이벤트 허브 이름, 지역을 입력하고 구독을 선택한 후 네임스페이스를 선택합니다. 이전에 네임스페이스를 만들지 않은 경우 **네임스페이스** 텍스트 상자에 이름을 입력하여 만들 수 있습니다. 모든 속성이 구성되면 **새 이벤트 허브 만들기** 를 클릭하여 이벤트 허브를 만듭니다.

![이벤트 허브 만들기][create-event-hub]

다음으로 새 이벤트 허브에 대한 **구성** 탭으로 이동하여 두 개의 **공유 액세스 정책**을 만듭니다. 첫 번째 정책의 이름을 **전송**으로 지정하고 **보내기** 권한을 부여합니다.

![전송 정책][sending-policy]

두 번째 정책의 이름을 **수신**으로 지정하고 **수신** 권한을 부여한 후 **저장**을 클릭합니다.

![수신 정책][receiving-policy]

각 공유 액세스 정책은 응용 프로그램과 이벤트 허브 간의 이벤트 전송 및 수신을 허용합니다. 이러한 정책에 대한 연결 문자열에 액세스하려면 이벤트 허브의 **대시보드** 탭으로 이동하여 **연결 정보**를 클릭합니다.

![연결 문자열][event-hub-dashboard]

**전송** 연결 문자열은 이벤트를 기록할 때 사용되고 **수신** 연결 문자열은 이벤트 허브에서 이벤트를 다운로드할 때 사용됩니다.

![연결 문자열][event-hub-connection-string]

## <a name="create-an-api-management-logger"></a>API 관리 로거 만들기
이제 이벤트 허브를 만들었으므로 다음 단계는 API 관리 서비스에서 [로거](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) 를 구성하여 이벤트 허브에 이벤트를 기록할 수 있도록 하는 것입니다.

API 관리 로거는 [API 관리 REST API](http://aka.ms/smapi)를 사용하여 구성됩니다. REST API를 처음으로 사용하기 전에 [필수 구성 요소](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest#Prerequisites)를 검토하고 [REST API에 액세스할 수 있도록 설정](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest#EnableRESTAPI)했는지 확인합니다.

로거를 만들려면 다음 URL 템플릿을 사용하여 HTTP PUT 요청을 만듭니다.

`https://{your service}.management.azure-api.net/loggers/{new logger name}?api-version=2014-02-14-preview`

* `{your service}` 를 API 관리 서비스 인스턴스의 이름으로 바꿉니다.
* `{new logger name}` 을 새 로거에 대하여 원하는 이름으로 바꿉니다. [log-to-eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub) 정책을 구성할 때 이 이름을 참조하게 됩니다.

요청에 다음 헤더를 추가합니다.

* 콘텐츠 형식 : 응용 프로그램/json
* 권한 부여 : SharedAccessSignature 58...
  * `SharedAccessSignature` 생성에 대한 지침은 [Azure API 관리 REST API 인증](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-authentication)을 참조하세요.

다음 템플릿을 사용하여 요청 본문을 지정합니다.

```json
{
  "type" : "AzureEventHub",
  "description" : "Sample logger description",
  "credentials" : {
    "name" : "Name of the Event Hub from the Azure Classic Portal",
    "connectionString" : "Endpoint=Event Hub Sender connection string"
    }
}
```

* `type`은 `AzureEventHub`로 설정해야 합니다.
* `description`는 로거에 대한 선택적 설명을 제공하고 원하는 경우 길이가 0인 문자열이 될 수 있습니다.
* `credentials`는 Azure 이벤트 허브의 `name` 및 `connectionString`을 포함합니다.

요청을 만들 때 로거가 생성되면 `201 Created` 의 상태 코드가 반환됩니다.

> [!NOTE]
> 다른 가능한 반환 코드 및 해당 이유의 경우 [로거 만들기](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity#PUT)를 참조하세요. 목록, 업데이트, 삭제 등의 다른 작업을 수행하는 방법을 보려면 [로거](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) 엔터티 설명서를 참조하세요.
>
>

## <a name="configure-log-to-eventhubs-policies"></a>log-to-eventhubs 정책 구성
API 관리에 로거가 구성되면 원하는 이벤트를 기록하는 log-to-eventhubs 정책을 구성할 수 있습니다. log-to-eventhubs 정책은 인바운드 정책 섹션 또는 아웃바운드 정책 섹션에서 사용할 수 있습니다.

정책을 구성하려면 [Azure Portal](https://portal.azure.com)에 로그인하여, API 관리 서비스로 이동한 후 **게시자 포털**을 클릭하여 게시자 포털에 액세스합니다.

![게시자 포털][publisher-portal]

왼쪽의 API Management 메뉴에서 **정책**을 클릭하고 원하는 제품 및 API를 선택한 후 **정책 추가**를 클릭합니다. 이 예제에서는 **무제한** 제품의 **Echo API**에 정책을 추가합니다.

![정책 추가][add-policy]

`inbound` 정책 섹션에 커서를 놓고 **이벤트 허브에 기록** 정책을 클릭하여 `log-to-eventhub` 정책 명령문 템플릿을 삽입합니다.

![정책 편집기][event-hub-policy]

```xml
<log-to-eventhub logger-id ='logger-id'>
  @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name))
</log-to-eventhub>
```

`logger-id` 를 이전 단계에서 구성한 API 관리 로거의 이름으로 바꿉니다.

문자열을 `log-to-eventhub` 요소에 대한 값으로 반환하는 모든 식을 사용할 수 있습니다. 이 예제에서는 날짜 및 시간, 서비스 이름, 요청 id, 요청 ip 주소 및 작업 이름을 포함하는 문자열이 기록됩니다.

**저장** 을 클릭하여 업데이트된 정책 구성을 저장합니다. 저장되는 즉시 정책이 활성화되며 지정된 이벤트 허브에 이벤트가 기록됩니다.

## <a name="next-steps"></a>다음 단계
* Azure 이벤트 허브에 대해 자세히 알아보기
  * [Azure 이벤트 허브 시작](../event-hubs/event-hubs-c-getstarted-send.md)
  * [EventProcessorHost를 사용하여 메시지 수신](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [이벤트 허브 프로그래밍 가이드](../event-hubs/event-hubs-programming-guide.md)
* API 관리 및 이벤트 허브 통합에 대해 자세히 알아보기
  * [로거 엔터티 참조](https://docs.microsoft.com/rest/api/apimanagement/loggers)
  * [log-to-eventhub 정책 참조](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#log-to-eventhub)
  * [Azure API 관리, 이벤트 허브 및 Runscope를 사용하여 API 모니터링](api-management-log-to-eventhub-sample.md)    

## <a name="watch-a-video-walkthrough"></a>연습 동영상 시청
> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Integrate-Azure-API-Management-with-Event-Hubs/player]
>
>

[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png
