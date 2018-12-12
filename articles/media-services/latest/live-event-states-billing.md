---
title: Azure Media Services의 LiveEvent 상태 및 청구 | Microsoft Docs
description: 이 항목에서는 Azure Media Services LiveEvent 상태 및 청구에 대해 간략하게 설명합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 11/16/2018
ms.author: juliako
ms.openlocfilehash: 1a49f62d7b5e21fe9d6483f71b729a9100aff1a3
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2018
ms.locfileid: "52585437"
---
# <a name="liveevent-states-and-billing"></a>LiveEvent 상태 및 청구

Azure Media Services에서 LiveEvent는 상태가 **실행 중**으로 전환되는 즉시 청구를 시작합니다. LiveEvent가 청구되지 않도록 하려면 LiveEvent를 중지해야 합니다.

[LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents)의 **LiveEventEncodingType**이 표준(기본)으로 설정된 경우 Media Services는 입력 피드가 손실된 후 12시간 동안 여전히 **실행 중** 상태이며 실행 중인 **LiveOutput**이 없는 LiveEvent를 모두 자동 종료합니다. 그러나 LiveEvent가 **실행 중** 상태였던 시간에 대한 요금은 청구됩니다.

## <a name="states"></a>상태

LiveEvent는 다음 상태 중 하나일 수 있습니다.

|시스템 상태|설명|
|---|---|
|**중지**| LiveEvent를 만든 후 초기 상태입니다(autostart가 true로 설정되지 않은 경우). 이 상태에서는 요금이 청구되지 않습니다. 이 상태에서 LiveEvent 속성을 업데이트할 수 있지만 스트리밍은 허용되지 않습니다.|
|**시작 중**| LiveEvent가 시작되고, 리소스가 할당되고 있습니다. 이 상태에서는 요금이 청구되지 않습니다. 이 상태 중에는 업데이트나 스트리밍이 허용되지 않습니다. 오류가 발생하면 LiveEvent가 중지 상태로 돌아갑니다.|
|**실행 중**| LiveEvent 리소스가 할당되고, 수집 및 미리 보기 URL이 생성되었으며, 라이브 스트림을 수신할 수 있습니다. 이 시점에는 청구가 활성 상태입니다. 추가 청구를 중지하려면 LiveEvent 리소스에 대해 명시적으로 Stop을 호출해야 합니다.|
|**중지 중**| LiveEvent가 중지되고, 리소스가 프로비전 해제되고 있습니다. 이 일시적인 상태에서는 요금이 청구되지 않습니다. 이 상태 중에는 업데이트나 스트리밍이 허용되지 않습니다.|
|**삭제 중**| LiveEvent가 삭제됩니다. 이 일시적인 상태에서는 요금이 청구되지 않습니다. 이 상태 중에는 업데이트나 스트리밍이 허용되지 않습니다.|

## <a name="next-steps"></a>다음 단계

- [라이브 스트리밍 개요](live-streaming-overview.md)
- [라이브 스트리밍 자습서](stream-live-tutorial-with-api.md)
