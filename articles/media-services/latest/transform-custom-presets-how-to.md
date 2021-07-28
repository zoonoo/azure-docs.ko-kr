---
title: 사용자 지정 변환 .NET 인코딩
description: 이 항목에서는 Azure Media Services v3에서 .NET을 사용하여 사용자 지정 변환을 인코딩하는 방법을 보여 줍니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 05/11/2021
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 21aae19b92be4afe8af5f3a1e658084547c97d5f
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109790868"
---
# <a name="how-to-encode-with-a-custom-transform---net"></a>사용자 지정 변환을 사용하여 인코딩하는 방법 - .NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services로 인코딩할 때 [파일 스트리밍](stream-files-tutorial-with-api.md) 자습서에 설명된 업계 모범 사례에 따라 권장되는 기본 제공 사전 설정 중 하나를 사용하여 빠르게 시작할 수 있습니다. 특정 시나리오 또는 디바이스 요구 사항을 대상으로 사용자 지정 사전 설정을 빌드할 수 있습니다.

## <a name="considerations"></a>고려 사항

사용자 지정 사전 설정을 만들 때 고려 사항은 다음과 같습니다.

* AVC 콘텐츠의 높이 및 너비의 모든 값은 4의 배수여야 합니다.
* Azure Media Services v3에서 모든 인코딩 비트 전송률은 비트/초입니다. 이는 킬로비트/초를 단위로 사용한 v2 API의 사전 설정과는 다릅니다. 예를 들어 v2의 비트 전송률이 128(킬로비트/초)로 지정된 경우 v3에서는 128000(비트/초)로 설정됩니다.

## <a name="prerequisites"></a>필수 구성 요소

[Media Services 계정 만들기](./account-create-how-to.md)

## <a name="download-the-sample"></a>샘플 다운로드

다음 명령을 사용하여 전체 .NET Core 샘플이 포함된 GitHub 리포지토리를 컴퓨터에 복제합니다.  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet.git
 ```
 
사용자 지정 사전 설정 샘플은 [.NET을 사용하는 사용자 지정 사전 설정으로 인코딩](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_H264) 폴더에 있습니다.

## <a name="create-a-transform-with-a-custom-preset"></a>사용자 지정 미리 설정을 사용하여 변환 만들기

새 [Transform](/rest/api/media/transforms)을 만드는 경우 출력으로 생성하려는 것을 지정해야 합니다. 필수 매개 변수는 아래 코드와 같이 [TransformOutput](/rest/api/media/transforms/createorupdate#transformoutput) 개체입니다. 각 **TransformOutput** 에는 **Preset** 이 포함됩니다. **사전 설정** 은 원하는 **TransformOutput** 을 생성하는 데 사용되는 비디오 및/또는 오디오 처리 작업에 대한 단계별 지침을 설명합니다. 다음 **TransformOutput** 은 사용자 지정 코덱 및 레이어 출력 설정을 만듭니다.

[Transform](/rest/api/media/transforms)을 만드는 경우 먼저 **Get** 메서드를 사용하여 해당 Transform이 이미 있는지 확인해야 합니다. 아래 코드를 참조하세요. Media Services v3에서 엔터티가 존재하지 않으면 엔터티에 대한 **Get** 메서드에서 **null** 을 반환합니다(이름에서 대/소문자를 구분하지 않음).

### <a name="example-custom-transform"></a>사용자 지정 변환 예제

다음 예제에서는 이 변환을 사용하여 생성하려는 출력 집합을 정의합니다. 먼저 오디오 인코딩에 AacAudio 레이어를 추가하고 비디오 인코딩에 두 개의 H264Video 레이어를 추가합니다. 비디오 레이어에서 출력 파일 이름에 사용할 수 있도록 레이블을 할당합니다. 다음으로, 출력에 썸네일도 포함하려고 합니다. 아래 예제에서 입력 비디오의 50% 해상도로 생성되고 입력 비디오의 길이에 대해 3개의 타임스탬프{25%, 50%, 75%}로 생성된 PNG 형식의 이미지를 지정합니다. 마지막으로 출력 파일의 형식을 지정합니다. 하나는 비디오 + 오디오용이고, 다른 하나는 썸네일용입니다. 여러 H264Layer가 있으므로 레이어별로 고유한 이름을 생성하는 매크로를 사용해야 합니다. `{Label}` 또는 `{Bitrate}` 매크로를 사용할 수 있습니다. 이 예제에서는 전자를 보여 줍니다.

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoEncoding/Encoding_H264/Program.cs#EnsureTransformExists)]

## <a name="next-steps"></a>다음 단계

[파일 스트리밍](stream-files-tutorial-with-api.md)
