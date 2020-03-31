---
title: Azure 미디어 서비스 v3로 신호 설명 오디오 트랙 | 마이크로 소프트 문서
description: 이 자습서의 단계를 따라 파일을 업로드하고, 비디오를 인코딩하고, 설명이 많은 오디오 트랙을 추가하고, Media Services v3로 콘텐츠를 스트리밍합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 09/25/2019
ms.author: juliako
ms.openlocfilehash: 0d8f88e6c2fe273efa969278146de67ba18eaecf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72392190"
---
# <a name="signal-descriptive-audio-tracks"></a>신호 설명 오디오 트랙

비디오에 내레이션 트랙을 추가하여 시각적으로 장애가 있는 클라이언트가 내레이션을 들어 비디오 녹화를 따르도록 할 수 있습니다. Media Services v3에서는 매니페스트 파일에 오디오 트랙에 추가하여 설명 오디오 트랙에 신호를 보시고 있습니다.

이 문서에서는 비디오를 인코딩하고, 설명 오디오가 포함된 오디오 전용 MP4 파일(AAC 코덱)을 출력 자산에 업로드하고, 설명 오디오를 포함하도록 .ism 파일을 편집하는 방법을 보여 주며, 이 문서에서는 설명오디오를 포함합니다.

## <a name="prerequisites"></a>사전 요구 사항

- [Media Services 계정 만들기](create-account-cli-how-to.md)
- [Azure CLI를 사용하여 Azure Media Services API 액세스](access-api-cli-how-to.md)의 단계를 수행하고 자격 증명을 저장합니다. API에 액세스할 때 필요합니다.
- [동적 패키징을](dynamic-packaging-overview.md)검토합니다.
- 동영상 [업로드, 인코딩 및 스트리밍](stream-files-tutorial-with-api.md) 자습서를 검토합니다.

## <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>입력 자산을 만들고 여기에 로컬 파일 업로드 

**CreateInputAsset** 함수는 새 입력 [자산을](https://docs.microsoft.com/rest/api/media/assets) 만들고 지정된 로컬 비디오 파일을 업로드합니다. 이 **에셋은** 인코딩 작업에 대한 입력으로 사용됩니다. Media Services v3에서 **작업**에 대한 입력은 **자산**이거나 HTTPS URL을 통해 Media Services 계정에서 사용할 수 있는 콘텐츠일 수도 있습니다. 

HTTPS URL에서 인코딩하는 방법을 알아보려면 [이 문서를](job-input-from-http-how-to.md) 참조하십시오.  

Media Services v3에서는 Azure Storage API를 사용하여 파일을 업로드합니다. 다음 .NET 코드 조각에서 방법을 참조하세요.

다음 함수는 아래와 같은 작업을 수행합니다.

* **에셋** 생성 
* [저장소에](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#upload-blobs-to-a-container) 있는 자산의 컨테이너에 쓰기 가능한 [SAS URL을](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) 가져옵니다.
* SAS URL을 사용하여 스토리지의 컨테이너에 파일 업로드

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

생성된 입력 자산의 이름을 다른 메서드에 전달해야 하는 경우 `Name` inputAsset.Name 등에서 반환된 `CreateInputAssetAsync`자산 개체에서 속성을 사용해야 합니다. 

## <a name="create-an-output-asset-to-store-the-result-of-the-encoding-job"></a>인코딩 작업의 결과를 저장하는 출력 자산 만들기

출력 [자산은](https://docs.microsoft.com/rest/api/media/assets) 인코딩 작업의 결과를 저장합니다. 다음 함수는 출력 자산을 만드는 방법을 보여 주며 있습니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

생성된 출력 자산의 이름을 다른 메서드에 전달해야 하는 경우 `Name` outputAsset.Name 와 같은 에서 `CreateIOutputAssetAsync`반환된 자산 개체에서 속성을 사용해야 합니다. 

이 문서의 경우 `outputAsset.Name` 값을 `SubmitJobAsync` 함수에 `UploadAudioIntoOutputAsset` 전달합니다.

## <a name="create-a-transform-and-a-job-that-encodes-the-uploaded-file"></a>업로드된 파일을 인코딩하는 변환 및 작업 만들기

Media Services에서 콘텐츠를 인코딩하거나 처리할 때 인코딩 설정을 레시피로 설정하는 것이 일반적인 패턴입니다. 그런 다음, 이 레시피가 비디오에 적용되도록 **Job**을 제출합니다. 새 비디오에 대한 새 작업을 제출하면 라이브러리의 모든 비디오에 레시피가 적용됩니다. Media Services의 레시피를 **Transform**이라고 합니다. 자세한 내용은 [변환 및 작업](transform-concept.md)을 참조하십시오. 이 자습서에서 설명하는 샘플은 다양한 iOS 및 Android 디바이스로 스트리밍하기 위해 비디오를 인코딩하는 레시피를 정의합니다. 

다음 예제에서는 변환이 없는 경우 변환을 만듭니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#EnsureTransformExists)]

다음 함수는 작업을 제출합니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>작업이 완료될 때까지 대기

작업을 완료하는 데 시간이 다소 걸리기 때문에 완료되면 알림을 받는 것이 좋습니다. 작업이 완료될 때까지 기다리려면 이벤트 그리드를 사용하는 것이 좋습니다.

작업은 일반적으로 다음과 같은 상태를 거칩니다: **예약된,** **큐에 대기,** **처리,** **완료(최종** 상태). 작업에서 오류가 발생하면 **오류** 상태가 표시됩니다. 작업을 취소 중인 경우 **취소 중**이 표시되고 완료되면 **취소됨**이 표시됩니다.

자세한 내용은 [이벤트 그리드 이벤트 처리를](reacting-to-media-services-events.md)참조하십시오.

## <a name="upload-the-audio-only-mp4-file"></a>오디오 전용 MP4 파일 업로드

설명오디오가 포함된 추가 오디오 전용 MP4 파일(AAC 코덱)을 출력 자산에 업로드합니다.  

```csharp
private static async Task UpoadAudioIntoOutputAsset(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string outputAssetName,
    string fileToUpload)
{
    // Use the Assets.Get method to get the existing asset. 
    // In Media Services v3, the Get method on entities returns null 
    // if the entity doesn't exist (a case-insensitive check on the name).

    // Call Media Services API to create an Asset.
    // This method creates a container in storage for the Asset.
    // The files (blobs) associated with the asset will be stored in this container.
    Asset asset = await client.Assets.GetAsync(resourceGroupName, accountName, outputAssetName);
    
    if (asset != null)
    {
      // Use Media Services API to get back a response that contains
      // SAS URL for the Asset container into which to upload blobs.
      // That is where you would specify read-write permissions 
      // and the exparation time for the SAS URL.
      var response = await client.Assets.ListContainerSasAsync(
          resourceGroupName,
          accountName,
          outputAssetName,
          permissions: AssetContainerPermission.ReadWrite,
          expiryTime: DateTime.UtcNow.AddHours(4).ToUniversalTime());

      var sasUri = new Uri(response.AssetContainerSasUrls.First());

      // Use Storage API to get a reference to the Asset container
      // that was created by calling Asset's CreateOrUpdate method.  
      CloudBlobContainer container = new CloudBlobContainer(sasUri);
      var blob = container.GetBlockBlobReference(Path.GetFileName(fileToUpload));

      // Use Strorage API to upload the file into the container in storage.
      await blob.UploadFromFileAsync(fileToUpload);
    }
}
```

함수 호출의 `UpoadAudioIntoOutputAsset` 예는 다음과 같습니다.

```csharp
await UpoadAudioIntoOutputAsset(client, config.ResourceGroup, config.AccountName, outputAsset.Name, "audio_description.m4a");
```

## <a name="edit-the-ism-file"></a>.ism 파일 편집

인코딩 작업이 완료되면 출력 자산에는 인코딩 작업에 의해 생성된 파일이 포함됩니다. 

1. Azure 포털에서 Media Services 계정과 연결된 저장소 계정으로 이동합니다. 
1. 출력 자산의 이름이 있는 컨테이너를 찾습니다. 
1. 컨테이너에서 .ism 파일을 찾아 오른쪽 창에서 **Blob 편집을** 클릭합니다. 
1. 설명 오디오가 포함된 업로드된 오디오 전용 MP4 파일(AAC 코덱)에 대한 정보를 추가하고 완료되면 **저장을** 눌러 .ism 파일을 편집합니다.

    설명이 있는 오디오 트랙을 신호하려면 .ism 파일에 "접근성" 및 "역할" 매개변수를 추가해야 합니다. 오디오 트랙을 오디오 설명으로 알리기 위해 이러한 매개 변수를 올바르게 설정하는 것은 사용자의 책임입니다. 예를 들어 `<param name="accessibility" value="description" />` 다음 `<param name="role" value="alternate" />` 예제와 같이 특정 오디오 트랙에 대한 .ism 파일을 추가하고 추가합니다.
 
```xml
<?xml version="1.0" encoding="utf-8"?>
<smil xmlns="http://www.w3.org/2001/SMIL20/Language">
  <head>
    <meta name="clientManifestRelativePath" content="ignite.ismc" />
    <meta name="formats" content="mp4-v3" />
  </head>
  <body>
    <switch>
      <audio src="ignite_320x180_AACAudio_381.mp4" systemBitrate="128041" systemLanguage="eng">
        <param name="systemBitrate" value="128041" valuetype="data" />
        <param name="trackID" value="2" valuetype="data" />
        <param name="trackName" value="aac_eng_2_128041_2_1" valuetype="data" />
        <param name="systemLanguage" value="eng" valuetype="data" />
        <param name="trackIndex" value="ignite_320x180_AACAudio_381_2.mpi" valuetype="data" />
      </audio>
      <audio src="audio_description.m4a" systemBitrate="194000" systemLanguage="eng">
        <param name="trackName" value="aac_eng_audio_description" />
        <param name="accessibility" value="description" />
        <param name="role" value="alternate" />     
      </audio>          
      <video src="ignite_1280x720_AACAudio_3549.mp4" systemBitrate="3549855">
        <param name="systemBitrate" value="3549855" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_1280x720_AACAudio_3549_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_960x540_AACAudio_2216.mp4" systemBitrate="2216764">
        <param name="systemBitrate" value="2216764" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_960x540_AACAudio_2216_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_640x360_AACAudio_1154.mp4" systemBitrate="1154569">
        <param name="systemBitrate" value="1154569" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_640x360_AACAudio_1154_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_480x270_AACAudio_721.mp4" systemBitrate="721893">
        <param name="systemBitrate" value="721893" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_480x270_AACAudio_721_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_320x180_AACAudio_381.mp4" systemBitrate="381027">
        <param name="systemBitrate" value="381027" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_320x180_AACAudio_381_1.mpi" valuetype="data" />
      </video>
    </switch>
  </body>
</smil>
```

## <a name="get-a-streaming-locator"></a>스트리밍 로케이터 받기

인코딩이 완료되면 다음 단계는 출력 자산의 비디오를 클라이언트가 재생할 수 있도록 만드는 것입니다. 이 작업은 두 단계로 수행할 수 있습니다. 첫째, [스트리밍 로케이터](https://docs.microsoft.com/rest/api/media/streaminglocators)를 만들고 둘째, 클라이언트가 사용할 수 있는 스트리밍 URL을 작성합니다. 

**스트리밍 로케이터를** 만드는 프로세스를 게시라고 합니다. 기본적으로 스트리밍 **로케이터는** API 호출을 한 직후에 유효하며 선택적 시작 및 종료 시간을 구성하지 않는 한 API 호출이 삭제될 때까지 지속됩니다. 

[StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators)를 만들 때 원하는 **StreamingPolicyName**을 지정해야 합니다. 이 예제에서는 미리 정의된 클리어 스트리밍**정책(사전 정의된 StreamingPolicy.ClearStreamingonlyOnly)이**사용되도록 명확히(또는 암호화되지 않은 콘텐츠) 스트리밍됩니다.

> [!IMPORTANT]
> 사용자 지정 [스트리밍 정책](https://docs.microsoft.com/rest/api/media/streamingpolicies)을 사용하는 경우 Media Service 계정에 대해 이러한 정책을 제한적으로 설계하고 동일한 암호화 옵션 및 프로토콜이 필요할 때마다 StreamingLocator에 다시 사용해야 합니다. Media Service 계정에는 Streaming Policy 항목의 수에 대한 할당량이 있습니다. 각 스트리밍 로케이터에 대해 새 스트리밍 정책을 만들지 않아야 합니다.

다음 코드에서는 고유한 locatorName을 사용하여 함수를 호출한다고 가정합니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateStreamingLocator)]

이 토픽의 샘플에서는 스트리밍에 대해 설명하지만 동일한 호출을 사용하여 점진적 다운로드를 통해 비디오를 전달하는 스트리밍 로케이터를 만들 수 있습니다.

### <a name="get-streaming-urls"></a>스트리밍 URL 얻기

[스트리밍 로케이터](https://docs.microsoft.com/rest/api/media/streaminglocators)가 생성되었으므로 **GetStreamingURLs**에 표시된 것처럼 스트리밍 URL을 가져올 수 있습니다. URL을 빌드하려면 [스트리밍 엔드포인트](https://docs.microsoft.com/rest/api/media/streamingendpoints) 호스트 이름과 **스트리밍 로케이터** 경로를 연결해야 합니다. 이 샘플에서는 *기본* **스트리밍 엔드포인트**가 사용됩니다. Media Service 계정을 처음으로 만들면 이 *기본* **스트리밍 엔드포인트**가 중지된 상태이므로 **Start**를 호출해야 합니다.

> [!NOTE]
> 이 메서드에서는 출력 자산에 대한 **스트리밍 로케이터**를 만들 때 사용된 locatorName이 필요합니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#GetStreamingURLs)]

## <a name="test-with-azure-media-player"></a>Azure Media Player로 테스트

이 문서에서는 스트림을 테스트하기 위해 Azure Media Player를 사용합니다. 

> [!NOTE]
> 플레이어가 https 사이트에 호스트 될 경우 URL을 "https"로 업데이트해야 합니다.

1. 웹 브라우저를 열고 [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/)로 이동합니다.
2. **URL:** 응용 프로그램에서 얻은 스트리밍 URL 값 중 하나를 붙여넣습니다. 
 
     URL을 HLS, Dash 또는 부드러운 스트리밍 형식으로 붙여넣을 수 있으며, Azure Media Player는 디바이스에서 재생하기 위한 적절한 스트리밍 프로토콜로 자동으로 전환합니다.
3. **플레이어 업데이트**를 누릅니다.

Azure Media Player는 테스트용으로 사용할 수 있지만 프로덕션 환경에서는 사용할 수 없습니다. 

## <a name="next-steps"></a>다음 단계

[비디오 분석](analyze-videos-tutorial-with-api.md)
