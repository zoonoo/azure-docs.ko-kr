---
title: Azure Content Moderator - .NET을 사용하여 조정 작업 시작 | Microsoft Docs
description: .NET용 Azure Content Moderator SDK를 사용하여 조정 작업을 시작하는 방법
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/06/2018
ms.author: sajagtap
ms.openlocfilehash: a103875607355993e216ce1ddea02009fc8fa1c4
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373151"
---
# <a name="start-moderation-jobs-using-net"></a>.NET을 사용하여 조정 작업 시작

이 문서에서는 .NET용 Content Moderator SDK를 사용하여 다음 작업을 수행할 수 있도록 지원하는 정보 및 코드 샘플을 제공합니다.
 
- 검사할 조정 작업을 시작하고 중재자를 위한 검토 만들기
- 보류 중인 검토의 상태 가져오기
- 검토의 최종 상태 추적 및 가져오기
- 콜백 URL로 결과 제출

이 문서에서는 사용자가 Visual Studio 및 C#에 이미 익숙한 것으로 가정합니다.

## <a name="sign-up-for-content-moderator-services"></a>Content Moderator 서비스 등록

REST API 또는 SDK를 통해 Content Moderator 서비스를 사용하려면 먼저 구독 키가 필요합니다.
키를 획득하는 방법은 [빠른 시작](quick-start.md)을 참조하세요.

## <a name="define-a-custom-moderation-workflow"></a>사용자 지정 조정 워크플로 정의

중재 작업은 API를 사용하여 콘텐츠를 검사하고 **워크플로**를 사용하여 검토를 만들지 여부를 결정합니다.
검토 도구에는 기본 워크플로가 포함되지만, 이 빠른 시작에서는 [사용자 지정 워크플로를 정의](Review-Tool-User-Guide/Workflows.md)해보겠습니다.

조정 작업을 시작하는 코드에서 워크플로의 이름을 사용합니다.

## <a name="create-your-visual-studio-project"></a>Visual Studio 프로젝트 만들기

1. 솔루션에 새 **콘솔 앱(.NET Framework)** 프로젝트를 추가합니다.

   샘플 코드에서 프로젝트의 이름을 **CreateReviews**로 지정합니다.

1. 이 프로젝트를 솔루션의 단일 시작 프로젝트로 선택합니다.

1. [Content Moderator 클라이언트 도우미 빠른 시작](content-moderator-helper-quickstart-dotnet.md)에서 만든 **ModeratorHelper** 프로젝트 어셈블리에 대한 참조를 추가합니다.

### <a name="install-required-packages"></a>필요한 패키지를 설치합니다.

다음 NuGet 패키지를 설치합니다.

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>프로그램의 using 문 업데이트

프로그램의 using 문을 수정합니다.

    using Microsoft.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator.Models;
    using ModeratorHelper;
    using Newtonsoft.Json;
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Threading;

### <a name="initialize-application-specific-settings"></a>응용 프로그램 관련 설정 초기화

Program.cs의 **Program** 클래스에 다음 상수 및 정적 필드를 추가합니다.

> [!NOTE]
> TeamName 상수를 Content Moderator 구독을 만들 때 사용한 이름으로 설정합니다. [Content Moderator 웹 사이트](https://westus.contentmoderator.cognitive.microsoft.com/)에서 TeamName을 검색합니다.
> 로그인하면 **설정**(톱니 모양) 메뉴에서 **자격 증명**을 선택합니다.
>
> 팀 이름은 **API** 섹션에서 **ID** 필드의 값입니다.


    /// <summary>
    /// The moderation job will use this workflow that you defined earlier.
    /// See the quickstart article to learn how to setup custom workflows.
    /// </summary>
    private const string WorkflowName = "OCR";
    
    /// <summary>
    /// The name of the team to assign the job to.
    /// </summary>
    /// <remarks>This must be the team name you used to create your 
    /// Content Moderator account. You can retrieve your team name from
    /// the Conent Moderator web site. Your team name is the Id associated 
    /// with your subscription.</remarks>
    private const string TeamName = "***";

    /// <summary>
    /// The URL of the image to create a review job for.
    /// </summary>
    private const string ImageUrl =
        "https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png";

    /// <summary>
    /// The name of the log file to create.
    /// </summary>
    /// <remarks>Relative paths are ralative the execution directory.</remarks>
    private const string OutputFile = "OutputLog.txt";

    /// <summary>
    /// The number of seconds to delay after a review has finished before
    /// getting the review results from the server.
    /// </summary>
    private const int latencyDelay = 45;

    /// <summary>
    /// The callback endpoint for completed reviews.
    /// </summary>
    /// <remarks>Revies show up for reviewers on your team. 
    /// As reviewers complete reviews, results are sent to the
    /// callback endpoint using an HTTP POST request.</remarks>
    private const string CallbackEndpoint = "";

## <a name="add-code-to-auto-moderate-create-a-review-and-get-the-job-details"></a>자동 조정에 코드 추가, 검토 만들기 및 작업 세부 정보 가져오기

> [!Note]
> 실제로 콜백 URL **CallbackEndpoint**를 수동 검토의 결과를 받는(HTTP POST 요청을 통해) URL로 설정합니다.

**Main** 메서드에 다음 코드를 추가하여 시작합니다.

    using (TextWriter writer = new StreamWriter(OutputFile, false))
    {
        using (var client = Clients.NewClient())
        {
            writer.WriteLine("Create review job for an image.");
            var content = new Content(ImageUrl);
        
            // The WorkflowName contains the nameof the workflow defined in the online review tool.
            // See the quickstart article to learn more.
            var jobResult = client.Reviews.CreateJobWithHttpMessagesAsync(
                    TeamName, "image", "contentID", WorkflowName, "application/json", content, CallbackEndpoint);

            // Record the job ID.
            var jobId = jobResult.Result.Body.JobIdProperty;

            // Log just the response body from the returned task.
            writer.WriteLine(JsonConvert.SerializeObject(
                jobResult.Result.Body, Formatting.Indented));

            Thread.Sleep(2000);
            writer.WriteLine();

            writer.WriteLine("Get review job status.");
            var jobDetails = client.Reviews.GetJobDetailsWithHttpMessagesAsync(
                    TeamName, jobId);

            // Log just the response body from the returned task.
            writer.WriteLine(JsonConvert.SerializeObject(
                    jobDetails.Result.Body, Formatting.Indented));

            Console.WriteLine();
            Console.WriteLine("Perform manual reviews on the Content Moderator site.");
            Console.WriteLine("Then, press any key to continue.");
            Console.ReadKey();

            Console.WriteLine();
            Console.WriteLine($"Waiting {latencyDelay} seconds for results to propagate.");
            Thread.Sleep(latencyDelay * 1000);

            writer.WriteLine("Get review details.");
            jobDetails = client.Reviews.GetJobDetailsWithHttpMessagesAsync(
            TeamName, jobId);

            // Log just the response body from the returned task.
            writer.WriteLine(JsonConvert.SerializeObject(
            jobDetails.Result.Body, Formatting.Indented));
        }
        writer.Flush();
        writer.Close();
    }

> [!NOTE]
> Content Moderator 서비스 키에는 RPS(초당 요청 수) 속도 제한이 있습니다. 제한을 초과하는 경우 SDK는 429 오류 코드로 예외가 throw됩니다. 
>
> 체험판 계층 키에는 하나의 RPS 속도 제한이 있습니다.

## <a name="run-the-program-and-review-the-output"></a>프로그램 실행 및 출력 검토

콘솔에서 다음과 같은 예제 출력이 표시됩니다.

    Perform manual reviews on the Content Moderator site.
    Then, press any key to continue.

Content Moderator 검토 도구에 로그인하여 보류 중인 이미지 검토를 확인합니다.

**다음** 단추를 사용하여 제출합니다.

![사용자 중재자를 위한 이미지 검토](images/ocr-sample-image.PNG)

## <a name="see-the-sample-output-in-the-log-file"></a>로그 파일에서 예제 출력 확인

> [!NOTE]
> 출력 파일에서 **Teamname**, **ContentId**, **CallBackEndpoint** 및 **WorkflowId** 문자열은 이전에 사용한 값을 반영합니다.

    Create moderation job for an image.
    {
        "JobId": "2018014caceddebfe9446fab29056fd8d31ffe"
    }

    Get review details.
    {
        "Id": "2018014caceddebfe9446fab29056fd8d31ffe",
        "TeamName": "some team name",
        "Status": "InProgress",
        "WorkflowId": "OCR",
        "Type": "Image",
        "CallBackEndpoint": "",
        "ReviewId": "",
        "ResultMetaData": [],
        "JobExecutionReport": [
        {
            "Ts": "2018-01-07T00:38:26.7714671",
            "Msg": "Successfully got hasText response from Moderator"
        },
        {
            "Ts": "2018-01-07T00:38:26.4181346",
            "Msg": "Getting hasText from Moderator"
        },
        {
            "Ts": "2018-01-07T00:38:25.5122828",
            "Msg": "Starting Execution - Try 1"
        }
        ]
    }


## <a name="your-callback-url-if-provided-receives-this-response"></a>사용자의 콜백 URL(제공되는 경우)은 이 응답을 받습니다.

다음 예제와 같은 응답이 표시됩니다.

> [!NOTE]
> 사용자의 콜백 응답에서 **ContentId** 및 **WorkflowId** 문자열은 이전에 사용한 값을 반영합니다.

    {
        "JobId": "2018014caceddebfe9446fab29056fd8d31ffe",
        "ReviewId": "201801i28fc0f7cbf424447846e509af853ea54",
        "WorkFlowId": "OCR",
        "Status": "Complete",
        "ContentType": "Image",
        "CallBackType": "Job",
        "ContentId": "contentID",
        "Metadata": {
            "hastext": "True",
            "ocrtext": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n",
            "imagename": "contentID"
        }
    }


## <a name="next-steps"></a>다음 단계

이 빠른 시작과 기타 .NET용 Content Moderator 빠른 시작을 위한 [Visual Studio 솔루션을 다운로드](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator)하고 통합을 시작합니다.
