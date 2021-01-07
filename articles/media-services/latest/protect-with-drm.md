---
title: Azure Media Services DRM 암호화 및 라이선스 배달 서비스
titleSuffix: Azure Media Services
description: DRM 동적 암호화 및 라이선스 배달 서비스를 사용하여 Microsoft PlayReady, Google Widevine 또는 Apple FairPlay 라이선스로 암호화된 스트림을 제공하는 방법에 대해 알아봅니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: abaa82d6f5f33a3dc29db50ae6d029dacd3f7c13
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89289363"
---
# <a name="tutorial-use-drm-dynamic-encryption-and-license-delivery-service"></a>자습서: DRM 동적 암호화 및 라이선스 배달 서비스 사용

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

> [!NOTE]
> 이 자습서에서 [.NET SDK](/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) 예제를 사용하지만, 일반적인 단계는 [REST API](/rest/api/media/liveevents), [CLI](/cli/azure/ams/live-event?view=azure-cli-latest) 또는 지원되는 기타 [SDK](media-services-apis-overview.md#sdks)와 동일합니다.

Azure Media Services를 사용하여 Microsoft PlayReady, Google Widevine 또는 Apple FairPlay 라이선스로 암호화된 스트림을 배달할 수 있습니다. 자세한 설명은 [동적 암호화를 사용하여 콘텐츠 보호](content-protection-overview.md)를 참조하세요.

또한 Media Services는 PlayReady, Widevine 및 FairPlay DRM 라이선스를 배달하는 서비스를 제공합니다. 사용자가 DRM으로 보호된 콘텐츠를 요청하면 플레이어 앱이 Media Services 라이선스 서비스에서 라이선스를 요청합니다. 플레이어 앱에 권한이 있으면 Media Services 라이선스 서비스에서 플레이어에 라이선스를 발급합니다. 라이선스에는 클라이언트 플레이어가 콘텐츠를 해독하고 스트림하는 데 사용할 수 있는 해독 키가 포함됩니다.

이 아티클은 [DRM으로 암호화](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM) 샘플에 기반합니다.

이 아티클에서 설명된 샘플에서는 다음과 같은 결과를 생성합니다.

![Azure Media Player에서 DRM으로 보호된 비디오가 포함 된 AMS](./media/protect-with-drm/ams_player.png)

이 자습서에서는 다음을 수행하는 방법을 보여 줍니다.

> [!div class="checklist"]
> * 인코딩 변환을 만듭니다.
> * 토큰을 확인하는 데 사용된 서명 키를 설정합니다.
> * 콘텐츠 키 정책에 대한 요구 사항을 설정합니다.
> * 지정된 스트리밍 정책을 사용하여 StreamingLocator를 만듭니다.
> * 파일을 재생하는 데 사용되는 URL을 만듭니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

자습서를 완료하는 데 필요한 항목은 다음과 같습니다.

* [콘텐츠 보호 개요](content-protection-overview.md) 문서를 검토합니다.
* [액세스 제어가 포함된 다중 DRM 콘텐츠 보호 시스템 설계](design-multi-drm-system-with-access-control.md)를 살펴봅니다.
* Visual Studio Code 또는 Visual Studio 설치
* [이 빠른 시작](./create-account-howto.md)에서 설명된 대로 새로운 Azure Media Services 계정을 만듭니다.
* [액세스 API](./access-api-howto.md)를 수행하여 Media Services API를 사용하는 데 필요한 자격 증명 가져오기
* 앱 구성 파일(appsettings.json)에서 적절한 값을 설정합니다.

## <a name="download-code"></a>코드 다운로드

다음 명령을 사용하여 이 항목에서 설명한 전체 .NET 샘플이 포함된 GitHub 리포지토리를 머신에 복제합니다.

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
"DRM으로 암호화" 샘플은 [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM) 폴더에 있습니다.

> [!NOTE]
> 이 샘플은 앱을 실행할 때마다 고유한 리소스를 만듭니다. 일반적으로 변환 및 정책 등 기존 리소스를 다시 사용합니다(기존 리소스에 필요한 구성이 있는 경우).

## <a name="start-using-media-services-apis-with-net-sdk"></a>.NET SDK로 Media Services API 사용하기

.NET으로 Media Services API를 사용하려면 **AzureMediaServicesClient** 개체를 만듭니다. 개체를 만들려면 Azure AD를 사용하여 클라이언트가 Azure에 연결하는 데 필요한 자격 증명을 제공해야 합니다. 아티클의 시작 부분에서 복제한 코드에서 **GetCredentialsAsync** 함수는 로컬 구성 파일에 제공된 자격 증명에 따라 ServiceClientCredentials 개체를 만듭니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>출력 자산 만들기  

출력 [Asset](assets-concept.md)은 인코딩 작업의 결과를 저장합니다.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateOutputAsset)]

## <a name="get-or-create-an-encoding-transform"></a>인코딩 Transform을 가져오거나 만들기

새로운 [Transform](transforms-jobs-concept.md) 인스턴스를 만드는 경우 이 인스턴스를 통해 출력하려는 것이 무엇인지 지정해야 합니다. 필수 매개 변수는 아래 코드와 같이 `transformOutput` 개체입니다. 각 TransformOutput에는 **Preset**이 포함됩니다. Preset은 원하는 TransformOutput을 생성하는 데 사용되는 비디오 및/또는 오디오 처리 작업에 대한 단계별 지침을 설명합니다. 이 자습서에서 설명하는 샘플은 **AdaptiveStreaming**이라는 기본 제공 Preset을 사용합니다. Preset은 입력 해상도 및 비트 전송률을 기반으로 자동 생성된 비트 전송률 사다리(비트 전송률-해상도 쌍)에 입력 비디오를 인코딩하고 각 비트 전송률-해상도 쌍에 해당하는 H.264 비디오 및 AAC 오디오가 포함된 ISO MP4 파일을 생성합니다. 

새 **변환**을 만들기 전에 다음 코드에 표시된 대로 먼저 **Get** 메서드를 사용하여 해당 변환이 이미 있는지 확인해야 합니다.  Media Services v3의 경우, 엔터티가 존재하지 않으면 엔터티에 대한 **Get** 메서드는 **null**을 반환합니다(이름의 대/소문자를 구분하지 않음).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>작업 제출

위에서 언급했듯이 **Transform** 개체는 레시피이며 [Job](transforms-jobs-concept.md)은 주어진 입력 비디오 또는 오디오 콘텐츠에 **Transform**을 적용하라는 Media Services에 대한 실제 요청입니다. **Job**은 입력 비디오의 위치 및 출력 위치와 같은 정보를 지정합니다.

이 자습서에서는 [HTTPS 원본 URL](job-input-from-http-how-to.md)에서 직접 수집한 파일을 기반으로 작업의 입력을 만듭니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>작업이 완료될 때까지 대기

작업이 완료될 때까지 시간이 조금 걸립니다. 적업이 완료될 때 알림을 받을 수 있습니다. 아래 코드 샘플은 **Job**의 상태에 대한 서비스를 폴링하는 방법을 보여줍니다. 폴링은 대기 시간이 발생할 가능성이 있기 때문에 프로덕션 앱에 권장하는 방법이 아닙니다. 폴링이 계정에서 초과 사용되면 정체될 수 있습니다. 대신 Event Grid를 사용해야 합니다. [이벤트를 사용자 지정 웹 엔드포인트로 라우팅](job-state-events-cli-how-to.md)을 참조하세요.

**작업**은 일반적으로 **예약됨**, **대기**, **처리 중**, **마침**(최종 상태) 상태를 거칩니다. 작업에서 오류가 발생하면 **오류** 상태가 표시됩니다. 작업을 취소 중인 경우 **취소 중**이 표시되고, 취소가 완료되면 **취소됨**이 표시됩니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>콘텐츠 키 정책 만들기

콘텐츠 키는 자산에 대한 보안 액세스를 제공합니다. DRM을 사용하여 콘텐츠를 암호화할 때는 [콘텐츠 키 정책](content-key-policy-concept.md)을 만들어야 합니다. 이 정책은 콘텐츠 키를 최종 클라이언트에 배달하는 방법을 구성합니다. 콘텐츠 키는 스트리밍 로케이터와 연결됩니다. Media Services는 권한 있는 사용자에게 암호화 키 및 라이선스를 배달하는 키 배달 서비스도 제공합니다.

지정된 구성을 사용하여 키를 배달하기 위해 충족되어야 하는 **콘텐츠 키 정책**에 대한 요구 사항(제한)을 설정해야 합니다. 이 예제에서는 다음 구성 및 요구 사항을 설정합니다.

* 구성

    [PlayReady](playready-license-template-overview.md) 및 [Widevine](widevine-license-template-overview.md) 라이선스를 Media Services 라이선스 배달 서비스에서 배달할 수 있도록 구성합니다. 이 샘플 앱이 [FairPlay](fairplay-license-overview.md) 라이선스를 구성하지 않았지만 FairPlay를 구성하는 데 사용할 수 있는 메서드가 포함됩니다. 또 다른 옵션으로 FairPlay 구성을 추가할 수 있습니다.

* 제한 사항

    앱은 정책에 대한 JWT(JSON Web Token) 토큰 형식 제한을 설정합니다.

플레이어가 스트림을 요청하면 Media Services는 지정된 키를 사용하고 동적으로 사용자의 콘텐츠를 암호화합니다. 스트림을 해독하기 위해 플레이어는 키 배달 서비스에서 키를 요청합니다. 사용자에게 키를 얻을 수 있는 권한이 있는지 여부를 결정하기 위해 서비스는 키에 지정된 콘텐츠 키 정책을 평가합니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>스트리밍 로케이터 만들기

인코딩이 완료되고 콘텐츠 키 정책이 설정되면 다음 단계는 출력 자산의 비디오를 클라이언트가 재생할 수 있도록 만드는 것입니다. 다음 두 단계로 비디오를 사용할 수 있습니다.

1. [스트리밍 로케이터](streaming-locators-concept.md)를 만듭니다.
2. 클라이언트가 사용할 수 있는 스트리밍 URL을 빌드합니다.

**스트리밍 로케이터**를 만드는 과정을 게시라고 합니다. 기본적으로 **스트리밍 로케이터**는 API를 호출한 즉시 유효합니다. 선택적 시작 및 종료 시간을 구성하지 않을 경우 삭제될 때까지 지속됩니다.

**스트리밍 로케이터**를 만들 때 원하는 `StreamingPolicyName`을 지정해야 합니다. 이 자습서에서는 스트리밍할 콘텐츠를 게시하는 방법을 Azure Media Services에 알려주는 미리 정의된 스트리밍 정책 중 하나를 사용합니다. 이 예제에서는 StreamingLocator.StreamingPolicyName을 "Predefined_MultiDrmCencStreaming" 정책으로 설정합니다. PlayReady 및 Widevine 암호화가 적용되고, 구성된 DRM 라이선스에 따라 재생 클라이언트로 키가 배달됩니다. 또한 CBCS(FairPlay)를 사용하여 스트림을 암호화하려면 "Predefined_MultiDrmStreaming"을 사용합니다.

> [!IMPORTANT]
> 사용자 지정 [스트리밍 정책](streaming-policy-concept.md)을 사용하는 경우 Media Service 계정에 대해 이러한 정책을 제한적으로 설계하고 동일한 암호화 옵션 및 프로토콜이 필요할 때마다 StreamingLocator에 다시 사용해야 합니다. Media Service 계정에는 StreamingPolicy 항목의 수에 대한 할당량이 있습니다. 각 StreamingLocator에 대해 새 StreamingPolicy를 만들지 말아야 합니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>테스트 토큰 가져오기

이 자습서에서는 콘텐츠 키 정책이 토큰을 제한하도록 지정합니다. 토큰 제한 정책에는 STS(보안 토큰 서비스)에서 발급한 토큰이 수반되어야 합니다. Media Services는 [JWT](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_3) 형식의 토큰을 지원하므로 샘플에서는 이를 구성합니다.

ContentKeyPolicy에는 ContentKeyIdentifierClaim을 사용하는데 이는 키 배달 서비스에 제공된 토큰에는 ContentKey의 식별자가 있어야 한다는 의미입니다. 이 샘플에서는 스트리밍 로케이터를 만들 때 콘텐츠 키를 지정하지 않으면 시스템이 임의의 스트리밍 로케이터를 만듭니다. 테스트 토큰을 생성하려면 ContentKeyId를 가져와 ContentKeyIdentifierClaim 클레임에 삽입해야 합니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetToken)]

## <a name="build-a-streaming-url"></a>스트리밍 URL 작성

[StreamingLocator](/rest/api/media/streaminglocators)가 만들어졌으므로 스트리밍 URL을 가져올 수 있습니다. URL을 작성하려면 [StreamingEndpoint](/rest/api/media/streamingendpoints) 호스트 이름과 **스트리밍 로케이터** 경로를 연결해야 합니다. 이 샘플에서는 *기본* **스트리밍 엔드포인트**가 사용됩니다. Media Service 계정을 처음으로 만들면 이 *기본* **스트리밍 엔드포인트**가 중지된 상태이므로 **Start**를 호출해야 합니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetMPEGStreamingUrl)]

앱을 실행하면 다음과 같은 화면이 표시됩니다.

![DRM으로 보호](./media/protect-with-drm/playready_encrypted_url.png)

브라우저를 열고 결과 URL을 붙여넣어 이미 작성된 토큰 및 URL을 사용하여 Azure Media Player 데모 페이지를 시작할 수 있습니다.

## <a name="clean-up-resources-in-your-media-services-account"></a>Media Services 계정의 리소스 정리

일반적으로 재사용할 개체를 제외하고 모두 정리해야 합니다. 일반적으로 Transform, StreamingLocator 등을 재사용합니다. 실험 후 계정을 정리하려면 재사용하지 않을 리소스를 삭제해야 합니다. 예를 들어 다음 코드는 작업, 만든 자산 및 콘텐츠 키 정책을 삭제합니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에서 만든 Media Services 및 스토리지 계정을 포함하여 리소스 그룹의 리소스가 더 이상 필요하지 않으면, 앞서 만든 리소스 그룹을 삭제합니다.

다음 CLI 명령을 실행합니다.

```azurecli
az group delete --name amsResourceGroup
```

## <a name="additional-notes"></a>추가적인 참고 사항

* Widevine은 Google Inc.에서 제공하는 서비스로, Google Inc.의 서비스 약관 및 개인정보처리방침을 따릅니다.

## <a name="ask-questions-give-feedback-get-updates"></a>질문, 피드백 제공, 업데이트 받기

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계

체크 아웃

> [!div class="nextstepaction"]
> [AES-128로 보호](protect-with-aes128.md)
