---
title: 개념-Azure Event Grid IoT Edge | Microsoft Docs
description: IoT Edge에 대 한 Event Grid 개념.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 73309e10e88c11e639e6ac6fd3bb061e1b5c685b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "72992549"
---
# <a name="event-grid-concepts"></a>Event Grid 개념

이 문서에서는 Azure Event Grid의 주요 개념을 설명합니다.

## <a name="events"></a>이벤트

이벤트는 시스템에서 발생하는 무언가를 완벽히 설명하는 가장 작은 크기의 정보입니다. 모든 이벤트에는 이벤트의 원본, 이벤트가 발생한 시간 및 고유 식별자와 같은 일반적인 정보가 있습니다. 또한 모든 이벤트에는 특정 유형의 이벤트에만 관련된 특정 정보도 있습니다. 최대 1mb 크기의 이벤트에 대 한 지원은 현재 미리 보기 상태입니다.

이벤트에 포함 된 속성은 [Azure Event Grid 이벤트 스키마](event-schemas.md)를 참조 하세요.

## <a name="publishers"></a>게시자

게시자는 Event Grid에 이벤트를 보내기로 결정한 사용자나 조직입니다. 사용자의 자체 애플리케이션에서 이벤트를 게시할 수 있습니다.

## <a name="event-sources"></a>이벤트 원본

이벤트 원본은 이벤트가 발생하는 위치입니다. 각 이벤트 원본은 하나 이상의 이벤트 유형과 관련이 있습니다. 예를 들어 Azure Storage는 이벤트가 생성된 Blob에 대한 이벤트 원본입니다. 애플리케이션은 사용자가 정의한 사용자 지정 이벤트에 대한 이벤트 원본입니다. 이벤트 원본은 Event Grid에 이벤트를 보내는 역할을 담당합니다.

## <a name="topics"></a>토픽

Event Grid 항목에는 원본이 이벤트를 보내는 엔드포인트가 제공됩니다. 게시자는 Event Grid 항목을 만들고 이벤트 원본에 항목이 하나 필요한지 또는 둘 이상 필요한지 여부를 결정합니다. 항목은 관련 이벤트의 컬렉션에 사용됩니다. 특정 이벤트 형식에 응답하려면 구독자가 구독할 토픽을 결정합니다.

응용 프로그램을 디자인할 때 만들 항목의 수를 유연 하 게 결정할 수 있습니다. 대규모 솔루션의 경우 관련된 이벤트의 각 범주에 대한 사용자 지정 토픽을 만듭니다. 사용자 계정을 수정하고 주문을 처리하는 것과 관련된 이벤트를 보내는 애플리케이션을 예로 들 수 있습니다. 이벤트 처리기가 이벤트의 두 범주 모두를 원할 가능성은 낮습니다. 두 개의 사용자 지정 토픽을 만들고 이벤트 처리기가 관심 있는 토픽에 구독할 수 있도록 합니다. 소규모 솔루션의 경우 모든 이벤트를 단일 토픽으로 전송할 수도 있습니다. 원하는 이벤트 유형에 대한 이벤트 구독자를 필터링할 수 있습니다.

Event Grid에서 항목을 관리 하는 방법에 대 한 [REST API 설명서](api.md) 를 참조 하세요.

## <a name="event-subscriptions"></a>이벤트 구독

구독은 사용자가 항목의 어떤 이벤트를 받고 싶어하는지를 Event Grid에 알려줍니다. 구독을 만들 때 이벤트 처리를 위한 엔드포인트를 제공합니다. 엔드포인트에 전송되는 이벤트를 필터링할 수 있습니다. 

Event Grid에서 구독을 관리 하는 방법에 대 한 [REST API 설명서](api.md) 를 참조 하세요.

## <a name="event-handlers"></a>이벤트 처리기

Event Grid 측면에서 볼 때 이벤트 처리기는 이벤트가 전송된 위치입니다. 처리기는 이벤트를 처리 하기 위해 추가 작업을 수행 합니다. Event Grid는 여러 가지 처리기 유형을 지원합니다. 지원 되는 Azure 서비스 또는 고유한 웹 후크를 처리기로 사용할 수 있습니다. 처리기의 형식에 따라 Event Grid는 이벤트의 배달을 보장하는 다양한 메커니즘을 따릅니다. 대상 이벤트 처리기가 HTTP 웹 후크가 면 처리기가 상태 코드를 반환할 때 이벤트를 다시 시도 합니다 `200 – OK` . Edge 허브의 경우 예외가 발생 하지 않고 이벤트가 전달 되 면 성공으로 간주 됩니다.

## <a name="security"></a>보안

Event Grid는 토픽 구독 및 게시에 대한 보안을 제공합니다. 자세한 내용은 [Event Grid 보안 및 인증](security-authentication.md)을 참조하세요.

## <a name="event-delivery"></a>이벤트 전달

Event Grid에서 이벤트가 구독자의 엔드포인트에서 수신되었는지 확인할 수 없는 경우 이벤트를 다시 배달합니다. 자세한 내용은 [Event Grid 메시지 배달 및 재시도](delivery-retry.md)를 참조하세요.

## <a name="batching"></a>일괄 처리

사용자 지정 토픽을 사용하는 경우 이벤트를 항상 배열에 게시해야 합니다. 처리량이 낮은 시나리오의 경우 배열에는 하나의 값만 있습니다. 대량 사용 사례의 경우에는 게시 당 여러 이벤트를 함께 배치 하 여 효율성을 높이는 것이 좋습니다. 일괄 처리의 최대 크기는 1MB입니다. 각 이벤트는 1mb (미리 보기) 보다 크지 않아야 합니다.