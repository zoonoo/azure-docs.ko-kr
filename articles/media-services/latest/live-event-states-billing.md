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
ms.date: 01/28/2019
ms.author: juliako
ms.openlocfilehash: 17fead25840e45f98478a6eb6c146bcc261dfe75
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55158012"
---
# <a name="live-event-states-and-billing"></a>라이브 이벤트 상태 및 청구

Azure Media Services에서 라이브 이벤트는 상태가 **실행 중**으로 전환되는 즉시 청구를 시작합니다. 라이브 이벤트가 청구되지 않도록 하려면 라이브 이벤트를 중지해야 합니다.

[라이브 이벤트](https://docs.microsoft.com/rest/api/media/liveevents)의 **LiveEventEncodingType**이 표준으로 설정된 경우 Media Services는 입력 피드가 손실된 후 12시간 동안 여전히 **실행 중** 상태이며 실행 중인 **라이브 출력**이 없는 라이브 이벤트를 모두 자동 종료합니다. 그러나 라이브 이벤트가 **실행 중** 상태였던 시간에 대한 요금은 청구됩니다.

## <a name="states"></a>상태

라이브 이벤트는 다음 상태 중 하나일 수 있습니다.

|시스템 상태|설명|
|---|---|
|**중지**| 라이브 이벤트를 만든 후 초기 상태입니다(autostart가 true로 설정되지 않은 경우). 이 상태에서는 요금이 청구되지 않습니다. 이 상태에서 라이브 이벤트 속성을 업데이트할 수 있지만 스트리밍은 허용되지 않습니다.|
|**시작 중**| 라이브 이벤트가 시작되고, 리소스가 할당되고 있습니다. 이 상태에서는 요금이 청구되지 않습니다. 이 상태 중에는 업데이트나 스트리밍이 허용되지 않습니다. 오류가 발생하면 라이브 이벤트가 중지 상태로 돌아갑니다.|
|**실행 중**| 라이브 이벤트 리소스가 할당되고, 수집 및 미리 보기 URL이 생성되었으며, 라이브 스트림을 수신할 수 있습니다. 이 시점에는 청구가 활성 상태입니다. 추가 청구를 중지하려면 라이브 이벤트 리소스에 대해 명시적으로 Stop을 호출해야 합니다.|
|**중지 중**| 라이브 이벤트가 중지되고, 리소스가 프로비전 해제되고 있습니다. 이 일시적인 상태에서는 요금이 청구되지 않습니다. 이 상태 중에는 업데이트나 스트리밍이 허용되지 않습니다.|
|**삭제 중**| 라이브 이벤트가 삭제됩니다. 이 일시적인 상태에서는 요금이 청구되지 않습니다. 이 상태 중에는 업데이트나 스트리밍이 허용되지 않습니다.|

## <a name="next-steps"></a>다음 단계

- [라이브 스트리밍 개요](live-streaming-overview.md)
- [라이브 스트리밍 자습서](stream-live-tutorial-with-api.md)
