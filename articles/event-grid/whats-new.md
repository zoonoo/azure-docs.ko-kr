---
title: 새로운 기능은 무엇입니까? 릴리스 정보 - Azure Event Grid
description: Azure Event Grid의 최신 릴리스 정보, 알려진 문제, 버그 수정, 사용되지 않는 기능, 예정된 변경 내용 등을 알아봅니다.
ms.topic: overview
ms.date: 07/23/2020
ms.openlocfilehash: 1edfa3e2bc4c8adae113b2215b7fb0483fba4c02
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87172698"
---
# <a name="whats-new-in-azure-event-grid"></a>Azure Event Grid의 새로운 기능

>이 URL `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Event+Grid%22&locale=en-us`를 ![RSS 피드 판독기 아이콘](./media/whats-new/feed-icon-16x16.png) 피드 판독기에 복사하고 붙어넣어 업데이트를 위해 이 페이지를 다시 방문해야 할 때 알림을 받습니다.

Azure Event Grid는 지속적인 향상되고 있습니다. 최신 개발 정보를 확인할 수 있도록 이 문서에서는 다음에 대한 정보를 제공합니다.

- 최신 릴리스
- 알려진 문제
- 버그 수정
- 사용되지 않는 기능
- 변경 계획

## <a name="600-2020-06"></a>6.0.0(2020년 6월)
- 새 GA(일반 공급) 서비스 API 버전 2020-06-01에 대한 지원을 추가합니다.
- GA가 된 새로운 기능:
    - [입력 매핑](input-mappings.md)
    - [사용자 지정 입력 스키마](input-mappings.md)
    - [클라우드 이벤트 V10 스키마](cloud-event-schema.md)
    - [대상으로 Service Bus 토픽](handler-service-bus.md)
    - [대상으로 Azure 함수](handler-functions.md)
    - [웹후크 일괄 처리](./edge/delivery-output-batching.md)
    - [보안 웹후크(Azure Active Directory 지원)](secure-webhook-delivery.md)
    - [IP 필터링](configure-firewall.md)
    - [Private Link 서비스 지원](configure-private-endpoints.md)
    - [이벤트 배달 스키마](event-schema.md)

## <a name="532-preview-2020-05"></a>5.3.2-preview(2020년 5월)
- 이 릴리스에는 품질 향상을 위한 추가 버그 수정이 포함되어 있습니다.
- 버전 5.3.1-preview인 이 릴리스는 다음과 같은 새로운 기능을 포함하는 2020-04-01-Preview API 버전에 해당합니다. 
    - [도메인 및 토픽에 이벤트를 게시할 때 IP 필터링 지원](configure-firewall.md)
    - [파트너 토픽](partner-topics-overview.md)
    - [Azure Portal에서 추적된 리소스로 시스템 항목](system-topics.md)
    - [관리형 서비스 ID를 사용하여 이벤트 전달](managed-service-identity.md) 
    - [Private Link 서비스 지원](configure-private-endpoints.md)

## <a name="531-preview-2020-04"></a>5.3.1-preview(2020년 4월)
- 이 릴리스에는 품질 향상을 위한 다양한 버그 수정이 포함되어 있습니다.
- 버전 5.3.0-preview인 이 릴리스는 다음과 같은 새로운 기능을 포함하는 2020-04-01-Preview API 버전에 해당합니다. 
    - [도메인 및 토픽에 이벤트를 게시할 때 IP 필터링 지원](configure-firewall.md)
    - [파트너 토픽](partner-topics-overview.md)
    - [Azure Portal에서 추적된 리소스로 시스템 항목](system-topics.md)
    - [관리형 서비스 ID를 사용하여 이벤트 전달](managed-service-identity.md) 
    - [Private Link 서비스 지원](configure-private-endpoints.md)

## <a name="530-preview-2020-03"></a>5.3.0-preview(2020년 3월)
- 5\.2.0-preview 버전에 이미 추가된 기능 외에 새로운 기능을 소개합니다. 
- 버전 5.2.0-preview인 이 릴리스는 2020-04-01-Preview API 버전에 해당합니다.
- 다음과 같은 새로운 기능을 지원합니다. 
    - [도메인 및 토픽에 이벤트를 게시할 때 IP 필터링 지원](configure-firewall.md)
    - [파트너 토픽](partner-topics-overview.md)
    - [Azure Portal에서 추적된 리소스로 시스템 항목](system-topics.md)
    - [관리형 서비스 ID를 사용하여 이벤트 전달](managed-service-identity.md) 
    - [Private Link 서비스 지원](configure-private-endpoints.md)

## <a name="520-preview-2020-01"></a>5.2.0-preview(2020년 1월)
- 이 릴리스는 2020-04-01-Preview API 버전에 해당합니다.
- 다음과 같은 새로운 기능을 지원합니다.
    - [도메인 및 토픽에 이벤트를 게시할 때 IP 필터링 지원](configure-firewall.md)

## <a name="500-2019-05"></a>5.0.0(2019년 5월)
- 이 릴리스는 `2019-06-01` API 버전에 해당합니다.
- 다음과 같은 새로운 기능을 지원합니다.
    * [도메인](event-domains.md)
    * 리소스 목록 작업에 대한 페이지 매김 및 검색 필터입니다. 예제는 [토픽 - 구독 별로 나열](/rest/api/eventgrid/version2020-04-01-preview/topics/listbysubscription)을 참조하세요.
    * [대상으로 Service Bus 큐](handler-service-bus.md)
    * [고급 필터링](event-filtering.md#advanced-filtering)

## <a name="410-preview-2019-03"></a>4.1.0-preview(2019년 3월)
- 이 릴리스는 2019-02-01-preview API 버전에 해당합니다.
- 다음과 같은 새로운 기능을 지원합니다.
    * 리소스 목록 작업에 대한 페이지 매김 및 검색 필터입니다. 예제는 [토픽 - 구독 별로 나열](/rest/api/eventgrid/version2020-04-01-preview/topics/listbysubscription)을 참조하세요.
    * [도메인 수동 만들기/삭제 토픽](how-to-event-domains.md)
    * [대상으로 Service Bus 큐](handler-service-bus.md)

## <a name="400-2018-12"></a>4.0.0(2018년 2월)
- 이 릴리스는 `2019-01-01` 안정 API 버전에 해당합니다.
- 이벤트 구독과 관련된 다음 기능의 GA(일반 공급)를 지원합니다.
    * [배달 못한 편지 대상](manage-event-delivery.md)
    * [대상으로 Azure Storage 큐](handler-storage-queues.md)
    * [Azure Relay - 대상으로 하이브리드 연결](handler-relay-hybrid-connections.md)
    * [수동 핸드셰이크 유효성 검사](webhook-event-delivery.md)
    * [재시도 정책 지원](delivery-and-retry.md)
- 아직 미리 보기에 있는 기능(예: [Event Grid 도메인](event-domains.md) 또는 [고급 필터 지원](event-filtering.md#advanced-filtering))은 3.0.1 미리 보기 버전의 SDK를 사용하여 계속 액세스할 수 있습니다.

## <a name="301-preview-2018-10"></a>3.0.1-preview(2018년 10월)
- [Newtonsoft NuGet 패키지 10.0.3 버전](https://www.nuget.org/packages/Newtonsoft.Json/10.0.3)에 대한 종속성을 수행합니다.

## <a name="300-preview-2018-10"></a>3.0.0-preview(2018년 10월)
- 이 릴리스는 2018-09-15-preview API 버전에 도입된 새로운 기능에 대한 미리 보기 SDK입니다. - 이 릴리스에는 다음에 대한 지원이 포함됩니다.
    - [도메인 및 도메인 토픽](event-domains.md)
    - [이벤트 구독 만료 날짜](concepts.md#event-subscription-expiration) 소개
    - 이벤트 구독에 대한 [고급 필터링](event-filtering.md#advanced-filtering) 소개
    - `2018-01-01` API 버전을 대상으로 하는 안정적인 버전의 SDK는 계속 버전 1.3.0으로 존재합니다.

## <a name="next-steps"></a>다음 단계
