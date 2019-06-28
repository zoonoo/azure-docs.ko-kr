---
title: 서브 클립 Azure Media Services를 사용 하 여 인코딩할 때 비디오
description: 이 항목에서는 Azure Media services.NET SDK를 사용 하 여 인코딩할 때에 비디오를 서브 클립 방법 설명
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/09/2019
ms.author: juliako
ms.openlocfilehash: 3d584ee742aa93cdecf4b04d942afb2ed83a7357
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304980"
---
# <a name="subclip-a-video-when-encoding-with-media-services---net"></a>서브 클립 Media Services-.NET을 사용 하 여 인코딩할 때 비디오

Trim 또는 사용 하 여 인코딩할 때에 비디오를 서브 클립 수는 [작업](https://docs.microsoft.com/rest/api/media/jobs)합니다. 이 기능을 사용 하 여 사용할 [변환](https://docs.microsoft.com/rest/api/media/transforms) 중 하나를 사용 하 여 빌드된 합니다 [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) 미리 설정 또는 [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) 사전 설정입니다.

다음 C# 예제에서는 인코딩 작업을 제출 하는 대로 자산에서 비디오를 삭제 하는 작업을 만듭니다. 

## <a name="prerequisites"></a>필수 조건

이 항목에 설명된 단계를 완료하려면 다음을 수행해야 합니다.

- [Azure Media Services 계정 만들기](create-account-cli-how-to.md)
- 변환 및 입력 만들고 자산을 출력 합니다. 변환 및 입력 및 출력 자산을 만드는 방법을 표시 합니다 [업로드, 인코딩 및.NET을 사용 하 여 비디오를 스트림](stream-files-tutorial-with-api.md) 자습서.
- 검토 합니다 [인코딩 개념](encoding-concept.md) 항목입니다.

## <a name="example"></a>예

```csharp
/// <summary>
/// Submits a request to Media Services to apply the specified Transform to a given input video.
/// </summary>
/// <param name="client">The Media Services client.</param>
/// <param name="resourceGroupName">The name of the resource group within the Azure subscription.</param>
/// <param name="accountName"> The Media Services account name.</param>
/// <param name="transformName">The name of the transform.</param>
/// <param name="jobName">The (unique) name of the job.</param>
/// <param name="inputAssetName">The name of the input asset.</param>
/// <param name="outputAssetName">The (unique) name of the  output asset that will store the result of the encoding job. </param>
// <SubmitJob>
private static async Task<Job> JobWithBuiltInStandardEncoderWithSingleClipAsync(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string transformName,
    string jobName,
    string inputAssetName,
    string outputAssetName)
{
    var jobOutputs = new List<JobOutputAsset>
    {
        new JobOutputAsset(state: JobState.Queued, progress: 0, assetName: outputAssetName)
    };

    var clipStart = new AbsoluteClipTime()
    {
        Time = new TimeSpan(0, 0, 20)
    };

    var clipEnd = new AbsoluteClipTime()
    {
        Time = new TimeSpan(0, 0, 30)
    };

    var jobInput = new JobInputAsset(assetName: inputAssetName, start: clipStart, end: clipEnd);

    Job job = await client.Jobs.CreateAsync(
        resourceGroupName,
        accountName,
        transformName,
        jobName,
        new Job(input: jobInput, outputs: jobOutputs.ToArray(), name: jobName)
        {
            Description = $"A Job with transform {transformName} and single clip.",
            Priority = Priority.Normal,
        });

    return job;
}
```

## <a name="next-steps"></a>다음 단계

[사용자 지정 변환을 사용 하 여 인코딩하는 방법](customize-encoder-presets-how-to.md) 
