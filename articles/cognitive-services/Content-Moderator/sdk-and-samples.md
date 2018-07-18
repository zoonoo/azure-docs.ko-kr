---
title: Azure Content Moderator에 대한 Content Moderation SDK 및 샘플 | Microsoft Docs
description: Content Moderator에 대한 SDK 및 샘플 가져오기
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 02/27/2018
ms.author: sajagtap
ms.openlocfilehash: 40b8fc0f63383e837f0813f876f20806e6e8c6eb
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373214"
---
# <a name="content-moderator-sdks-and-samples"></a>Content Moderator SDK 및 샘플

## <a name="sdks-for-python-java-nodejs-and-net"></a>Python, Java, Node.js 및 .NET용 SDK

- [Python용 Content Moderator SDK](https://pypi.python.org/pypi/azure-cognitiveservices-vision-contentmoderator)
- [Java용 Content Moderator SDK](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-cognitiveservices-contentmoderator%22)
- [Node.js용 Content Moderator SDK](https://www.npmjs.com/package/azure-cognitiveservices-contentmoderator)
- [.NET용 Content Moderator SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/)

## <a name="net-sdk-samples"></a>.NET SDK 샘플

다음 목록에는 .NET용 Azure Content Moderator SDK를 사용하여 빌드된 코드 샘플에 대한 링크가 포함되어 있습니다.

- **도우미 라이브러리**: [다른 샘플에서 사용하기 위한 Content Moderator 클라이언트를 만듭니다](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ModeratorHelper/Clients.cs). [빠른 시작](content-moderator-helper-quickstart-dotnet.md)을 참조하세요.

### <a name="moderation"></a>조정 
- **이미지 조정**: [성인 및 외설 콘텐츠, 텍스트 및 얼굴에 대한 이미지를 평가합니다](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs). [빠른 시작](image-moderation-quickstart-dotnet.md)을 참조하세요.
- **사용자 지정 이미지**: [사용자 지정 이미지 목록을 사용하여 조정합니다](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs). [빠른 시작](image-lists-quickstart-dotnet.md)을 참조하세요.

> [!NOTE]
> 최대 **5개 이미지 목록**으로 제한되고, 각 목록은 **10,000개 이미지를 초과하지 않아야** 합니다.
>

- **텍스트 조정**: [욕설 및 PII(개인 식별 정보)에 대한 텍스트를 차단합니다](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs). [빠른 시작](text-moderation-quickstart-dotnet.md)을 참조하세요.
- **사용자 지정 용어**: [사용자 지정 용어 목록을 사용하여 조정합니다](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TermListManagement/Program.cs). [빠른 시작](term-lists-quickstart-dotnet.md)을 참조하세요.

> [!NOTE]
> 최대 **5개 용어 목록**으로 제한되고, 각 목록은 **10,000개 용어를 초과하지 않아야** 합니다.
>

- **비디오 조정**: [성인 및 외설 콘텐츠에 대한 비디오를 검사하고 결과를 가져옵니다](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoModeration/Program.cs). [빠른 시작](video-moderation-api.md)을 참조하세요.

### <a name="review"></a>검토
- **이미지 작업**: [검토를 검사하고 만드는 조정 작업을 시작합니다](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs). [빠른 시작](moderation-jobs-quickstart-dotnet.md)을 참조하세요.
- **이미지 검토**: [사람이 참여하는 검토를 만듭니다](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs). [빠른 시작](moderation-reviews-quickstart-dotnet.md)을 참조하세요.
- **비디오 검토**: [사람이 참여하는 비디오 검토를 만듭니다](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs). [빠른 시작](video-reviews-quickstart-dotnet.md)을 참조하세요.
- **비디오 대본 검토**: [사람이 참여하는 비디오 대본 검토를 만듭니다](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs). [빠른 시작](video-reviews-quickstart-dotnet.md)을 참조하세요.

[GitHub의 Content Moderator .NET 샘플](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator)에서 모든 .NET 샘플을 참조하세요.

## <a name="rest-api-samples-in-c"></a>C#의 REST API 샘플

- [이미지 조정](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/ImageModeration)
- [텍스트 조정](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/TextModeration)
- [비디오 조정](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/VideoModeration)
- [이미지 검토](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/ImageReviews)
- [이미지 작업](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/ImageJob)

이러한 샘플을 연습하려면 [주문형 웹 세미나](https://info.microsoft.com/cognitive-services-content-moderator-ondemand.html)를 확인하세요.

## <a name="tutorials"></a>자습서
- [전자 상거래 카탈로그 조정](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration). [자습서](ecommerce-retail-catalog-moderation.md)를 참조하세요.
- [Facebook 콘텐츠 조정](https://github.com/MicrosoftContentModerator/samples-fbPageModeration). [자습서](facebook-post-moderation.md)를 참조하세요.
- [비디오 및 대본 조정 및 검토 솔루션](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp). [자습서](video-transcript-moderation-review-tutorial-dotnet.md)를 참조하세요.

## <a name="on-demand-webinars"></a>주문형 웹 세미나
- [Content Moderator를 통한 대규모 기계 지원 콘텐츠 조정](https://info.microsoft.com/cognitive-services-content-moderator-ondemand.html)
