---
title: '자습서: 데이터 웨어하우스로 Event Hubs 데이터 보내기 - Event Grid'
description: Azure Functions 및 Event Hubs 트리거를 통해 Azure Synapse Analytics에서 Event Hubs 캡처된 데이터를 저장하는 방법을 설명합니다.
ms.topic: tutorial
ms.date: 12/07/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 2887cdc1b4666daf73a21a76907f80d50d345d82
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2021
ms.locfileid: "112414090"
---
# <a name="tutorial-stream-big-data-into-a-data-warehouse"></a>자습서: 데이터 웨어하우스로 빅 데이터 스트림
Azure [Event Grid](overview.md)는 앱과 서비스의 알림 또는 이벤트에 응답하는 데 사용할 수 있는 인텔리전트 이벤트 라우팅 서비스입니다. 예를 들어 Azure 함수를 트리거하여 Blob Storage 또는 Data Lake Storage에 캡처된 Event Hubs 데이터를 처리할 수 있습니다. 이 [샘플](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo)에서는 Event Grid 및 Azure Functions를 사용하여 캡처된 Event Hubs를 Blob 스토리지에서 Azure Synapse Analytics, 특히 전용 SQL 풀로 마이그레이션하는 방법을 보여줍니다.

[!INCLUDE [event-grid-event-hubs-functions-synapse-analytics.md](./includes/event-grid-event-hubs-functions-synapse-analytics.md)]

## <a name="next-steps"></a>다음 단계

* Azure 메시징 서비스의 차이점을 알아보려면 [메시지를 배달하는 Azure 서비스 중에서 선택](compare-messaging-services.md)을 참조하세요.
* Event Grid에 대한 소개는 [Event Grid 정보](overview.md)를 참조하세요.
* Event Hubs 캡처에 대한 소개는 [Azure Portal을 사용하여 Event Hubs 캡처를 사용하도록 설정](../event-hubs/event-hubs-capture-enable-through-portal.md)을 참조하세요.
* 샘플 설정 및 실행에 대한 자세한 내용은 [Event Hubs 캡처 및 Event Grid 샘플](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo)을 참조하세요.
