---
title: Azure Event Grid를 사용하여 Auth0에서 Azure로 이벤트를 보내는 방법
description: Azure Event Grid를 사용하여 Auth0에서 Azure로 이벤트를 보내는 방법입니다.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: babanisa
ms.openlocfilehash: 950b17611ba4932cc986d1b6488b904f29fdf04b
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83690319"
---
# <a name="integrate-azure-event-grid-with-auth0"></a>Azure Event Grid와 Auth0 통합

이 문서에서는 Event Grid 파트너 토픽을 만들어 Auth0 및 Azure 계정을 연결하는 방법을 설명합니다.

Auth0 에서 지 원하는 이벤트의 전체 목록은 [Auth0 이벤트 유형 코드](https://auth0.com/docs/logs/references/log-event-type-codes)를 참조하세요.

## <a name="send-events-from-auth0-to-azure-event-grid"></a>Auth0에서 Azure Event Grid로 이벤트 보내기
Auth0 이벤트를 Azure로 보내려면 다음을 수행합니다.

1. Event Grid 리소스 공급자를 사용하도록 설정
1. Auth0 대시보드에서 Auth0 파트너 토픽 설정
1. Azure에서 파트너 토픽 활성화
1. Auth0에서 이벤트 구독

이러한 개념에 대한 자세한 내용은 Azure Event Grid [개념](concepts.md)을 참조하세요.

### <a name="enable-event-grid-resource-provider"></a>Event Grid 리소스 공급자를 사용하도록 설정
이전에 Event Grid를 사용하지 않은 경우 Event Grid 리소스 공급자를 등록해야 합니다. 이전에 Event Grid 사용한 경우 다음 섹션으로 건너뜁니다.

Azure Portal에서 다음을 수행합니다.
1. 왼쪽 메뉴에서 구독 선택
1. Event Grid에 대해 사용 중인 구독 선택
1. 왼쪽 메뉴의 설정에서 리소스 공급자 선택
1. Microsoft.EventGrid 찾기
1. 등록 선택
1. 새로 고쳐 상태가 등록됨으로 변경되는지 확인

### <a name="set-up-an-auth0-partner-topic"></a>Auth0 파트너 토픽 설정
통합 프로세스의 일부는 Auth0을 이벤트 원본으로 사용하도록 설정하는 것입니다(이 단계는 [Auth0 대시보드](https://manage.auth0.com/)에서 발생).

1. [Auth0 대시보드](https://manage.auth0.com/)에 로그인합니다.
1. 로그 > 스트림으로 이동합니다.
1. \+ 스트림 만들기를 클릭합니다.
1. Azure Event Grid를 선택하고 새 스트림에 고유한 이름을 입력합니다.
1. Azure 구독 ID, Azure 지역 및 리소스 그룹 이름을 제공하여 이벤트 원본을 만듭니다. 
1. 저장을 클릭합니다.

### <a name="activate-your-auth0-partner-topic-in-azure"></a>Azure에서 Auth0 파트너 토픽 활성화
Azure에서 Auth0 토픽을 활성화하면 Auth0에서 Azure로 이벤트를 전달할 수 있습니다.

1. Azure 포털에 로그인합니다.
1. 위쪽에서 `Partner Topics`를 검색하고 서비스 아래에서 `Event Grid Partner Topics`를 클릭합니다.
1. Auth0 대시보드에서 만든 스트림과 일치하는 토픽을 클릭합니다.
1. `Source` 필드가 Auth0 계정과 일치하는지 확인합니다.
1. 활성화를 클릭합니다.

### <a name="subscribe-to-auth0-events"></a>Auth0 이벤트 구독

#### <a name="create-an-event-handler"></a>이벤트 처리기 만들기
파트너 토픽을 테스트하려면 이벤트 처리기가 필요합니다. Azure 구독으로 이동하고, [Azure 함수](custom-event-to-function.md)와 같이 [이벤트 처리기](event-handlers.md)로 지원되는 서비스를 실행합니다.

#### <a name="subscribe-to-your-auth0-partner-topic"></a>Auth0 파트너 토픽 구독
Auth0 파트너 토픽을 구독하면 Event Grid에게 Auth0 이벤트가 이동할 위치를 알릴 수 있습니다.

1. Auth0 통합에 대한 파트너 토픽에서 맨 위의 + 이벤트 구독을 선택합니다.
1. 이벤트 구독 만들기 페이지에서
    1. 이벤트 구독의 이름을 입력합니다.
    1. 엔드포인트 유형에 대해 만든 Azure 서비스나 WebHook를 선택합니다.
    1. 특정 서비스에 대한 지침을 따릅니다.
    1. 만들기를 클릭합니다.

## <a name="testing"></a>테스트
Auth0 파트너 토픽과 Azure의 통합이 사용할 준비가 되어야 합니다.

### <a name="verify-the-integration"></a>통합 확인
통합이 예상대로 작동하는지 확인하려면 다음을 수행합니다.

1. Auth0 대시보드에 로그인합니다.
1. 로그 > 스트림으로 이동합니다.
1. Event Grid 스트림을 클릭합니다.
1. 스트림에서 상태 탭을 클릭합니다. 스트림이 활성 상태여야 하며, 오류가 표시되지 않으면 스트림이 작동하고 있는 것입니다.

이벤트 흐름을 보려면 [게시될 이벤트를 호출하는 Auth0 동작](https://auth0.com/docs/logs/references/log-event-type-codes)을 호출해 봅니다.

## <a name="delivery-attempts-and-retries"></a>배달 시도 및 다시 시도
Auth0 이벤트는 스트리밍 메커니즘을 통해 Azure에 배달됩니다. 각 이벤트는 Auth0에서 트리거될 때 전송됩니다. Event Grid가 이벤트를 수신할 수 없는 경우 Auth0은 이벤트 배달을 세 차례 다시 시도합니다. 그렇지 않으면 Auth0은 시스템에 배달 실패를 기록합니다.

## <a name="next-steps"></a>다음 단계

- [Auth0 파트너 토픽](auth0-overview.md)
- [파트너 토픽 개요](partner-topics-overview.md)
- [Event Grid 파트너 되기](partner-onboarding-overview.md)