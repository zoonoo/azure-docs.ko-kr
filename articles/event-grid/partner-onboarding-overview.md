---
title: Azure Event Grid 파트너로 온보딩
description: Azure Event Grid 파트너 토픽 유형으로 등록 합니다. 파트너 항목에 대 한 리소스 모델 및 게시 흐름을 이해 합니다.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 8003e3e6a28b0e6a05d553be01c171029e244d74
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86116117"
---
# <a name="onboard-as-an-azure-event-grid-partner"></a>Azure Event Grid 파트너로 온보딩

이 문서에서는 Azure Event Grid 파트너 리소스를 개인적으로 사용 하는 방법 및 공개적으로 사용 가능한 파트너 토픽 유형을 만드는 방법을 설명 합니다.

Event Grid 파트너로 게시 이벤트와 연결된 Event Grid 리소스 유형을 사용하는 데는 특별한 권한이 필요하지 않습니다. 실제로이를 사용 하 여 사용자 고유의 Azure 구독에 전용으로 이벤트를 게시 하 고 파트너가 되는 것으로 간주 하는 경우 리소스 모델을 테스트할 수 있습니다.

## <a name="become-an-event-grid-partner"></a>Event Grid 파트너가 됩니다.

공용 Event Grid 파트너가 되는 경우 [이 양식을](https://aka.ms/gridpartnerform)작성 하 여 시작 하세요. 그런 다음에서 Event Grid 팀에 문의 [GridPartner@microsoft.com](mailto:gridpartner@microsoft.com) 합니다.

## <a name="how-partner-topics-work"></a>파트너 토픽의 작동 방식
파트너 토픽은 이미를 사용 하 여 Azure Storage 및 Azure IoT Hub와 같은 Azure 리소스의 이벤트를 게시 하 Event Grid 기존 아키텍처를 사용 하며, 이러한 도구를 누구나 사용할 수 있도록 합니다. 이러한 도구를 사용 하는 것은 기본적으로 Azure 구독 전용입니다. 이벤트를 공개적으로 사용할 수 있도록 하려면 양식을 작성 하 고 [Event Grid 팀에 문의 하세요](mailto:gridpartner@microsoft.com).

파트너 토픽을 사용하면 Azure Event Grid에 다중 테넌트 사용에 대한 이벤트를 게시할 수 있습니다.

### <a name="onboarding-and-event-publishing-overview"></a>온보딩 및 이벤트 게시 개요

#### <a name="partner-flow"></a>파트너 흐름

1. 아직 Azure 테넌트가 없는 경우 새로 하나 만듭니다.
1. Azure CLI를 사용 하 여 새 Event Grid를 만듭니다 `partnerRegistration` . 이 리소스에는 표시 이름, 설명, 설치 URI 등의 정보가 포함 됩니다.

    ![파트너 항목 만들기](./media/partner-onboarding-how-to/create-partner-registration.png)

1. 이벤트를 게시 하려는 각 지역에 하나 이상의 파트너 네임 스페이스를 만듭니다. Event Grid 서비스는 게시 끝점 (예: `https://contoso.westus-1.eventgrid.azure.net/api/events` ) 및 액세스 키를 프로 비전 합니다.

    ![파트너 네임 스페이스 만들기](./media/partner-onboarding-how-to/create-partner-namespace.png)

1. 고객이 파트너 토픽을 원하는 시스템에 등록할 수 있는 방법을 제공 합니다.
1. 파트너 토픽 유형을 공용으로 지정할 수 있도록 Event Grid 팀에 문의 하세요.

#### <a name="customer-flow"></a>고객 흐름

1. 고객은 Azure Portal를 방문 하 여 파트너 토픽을 만드는 데 사용할 Azure 구독 ID 및 리소스 그룹을 확인 합니다.
1. 고객은 시스템을 통해 파트너 토픽을 요청 합니다. 응답으로 파트너 네임 스페이스에 대 한 이벤트 터널을 만듭니다.
1. Event Grid 고객의 Azure 구독 및 리소스 그룹에 **보류 중인** 파트너 항목을 만듭니다.

    ![이벤트 채널 만들기](./media/partner-onboarding-how-to/create-event-tunnel-partner-topic.png)

1. 고객은 Azure Portal를 통해 파트너 토픽을 활성화 합니다. 이제 이벤트는 서비스에서 고객의 Azure 구독으로 전달 될 수 있습니다.

    ![파트너 항목 활성화](./media/partner-onboarding-how-to/activate-partner-topic.png)

## <a name="resource-model"></a>리소스 모델


다음은 파트너 항목에 대 한 리소스 모델입니다.

### <a name="partner-registrations"></a>파트너 등록
* 리소스: `partnerRegistrations`
* 사용 대상: 파트너
* 설명: SaaS (software as a service) 파트너의 글로벌 메타 데이터 (예: 이름, 표시 이름, 설명, 설치 URI)를 캡처합니다.
    
    파트너 등록을 만들거나 업데이트 하는 작업은 파트너를 위한 셀프 서비스 작업입니다. 이 셀프 서비스 기능을 통해 파트너는 완전 한 종단 간 흐름을 빌드하고 테스트할 수 있습니다.
    
    Microsoft에서 승인한 파트너 등록만 고객이 검색할 수 있습니다.
* 범위: 파트너의 Azure 구독에 생성 됩니다. 메타 데이터는 공용으로 만든 후 고객에 게 표시 됩니다.

### <a name="partner-namespaces"></a>파트너 네임 스페이스
* 리소스: partnerNamespaces
* 사용 대상: 파트너
* 설명: 고객 이벤트를 게시하기 위한 지역별 리소스를 제공합니다. 각 파트너 네임 스페이스에는 게시 끝점과 인증 키가 있습니다. 네임 스페이스는 파트너가 지정 된 고객에 대 한 파트너 항목을 요청 하 고 활성 고객을 나열 하는 방법 이기도 합니다.
* 범위: 파트너의 구독에 상주 합니다.

### <a name="event-channel"></a>이벤트 채널
* 리소스: `partnerNamespaces/eventChannels`
* 사용 대상: 파트너
* 설명: 이벤트 터널은 고객의 파트너 토픽의 미러입니다. 이벤트 터널을 만들고 메타 데이터에 고객의 Azure 구독 및 리소스 그룹을 지정 하 여 고객에 대 한 파트너 토픽을 만들도록 Event Grid에 게 알립니다. Event Grid는 ARM 호출을 발행 하 여 고객의 구독에서 해당 하는 항목을 만듭니다. 파트너 토픽은 보류 중 상태로 생성 됩니다. 각 이벤트 터널 및 파트너 토픽 간에는 일대일 링크가 있습니다.
* 범위: 파트너의 구독에 상주 합니다.

### <a name="partner-topics"></a>파트너 토픽
* 리소스: `partnerTopics`
* 사용 대상: 고객
* 설명: 파트너 토픽은 Event Grid의 사용자 지정 항목 및 시스템 항목과 유사 합니다. 각 파트너 토픽은 특정 원본 (예: `Contoso:myaccount` ) 및 특정 파트너 토픽 유형 (예: Contoso)과 연결 됩니다. 고객은 파트너 토픽에 이벤트 구독을 만들어 이벤트를 다양 한 이벤트 처리기로 라우팅합니다.

    고객은 이 리소스를 직접 만들 수 없습니다. 파트너 항목을 만드는 유일한 방법은 이벤트 터널을 만드는 파트너 작업을 통하는 것입니다.
* 범위: 고객의 구독에 상주 합니다.

### <a name="partner-topic-types"></a>파트너 항목 유형
* 리소스: `partnerTopicTypes`
* 사용 대상: 고객
* 설명: 파트너 토픽 유형은 고객이 승인 된 파트너 토픽 유형 목록을 검색할 수 있도록 하는 tenantwide resource 유형입니다. URL은 다음과 같습니다.https://management.azure.com/providers/Microsoft.EventGrid/partnerTopicTypes)
* 범위: Global

## <a name="publish-events-to-event-grid"></a>Event Grid에 이벤트 게시
Azure 지역에서 파트너 네임 스페이스를 만드는 경우 지역 끝점과 해당 하는 인증 키를 가져옵니다. 해당 네임 스페이스의 모든 고객 이벤트 터널에 대해이 끝점에 이벤트 일괄 처리를 게시 합니다. 이벤트의 원본 필드에 따라 Azure Event Grid 각 이벤트를 해당 파트너 항목에 매핑합니다.

### <a name="event-schema-cloudevents-v10"></a>이벤트 스키마: CloudEvents v1.0
CloudEvents 1.0 스키마를 사용 하 여 Azure Event Grid에 이벤트를 게시 합니다. Event Grid는 구조적 모드와 일괄 처리 모드를 모두 지원합니다. CloudEvents 1.0는 파트너 네임 스페이스에 대해 유일 하 게 지원 되는 이벤트 스키마입니다.

### <a name="example-flow"></a>예제 흐름

1.  게시 서비스가 `https://contoso.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01`에 대한 HTTP POST를 수행합니다.
1.  요청에 인증을 위한 키를 포함하며 이름이 aeg-sas-key인 헤더를 포함합니다. 이 키는 파트너 네임 스페이스를 만드는 동안 프로 비전 됩니다. 예를 들어 유효한 헤더 값은 aeg-sas-key (VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==)입니다.
1.  콘텐츠 형식 헤더를 "application/cloudevents-batch + json;으로 설정 합니다. charset = 8a ".
1.  해당 지역에 해당 하는 이벤트 일괄 처리를 사용 하 여 게시 URL에 HTTP POST를 수행 합니다. 예를 들면 다음과 같습니다.

``` json
[
{
    "specversion" : "1.0-rc1",
    "type" : "com.contoso.ticketcreated",
    "source" : " com.contoso.account1",
    "subject" : "tickets/123",
    "id" : "A234-1234-1234",
    "time" : "2019-04-05T17:31:00Z",
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
          object-unique-to-each-publisher
    }
},
{
    "specversion" : "1.0-rc1",
    "type" : "com.contoso.ticketclosed",
    "source" : "https://contoso.com/account2",
    "subject" : "tickets/456",
    "id" : "A234-1234-1234",
    "time" : "2019-04-05T17:31:00Z",
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
          object-unique-to-each-publisher
    }
}
]
```

대상 네임 스페이스 끝점에 게시 한 후 응답을 받게 됩니다. 응답은 표준 HTTP 응답 코드입니다. 몇 가지 일반적인 응답은 다음과 같습니다.

| 결과                             | 응답              |
|------------------------------------|-----------------------|
| Success                            | 200 정상                |
| 이벤트 데이터의 형식이 잘못되었습니다.    | 400 잘못된 요청       |
| 잘못된 액세스 키                 | 401 권한 없음      |
| 잘못된 엔드포인트                 | 404 찾을 수 없음         |
| 배열 또는 이벤트가 크기 제한을 초과합니다. | 413 페이로드가 너무 큼 |

## <a name="references"></a>참조 항목

  * [Swagger](https://github.com/ahamad-MS/azure-rest-api-specs/blob/master/specification/eventgrid/resource-manager/Microsoft.EventGrid/preview/2020-04-01-preview/EventGrid.json)
  * [ARM 템플릿](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/allversions)
  * [ARM 템플릿 스키마](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2020-04-01-preview/Microsoft.EventGrid.json)
  * [REST API](https://docs.microsoft.com/rest/api/eventgrid/version2020-04-01-preview/partnernamespaces)
  * [CLI 확장](https://docs.microsoft.com/cli/azure/ext/eventgrid/?view=azure-cli-latest)

### <a name="sdks"></a>SDK
  * [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid/5.3.1-preview)
  * [Python](https://pypi.org/project/azure-mgmt-eventgrid/3.0.0rc6/)
  * [Java](https://search.maven.org/artifact/com.microsoft.azure.eventgrid.v2020_04_01_preview/azure-mgmt-eventgrid/1.0.0-beta-3/jar)
  * [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid/versions/0.19.0)
  * [JS](https://www.npmjs.com/package/@azure/arm-eventgrid/v/7.0.0)
  * [Go](https://github.com/Azure/azure-sdk-for-go)


## <a name="next-steps"></a>다음 단계
- [파트너 토픽 개요](partner-topics-overview.md)
- [파트너 토픽 등록 양식](https://aka.ms/gridpartnerform)
- [좋아하게 되었습니다 partner 항목](auth0-overview.md)
- [좋아하게 되었습니다 partner 항목을 사용 하는 방법](auth0-how-to.md)
