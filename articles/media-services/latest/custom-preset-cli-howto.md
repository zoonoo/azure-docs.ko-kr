---
title: 미디어 서비스 v3 Azure CLI를 사용하여 사용자 지정 변환 인코딩 | 마이크로 소프트 문서
description: 이 항목에서는 Azure Media Services v3를 사용하여 Azure CLI를 사용하여 사용자 지정 변환을 인코딩하는 방법을 보여 주며 있습니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 05/14/2019
ms.author: juliako
ms.openlocfilehash: 7c1b446ccf04199449f012e738f6a03660735f50
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382956"
---
# <a name="how-to-encode-with-a-custom-transform---azure-cli"></a>사용자 지정 변환으로 인코딩하는 방법 - Azure CLI

Azure Media Services로 인코딩할 때 [스트리밍 파일](stream-files-cli-quickstart.md#create-a-transform-for-adaptive-bitrate-encoding) 빠른 시작에서 설명한 것처럼 업계 모범 사례를 기반으로 권장되는 기본 제공 사전 설정 중 하나를 빠르게 시작할 수 있습니다. 특정 시나리오 또는 장치 요구 사항을 대상으로 사용자 지정 사전 설정을 빌드할 수도 있습니다.

## <a name="considerations"></a>고려 사항

사용자 지정 사전 설정을 만들 때 다음 고려 사항이 적용됩니다.

* AVC 콘텐츠의 높이 및 너비에 대한 모든 값은 4의 배수여야 합니다.
* Azure Media Services v3에서 모든 인코딩 비트 레이트(bitrates)는 초당 비트입니다. 이것은 단위로 킬로비트 / 초를 사용하는 v2 API의 사전 설정과 다릅니다. 예를 들어 v2의 비트 레이트(128(킬로비트/초)로 지정된 경우 v3에서는 128000(비트/초)으로 설정됩니다.

## <a name="prerequisites"></a>사전 요구 사항

[Media Services 계정 만들기](create-account-cli-how-to.md)

리소스 그룹 이름과 Media Services 계정 이름을 기억해 두어야 합니다.

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-custom-preset"></a>사용자 지정 사전 설정 정의

다음 예제는 새 변환의 요청 본문을 정의합니다. 이 변환을 사용할 때 생성할 출력 집합을 정의합니다.

이 예제에서는 먼저 오디오 인코딩을 위한 AacAudio 레이어와 비디오 인코딩을 위한 두 개의 H264Video 레이어를 추가합니다. 비디오 레이어에서 출력 파일 이름에 사용할 수 있도록 레이블을 할당합니다. 다음으로 출력에 축소판 그림도 포함하기를 원합니다. 아래 예제에서는 입력 비디오 해상도의 50%에서 생성된 PNG 형식으로 이미지를 지정하고 입력 비디오 길이의 {25%, 50%, 75}의 세 개의 타임스탬프에서 이미지를 지정합니다. 마지막으로 비디오 + 오디오용, 썸네일용 파일 등 출력 파일의 형식을 지정합니다. 여러 개의 H264Layers가 있으므로 레이어당 고유한 이름을 생성하는 매크로를 사용해야 합니다. 우리는 또는 `{Label}` `{Bitrate}` 매크로를 사용할 수 있습니다, 예제는 전자를 보여줍니다.

이 변환을 파일에 저장하려고 합니다. 이 예제에서는 파일의 `customPreset.json`이름을 지정합니다.

```json
{
    "@odata.type": "#Microsoft.Media.StandardEncoderPreset",
    "codecs": [
        {
            "@odata.type": "#Microsoft.Media.AacAudio",
            "channels": 2,
            "samplingRate": 48000,
            "bitrate": 128000,
            "profile": "AacLc"
        },
        {
            "@odata.type": "#Microsoft.Media.H264Video",
            "keyFrameInterval": "PT2S",
            "stretchMode": "AutoSize",
            "sceneChangeDetection": false,
            "complexity": "Balanced",
            "layers": [
                {
                    "width": "1280",
                    "height": "720",
                    "label": "HD",
                    "bitrate": 3400000,
                    "maxBitrate": 3400000,
                    "bFrames": 3,
                    "slices": 0,
                    "adaptiveBFrame": true,
                    "profile": "Auto",
                    "level": "auto",
                    "bufferWindow": "PT5S",
                    "referenceFrames": 3,
                    "entropyMode": "Cabac"
                },
                {
                    "width": "640",
                    "height": "360",
                    "label": "SD",
                    "bitrate": 1000000,
                    "maxBitrate": 1000000,
                    "bFrames": 3,
                    "slices": 0,
                    "adaptiveBFrame": true,
                    "profile": "Auto",
                    "level": "auto",
                    "bufferWindow": "PT5S",
                    "referenceFrames": 3,
                    "entropyMode": "Cabac"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Media.PngImage",
            "stretchMode": "AutoSize",
            "start": "25%",
            "step": "25%",
            "range": "80%",
            "layers": [
                {
                    "width": "50%",
                    "height": "50%"
                }
            ]
        }
    ],
    "formats": [
        {
            "@odata.type": "#Microsoft.Media.Mp4Format",
            "filenamePattern": "Video-{Basename}-{Label}-{Bitrate}{Extension}",
            "outputFiles": []
        },
        {
            "@odata.type": "#Microsoft.Media.PngFormat",
            "filenamePattern": "Thumbnail-{Basename}-{Index}{Extension}"
        }
    ]
}
```

## <a name="create-a-new-transform"></a>새 변환 만들기  

이 예제에서는 앞에서 정의한 사용자 지정 사전 설정을 기반으로 하는 **변환을** 만듭니다. 변환을 만들 때 먼저 변환이 이미 있는지 확인해야 합니다. 변환이 있는 경우 다시 사용합니다. 다음 `show` 명령은 변환이 있는 경우 변환을 `customTransformName` 반환합니다.

```azurecli-interactive
az ams transform show -a amsaccount -g amsResourceGroup -n customTransformName
```

다음 Azure CLI 명령은 사용자 지정 사전 설정(이전에 정의된)을 기반으로 변환을 만듭니다.

```azurecli-interactive
az ams transform create -a amsaccount -g amsResourceGroup -n customTransformName --description "Basic Transform using a custom encoding preset" --preset customPreset.json
```

미디어 서비스가 지정된 비디오 또는 오디오에 변환을 적용하려면 해당 변환 아래에 작업을 제출해야 합니다. 변환에서 작업을 제출하는 방법을 보여 주는 전체 예제는 [빠른 시작: 비디오 파일 스트림 - Azure CLI](stream-files-cli-quickstart.md)를 참조하십시오.

## <a name="see-also"></a>참조

[Azure CLI](/cli/azure/ams)
