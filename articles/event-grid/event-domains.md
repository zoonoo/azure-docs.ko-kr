---
title: Azure Event Grid의 이벤트 도메인
description: 이 문서에서는 이벤트 도메인을 사용하여 다양한 비즈니스 조직, 고객 또는 애플리케이션에 대한 사용자 지정 이벤트의 흐름을 관리하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 02529ba770e636021cf9cec4ed555247e1c63d8c
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86114366"
---
# <a name="understand-event-domains-for-managing-event-grid-topics"></a>Event Grid 토픽을 관리하는 이벤트 도메인 이해

이 문서에서는 이벤트 도메인을 사용하여 다양한 비즈니스 조직, 고객 또는 애플리케이션에 대한 사용자 지정 이벤트의 흐름을 관리하는 방법을 설명합니다. 이벤트 도메인의 용도:

* 대규모 다중 테넌트 이벤트 아키텍처를 관리합니다.
* 인증 및 권한 부여를 관리합니다.
* 각 토픽을 개별적으로 관리하지 않고 토픽을 분할합니다.
* 각 토픽 엔드포인트에 개별적으로 게시할 필요가 없습니다.

## <a name="event-domain-overview"></a>이벤트 도메인 개요

이벤트 도메인은 동일한 애플리케이션과 관련된 대규모 Event Grid 토픽을 관리하는 도구입니다. 수천 개의 개별 토픽을 가질 수 있는 메타 항목으로 생각할 수 있습니다.

이벤트 도메인은 Storage 및 IoT Hub와 같은 Azure 서비스에서 사용하는 것과 동일한 아키텍처를 사용하여 이벤트를 게시합니다. 이벤트 도메인을 사용하면 수천 개의 토픽에 이벤트를 게시할 수 있습니다. 도메인 역시 각 토픽에 대한 권한 부여 및 인증 제어를 제공하므로 테넌트를 분할할 수 있습니다.

### <a name="example-use-case"></a>사용 사례 예

이벤트 도메인을 가장 쉽게 설명하는 방법은 예제입니다. Contoso 생성 기계를 실행 하는 경우, 예를 들어 tractors, 장비 장비 및 기타 많은 기계를 제조할 수 있습니다. 비즈니스의 일부로, 여러분은 장비 유지 관리, 시스템 상태 및 계약 업데이트에 대한 실시간 정보를 고객에게 푸시합니다. 이 모든 정보는 회사 앱, 고객 엔드포인트, 고객이 설치한 기타 인프라를 비롯한 다양한 엔드포인트로 이동합니다.

이벤트 도메인을 사용하면 Contoso Construction Machinery를 단일 이벤트 엔터티로 모델링할 수 있습니다. 각 고객은 도메인 내의 토픽으로 표시됩니다. 인증 및 권한 부여는 Azure Active Directory를 사용하여 처리됩니다. 각 고객은 토픽을 구독하고 자신에게 전달된 이벤트를 가져올 수 있습니다. 이벤트 도메인을 통해 관리되는 액세스는 고객이 자신의 토픽에만 액세스할 수 있도록 보장합니다.

또한 모든 고객 이벤트를 게시할 수 있는 단일 엔드포인트를 제공합니다. Event Grid는 각 토픽이 해당 테넌트로 범위가 지정된 이벤트만 인식하도록 처리합니다.

![Contoso Construction 예제](./media/event-domains/contoso-construction-example.png)

## <a name="access-management"></a>액세스 관리

도메인을 사용하면 Azure의 RBAC(역할 기반 액세스 제어)를 통해 각 토픽에 대한 권한 부여 및 인증을 세밀하게 제어할 수 있습니다. 이러한 역할을 사용하여 애플리케이션의 각 테넌트를 제한하는 방법으로 원하는 토픽에만 액세스 권한을 부여할 수 있습니다.

이벤트 도메인의 RBAC는 Event Grid 및 Azure의 나머지 부분에서 [관리 액세스 제어](security-authorization.md)가 작동하는 방식과 동일하게 작동합니다. RBAC를 사용하여 이벤트 도메인에 사용자 지정 역할 정의를 만들고 적용할 수 있습니다.

### <a name="built-in-roles"></a>기본 제공 역할

Event Grid에는 RBAC를 이벤트 도메인 작업에 보다 쉽게 적용할 수 있는 두 개의 기본 제공 역할 정의가 제공됩니다. 이러한 역할은 **EventGrid EventSubscription 기여자(미리 보기)** 및 **EventGrid EventSubscription 읽기 권한자(미리 보기)** 입니다. 이벤트 도메인의 토픽을 구독해야 하는 사용자에게 이러한 역할을 할당합니다. 사용자가 구독 해야 하는 토픽 으로만 역할 할당의 범위를 지정 합니다.

이러한 역할에 대한 자세한 내용은 [Event Grid의 기본 제공 역할](security-authorization.md#built-in-roles)을 참조하세요.

## <a name="subscribing-to-topics"></a>토픽 구독

이벤트 도메인 내에서 토픽의 이벤트를 구독하는 것은 [사용자 지정 토픽에 이벤트 구독 만들기](./custom-event-quickstart.md) 또는 Azure 서비스에서 이벤트를 구독하는 것과 동일합니다.

### <a name="domain-scope-subscriptions"></a>도메인 범위 구독

이벤트 도메인은 도메인 범위 구독도 허용합니다. 이벤트 도메인의 이벤트 구독은 이벤트로 전송되는 토픽에 관계없이 도메인에 전송된 모든 이벤트를 수신합니다. 도메인 범위 구독은 관리 및 감사 용도로 유용하게 사용할 수 있습니다.

## <a name="publishing-to-an-event-domain"></a>이벤트 도메인에 게시

이벤트 도메인을 만들면 Event Grid에 토픽을 만들 때와 비슷한 게시 엔드포인트가 제공됩니다. 

이벤트 도메인의 모든 토픽에 이벤트를 게시 하려면 [사용자 지정 토픽의 경우와 동일한 방식](./post-to-custom-topic.md)으로 이벤트를 도메인의 끝점에 푸시합니다. 유일한 차이점은 이벤트를 전달할 토픽을 지정해야 한다는 것입니다.

예를 들어 다음 이벤트 배열을 게시하면 `"id": "1111"`인 이벤트는 `foo` 토픽으로 전송되고 `"id": "2222"`인 이벤트는 `bar` 토픽으로 전송됩니다.

```json
[{
  "topic": "foo",
  "id": "1111",
  "eventType": "maintenanceRequested",
  "subject": "myapp/vehicles/diggers",
  "eventTime": "2018-10-30T21:03:07+00:00",
  "data": {
    "make": "Contoso",
    "model": "Small Digger"
  },
  "dataVersion": "1.0"
},
{
  "topic": "bar",
  "id": "2222",
  "eventType": "maintenanceCompleted",
  "subject": "myapp/vehicles/tractors",
  "eventTime": "2018-10-30T21:04:12+00:00",
  "data": {
    "make": "Contoso",
    "model": "Big Tractor"
  },
  "dataVersion": "1.0"
}]
```

이벤트 도메인은 토픽에 게시를 자동으로 처리합니다. 관리하는 각 토픽에 개별적으로 이벤트를 게시하는 대신, 도메인의 엔드포인트에 모든 이벤트를 게시할 수 있습니다. Event Grid는 각 이벤트가 올바른 토픽에 전송되도록 합니다.

## <a name="limits-and-quotas"></a>한도 및 할당량
다음은 이벤트 도메인과 관련 된 제한 및 할당량입니다.

- 10만 이벤트 도메인당 항목 
- 100 Azure 구독 당 이벤트 도메인 
- 500 이벤트 도메인의 토픽 당 이벤트 구독
- 50 도메인 범위 구독 
- 5000 초당 이벤트 수집 율 (도메인)

이러한 제한에 맞지 않는 경우 지원 티켓을 열거나로 전자 메일을 보내 제품 팀에 연락 [askgrid@microsoft.com](mailto:askgrid@microsoft.com) 하세요. 

## <a name="pricing"></a>가격 책정
이벤트 도메인은 Event Grid의 다른 모든 기능에서 사용 하는 것과 동일한 [작업 가격 책정](https://azure.microsoft.com/pricing/details/event-grid/) 을 사용 합니다.

작업은 이벤트 도메인에서도 사용자 지정 토픽과 동일한 방식으로 작동합니다. 각 이벤트가 이벤트 도메인에 수신되는 것도 작업이고, 각 이벤트 전달 시도도 작업입니다.

## <a name="next-steps"></a>다음 단계

* 이벤트 도메인을 설정 하 고, 항목을 만들고, 이벤트 구독을 만들고, 이벤트를 게시 하는 방법에 대해 알아보려면 [이벤트 도메인 관리](./how-to-event-domains.md)를 참조 하세요.
