---
title: Media Services v3을 사용하여 사용자 지정 변환 인코딩 - Azure | Microsoft Docs
description: 이 항목에서는 Azure Media Services v3을 사용하여 사용자 지정 변환을 인코딩하는 방법을 보여 줍니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 12/08/2018
ms.author: juliako
ms.openlocfilehash: c62d9132cdd7eb2ebcbecc3c417ad30d368a278a
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53138707"
---
# <a name="how-to-encode-with-a-custom-transform"></a>사용자 지정 Transform을 사용하여 인코딩하는 방법

Azure Media Services를 사용하여 인코딩하는 경우, [파일 스트리밍](stream-files-tutorial-with-api.md) 자습서에 나와 있는 업계 모범 사례에 따라 권장되는 기본 제공 미리 설정 중 하나를 사용하여 빠르게 시작하거나 특정 시나리오 또는 디바이스 요구 사항을 대상으로 지정하는 사용자 지정 미리 설정을 작성할 수 있습니다. 

> [!Note]
> Azure Media Services v3에서 모든 인코딩 비트 전송률은 비트/초입니다. 이는 REST v2 Media Encoder Standard 미리 설정과 다릅니다. 예를 들어 v2의 비트 전송률은 128로 지정되지만 v3에서는 128000이 됩니다.

## <a name="download-the-sample"></a>샘플 다운로드

다음 명령을 사용하여 전체 .NET Core 샘플이 포함된 GitHub 리포지토리를 컴퓨터에 복제합니다.  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```
 
사용자 지정 미리 설정 샘플은 [EncodeCustomTransform](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/EncodeCustomTransform/) 폴더에 있습니다.

## <a name="create-a-transform-with-a-custom-preset"></a>사용자 지정 미리 설정을 사용하여 변환 만들기 

새 [Transform](https://docs.microsoft.com/rest/api/media/transforms)을 만드는 경우 출력으로 생성하려는 것을 지정해야 합니다. 필수 매개 변수는 아래 코드와 같이 **TransformOutput** 개체입니다. 각 **TransformOutput**에는 **Preset**이 포함됩니다. **Preset**은 원하는 **TransformOutput**을 생성하는 데 사용되는 비디오 및/또는 오디오 처리 작업에 대한 단계별 지침을 설명합니다. 다음 **TransformOutput**은 사용자 지정 코덱 및 레이어 출력 설정을 만듭니다.

[Transform](https://docs.microsoft.com/rest/api/media/transforms)을 만드는 경우 먼저 **Get** 메서드를 사용하여 해당 Transform이 이미 있는지 확인해야 합니다. 아래 코드를 참조하세요.  Media Services v3에서 엔터티가 존재하지 않으면 엔터티에 대한 **Get** 메서드에서 **null**을 반환합니다(이름에서 대/소문자를 구분하지 않음).

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/EncodeCustomTransform/MediaV3ConsoleApp/Program.cs#EnsureTransformExists)]

## <a name="next-steps"></a>다음 단계

[파일 스트리밍](stream-files-tutorial-with-api.md) 
