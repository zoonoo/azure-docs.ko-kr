---
title: Azure Media Services v3에서 라이브 스트림 | Microsoft Docs
description: 이 자습서에서는 .NET Core를 사용하여 Media Services v3에서 라이브 스트림의 단계를 설명합니다.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 11/08/2018
ms.author: juliako
ms.openlocfilehash: 7863f007093b5a86fb5095ee8bf1e14fc01d0348
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2018
ms.locfileid: "51613395"
---
# <a name="tutorial-stream-live-with-media-services-v3-using-apis"></a>자습서: API를 사용하여 Azure Media Services v3에서 라이브 스트림

[LiveEvents](https://docs.microsoft.com/rest/api/media/liveevents)는 Azure Media Services에서 라이브 스트리밍 콘텐츠를 처리하는 작업을 담당합니다. LiveEvent는 라이브 인코더에 제공할 입력 엔드포인트(수집 URL)를 제공합니다. LiveEvent는 라이브 인코더에서 라이브 입력 스트림을 수신하여 하나 이상의 [StreamingEndpoints](https://docs.microsoft.com/rest/api/media/streamingendpoints)를 통해 스트리밍하는 데 사용할 수 있도록 합니다. 또한 LiveEvents는 스트림을 추가로 처리하고 배달하기 전에 미리 보고 유효성을 검색하는 데 사용되는 미리 보기 엔드포인트(미리 보기 URL)을 제공합니다. 이 자습서에는 .NET Core를 사용하여 **통과** 형식의 라이브 이벤트를 만드는 방법을 보여줍니다. 

> [!NOTE]
> 계속 진행하기 전에 [Media Services v3에서 라이브 스트리밍](live-streaming-overview.md)을 검토해야 합니다. 

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.    

> [!div class="checklist"]
> * Media Services API 액세스
> * 샘플 앱 구성
> * 라이브 스트리밍을 수행하는 코드 검사
> * http://ampdemo.azureedge.net에서 [Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html)를 사용하여 이벤트 감시
> * 리소스 정리

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

자습서를 완료하는 데 필요한 조건은 다음과 같습니다.

- Visual Studio Code 또는 Visual Studio 설치
- CLI를 로컬로 설치하여 사용하기 위해 이 문서에는 Azure CLI 버전 2.0 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요. 

    현재 일부 [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) 명령만 Azure Cloud Shell에서 작동합니다. CLI를 로컬로 사용하는 것이 좋습니다.

- [Media Services 계정 만들기](create-account-cli-how-to.md)

    리소스 그룹 이름 및 Media Services 계정 이름에 사용한 값을 기억해 두세요.

- 브로드캐스트 또는 이벤트에 사용되는 카메라 또는 디바이스(예: 랩톱)입니다.
- 카메라에서 Media Services 라이브 스트리밍 서비스로 보내는 스트림으로 신호를 변환하는 온-프레미스 라이브 인코더입니다. 스트림은 **RTMP** 또는 **부드러운 스트리밍** 형식이어야 합니다.

## <a name="download-the-sample"></a>샘플 다운로드

다음 명령을 사용하여 스트리밍 .NET 샘플이 포함된 GitHub 리포지토리를 컴퓨터에 복제합니다.  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```

라이브 스트리밍 샘플은 [Live](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/Live/MediaV3LiveApp) 폴더에 있습니다.

> [!IMPORTANT]
> 이 샘플에서는 각 리소스에 고유한 접미사를 사용합니다. 디버깅을 취소하거나 앱을 실행하지 않고 종료하는 경우 계정에 여러 LiveEvents가 발생합니다. <br/>
> 실행 중인 LiveEvents를 중지해야 합니다. 그렇지 않으면 **비용이 청구**됩니다.

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="examine-the-code-that-performs-live-streaming"></a>라이브 스트리밍을 수행하는 코드 검사

이 섹션에서는 *MediaV3LiveApp* 프로젝트의 [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs) 파일에 정의된 함수를 살펴봅니다.

샘플을 정리하지 않고 여러 번 실행하는 경우 샘플은 이름이 충돌되지 않도록 각 리소스에 고유한 접미사를 만듭니다.

> [!IMPORTANT]
> 이 샘플에서는 각 리소스에 고유한 접미사를 사용합니다. 디버깅을 취소하거나 앱을 실행하지 않고 종료하는 경우 계정에 여러 LiveEvents가 발생합니다. <br/>
> 실행 중인 LiveEvents를 중지해야 합니다. 그렇지 않으면 **비용이 청구**됩니다.
 
### <a name="start-using-media-services-apis-with-net-sdk"></a>.NET SDK로 Media Services API 사용하기

.NET으로 Media Services API를 사용하려면 **AzureMediaServicesClient** 개체를 만들어야 합니다. 개체를 만들려면 Azure AD를 사용하여 클라이언트가 Azure에 연결하는 데 필요한 자격 증명을 제공해야 합니다. 아티클의 시작 부분에서 복제한 코드에서 **GetCredentialsAsync** 함수는 로컬 구성 파일에 제공된 자격 증명에 따라 ServiceClientCredentials 개체를 만듭니다. 

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>라이브 이벤트 만들기

이 섹션에서는 **통과** 형식의 LiveEvent를 만드는 방법을 보여줍니다(None으로 설정된 LiveEventEncodingType). 라이브 인코딩에 사용된 LiveEvent를 만들려는 경우 LiveEventEncodingType를 기본으로 설정합니다. 

라이브 이벤트를 만들 때 지정할 수 있는 몇 가지 다른 점은 다음과 같습니다.

* Media Services 위치 
* 라이브 이벤트의 스트리밍 프로토콜(현재 RTMP 및 부드러운 스트리밍 프로토콜이 지원됨)
       
    LiveEvent 또는 연결된 LiveOutputs이 실행 중인 동안에는 프로토콜 옵션을 변경할 수 없습니다. 다른 프로토콜을 요청하는 경우 각각의 스트리밍 프로토콜에 대한 별도 LiveEvent를 만들어야 합니다.  
* 수집 및 미리 보기에서 IP 제한입니다. 이 LiveEvent에 비디오를 수집하도록 허용된 IP 주소를 정의할 수 있습니다. 허용된 IP 주소는 단일 IP 주소(예: '10.0.0.1'), IP 주소 및 CIDR 서브넷 마스크를 사용하는 IP 범위(예: '10.0.0.1/22') 또는 IP 주소와 점으로 구분된 십진수 서브넷 마스크를 사용하는 IP 범위(예: '10.0.0.1(255.255.252.0)')로 지정할 수 있습니다.
    
    지정된 IP 주소가 없고 정의된 규칙이 없는 경우, IP 주소가 허용되지 않습니다. 모든 IP 주소를 허용하려면 규칙을 만들고 0.0.0.0/0으로 설정합니다.

이벤트를 만들 때 자동 시작을 지정할 수 있습니다. 

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveEvent)]

### <a name="get-ingest-urls"></a>수집 URL 가져오기

채널을 만든 후 라이브 인코더에 제공할 수집 URL을 가져올 수 있습니다. 인코더는 이러한 URL을 사용하여 라이브 스트림을 입력합니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetIngestURL)]

### <a name="get-the-preview-url"></a>미리 보기 URL 가져오기

previewEndpoint를 사용하여 인코더에서 입력이 실제로 수신되고 있는지 미리 보고 확인합니다.

> [!IMPORTANT]
> 계속하기 전에 비디오가 미리 보기 URL로 전달되고 있는지 확인합니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetPreviewURLs)]

### <a name="create-and-manage-liveevents-and-liveoutputs"></a>LiveEvents 및 LiveOutputs 만들기 및 관리

LiveEvent로 들어오는 스트림이 있으면 자산, LiveOutput 및 StreamingLocator를 만들어 스트리밍 이벤트를 시작할 수 있습니다. 이렇게 하면 스트림이 보관되고 StreamingEndpoint를 통해 시청자가 스트림을 사용할 수 있게 됩니다. 

#### <a name="create-an-asset"></a>자산 만들기

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateAsset)]

사용할 LiveOutput에 대한 자산을 만듭니다.

#### <a name="create-a-liveoutput"></a>LiveOutput 만들기

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveOutput)]

#### <a name="create-a-streaminglocator"></a>StreamingLocator 만들기

> [!NOTE]
> Azure Media Services 계정이 만들어지면 **기본** 스트리밍 엔드포인트가 **중지됨** 상태에 있는 계정에 추가됩니다. 콘텐츠 스트리밍을 시작하고 동적 패키징 및 동적 암호화를 활용하려면 콘텐츠를 스트리밍하려는 스트리밍 엔드포인트는 **실행** 상태에 있어야 합니다. 

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateStreamingLocator)]

```csharp

// Get the url to stream the output
ListPathsResponse paths = await client.StreamingLocators.ListPathsAsync(resourceGroupName, accountName, locatorName);

foreach (StreamingPath path in paths.StreamingPaths)
{
    UriBuilder uriBuilder = new UriBuilder();
    uriBuilder.Scheme = "https";
    uriBuilder.Host = streamingEndpoint.HostName;

    uriBuilder.Path = path.Paths[0];
    // Get the URL from the uriBuilder: uriBuilder.ToString()
}
```

### <a name="cleaning-up-resources-in-your-media-services-account"></a>Media Services 계정에서 리소스 정리

스트리밍 이벤트를 완료하고 이전에 프로비전된 리소스를 정리하려면 다음 절차를 수행합니다.

* 인코더에서 스트림의 푸시를 중지합니다.
* LiveEvent를 중지합니다. LiveEvent가 중지되면 채널에 대한 요금이 발생하지 않습니다. 채널을 다시 시작해야 하는 경우 채널의 수집 URL은 동일하므로 인코더를 다시 구성하지 않아도 됩니다.
* 라이브 이벤트의 보관 파일을 주문형 스트림으로 계속 제공하지 않으려면 StreamingEndpoint를 중지할 수 있습니다. LiveEvent가 중지된 상태이면 채널에 대한 요금이 발생하지 않습니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLiveEventAndOutput)]

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLocatorAssetAndStreamingEndpoint)]

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupAccount)]   

## <a name="watch-the-event"></a>이벤트 보기

이벤트를 시청하려면 [StreamingLocator 만들기](#create-a-streaminglocator)에서 설명된 코드를 실행할 때 가져온 스트리밍 URL을 복사하고 원하는 플레이어를 사용합니다. [Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html)를 사용하여 http://ampdemo.azureedge.net에서 스트림을 테스트할 수 있습니다. 

라이브 이벤트가 중지되면 이벤트를 주문형 콘텐츠로 자동으로 변환합니다. 이벤트를 중단 및 삭제한 다음에도 자산을 삭제하지 않는 한 사용자는 주문형 비디오로 보관된 콘텐츠를 스트림할 수 있습니다. 자산을 이벤트에서 사용하는 경우 삭제할 수 없습니다. 이벤트를 먼저 삭제해야 합니다. 

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에서 만든 Media Services 및 저장소 계정을 포함하여 리소스 그룹의 리소스가 더 이상 필요하지 않으면, 앞서 만든 리소스 그룹을 삭제합니다.

다음 CLI 명령을 실행합니다.

```azurecli-interactive
az group delete --name amsResourceGroup
```

> [!IMPORTANT]
> LiveEvent를 실행한 채로 두면 청구 비용이 발생합니다. 프로젝트/프로그램이 충돌하거나 어떤 이유로든 닫힌 경우 청구 상태에서 LiveEvent를 실행한 채로 둘 수 있습니다.

## <a name="next-steps"></a>다음 단계

[파일 스트리밍](stream-files-tutorial-with-api.md)

