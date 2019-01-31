---
title: '빠른 시작: .NET용 조정 클라이언트 만들기 - Content Moderator'
titlesuffix: Azure Cognitive Services
description: Azure Content Moderator SDK for .NET을 사용하여 Content Moderator 클라이언트를 반환하는 방법
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 08902df231fedd2c1c5653052540cb1cdabba9cf
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55224946"
---
# <a name="quickstart-helper-code-to-return-a-content-moderator-client"></a>빠른 시작: Content Moderator 클라이언트를 반환하는 도우미 코드

이 문서에서는 Content Moderator SDK for .NET을 사용하여 구독에 사용할 Content Moderator 클라이언트를 만드는 방법과 코드 샘플을 제공합니다.

라이브러리는 이 섹션의 다른 빠른 시작에서 사용됩니다.

이 문서에서는 사용자가 Visual Studio 및 C#에 이미 익숙한 것으로 가정합니다.

> [!IMPORTANT]
> 이 클래스 라이브러리에 포함된 코드는 데모 용도로만 사용할 수 있습니다.
> 이 코드를 프로덕션 환경에 도입할 경우 Content Moderator 구독 키를 저장 및 사용하는 보안 메서드를 사용하시기 바랍니다.

## <a name="sign-up-for-content-moderator-services"></a>Content Moderator 서비스 등록

REST API 또는 SDK를 통해 Content Moderator 서비스를 사용하려면 먼저 구독 키가 필요합니다.
[웹에서 Content Moderator 사용해 보기](quick-start.md) 빠른 시작을 참조하여 키를 가져오는 방법을 배웁니다.

## <a name="create-your-visual-studio-project"></a>Visual Studio 프로젝트 만들기

1. 새 **클래스 라이브러리(.NET Framework)** 프로젝트를 만듭니다.

   이 샘플 코드에서는 프로젝트 이름을 **ModeratorHelper**로 지정했습니다.

1. **System.Configuration** 프레임워크 어셈블리에 참조를 추가합니다.

### <a name="install-required-packages"></a>필요한 패키지를 설치합니다.

다음 NuGet 패키지를 설치합니다.

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="create-the-content-moderator-client"></a>Content Moderator 클라이언트 만들기

ModeratorHelper.cs 파일의 내용을 다음 코드로 바꿉니다.

    using Microsoft.CognitiveServices.ContentModerator;

    namespace ModeratorHelper
    {
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
        private static readonly string AzureRegion = "myRegion";

        /// <summary>
        /// The base URL fragment for Content Moderator calls.
        /// </summary>
        private static readonly string AzureBaseURL =
            $"{AzureRegion}.api.cognitive.microsoft.com";

        /// <summary>
        /// Your Content Moderator subscription key.
        /// </summary>
        private static readonly string CMSubscriptionKey = "myKey";

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

            client.BaseUrl = AzureBaseURL;
            return client;
        }
    }
    }


> [!IMPORTANT]
> **AzureRegion** 및 **CMSubscriptionKey** 필드를 해당 지역 식별자 및 구독 키 값으로 업데이트합니다.

이제 구독에 사용할 Content Moderator 클라이언트를 신속하게 만들 수 있는 방법이 생겼습니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작과 기타 .NET용 Content Moderator 빠른 시작을 위한 [Visual Studio 솔루션을 다운로드](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator)하고 통합을 시작합니다.
