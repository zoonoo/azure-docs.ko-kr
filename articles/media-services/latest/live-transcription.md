---
title: 라이브 전사
titleSuffix: Azure Media Services
description: Azure Media Services 라이브 기록에 대해 알아봅니다.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79499865"
---
# <a name="live-transcription-preview"></a>라이브 기록 (미리 보기)

Azure 미디어 서비스는 다양 한 프로토콜에서 비디오, 오디오 및 텍스트를 제공 합니다. MPEG-2 또는 HLS/CMAF를 사용 하 여 라이브 스트림을 게시 하면 비디오 및 오디오와 함께 서비스가 IMSC 1.1 호환 TTML에 transcribed 텍스트를 제공 합니다. 배달은 MPEG-4 30 (ISO/IEC 14496-30) 조각으로 패키지 됩니다. HLS/TS를 통해 배달을 사용 하는 경우 텍스트는 청크 분할 VTT로 배달 됩니다.

이 문서에서는 Azure Media Services v3로 라이브 이벤트를 스트리밍할 때 실시간 기록을 사용 하도록 설정 하는 방법을 설명 합니다. 계속 하기 전에 Media Services v3 REST Api를 사용 하는 방법을 잘 알고 있어야 합니다. 자세한 내용은 [이 자습서](stream-files-tutorial-with-rest.md) 를 참조 하세요. 또한 [라이브 스트리밍](live-streaming-overview.md) 개념에 대해 잘 알고 있어야 합니다. Media Services 자습서를 [사용 하 여 스트림 라이브](stream-live-tutorial-with-api.md) 를 완료 하는 것이 좋습니다.

> [!NOTE]
> 현재 라이브 기록은 미국 서 부 2 지역 에서만 미리 보기 기능으로 사용할 수 있습니다. 텍스트에 대 한 음성 단어 기록을 지원 합니다. 이 기능에 대 한 API 참조는 아래에 있습니다. 미리 보기로 볼륨 REST 문서에서는 세부 정보를 사용할 수 없습니다.

## <a name="creating-the-live-event"></a>라이브 이벤트 만들기

라이브 이벤트를 만들려면 다음과 같이 PUT 작업을 2019-05-01-preview 버전으로 보냅니다.

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview&autoStart=true 
```

작업에는 다음 본문이 있습니다. 여기서 통과 라이브 이벤트는 수집 프로토콜로 RTMP을 사용 하 여 생성 됩니다. 이러한 속성을 추가 합니다. Language에 대해 유일 하 게 허용 되는 값은 en-us입니다.

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

"실행 중" 상태가 될 때까지 라이브 이벤트의 상태를 폴링하고 이제 기여 RTMP 피드를 보낼 수 있음을 나타냅니다. 이제 미리 보기 피드를 확인 하 고 라이브 출력을 만드는 것과 같이이 자습서와 동일한 단계를 수행할 수 있습니다.

## <a name="transcription-delivery-and-playback"></a>기록 배달 및 재생

서비스에서 동적 패키징을 사용 하 여 다양 한 프로토콜에서 비디오, 오디오 및 텍스트를 배달 하는 방법에 대 한 [동적 패키징 개요](dynamic-packaging-overview.md#to-prepare-your-source-files-for-delivery) 문서를 검토 합니다. MPEG-2 또는 HLS/CMAF를 사용 하 여 라이브 스트림을 게시 하면 비디오 및 오디오와 함께 서비스가 IMSC 1.1 호환 TTML에 transcribed 텍스트를 제공 합니다. 이 배달은 MPEG-4 30 (ISO/IEC 14496-30) 조각으로 패키지 됩니다. HLS/TS를 통해 배달을 사용 하는 경우 텍스트는 청크 분할 된 VTT로 배달 됩니다. [Azure Media Player](use-azure-media-player.md) 와 같은 웹 플레이어를 사용 하 여 스트림을 재생할 수 있습니다.  

> [!NOTE]
> Azure Media Player 사용 하는 경우 버전 2.3.3 이상을 사용 합니다.

## <a name="known-issues"></a>알려진 문제

미리 보기의 경우 라이브 기록의 알려진 문제는 다음과 같습니다.

* 이 기능은 미국 서 부 2 에서만 사용할 수 있습니다.
* 앱은 [Media Services V3 OpenAPI 사양](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2019-05-01-preview/streamingservice.json)에 설명 된 미리 보기 api를 사용 해야 합니다.
* 유일 하 게 지원 되는 언어는 영어 (en-us)입니다.
* 콘텐츠 보호를 사용 하면 AES 봉투 암호화만 지원 됩니다.

## <a name="next-steps"></a>다음 단계

* [Media Services 개요](media-services-overview.md)
