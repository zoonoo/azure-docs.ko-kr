---
title: Azure Media Services 라이브 이벤트 오류 코드 | Microsoft Docs
description: 이 문서에는 라이브 이벤트 오류 코드가 나열 되어 있습니다.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2020
ms.author: juliako
ms.openlocfilehash: bfbef771d33ad4d63ec8eaef83331e497d476071
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599471"
---
# <a name="media-services-live-event-error-codes"></a>Media Services 라이브 이벤트 오류 코드

다음 표에서는 [라이브 이벤트](live-events-outputs-concept.md) 오류 코드를 보여 줍니다.

|Error|Description|
|---|---| 
|MPE_INGEST_FRAMERATE_EXCEEDED|이 오류는 들어오는 인코더가 라이브 이벤트/채널 인코딩에 대해 30fps를 초과 하는 스트림을 보내는 경우에 발생 합니다.|
|MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED|이 오류는 들어오는 인코더가 라이브 이벤트/채널 인코딩에 대 한 1920x1088 및 통과 라이브 이벤트/채널에 대 한 4096 x 2160 해상도를 초과 하는 스트림을 보내는 경우에 발생 합니다.|

## <a name="see-also"></a>참고 항목

[스트리밍 끝점 (원본) 오류 코드](streaming-endpoint-error-codes.md)

## <a name="next-steps"></a>다음 단계

[자습서: Media Services로 라이브 스트리밍](stream-live-tutorial-with-api.md)
