---
title: Azure Media Services v3를 사용할 수 있는 클라우드 및 지역 | Microsoft Docs
description: 이 문서에서는 Azure Media Services v3를 사용할 수 있는 Azure 클라우드 및 지역에 대해 설명합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/11/2019
ms.author: juliako
ms.openlocfilehash: 8eb49010d89c3039f46e5c84cd305b7d0b5ca025
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2019
ms.locfileid: "54306975"
---
# <a name="clouds-and-regions-in-which-azure-media-services-v3-exists"></a>Azure Media Services v3가 있는 클라우드 및 지역

Azure Media Services v3는 전 세계 Azure, Azure Government, Azure Germany, Azure China 21Vianet에서 Azure Resource Manager 매니페스트를 통해 사용할 수 있습니다. 그러나 모든 Media Services 기능을 모든 Azure 클라우드에서 사용할 수 있는 것은 아닙니다. 이 문서에서는 기본 Media Services v3 구성 요소의 가용성을 설명합니다.

## <a name="feature-availability-in-azure-clouds"></a>Azure 클라우드의 기능 가용성

| 기능|전 세계 Azure 지역 | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| [Azure EventGrid](reacting-to-media-services-events.md) | 사용 가능 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |
| [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) |  사용 가능 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |
| [AudioAnalyzerPreset](analyzing-video-audio-files-concept.md) |  사용 가능 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |
| [StandardEncoderPreset](encoding-concept.md) | 사용 가능 | 사용 가능 | 사용 가능 | 사용 가능 |
| [LiveEvents](live-streaming-overview.md) | 사용 가능 | 사용 가능 | 사용 가능 | 사용 가능 |
| [StreamingEndpoints](streaming-endpoint-concept.md) | 사용 가능 | 사용 가능 | 사용 가능 | 사용 가능 |

## <a name="regions"></a>영역 

**location** 매개 변수를 제공해야 하는 경우 지역 코드 이름을 **location** 값으로 제공해야 합니다. 계정이 있고 호출을 라우팅해야 하는 지역의 코드 이름을 가져오기 위해 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)에서 다음 줄을 실행할 수 있습니다.

```bash
az account list-locations
```

일단 위에 표시된 줄을 실행하면 모든 Azure 지역 목록이 표시됩니다. 찾으려는 *displayName*이 있는 Azure 지역으로 이동한 후 **location** 매개 변수에 대해 해당 *name* 값을 사용합니다.

예를 들어 미국 서부 2의 Azure 지역에서(아래에 표시됨)는 **location** 매개 변수에 "westus2"를 사용합니다.

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/00000000-23da-4fce-b59c-f6fb9513eeeb/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="next-steps"></a>다음 단계

[Media Services v3 개요](media-services-overview.md)
