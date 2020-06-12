---
title: Azure Event Grid 파트너 토픽
description: Azure Event Grid를 사용하여 타사 Event Grid SaaS 및 PaaS 파트너의 이벤트를 Azure 서비스로 직접 보냅니다.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: babanisa
ms.openlocfilehash: 4546874b8a6cd8a7e45c3e6957a5181d66c7433f
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83690039"
---
# <a name="partner-topics-in-azure-event-grid-preview"></a>Azure Event Grid의 파트너 토픽(미리 보기)
파트너 토픽을 통해 타사 이벤트 원본을 Event Grid에 직접 연결할 수 있습니다. 이러한 통합을 통해 Azure 서비스의 이벤트를 구독하는 것과 같은 방법으로 파트너의 이벤트를 구독할 수 있습니다. 

## <a name="available-partners"></a>사용 가능한 파트너
Event Grid 파트너 토픽을 통해 제공되는 첫 번째 파트너는 Auth0입니다. [Auth0 토픽](auth0-overview.md)을 사용하면 Auth0 및 Azure 계정을 연결할 수 있습니다. Auth0 이벤트를 실시간으로 반응, 기록 및 모니터링하기 위한 통합입니다.

Auth0 계정에 로그인하고 Event Grid 통합을 만들어 지금 [사용해보세요](auth0-how-to.md). Auth0에서 만들기를 클릭하면 Azure 계정에 보류 중인 Auth0 토픽이 표시됩니다. 활성화를 클릭하면 다른 이벤트 원본과 마찬가지로 이벤트 구독을 만들고, 라우팅하고, 필터링하고, 전달할 수 있습니다.

## <a name="pricing"></a>가격 책정
파트너 토픽은 시스템 토픽과 동일한 작업 비율로 청구됩니다.

## <a name="limits"></a>제한
파트너 토픽은 공개 미리 보기로 제공됩니다. 공개 미리 보기 중에 파트너 토픽은 시스템 항목 및 사용자 지정 항목으로 [동일한 제한](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#event-grid-limits)이 적용됩니다.

## <a name="how-do-i-become-an-event-grid-partner"></a>Event Grid 파트너가 되려면 어떻게 해야 하나요?
이 출시를 지원하기 위해 만든 인프라를 통해 새 파트너는 이벤트 기능을 Event Grid와 쉽고 빠르게 통합할 수 있습니다. 자세한 내용은 [파트너 온보딩 설명서](partner-onboarding-overview.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Auth0 파트너 토픽](auth0-overview.md)
- [Auth0 파트너 토픽을 사용하는 방법](auth0-how-to.md)
- [Event Grid 파트너 되기](partner-onboarding-overview.md)