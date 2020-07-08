---
title: Azure Event Grid 파트너 토픽
description: Azure Event Grid를 사용하여 타사 Event Grid SaaS 및 PaaS 파트너의 이벤트를 Azure 서비스로 직접 보냅니다.
services: event-grid
author: femila
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: femila
ms.openlocfilehash: 5327efea8af734c723ba76d1a00b72c08f5c88b9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84560264"
---
# <a name="partner-topics-in-azure-event-grid-preview"></a>Azure Event Grid의 파트너 항목 (미리 보기)
파트너 토픽을 사용 하 여 타사 이벤트 원본을 Azure Event Grid에 직접 연결할 수 있습니다. 이러한 통합을 통해 Azure 서비스의 이벤트를 구독하는 것과 같은 방법으로 파트너의 이벤트를 구독할 수 있습니다. 

## <a name="available-partners"></a>사용 가능한 파트너
Event Grid 파트너 항목을 통해 제공 되는 첫 번째 파트너는 좋아하게 되었습니다입니다. [좋아하게 되었습니다 partner 항목](auth0-overview.md) 을 사용 하 여 좋아하게 되었습니다 및 Azure 계정을 연결할 수 있습니다. 통합을 사용 하면 실시간으로 좋아하게 되었습니다 이벤트에 대응 하 고, 로그 하 고, 모니터링할 수 있습니다.

[이를 시도 하려면](auth0-how-to.md)좋아하게 되었습니다 계정에 로그인 하 고 Event Grid 통합을 만듭니다. 좋아하게 되었습니다에서 **만들기** 를 선택 하면 Azure 계정에 보류 중인 좋아하게 되었습니다 항목이 표시 됩니다. **활성화**를 선택 하 고 다른 이벤트 원본과 마찬가지로 이벤트를 라우팅, 필터링 및 전달 하는 Event Grid 구독을 만들 수 있습니다.

## <a name="pricing"></a>가격 책정
파트너 토픽은 시스템 토픽과 동일한 작업 비율로 청구됩니다.

## <a name="limits"></a>제한
파트너 토픽은 공개 미리 보기로 제공 됩니다. 공개 미리 보기 중에는 파트너 토픽에 시스템 항목 및 사용자 지정 항목과 [동일한 제한이](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#event-grid-limits) 적용 됩니다.

## <a name="how-do-i-become-an-event-grid-partner"></a>Event Grid 파트너가 되려면 어떻게 해야 하나요?
이 출시를 지원하기 위해 만든 인프라를 통해 새 파트너는 이벤트 기능을 Event Grid와 쉽고 빠르게 통합할 수 있습니다. 자세한 내용은 [파트너 온 보 딩 설명서](partner-onboarding-overview.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [좋아하게 되었습니다 partner 항목](auth0-overview.md)
- [좋아하게 되었습니다 partner 항목을 사용 하는 방법](auth0-how-to.md)
- [Event Grid 파트너 되기](partner-onboarding-overview.md)