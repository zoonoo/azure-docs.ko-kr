---
title: '빠른 시작: .NET을 사용하여 검토 만들기 - Content Moderator'
titlesuffix: Azure Cognitive Services
description: .NET용 Azure Content Moderator SDK를 사용하여 검토를 만드는 방법입니다.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 318d80049caf1328aac1cc4423b0de10df7a8c58
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55211511"
---
# <a name="quickstart-create-reviews-using-net"></a>빠른 시작: .NET을 사용하여 검토 만들기

이 문서에서는 [.NET용 Content Moderator SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/)를 사용하여 다음 작업을 수행할 수 있도록 지원하는 정보 및 코드 샘플을 제공합니다.
 
- 사용자 중재자에 대한 검토 집합 만들기
- 사용자 중재자에 대한 기존 검토의 상태 가져오기

일반적으로 콘텐츠는 사용자 검토를 위해 예약되기 전에 일부 자동화된 중재를 거칩니다. 이 문서에서는 사용자 중재에 대한 검토를 만드는 방법만을 다룹니다. 자세한 전체 시나리오는 [Facebook 콘텐츠 조정](facebook-post-moderation.md) 및 [전자 상거래 제품 이미지 조정](ecommerce-retail-catalog-moderation.md) 자습서를 참조하세요.

이 문서에서는 사용자가 Visual Studio 및 C#에 이미 익숙한 것으로 가정합니다.

## <a name="sign-up-for-content-moderator"></a>Content Moderator 등록

REST API 또는 SDK를 통해 Content Moderator 서비스를 사용하려면 먼저 구독 키가 필요합니다. [Cognitive Services 계정 만들기](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)의 지침에 따라 Content Moderator를 구독하고 키를 가져옵니다.

## <a name="sign-up-for-a-review-tool-account-if-not-completed-in-the-previous-step"></a>이전 단계에서 완료되지 않은 경우 검토 도구 계정에 등록

Azure Portal에서 Content Moderator를 가져온 경우 [검토 도구 계정에 등록](https://contentmoderator.cognitive.microsoft.com/)하고 검토 팀을 만듭니다. 작업을 시작하고 검토 도구에서 검토를 보도록 검토 API를 호출하려면 팀 ID 및 검토 도구가 필요합니다.

## <a name="ensure-your-api-key-can-call-the-review-api-for-review-creation"></a>API 키에서 검토 만들기에 대한 검토 API를 호출할 수 있는지 확인

이전 단계를 완료한 후 Azure Portal에서 시작한 경우 두 개의 Content Moderator 키가 생성됩니다. 

SDK 샘플에서 Azure가 제공한 API 키를 사용하려는 경우 [검토 API를 사용하여 Azure 키 사용](review-tool-user-guide/credentials.md#use-the-azure-account-with-the-review-tool-and-review-api) 섹션에서 언급된 단계를 수행하여 애플리케이션에서 검토 API를 호출하고 검토를 만들도록 허용합니다.

검토 도구에서 생성된 평가판 키를 사용하는 경우 검토 도구 계정은 키에 대해 이미 알고 있으므로 추가 단계가 필요하지 않습니다.

## <a name="create-your-visual-studio-project"></a>Visual Studio 프로젝트 만들기

1. 솔루션에 새 **콘솔 앱(.NET Framework)** 프로젝트를 추가합니다.

   샘플 코드에서 프로젝트의 이름을 **CreateReviews**로 지정합니다.

1. 이 프로젝트를 솔루션의 단일 시작 프로젝트로 선택합니다.

### <a name="install-required-packages"></a>필요한 패키지를 설치합니다.

다음 NuGet 패키지를 설치합니다.

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>프로그램의 using 문 업데이트

프로그램의 using 문을 수정합니다.

    using Microsoft.Azure.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator.Models;
    using Newtonsoft.Json;
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Threading;

### <a name="create-the-content-moderator-client"></a>Content Moderator 클라이언트 만들기

다음 코드를 추가하여 구독에 대한 Content Moderator 클라이언트를 만듭니다.

> [!IMPORTANT]
> **AzureRegion** 및 **CMSubscriptionKey** 필드를 해당 지역 식별자 및 구독 키 값으로 업데이트합니다.


    /// <summary>
    /// Wraps the creation and configuration of a Content Moderator client.
    /// </summary>
    /// <remarks>This class library contains insecure code. If you adapt this 
    /// code for use in production, use a secure method of storing and using
    /// your Content Moderator subscription key.</remarks>
    public static class Clients
    {
        /// <summary>
        /// The region/location for your Content Moderator account, 
        /// for example, westus.
        /// </summary>
        private static readonly string AzureRegion = "YOUR API REGION";

        /// <summary>
        /// The base URL fragment for Content Moderator calls.
        /// </summary>
        private static readonly string AzureBaseURL =
            $"https://{AzureRegion}.api.cognitive.microsoft.com";

        /// <summary>
        /// Your Content Moderator subscription key.
        /// </summary>
        private static readonly string CMSubscriptionKey = "YOUR API KEY";

        /// <summary>
        /// Returns a new Content Moderator client for your subscription.
        /// </summary>
        /// <returns>The new client.</returns>
        /// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
        /// When you have finished using the client,
        /// you should dispose of it either directly or indirectly. </remarks>
        public static ContentModeratorClient NewClient()
        {
            // Create and initialize an instance of the Content Moderator API wrapper.
            ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey));

            client.Endpoint = AzureBaseURL;
            return client;
        }
    }

## <a name="create-a-class-to-associate-internal-content-information-with-a-review-id"></a>내부 콘텐츠 정보를 검토 ID와 연결하는 클래스 만들기

**Program** 클래스에 다음 클래스를 추가합니다.
이 클래스를 사용하여 항목에 대한 내부 콘텐츠 ID에 검토 ID를 연결합니다.

    /// <summary>
    /// Associates the review ID (assigned by the service) to the internal
    /// content ID of the item.
    /// </summary>
    public class ReviewItem
    {
        /// <summary>
        /// The media type for the item to review.
        /// </summary>
        public string Type;

        /// <summary>
        /// The URL of the item to review.
        /// </summary>
        public string Url;

        /// <summary>
        /// The internal content ID for the item to review.
        /// </summary>
        public string ContentId;

        /// <summary>
        /// The ID that the service assigned to the review.
        /// </summary>
        public string ReviewId;
    }

### <a name="initialize-application-specific-settings"></a>애플리케이션 관련 설정 초기화

> [!NOTE]
> Content Moderator 서비스 키에는 RPS(초당 요청 수) 속도 제한이 있으며, 제한을 초과하는 경우 SDK에서 429 오류 코드로 예외를 throw합니다. 
>
> 체험판 계층 키에는 하나의 RPS 속도 제한이 있습니다.

#### <a name="add-the-following-constants-to-the-program-class-in-programcs"></a>Program.cs의 **Program** 클래스에 다음 상수를 추가합니다.
    
    /// <summary>
    /// The minimum amount of time, in milliseconds, to wait between calls
    /// to the Image List API.
    /// </summary>
    private const int throttleRate = 3000;

    /// <summary>
    /// The number of seconds to delay after a review has finished before
    /// getting the review results from the server.
    /// </summary>
    private const double latencyDelay = 45;

    /// <summary>
    /// The name of the log file to create.
    /// </summary>
    /// <remarks>Relative paths are relative to the execution directory.</remarks>
    private const string OutputFile = "OutputLog.txt";

#### <a name="add-the-following-constants-and-static-fields-to-the-program-class-in-programcs"></a>Program.cs의 **Program** 클래스에 다음 상수 및 정적 필드를 추가합니다.

구독 및 팀에 관련된 정보를 포함하도록 이러한 값을 업데이트합니다.

> [!NOTE]
> TeamName 상수를 [Content Moderator 검토 도구](https://contentmoderator.cognitive.microsoft.com/) 구독을 만들 때 사용한 이름으로 설정합니다. **설정**(기어) 메뉴의 **자격 증명** 섹션에서 TeamName을 검색합니다.
>
> 팀 이름은 **API** 섹션에서 **ID** 필드의 값입니다.

    /// <summary>
    /// The name of the team to assign the review to.
    /// </summary>
    /// <remarks>This must be the team name you used to create your 
    /// Content Moderator account. You can retrieve your team name from
    /// the Content Moderator web site. Your team name is the Id associated 
    /// with your subscription.</remarks>
    private const string TeamName = "YOUR REVIEW TEAM ID";

    /// <summary>
    /// The optional name of the subteam to assign the review to.
    /// </summary>
    private const string Subteam = null;

    /// <summary>
    /// The callback endpoint for completed reviews.
    /// </summary>
    /// <remarks>Reviews show up for reviewers on your team. 
    /// As reviewers complete reviews, results are sent to the
    /// callback endpoint using an HTTP POST request.</remarks>
    private const string CallbackEndpoint = "YOUR API ENDPOINT";

    /// <summary>
    /// The media type for the item to review.
    /// </summary>
    /// <remarks>Valid values are "image", "text", and "video".</remarks>
    private const string MediaType = "image";

    /// <summary>
    /// The URLs of the images to create review jobs for.
    /// </summary>
    private static readonly string[] ImageUrls = new string[] {
        "https://moderatorsampleimages.blob.core.windows.net/samples/sample1.jpg"
        // add more if you want
    };

    /// <summary>
    /// The metadata key to initially add to each review item.
    /// </summary>
    private const string MetadataKey = "sc";

    /// <summary>
    /// The metadata value to initially add to each review item.
    /// </summary>
    private const string MetadataValue = "true";

#### <a name="add-the-following-static-fields-to-the-program-class-in-programcs"></a>Program.cs의 **Program** 클래스에 다음 정적 필드를 추가합니다.

이러한 필드를 사용하여 애플리케이션 상태를 추적합니다.

    /// <summary>
    /// A static reference to the text writer to use for logging.
    /// </summary>
    private static TextWriter writer;

    /// <summary>
    /// The cached review information, associating a local content ID
    /// to the created review ID for each item.
    /// </summary>
    private static List<ReviewItem> reviewItems =
        new List<ReviewItem>();

## <a name="create-a-method-to-write-messages-to-the-log-file"></a>로그 파일에 메시지를 작성하는 메서드 만들기

**Program** 클래스에 다음 메서드를 추가합니다. 

    /// <summary>
    /// Writes a message to the log file, and optionally to the console.
    /// </summary>
    /// <param name="message">The message.</param>
    /// <param name="echo">if set to <c>true</c>, write the message to the console.</param>
    private static void WriteLine(string message = null, bool echo = false)
    {
        writer.WriteLine(message ?? String.Empty);

        if (echo)
        {
            Console.WriteLine(message ?? String.Empty);
        }
    }

## <a name="create-a-method-to-create-a-set-of-reviews"></a>검토 집합을 만드는 메서드 만들기

일반적으로 검토해야 하는 들어오는 이미지, 텍스트 또는 비디오를 식별하기 위한 일부 비즈니스 논리가 있습니다. 그러나 여기에서는 고정된 이미지 목록을 사용합니다.

**Program** 클래스에 다음 메서드를 추가합니다.

    /// <summary>
    /// Create the reviews using the fixed list of images.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    private static void CreateReviews(ContentModeratorClient client)
    {
        WriteLine(null, true);
        WriteLine("Creating reviews for the following images:", true);

        // Create the structure to hold the request body information.
        List<CreateReviewBodyItem> requestInfo =
            new List<CreateReviewBodyItem>();

        // Create some standard metadata to add to each item.
        List<CreateReviewBodyItemMetadataItem> metadata =
            new List<CreateReviewBodyItemMetadataItem>(
            new CreateReviewBodyItemMetadataItem[] {
                new CreateReviewBodyItemMetadataItem(
                    MetadataKey, MetadataValue)
            });

        // Populate the request body information and the initial cached review information.
        for (int i = 0; i < ImageUrls.Length; i++)
        {
            // Cache the local information with which to create the review.
            var itemInfo = new ReviewItem()
            {
                Type = MediaType,
                ContentId = i.ToString(),
                Url = ImageUrls[i],
                ReviewId = null
            };

            WriteLine($" - {itemInfo.Url}; with id = {itemInfo.ContentId}.", true);

            // Add the item informaton to the request information.
            requestInfo.Add(new CreateReviewBodyItem(
                itemInfo.Type, itemInfo.Url, itemInfo.ContentId,
                CallbackEndpoint, metadata));

            // Cache the review creation information.
            reviewItems.Add(itemInfo);
        }

        var reviewResponse = client.Reviews.CreateReviewsWithHttpMessagesAsync(
            "application/json", TeamName, requestInfo);

        // Update the local cache to associate the created review IDs with
        // the associated content.
        var reviewIds = reviewResponse.Result.Body;
        for (int i = 0; i < reviewIds.Count; i++)
        {
            Program.reviewItems[i].ReviewId = reviewIds[i];
        }

        WriteLine(JsonConvert.SerializeObject(
        reviewIds, Formatting.Indented));

        Thread.Sleep(throttleRate);
    }

## <a name="create-a-method-to-get-the-status-of-existing-reviews"></a>기존 검토의 상태를 가져오는 메서드 만들기

**Program** 클래스에 다음 메서드를 추가합니다. 

> [!Note]
> 실제로 콜백 URL `CallbackEndpoint`를 수동 검토의 결과를 받는(HTTP POST 요청을 통해) URL로 설정합니다.
> 보류 중인 검토의 상태를 확인하도록 이 메서드를 수정할 수 있습니다.

    /// <summary>
    /// Gets the review details from the server.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    private static void GetReviewDetails(ContentModeratorClient client)
    {
        WriteLine(null, true);
        WriteLine("Getting review details:", true);
        foreach (var item in reviewItems)
        {
            var reviewDetail = client.Reviews.GetReviewWithHttpMessagesAsync(
                TeamName, item.ReviewId);

            WriteLine(
                $"Review {item.ReviewId} for item ID {item.ContentId} is " +
                $"{reviewDetail.Result.Body.Status}.", true);
            WriteLine(JsonConvert.SerializeObject(
                reviewDetail.Result.Body, Formatting.Indented));

            Thread.Sleep(throttleRate);
        }
    }

## <a name="add-code-to-create-a-set-of-reviews-and-check-its-status"></a>검토의 집합을 만들고 해당 상태를 확인하는 코드 추가

**Main** 메서드에 다음 코드를 추가합니다.

이 코드는 목록을 정의 및 관리하고 이미지를 차단하도록 목록을 사용하는 데 수행하는 많은 작업을 시뮬레이션합니다. 로깅 기능을 통해 Content Moderator 서비스에 대한 SDK 호출에 의해 생성된 응답 개체를 볼 수 있습니다.

    using (TextWriter outputWriter = new StreamWriter(OutputFile, false))
    {
        writer = outputWriter;
        using (var client = Clients.NewClient())
        {
            CreateReviews(client);
            GetReviewDetails(client);

            Console.WriteLine();
            Console.WriteLine("Perform manual reviews on the Content Moderator site.");
            Console.WriteLine("Then, press any key to continue.");
            Console.ReadKey();

            Console.WriteLine();
            Console.WriteLine($"Waiting {latencyDelay} seconds for results to propigate.");
            Thread.Sleep(latencyDelay * 1000);

            GetReviewDetails(client);
        }

        writer = null;
        outputWriter.Flush();
        outputWriter.Close();
    }

    Console.WriteLine();
    Console.WriteLine("Press any key to exit...");
    Console.ReadKey();

## <a name="run-the-program-and-review-the-output"></a>프로그램 실행 및 출력 검토

다음과 같은 샘플 출력이 표시됩니다.

    Creating reviews for the following images:
        - https://moderatorsampleimages.blob.core.windows.net/samples/sample1.jpg; with id = 0.

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Pending.

Content Moderator 검토 도구에 로그인하여 **sc** 레이블을 **true**로 설정하여 보류 중인 이미지 검토를 확인합니다. 기본 **a** 및 **r** 태그와 검토 도구 내에서 정의한 모든 사용자 지정 태그를 확인할 수도 있습니다. 

**다음** 단추를 사용하여 제출합니다.

![사용자 중재자를 위한 이미지 검토](images/moderation-reviews-quickstart-dotnet.PNG)

그런 다음, 계속하려면 아무 키나 누릅니다.

    Waiting 45 seconds for results to propagate.

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Complete.

    Press any key to exit...

## <a name="check-out-the-following-output-in-the-log-file"></a>로그 파일에서 다음 출력을 확인합니다.

> [!NOTE]
> 출력 파일에서 문자열 "\{teamname}" 및 "\{callbackUrl}"은 `TeamName` 및 `CallbackEndpoint` 필드에 대한 값을 각각 반영합니다.

검토 ID 및 이미지 콘텐츠 URL은 애플리케이션을 실행할 때마다 다르며, 검토가 완료되면 `reviewerResultTags` 필드는 검토자가 항목의 태그를 지정한 방법을 반영합니다.

    Creating reviews for the following images:
        - https://moderatorsampleimages.blob.core.windows.net/samples/sample1.jpg; with id = 0.
    [
        "201712i46950138c61a4740b118a43cac33f434",
    ]

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Pending.
    {
        "reviewId": "201712i46950138c61a4740b118a43cac33f434",
        "subTeam": "public",
        "status": "Pending",
        "reviewerResultTags": [],
        "createdBy": "{teamname}",
        "metadata": [
        {
            "key": "sc",
            "value": "true"
        }
        ],
        "type": "Image",
        "content": "https://reviewcontentprod.blob.core.windows.net/{teamname}/IMG_201712i46950138c61a4740b118a43cac33f434",
        "contentId": "0",
        "callbackEndpoint": "{callbackUrl}"
    }

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Complete.
    {
        "reviewId": "201712i46950138c61a4740b118a43cac33f434",
        "subTeam": "public",
        "status": "Complete",
        "reviewerResultTags": [
        {
            "key": "a",
            "value": "False"
        },
        {
            "key": "r",
            "value": "True"
        },
        {
            "key": "sc",
            "value": "True"
        }
        ],
        "createdBy": "{teamname}",
        "metadata": [
        {
            "key": "sc",
            "value": "true"
        }
        ],
        "type": "Image",
        "content": "https://reviewcontentprod.blob.core.windows.net/{teamname}/IMG_201712i46950138c61a4740b118a43cac33f434",
        "contentId": "0",
        "callbackEndpoint": "{callbackUrl}"
    }

## <a name="your-callback-url-if-provided-receives-this-response"></a>콜백 Url(제공되는 경우)은 이 응답을 받습니다.

다음 예제와 같은 응답이 표시됩니다.

    {
        "ReviewId": "201801i48a2937e679a41c7966e838c92f5e649",
        "ModifiedOn": "2018-01-06T05:04:40.5525865Z",
        "ModifiedBy": "yourusername",
        "CallBackType": "Review",
        "ContentId": "0",
        "ContentType": "Image",
        "Metadata": {
            "sc": "true"
            },
        "ReviewerResultTags": {
            "a": "False",
            "r": "False",
        }
    }


## <a name="next-steps"></a>다음 단계

이 빠른 시작과 기타 .NET용 Content Moderator 빠른 시작을 위한 [Content Moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) 및 [Visual Studio 솔루션](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator)을 가져오고 통합을 시작합니다.
