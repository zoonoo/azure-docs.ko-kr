---
title: Azure Media Services에서 라이브 이벤트 상태 및 청구 Microsoft Docs
description: 이 항목에서는 Azure Media Services 라이브 이벤트 상태 및 청구에 대 한 개요를 제공 합니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: inhenkel
ms.openlocfilehash: 2d3d3f5c56bd42aeb148c19fefebc0e7d364cd1c
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92782368"
---
# <a name="live-event-states-and-billing"></a>라이브 이벤트 상태 및 청구

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services에서 라이브 이벤트는 상태를 **실행 중** 또는 **대기** 중으로 전환 하는 즉시 청구를 시작 합니다. 서비스를 통해 흐르는 비디오가 없는 경우에도 요금이 청구 됩니다. 청구에서 라이브 이벤트를 중지 하려면 라이브 이벤트를 중지 해야 합니다. 라이브 기록을 라이브 이벤트와 동일한 방식으로 청구 합니다.

[라이브 이벤트](/rest/api/media/liveevents) 의 **LiveEventEncodingType** 표준 또는 Premium1080p로 설정 된 경우 입력 피드가 손실 된 후 12 시간 동안 계속 **실행** 되 고 있는 라이브 이벤트를 자동 Media Services으로 종료 하 고, 실행 중인 **라이브 출력이** 없습니다. 그러나 라이브 이벤트가 **실행** 상태 였던 시간에 대 한 요금이 계속 청구 됩니다.

> [!NOTE]
> 통과 라이브 이벤트는 자동으로 종료 되지 않으며 과도 한 청구를 방지 하기 위해 API를 통해 명시적으로 중지 해야 합니다.

## <a name="states"></a>상태

라이브 이벤트는 다음 상태 중 하나일 수 있습니다.

|시스템 상태|Description|
|---|---|
|**중지됨**| 자동 시작이 true로 설정 되지 않은 경우 생성 후 라이브 이벤트의 초기 상태입니다. 이 상태에서는 청구가 발생 하지 않습니다. 라이브 이벤트에서 입력을 수신할 수 없습니다. |
|**시작 중**| 라이브 이벤트가 시작 되 고 리소스를 할당 하는 중입니다. 이 상태에서는 요금이 청구되지 않습니다.  오류가 발생 하는 경우 라이브 이벤트는 중지 됨 상태로 돌아갑니다.|
| **할당** | 할당 작업이 라이브 이벤트에서 호출 되었으며이 라이브 이벤트에 대해 리소스가 프로 비전 되 고 있습니다. 이 작업이 성공적으로 완료 되 면 라이브 이벤트가 대기 상태로 전환 됩니다.
|**상태의**| 라이브 이벤트 리소스가 프로 비전 되었으며 시작할 준비가 되었습니다. 이 상태에서는 청구가 발생 합니다.  대부분의 속성을 계속 업데이트할 수 있지만이 상태에서는 수집 또는 스트리밍이 허용 되지 않습니다.
|**실행 중**| 라이브 이벤트 리소스가 할당 되 고, 수집 및 미리 보기 Url이 생성 되었으며, 라이브 스트림을 받을 수 있습니다. 이 시점에는 청구가 활성 상태입니다. 추가 청구를 중지 하려면 라이브 이벤트 리소스에서 Stop을 명시적으로 호출 해야 합니다.|
|**중지 중**| 라이브 이벤트가 중지 되 고 리소스가 프로 비전 해제 되 고 있습니다. 이 일시적인 상태에서는 요금이 청구되지 않습니다. |
|**삭제 중**| 라이브 이벤트를 삭제 하 고 있습니다. 이 일시적인 상태에서는 요금이 청구되지 않습니다. |

라이브 이벤트를 만들 때 라이브 이벤트를 사용 하도록 선택할 수 있습니다. 이 작업을 수행 하는 경우 라이브 이벤트가 **실행 중** 상태일 때마다 실시간으로 청구 됩니다. 라이브 이벤트를 통과 하는 오디오가 없는 경우에도 요금이 청구 됩니다.

## <a name="next-steps"></a>다음 단계

- [라이브 스트리밍 개요](live-streaming-overview.md)
- [라이브 스트리밍 자습서](stream-live-tutorial-with-api.md)
