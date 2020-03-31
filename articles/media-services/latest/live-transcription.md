---
title: 라이브 전사
titleSuffix: Azure Media Services
description: Azure 미디어 서비스 라이브 전사에 대해 알아봅니다.
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
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: b364b6e70e3b5723c483bc3435f0c3a152c03aa9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499865"
---
# <a name="live-transcription-preview"></a>라이브 전사(미리보기)

Azure Media Service는 다양한 프로토콜로 비디오, 오디오 및 텍스트를 제공합니다. MPEG-DASH 또는 HLS/CMAF를 사용하여 라이브 스트림을 게시한 다음 비디오 및 오디오와 함께 IMSC1.1 호환 TTML로 전사된 텍스트를 제공합니다. 배송은 MPEG-4 Part 30(ISO/IEC 14496-30) 조각으로 포장됩니다. HLS/TS를 통해 배달을 사용하는 경우 텍스트가 청크 VTT로 전달됩니다.

이 문서에서는 Azure Media Services v3를 사용하여 라이브 이벤트를 스트리밍할 때 라이브 전사를 활성화하는 방법을 설명합니다. 계속하기 전에 미디어 서비스 v3 REST API 사용에 대해 잘 알고 있는지 확인하십시오(자세한 내용은 [이 자습서](stream-files-tutorial-with-rest.md) 참조). 또한 [라이브 스트리밍](live-streaming-overview.md) 개념에 대해서도 잘 알고 있어야 합니다. 미디어 서비스 자습서를 [사용하여 라이브 스트림을](stream-live-tutorial-with-api.md) 완료하는 것이 좋습니다.

> [!NOTE]
> 현재 라이브 전사는 미국 서부 2 지역에서 미리 보기 기능으로만 사용할 수 있습니다. 그것은 텍스트에 영어로 음성 단어의 전사를 지원합니다. 이 기능에 대한 API 참조는 아래에 있습니다.이 기능은 미리 보기에서 becasuse이며 REST 문서에서 세부 정보를 사용할 수 없습니다.

## <a name="creating-the-live-event"></a>라이브 이벤트 만들기

라이브 이벤트를 만들려면 PUT 작업을 2019-05-01 미리 보기 버전으로 보냅니다.

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview&autoStart=true 
```

작업에는 다음 본문이 있습니다(RTMP를 인제스트 프로토콜로 통과 라이브 이벤트가 생성되는 경우). 전사 속성의 추가를 기록합니다. 언어에 대해 허용되는 유일한 값은 en-US입니다.

```
{ 
  "properties": { 
    "description": "Demonstrate how to enable live transcriptions", 
    "input": { 
      "streamingProtocol": "RTMP", 
      "accessControl": { 
        "ip": { 
          "allow": [ 
            { 
              "name": "Allow All", 
              "address": "0.0.0.0", 
              "subnetPrefixLength": 0 
            } 
          ] 
        } 
      } 
    }, 
    "preview": { 
      "accessControl": { 
        "ip": { 
          "allow": [ 
            { 
              "name": "Allow All", 
              "address": "0.0.0.0", 
              "subnetPrefixLength": 0 
            } 
          ] 
        } 
      } 
    }, 
    "encoding": { 
      "encodingType": "None" 
    }, 
    "transcriptions": [ 
      { 
        "language": "en-US" 
      } 
    ], 
    "vanityUrl": false, 
    "streamOptions": [ 
      "Default" 
    ] 
  }, 
  "location": "West US 2" 
} 
```

이제 기여 RTMP 피드를 보낼 수 있음을 나타내는 "실행 중" 상태가 될 때까지 라이브 이벤트의 상태를 폴링합니다. 이제 미리 보기 피드를 확인하고 라이브 출력을 만드는 것과 같이 이 자습서와 동일한 단계를 수행할 수 있습니다.

## <a name="transcription-delivery-and-playback"></a>전사 전달 및 재생

[다이내믹 패키징 개요](dynamic-packaging-overview.md#to-prepare-your-source-files-for-delivery) 문서를 검토하여 다이내믹 패키징을 사용하여 다양한 프로토콜로 비디오, 오디오 및 텍스트를 전달하는 방법을 검토합니다. MPEG-DASH 또는 HLS/CMAF를 사용하여 라이브 스트림을 게시한 다음 비디오 및 오디오와 함께 IMSC1.1 호환 TTML로 전사된 텍스트를 제공합니다. 이 배달은 MPEG-4 Part 30(ISO/IEC 14496-30) 조각으로 포장됩니다. HLS/TS를 통해 배달을 사용하는 경우 텍스트가 청크 VTT로 전달됩니다. [Azure Media Player와](use-azure-media-player.md) 같은 웹 플레이어를 사용하여 스트림을 재생할 수 있습니다.  

> [!NOTE]
> Azure 미디어 플레이어를 사용하는 경우 버전 2.3.3 이상을 사용합니다.

## <a name="known-issues"></a>알려진 문제

미리 보기의 경우 라이브 전사와 관련된 알려진 문제는 다음과 같습니다.

* 이 기능은 미국 서부 2에서만 사용할 수 있습니다.
* 앱은 [미디어 서비스 v3 OpenAPI 사양에](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2019-05-01-preview/streamingservice.json)설명된 미리 보기 API를 사용해야 합니다.
* 유일한 지원되는 언어는 영어 (en-us)입니다.
* 콘텐츠 보호를 사용하면 AES 봉투 암호화만 지원됩니다.

## <a name="next-steps"></a>다음 단계

* [Media Services 개요](media-services-overview.md)
