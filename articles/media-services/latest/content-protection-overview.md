---
title: Azure Media Services를 사용한 콘텐츠 보호 | Microsoft Docs
description: 이 기사는 Media Services 콘텐츠 보호에 대한 개요를 제공합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: f8ef92a335dd6faee076356dbffc873b08afbdc0
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49394278"
---
# <a name="content-protection-overview"></a>콘텐츠 보호 개요

Azure Media Services를 사용하여 컴퓨터를 떠날 때부터 저장, 처리 및 배달에 이르는 과정 내내 미디어를 보호할 수 있습니다. Microsoft Azure Media Services를 사용하면 Advanced Encryption Standard (AES-128) 또는 Microsoft PlayReady, Google Widevine 및 Apple FairPlay 등 세 가지 주요 DRM(디지털 권한 관리) 시스템 중 하나로 동적 암호화된 라이브 및 주문형 콘텐츠를 제공할 수 있습니다. 또한 Media Services는 인증된 클라이언트에게 AES 키 및DRM(PlayReady, Widevine 및 FairPlay) 라이선스를 배달하는 서비스를 제공합니다. 

다음 이미지는 Media Services 콘텐츠 보호 워크플로를 보여 줍니다. 

![콘텐츠 보호](./media/content-protection/content-protection.png)

&#42; *동적 암호화는 AES-128 "암호화되지 않은 키", CBCS 및 CENC를 지원합니다. 자세한 내용은 [여기](#streaming-protocols-and-encryption-types)에서 지원 매트릭스를 참조하세요.*

이 문서에서는 Media Services를 사용한 콘텐츠 보호를 이해하는 것과 관련된 개념 및 용어를 설명합니다. 또한 이 문서에는 [FAQ](#faq) 섹션이 있으며, 콘텐츠를 보호하는 방법을 보여 주는 문서에 대한 링크가 제공됩니다. 

## <a name="main-components-of-the-content-protection-system"></a>콘텐츠 보호 시스템의 기본 구성 요소

"content protection" 시스템/응용 프로그램 디자인을 성공적으로 완료하려면 활동 범위를 완전히 이해해야 합니다. 다음 목록은 구현해야 할 세 부분에 대한 개요를 제공합니다. 

1. Azure Media Services 코드
  
  * PlayReady, Widevine 및/또는 FairPlay에 대한 라이선스 템플릿. 이 템플릿을 사용하면 각 사용된 DRM에 대한 권한을 구성할 수 있음
  * JWT의 클레임에 따라 권한 부여 확인의 논리를 지정하면서 라이선스 배달 권한 부여
  * DRM 암호화를 정의하면서 적용된 해당 DRM, 스트리밍 프로토콜 및 콘텐츠 키

  > [!NOTE]
  > 여러 암호화 유형(AES-128, PlayReady, Widevine, FairPlay)을 사용하여 각 자산을 암호화할 수 있습니다. 결합에 적합한 것을 확인하려면 [스트리밍 프로토콜 및 암호화 유형](#streaming-protocols-and-encryption-types)을 참조합니다.
  
  다음 문서에서는 AES 및/또는 DRM으로 콘텐츠를 암호화하는 단계를 보여 줍니다. 
  
  * [AES 암호화로 보호](protect-with-aes128.md)
  * [DRM으로 보호](protect-with-drm.md)

2. AES 또는 DRM 클라이언트를 사용한 플레이어. 플레이어 SDK(기본 또는 브라우저 기반)에 따라 비디오 플레이어 앱은 다음 요구 사항을 충족해야 합니다.
  * 플레이어 SDK는 필요한 DRM 클라이언트 지원
  * 플레이어 SDK는 부드러운, DASH 및/또는 HLS 등의 필요한 스트리밍 프로토콜 지원
  * 플레이어 SDK는 라이선스 취득 요청에서 JWT 토큰 전달을 처리할 수 있어야 함
  
    [Azure Media Player API](http://amp.azure.net/libs/amp/latest/docs/)를 사용하여 플레이어를 만들 수 있습니다. [Azure Media Player의 ProtectionInfo API](http://amp.azure.net/libs/amp/latest/docs/)를 통해 다양한 DRM 플랫폼에 사용할 DRM 기술을 지정할 수 있습니다.

    AES 또는 CENC(Widevine 및/또는 PlayReady) 암호화된 콘텐츠를 테스트하려면 [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html)를 사용할 수 있습니다. “고급 옵션”을 클릭하고 암호화 옵션을 확인합니다.

    FairPlay 암호화된 콘텐츠를 테스트하려는 경우 [이 테스트 플레이어](http://aka.ms/amtest)를 사용합니다. 플레이어는 Widevine, PlayReady, FairPlay DRMs 및 AES-128 암호화되지 않은 키 암호화를 지원합니다. Chrome/Opera/Firefox for Widevine, MS Edge/IE11 for PlayReady, Safari on macOS for FairPlay 등의 다양한 DRM을 테스트하려면 올바른 브라우저를 선택해야 합니다.

3. 백 엔드 리소스 액세스에 대한 액세스 토큰으로 JWT(JSON Web Token)를 발급하는 STS(보안 토큰 서비스). AMS 라이선스 배달 서비스를 백 엔드 리소스로 사용할 수 있습니다. STS는 다음을 정의해야 합니다.

  * 발급자 및 대상(또는 범위)
  * 콘텐츠 보호의 비즈니스 요구 사항에 종속되는 클레임
  * 서명 확인에 대한 대칭 또는 비대칭 확인
  * 키 롤오버 지원(필요한 경우)

    대칭, 비대칭 또는 키 롤오버를 통한 AAD 등의 모든 3가지 유형의 확인 키를 지원하는 STS를 테스트하려면 [이 STS 도구](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt)를 사용할 수 있습니다. 

> [!NOTE]
> 다음 부분으로 이동하기 전에 각 부분을 완벽히 테스트하고 집중하는 것을 매우 권장합니다. "content protection" 시스템을 테스트하려면 위 목록에서 지정된 도구를 사용합니다.  

## <a name="streaming-protocols-and-encryption-types"></a>스트리밍 프로토콜 및 암호화 유형

Media Services를 사용하여 AES 암호화되지 않은 키 또는 PlayReady, Widevine 또는 FairPlay 등과 같은 DRM 암호화로 동적 암호화된 콘텐츠를 제공할 수 있습니다. 현재, HLS(HTTP 라이브 스트리밍) MPEG DASH 및 부드러운 스트리밍 형식을 암호화할 수 있습니다. 각 프로토콜은 다음 암호화 방법을 지원합니다.

|프로토콜|컨테이너 형식|암호화 체계|
|---|---|---|---|
|MPEG-DASH|모두|AES|
||CSF(fmp4) |CENC(Widevine + PlayReady) |
||CMAF(fmp4)|CENC(Widevine + PlayReady)|
|HLS|모두|AES|
||MPG2-TS |CBCS(Fairplay) |
||MPG2-TS |CENC(PlayReady) |
||CMAF(fmp4) |CENC(PlayReady) |
|부드러운 스트리밍|fMP4|AES|
||fMP4 | CENC(PlayReady) |

## <a name="dynamic-encryption"></a>동적 암호화

Media Services v3에서 콘텐츠 키는 StreamingLocator와 연결됩니다([이 예제](protect-with-aes128.md) 참조). Media Services 키 배달 서비스를 사용하는 경우 콘텐츠 키를 자동 생성해야 합니다. 자체 키 배달 서비스를 사용 중인 경우 또는 두 데이터 센터에 동일한 콘텐츠 키가 있어야 하는 고가용성 시나리오를 처리해야 하는 경우 콘텐츠 키를 직접 생성해야 합니다.

플레이어가 스트림을 요청하면 Media Services는 지정된 키를 사용하고 AES 암호화되지 않은 키 또는 DRM 암호화를 사용하여 동적으로 사용자의 콘텐츠를 암호화합니다. 스트림을 해독하기 위해 플레이어는 Media Services 키 배달 서비스 또는 지정한 키 배달 서비스에서 키를 요청합니다. 사용자에게 키를 얻을 권한이 있는지 여부를 결정하기 위해 서비스는 키에 지정된 권한 부여 정책을 평가합니다.

## <a name="aes-128-clear-key-vs-drm"></a>AES-128 암호화되지 않은 키 및 DRM

고객들은 종종 AES 암호화 또는 DRM 시스템을 사용해야 할지 여부를 궁금해 합니다. 두 시스템 간의 주요 차이점은 AES 암호화를 사용하면 콘텐츠 키가 암호화되지 않은 형식으로 클라이언트에 전송된다는 것입니다. 그 결과 콘텐츠를 암호화하는 데 사용되는 키는 일반 텍스트로 클라이언트에 대한 네트워크 추적에서 볼 수 있습니다. AES-128 암호화되지 않은 키 암호화는 뷰어가 신뢰할 만한 당사자(예: 직원이 볼 수 있도록 회사 내에 배포되는 회사 비디오 암호화)인 사용 사례에 적합합니다.

PlayReady, Widevine, 및 FairPlay 은 모두 AES-128 암호화되지 않은 키와 비교해서 더 높은 수준의 암호화를 제공합니다. 콘텐츠 키는 암호화된 형식으로 전송됩니다. 또한 암호 해독은 악의적인 사용자가 공격하기에 좀 더 어려운 운영 체제 수준의 보안 환경에서 처리됩니다. DRM은 뷰어가 신뢰할 만한 당사자가 아니고 가장 높은 수준의 보안이 필요한 사용 사례에 권장됩니다.

## <a name="storage-side-encryption"></a>저장소 쪽 암호화

미사용 자산을 보호하려면 저장소 쪽 암호화를 사용하여 자산을 암호화해야 합니다. 다음 표는 Media Services v3에서 저장소 쪽 암호화가 작동하는 원리를 보여줍니다.

|암호화 옵션|설명|Media Services v3|
|---|---|---|---|
|Media Services 저장소 암호화| AES-256 암호화, Media Services에서 키 관리|지원되지 않음<sup>(1)</sup>|
|[미사용 데이터에 대한 Storage 서비스 암호화](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Azure Storage가 제공하는 서버 쪽 암호화, Azure 또는 고객이 키 관리|지원됨|
|[저장소 클라이언트 쪽 암호화](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Azure Storage가 제공하는 클라이언트 쪽 암호화, Key Vault의 고객이 키 관리|지원되지 않음|

<sup>1</sup> Media Services v3에서 저장소 암호화(AES-256 암호화)는 자산을 Media Services v2를 사용하여 만들었을 경우 이전 버전과의 호환성에 대해서만 지원됩니다. v3는 기존 저장소 암호화된 자산과 함께 작동하지만 새로 만들기를 허용하지는 않습니다.

## <a name="licenses-and-keys-delivery-service"></a>라이선스 및 키 배달 서비스

Microsoft Azure Media Services는 DRM(PlayReady, Widevine, FairPlay) 라이선스 및 AES 키를 인증된 클라이언트에 키 배달 서비스를 제공합니다. REST API 또는 Media Services 클라이언트 라이브러리를 사용하여 라이선스 및 키에 대한 권한 부여 및 인증 정책을 구성할 수 있습니다.

## <a name="control-content-access"></a>콘텐츠 액세스 제어

콘텐츠 키 정책을 구성하여 콘텐츠에 액세스하는 사람을 제어할 수 있습니다. Media Services는 키를 요청 하는 사용자를 인증 하는 여러 방법을 지원합니다. 콘텐츠 키 정책을 구성해야 합니다. 키가 클라이언트에 배달되려면 먼저 클라이언트(플레이어)에서 정책을 충족해야 합니다. 콘텐츠 키 정책에는 **개방** 또는 **토큰** 제한이 있을 수 있습니다. 

토큰 제한 콘텐츠 키 정책을 사용하여 콘텐츠 키를 키/라이선스 요청에서 유효한 JWT(JSON Web Token) 또는 SWT(단순 웹 토큰)를 표시하는 클라이언트에만 보낼 수 있습니다. 이 토큰은 STS(보안 토큰 서비스)에 의해 발급되어야 합니다. Azure Active Directory를 STS로 사용하거나 사용자 지정 STS를 배포할 수 있습니다. 지정된 키로 서명된 토큰을 만들고 토큰 제한 구성에서 지정한 클레임을 발급하려면 반드시 STS를 구성해야 합니다. 토큰이 유효하고 해당 토큰의 클레임이 키/라이선스에 대해 구성된 클레임과 일치하는 경우 Media Services 키 배달 서비스는 요청된 키/라이선스를 클라이언트에 반환합니다.

토큰 제한 정책을 구성하는 경우 기본 확인 키, 발급자 및 대상 매개 변수를 지정해야 합니다. 기본 확인 키에는 토큰 서명에 사용된 키가 포함됩니다. 발급자는 토큰을 발급하는 보안 토큰 서비스입니다. 때로 범위라고도 하는 대상은 토큰의 의도 또는 토큰에서 접근을 인증하는 대상 리소스를 설명합니다. Media Services 키 배달 서비스는 이러한 토큰의 값이 템플릿 파일에 있는 값과 일치하는지 확인합니다.

## <a name="a-idfaqfrequently-asked-questions"></a><a id="faq"/>질문과 대답

### <a name="question"></a>질문

AMS(Azure Media Services) v3를 사용하여 다중 DRM(PlayReady, Widevine 및 FairPlay) 시스템을 구현하고 AMS 라이선스/키 배달 서비스를 사용하려면 어떻게 할까요?

### <a name="answer"></a>응답

종단 간 시나리오의 경우 [이 코드 예제](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs)를 참조하세요. 

예제에서는 다음을 수행하는 방법을 보여 줍니다.

1. ContentKeyPolicies를 만들고 구성합니다.

  샘플에는 [PlayReady](playready-license-template-overview.md), [Widevine](widevine-license-template-overview.md) 및 [FairPlay](fairplay-license-overview.md) 라이선스를 구성하는 함수가 포함되어 있습니다.

    ```
    ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
    ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
    ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
    ```

2. 암호화된 자산을 스트림하도록 구성된 StreamingLocator를 만듭니다. 

  예를 들어 StreamingLocator.StreamingPolicyName을 "Predefined_MultiDrmCencStreaming" 정책으로 설정할 수 있습니다. 이 정책은 로케이터에서 생성되고 설정할 두 가지 콘텐츠 키(봉투 및 CENC)에 대한 것임을 나타냅니다. 따라서 봉투, PlayReady 및 Widevine 암호화가 적용됩니다(키가 구성된 DRM 라이선스에 따라 재생 클라이언트로 배달됨). 또한 CBCS(FairPlay)를 사용하여 스트림을 암호화하려면 "Predefined_MultiDrmStreaming"을 사용합니다.

3. 테스트 토큰을 만듭니다.

  **GetTokenAsync** 메서드에서 테스트 토큰을 만드는 방법을 보여 줍니다.
  
4. 스트리밍 URL을 작성합니다.

  **GetDASHStreamingUrlAsync** 메서드에서 스트리밍 URL을 작성하는 방법을 보여 줍니다. 이 경우 URL은 **DASH** 콘텐츠를 스트림합니다.

### <a name="question"></a>질문

라이선스 또는 키를 요청하는 데 사용하기 전에 JWT 토큰을 가져올 수 있는 방법과 위치는 어떻게 되나요?

### <a name="answer"></a>응답

1. 프로덕션 환경의 경우 HTTPS 요청 시 JWT 토큰을 발급하는 STS(보안 토큰 서비스)(웹 서비스)가 있어야 합니다. 테스트를 위해 [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs)에 정의된 **GetTokenAsync** 메서드에 표시된 코드를 사용할 수 있습니다.
2. 사용자가 인증되면 플레이어에서 이러한 토큰에 대해 STS에 요청하고 토큰의 값으로 할당해야 합니다. [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/)를 사용할 수 있습니다.

* 대칭 및 비대칭 키를 사용하여 STS를 실행하는 예제는 [http://aka.ms/jwt](http://aka.ms/jwt)를 참조하세요. 
* 이러한 JWT 토큰을 사용하는 Azure Media Player를 기반으로 하는 플레이어의 예제는 [http://aka.ms/amtest](http://aka.ms/amtest)를 참조하세요("player_settings" 링크를 펼쳐 토큰 입력을 확인함).

### <a name="question"></a>질문

AES 암호화를 사용하여 비디오를 스트림할 수 있도록 요청에 권한을 부여하려면 어떻게 할까요?

### <a name="answer"></a>응답

올바른 방법은 다음과 같이 STS(보안 토큰 서비스)를 활용하는 것입니다.

STS에서 사용자 프로필에 따라 서로 다른 클레임(예: "프리미엄 사용자", "기본 사용자", "평가판 사용자")을 추가합니다. JWT에서 서로 다른 클레임을 사용하면 사용자가 각각의 콘텐츠를 볼 수 있습니다. 물론 ContentKeyPolicyRestriction에는 서로 다른 콘텐츠/자산에 해당하는 RequiredClaims가 있습니다.

[이 샘플](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)과 같이 Azure Media Services API를 사용하여 라이선스/키 배달을 구성하고 자산을 암호화합니다.

## <a name="next-steps"></a>다음 단계

다음 문서를 확인합니다.

  * [AES 암호화로 보호](protect-with-aes128.md)
  * [DRM으로 보호](protect-with-drm.md)

추가 정보는 [액세스 제어가 포함된 다중 DRM 콘텐츠 보호 시스템 설계](design-multi-drm-system-with-access-control.md)에서 찾을 수 있습니다.


