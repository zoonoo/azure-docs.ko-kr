---
title: 미디어 서비스 v3 .NET - Azure | 을 사용하여 사용자 지정 변환인코딩 마이크로 소프트 문서
description: 이 항목에서는 Azure Media Services v3를 사용하여 .NET을 사용하여 사용자 지정 변환을 인코딩하는 방법을 보여 주며 있습니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/03/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: ebe701032e6416b3e007a28db62f5a8235bb1bb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80068041"
---
# <a name="how-to-encode-with-a-custom-transform---net"></a>사용자 지정 변환으로 인코딩하는 방법 - .NET

Azure Media Services로 인코딩할 때 [스트리밍 파일](stream-files-tutorial-with-api.md) 자습서에서 설명한 것처럼 업계 모범 사례를 기반으로 권장되는 기본 제공 사전 설정 중 하나를 빠르게 시작할 수 있습니다. 특정 시나리오 또는 장치 요구 사항을 대상으로 사용자 지정 사전 설정을 빌드할 수도 있습니다.

## <a name="considerations"></a>고려 사항

사용자 지정 사전 설정을 만들 때 다음 고려 사항이 적용됩니다.

* AVC 콘텐츠의 높이 및 너비에 대한 모든 값은 4의 배수여야 합니다.
* Azure Media Services v3에서 모든 인코딩 비트 레이트(bitrates)는 초당 비트입니다. 이것은 단위로 킬로비트 / 초를 사용하는 v2 API의 사전 설정과 다릅니다. 예를 들어 v2의 비트 레이트(128(킬로비트/초)로 지정된 경우 v3에서는 128000(비트/초)으로 설정됩니다.

## <a name="prerequisites"></a>사전 요구 사항 

[미디어 서비스 계정 만들기](create-account-cli-how-to.md)

## <a name="download-the-sample"></a>샘플 다운로드

다음 명령을 사용하여 전체 .NET Core 샘플이 포함된 GitHub 리포지토리를 컴퓨터에 복제합니다.  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```
 
사용자 지정 미리 설정 샘플은 [EncodeCustomTransform](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/EncodeCustomTransform/) 폴더에 있습니다.

## <a name="create-a-transform-with-a-custom-preset"></a>사용자 지정 미리 설정을 사용하여 변환 만들기 

새 [Transform](https://docs.microsoft.com/rest/api/media/transforms)을 만드는 경우 출력으로 생성하려는 것을 지정해야 합니다. 필수 매개 변수는 아래 코드와 같이 [TransformOutput](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#transformoutput) 개체입니다. 각 **TransformOutput**에는 **Preset**이 포함됩니다. **사전 설정은** 원하는 **TransformOutput을**생성하는 데 사용할 비디오 및/또는 오디오 처리 작업의 단계별 지침을 설명합니다. 다음 **TransformOutput**은 사용자 지정 코덱 및 레이어 출력 설정을 만듭니다.

[Transform](https://docs.microsoft.com/rest/api/media/transforms)을 만드는 경우 먼저 **Get** 메서드를 사용하여 해당 Transform이 이미 있는지 확인해야 합니다. 아래 코드를 참조하세요. Media Services v3에서 엔터티가 존재하지 않으면 엔터티에 대한 **Get** 메서드에서 **null**을 반환합니다(이름에서 대/소문자를 구분하지 않음).

### <a name="example"></a>예제

다음 예제에서는 이 변환을 사용할 때 생성할 출력 집합을 정의합니다. 먼저 오디오 인코딩을 위한 AacAudio 레이어와 비디오 인코딩을 위한 두 개의 H264Video 레이어를 추가합니다. 비디오 레이어에서 출력 파일 이름에 사용할 수 있도록 레이블을 할당합니다. 다음으로 출력에 축소판 그림도 포함하기를 원합니다. 아래 예제에서는 입력 비디오 해상도의 50%에서 생성된 PNG 형식으로 이미지를 지정하고 입력 비디오 길이의 {25%, 50%, 75}의 세 개의 타임스탬프에서 이미지를 지정합니다. 마지막으로 비디오 + 오디오용, 썸네일용 파일 등 출력 파일의 형식을 지정합니다. 여러 개의 H264Layers가 있으므로 레이어당 고유한 이름을 생성하는 매크로를 사용해야 합니다. 우리는 또는 `{Label}` `{Bitrate}` 매크로를 사용할 수 있습니다, 예제는 전자를 보여줍니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/EncodeCustomTransform/MediaV3ConsoleApp/Program.cs#EnsureTransformExists)]

## <a name="next-steps"></a>다음 단계

[파일 스트리밍](stream-files-tutorial-with-api.md) 
