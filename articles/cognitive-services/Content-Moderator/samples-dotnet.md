---
title: Azure Content Moderator 코드 샘플 | Microsoft Docs
description: 응용 프로그램에서 Content Moderator 사용
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/10/2018
ms.author: sajagtap
ms.openlocfilehash: a7f5b0a7433631e303de47667871cc1354053c08
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374319"
---
# <a name="net-sdk-samples"></a>.NET SDK 샘플

다음 목록에는 .NET 용 Azure Content Moderator SDK를 사용하여 빌드된 코드 샘플에 대한 링크가 포함됩니다.

- **도우미 라이브러리**: [다른 샘플에서 사용하기 위한 Content Moderator 클라이언트를 만듭니다](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ModeratorHelper/Clients.cs). [빠른 시작](content-moderator-helper-quickstart-dotnet.md)을 참조합니다.

## <a name="moderation"></a>조정

- **이미지 중재**: [성인 및 선정적 콘텐츠, 텍스트 및 얼굴에 대한 이미지를 평가합니다](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs). [빠른 시작](image-moderation-quickstart-dotnet.md)을 참조합니다.
- **사용자 지정 이미지**: [사용자 지정 이미지 목록을 사용하여 조정합니다](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs). [빠른 시작](image-lists-quickstart-dotnet.md)을 참조합니다.

> [!NOTE]
> 최대 **5개 이미지 목록**으로 제한되고, 각 목록은 **10,000개 이미지를 초과하지 않아야** 합니다.
>

- **텍스트 조정**: [욕설 및 개인 식별이 가능한 정보(PII)에 대한 텍스트를 걸러냅니다](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs). [빠른 시작](text-moderation-quickstart-dotnet.md)을 참조합니다.
- **사용자 지정 용어**: [사용자 지정 용어 목록을 사용하여 조정합니다](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TermListManagement/Program.cs). [빠른 시작](term-lists-quickstart-dotnet.md)을 참조합니다.

> [!NOTE]
> 최대 **5개 용어 목록**으로 제한되고, 각 목록이 **10,000개 용어를 초과하지 않아야** 합니다.
>

- **비디오 조정**: [성인 및 선정적 콘텐츠에 대한 비디오를 검사하고 결과를 가져옵니다](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoModeration/Program.cs). [빠른 시작](video-moderation-api.md)을 참조합니다.

## <a name="review"></a>검토

- **이미지 작업**: [검토를 검사하고 만드는 조정 작업을 시작합니다](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs). [빠른 시작](moderation-jobs-quickstart-dotnet.md)을 참조합니다.
- **이미지 검토**: [human-in-the-loop에 대한 검토를 만듭니다](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs). [빠른 시작](moderation-reviews-quickstart-dotnet.md)을 참조합니다.
- **비디오 검토**: [human-in-the-loop에 대한 비디오 검토를 만듭니다](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs). [빠른 시작](video-reviews-quickstart-dotnet.md)을 참조합니다.
- **비디오 대본 검토**: [human-in-the-loop에 대한 비디오 대본 검토 만들기](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs) [빠른 시작](video-reviews-quickstart-dotnet.md) 참조

[GitHub의 Content Moderator .NET 샘플](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator)에서 모든 .NET 샘플을 참조합니다.
