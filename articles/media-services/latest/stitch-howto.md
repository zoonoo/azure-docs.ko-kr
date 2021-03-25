---
title: .NET을 사용 하 여 두 개 이상의 비디오 파일을 장착 하는 방법 | Microsoft Docs
description: 이 문서에서는 두 개 이상의 비디오 파일을 추가 하는 방법을 보여 줍니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/24/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 5eacc366961d3101a7eaf8877a34ef2d462ea76b
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105111456"
---
# <a name="how-to-stitch-two-or-more-video-files-with-net"></a>.NET을 사용 하 여 두 개 이상의 비디오 파일을 장착 하는 방법

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="stitch-two-or-more-video-files"></a>두 개 이상의 비디오 파일을 추가 합니다.

다음 예에서는 두 개 이상의 비디오 파일을 추가 하기 위해 미리 설정을 생성 하는 방법을 보여 줍니다. 가장 일반적인 시나리오는 기본 비디오에 헤더나 트레일러를 추가하려는 경우입니다.

> [!NOTE]
> 함께 편집 되는 비디오 파일은 속성 (비디오 해상도, 프레임 율, 오디오 트랙 수 등)을 공유 해야 합니다. 프레임 속도나 오디오 트랙 수가 서로 다른 비디오를 섞지 않게 주의가 필요합니다.

## <a name="prerequisites"></a>사전 요구 사항

[Media Services .net 샘플](https://github.com/Azure-Samples/media-services-v3-dotnet/)을 복제 하거나 다운로드 합니다.  아래 참조 된 코드는 [EncodingWithMESCustomStitchTwoAssets 폴더](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/VideoEncoding/EncodingWithMESCustomStitchTwoAssets/Program.cs)에 있습니다.

### <a name="net-code"></a>.NET 코드

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoEncoding/EncodingWithMESCustomStitchTwoAssets/Program.cs)]
