---
title: Media Encoder Standard .NET을 사용해 썸네일 생성
description: 이 문서에서는 .NET과 함께 Media Encoder Standard를 사용하여 동시에 자산을 인코딩하고 썸네일을 생성하는 방법을 보여줍니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 12/01/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 135c163c555bc30bea57ccbdf73bf0414a834da2
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106109648"
---
# <a name="how-to-generate-thumbnails-using-encoder-standard-with-net"></a>.NET과 함께 Encoder Standard를 사용하여 썸네일을 생성하는 방법

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Media Encoder Standard를 사용하여 입력 비디오에서 하나 이상의 미리 보기를 [JPEG](https://en.wikipedia.org/wiki/JPEG) 또는 [PNG](https://en.wikipedia.org/wiki/Portable_Network_Graphics) 이미지 파일 형식으로 생성할 수 있습니다.

## <a name="recommended-reading-and-practice"></a>권장되는 참조 및 구성 요소

[사용자 지정 변환을 사용하여 인코딩하는 방법 - .NET](encode-custom-presets-how-to.md)을 읽어 사용자 지정 변환에 익숙해지는 것이 좋습니다.

## <a name="transform-code-example"></a>코드 변환 예제

아래 코드 예제에서는 썸네일만 만듭니다.  다음 매개 변수를 설정해야 합니다.

- **start** - 썸네일 생성을 시작할 입력 비디오의 위치입니다. 값은 ISO 8601 형식(예: 5초에서 시작할 경우 PT05S), 프레임 수(예: 10번째 프레임에서 시작할 경우 10개) 또는 스트림 기간의 상대값(예: 스트림 기간의 10%에서 시작할 경우 10%)이 될 수 있습니다. 또한 인코더가 비디오의 처음 몇 초에서 최상의 썸네일을 선택하고, 단계와 범위에 대한 다른 설정에 관계없이 하나의 썸네일만 생성하도록 지시하는 {Best} 매크로를 지원합니다. 기본값은 {Best} 매크로입니다.
- **step** - 썸네일이 생성되는 간격입니다. 값은 ISO 8601 형식(예: 5초마다 한 이미지에 대해 PT05S), 프레임 수(예: 30개의 프레임마다 한 이미지에 대해 30개) 또는 스트림 기간의 상대값(예: 스트림 기간의 10%마다 한 이미지에 대해 10%)이 될 수 있습니다. step 값은 처음 생성된 썸네일에 영향을 미치므로, 변환이 사전 설정된 시작 시간에 지정된 것과 정확히 일치하지 않을 수도 있습니다. 이는 인코더가 시작 시간과 시작 시간부터 단계 위치 사이에서 가장 좋은 썸네일을 첫 번째 출력으로 선택하려고 하기 때문입니다. 기본값은 10%이므로, 스트리밍 기간이 길 경우 처음 생성된 썸네일이 시작 시간에 지정된 썸네일과 멀리 떨어져 있을 수 있음을 의미합니다. 첫 번째 썸네일이 시작 시간에 가까울 것으로 예상되면 단계에 적합한 값을 선택하고, 시작 시간에 썸네일이 하나만 필요한 경우 범위 값을 1로 설정하세요.
- **range** - 썸네일 생성을 중지할 입력 비디오에서 변환이 사전 설정된 시작 시간을 기준으로 하는 위치입니다. 값은 ISO 8601 형식(예: 시작 시간으로부터 5분 30초 후에 중지할 경우 PT5M30S) 또는 프레임 수(예: 시작 시간으로부터 프레임의 300번째 프레임에서 중지할 경우 300개)가 될 수 있습니다. 이 값이 1인 경우, 시작 시간에 썸네일 하나만 생성함을 의미하거나 스트림 기간에 대한 상대값(예: 시작 시간으로부터 스트림 기간의 절반에서 중지할 경우 50%)을 나타냅니다. 기본값은 100%로, 스트리밍 끝부분에서 중지함을 의미합니다.
- **layers** - 인코더에서 생성할 출력 이미지 레이어의 컬렉션입니다.

```csharp

private static Transform EnsureTransformExists(IAzureMediaServicesClient client, string resourceGroupName, string accountName, string transformName)
{
    // Does a Transform already exist with the desired name? Assume that an existing Transform with the desired name
    // also uses the same recipe or Preset for processing content.
    Transform transform = client.Transforms.Get(resourceGroupName, accountName, transformName);

    if (transform == null)
    {
        // Create a new Transform Outputs array - this defines the set of outputs for the Transform
        TransformOutput[] outputs = new TransformOutput[]
        {
            // Create a new TransformOutput with a custom Standard Encoder Preset
            // This demonstrates how to create custom codec and layer output settings

          new TransformOutput(
                new StandardEncoderPreset(
                    codecs: new Codec[]
                    {
                        // Generate a set of PNG thumbnails
                        new PngImage(
                            start: "25%",
                            step: "25%",
                            range: "80%",
                            layers: new PngLayer[]{
                                new PngLayer(
                                    width: "50%",
                                    height: "50%"
                                )
                            }
                        )
                    },
                    // Specify the format for the output files for the thumbnails
                    formats: new Format[]
                    {
                        new PngFormat(
                            filenamePattern:"Thumbnail-{Basename}-{Index}{Extension}"
                        )
                    }
                ),
                onError: OnErrorType.StopProcessingJob,
                relativePriority: Priority.Normal
            )
        };

        string description = "A transform that includes thumbnails.";
        // Create the custom Transform with the outputs defined above
        transform = client.Transforms.CreateOrUpdate(resourceGroupName, accountName, transformName, outputs, description);
    }

    return transform;
}
```

## <a name="next-steps"></a>다음 단계

[REST로 썸네일 생성](media-services-generate-thumbnails-rest.md)
