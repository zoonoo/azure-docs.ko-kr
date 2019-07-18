---
title: 코드 샘플 - Content Moderator, .NET
description: SDK를 통해 .NET 애플리케이션에서 Content Moderator를 사용합니다.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: sample
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 0857d571e3bb029f564299efbe8cc4fdf5fbb3ff
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604101"
---
# <a name="content-moderator-net-sdk-samples"></a>Content Moderator .NET SDK 샘플

다음 목록에는 .NET용 Azure Content Moderator SDK를 사용하여 빌드된 코드 샘플에 대한 링크가 포함되어 있습니다.

## <a name="moderation"></a>조정

- **이미지 조정**: [성인 및 외설 콘텐츠, 텍스트 및 얼굴에 대한 이미지를 평가합니다](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs). [빠른 시작](image-moderation-quickstart-dotnet.md)을 참조하세요.
- **사용자 지정 이미지**: [사용자 지정 이미지 목록을 사용하여 조정합니다](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs). [빠른 시작](image-lists-quickstart-dotnet.md)을 참조하세요.

> [!NOTE]
> 최대 **5개 이미지 목록**으로 제한되고, 각 목록은 **10,000개 이미지를 초과하지 않아야** 합니다.
>

- **텍스트 조정**: [텍스트에서 불경한 언어 및 개인 데이터를 검색합니다](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs). [빠른 시작](text-moderation-quickstart-dotnet.md)을 참조하세요.
- **사용자 지정 용어**: [사용자 지정 용어 목록을 사용하여 조정합니다](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TermListManagement/Program.cs). [빠른 시작](term-lists-quickstart-dotnet.md)을 참조하세요.

> [!NOTE]
> 최대 **5개 용어 목록**으로 제한되고, 각 목록은 **10,000개 용어를 초과하지 않아야** 합니다.
>

- **비디오 조정**: [성인 및 외설 콘텐츠에 대한 비디오를 검사하고 결과를 가져옵니다](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoModeration/Program.cs). [빠른 시작](video-moderation-api.md)을 참조하세요.

## <a name="review"></a>검토

- **이미지 작업**: [검토를 검사하고 만드는 조정 작업을 시작합니다](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs). [빠른 시작](moderation-jobs-quickstart-dotnet.md)을 참조하세요.
- **이미지 검토**: [사람이 참여하는 검토를 만듭니다](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs). [빠른 시작](moderation-reviews-quickstart-dotnet.md)을 참조하세요.
- **비디오 검토**: [사람이 참여하는 비디오 검토를 만듭니다](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs). [빠른 시작](video-reviews-quickstart-dotnet.md)을 참조하세요.
- **비디오 대본 검토**: [사람이 참여하는 비디오 대본 검토를 만듭니다](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs). [빠른 시작](video-reviews-quickstart-dotnet.md)을 참조하세요.

[GitHub의 Content Moderator .NET 샘플](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator)에서 모든 .NET 샘플을 참조하세요.
