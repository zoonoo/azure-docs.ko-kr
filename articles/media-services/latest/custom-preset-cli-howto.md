---
title: 사용자 지정 변환 CLI 인코딩
description: 이 항목에서는 Azure Media Services v3를 사용하여 Azure CLI를 통해 사용자 지정 변환을 인코딩하는 방법을 보여줍니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: d3977b02bffd49762bdcb69fa4940052518c9d25
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105963481"
---
# <a name="how-to-encode-with-a-custom-transform---azure-cli"></a>사용자 지정 변환을 사용하여 인코딩하는 방법 - Azure CLI

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services를 통해 인코딩하는 경우 [파일 스트리밍](stream-files-cli-quickstart.md#create-a-transform-for-adaptive-bitrate-encoding) 빠른 시작에 설명된 대로 업계 모범 사례에 따라 권장되는 기본 제공 미리 설정 중 하나를 사용하여 빠르게 시작할 수 있습니다. 특정 시나리오 또는 디바이스 요구 사항을 대상으로 사용자 지정 사전 설정을 빌드할 수도 있습니다.

## <a name="considerations"></a>고려 사항

사용자 지정 사전 설정을 만들 때 다음 고려 사항이 적용됩니다.

* AVC 콘텐츠의 높이 및 너비에 대한 모든 값은 4의 배수여야 합니다.
* Azure Media Services v3에서 모든 인코딩 비트 전송률은 비트/초입니다. 이는 킬로비트/초를 단위로 사용하는 v2 API의 사전 설정과 다릅니다. 예를 들어 v2의 비트 전송률이 128(킬로비트/초)로 지정된 경우 v3에서는 128000(비트/초)으로 설정됩니다.

## <a name="prerequisites"></a>사전 요구 사항

[Media Services 계정 만들기](./account-create-how-to.md)

리소스 그룹 이름과 Media Services 계정 이름을 기억해 두어야 합니다.

## <a name="define-a-custom-preset"></a>사용자 지정 사전 설정 정의

다음 예제에서는 새 변환의 요청 본문을 정의합니다. 이 변환을 사용할 때 생성하려는 출력 세트를 정의합니다.

이 예제에서는 먼저 오디오 인코딩을 위한 AacAudio 레이어와 비디오 인코딩을 위한 두 개의 H264Video 레이어를 추가합니다. 비디오 레이어에서는 출력 파일 이름에 사용할 수 있도록 레이블을 할당합니다. 다음으로, 출력에 썸네일도 포함하려고 합니다. 아래 예제에서는 입력 비디오의 해상도 50% 및 입력 비디오의 길이{25%, 50%, 75}인 세 개의 타임스탬프에서 생성된 PNG 형식의 이미지를 지정합니다. 마지막으로, 출력 파일의 형식(하나는 비디오 + 오디오용이고 다른 하나는 썸네일용)을 지정합니다. 여러 H264Layers가 있으므로 레이어별로 고유한 이름을 생성하는 매크로를 사용해야 합니다. `{Label}` 또는 `{Bitrate}` 매크로를 사용할 수 있습니다. 이 예제에서는 전자를 보여 줍니다.

이 변환을 파일에 저장하려고 합니다. 이 예제에서는 파일 이름을 `customPreset.json`으로 지정합니다.

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

이 예제에서는 앞에서 정의한 사용자 지정 미리 설정을 기반으로 **변환** 을 만듭니다. 변환을 만들 때는 먼저 변환이 이미 있는지 확인해야 합니다. 변환이 있으면 다시 사용합니다. 다음 `show` 명령은 변환이 있는 경우 `customTransformName` 변환을 반환합니다.

```azurecli-interactive
az ams transform show -a amsaccount -g amsResourceGroup -n customTransformName
```

다음 Azure CLI 명령은 사용자 지정 사전 설정(이전에 정의됨)을 기반으로 변환을 만듭니다.

```azurecli-interactive
az ams transform create -a amsaccount -g amsResourceGroup -n customTransformName --description "Basic Transform using a custom encoding preset" --preset customPreset.json
```

Media Services가 지정된 비디오 또는 오디오에 변환을 적용하려면 해당 변환에서 작업을 제출해야 합니다. 변환에서 작업을 제출하는 방법을 보여주는 전체 예제는 [빠른 시작: 비디오 파일 스트리밍 - Azure CLI](stream-files-cli-quickstart.md)를 참조하세요.

## <a name="see-also"></a>참고 항목

[Azure CLI](/cli/azure/ams)
