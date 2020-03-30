---
title: 미디어 서비스 v3 동적 암호화로 콘텐츠 보호
titleSuffix: Azure Media Services
description: Azure Media Services에서 동적 암호화, 스트리밍 프로토콜 및 암호화 유형을 사용한 콘텐츠 보호에 대해 알아봅니다.
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
ms.date: 03/17/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: c1c9440f7ec70cea98f270f04c3030c800dd0fde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79461115"
---
# <a name="protect-your-content-with-media-services-dynamic-encryption"></a>미디어 서비스 동적 암호화로 콘텐츠 보호

Azure Media 서비스를 사용하면 저장, 처리 및 배달을 통해 컴퓨터를 떠날 때부터 미디어를 보호할 수 있습니다. Microsoft Azure Media Services를 사용하면 Advanced Encryption Standard (AES-128) 또는 Microsoft PlayReady, Google Widevine 및 Apple FairPlay 등 세 가지 주요 DRM(디지털 권한 관리) 시스템 중 하나로 동적 암호화된 라이브 및 주문형 콘텐츠를 제공할 수 있습니다. 또한 Media Services는 인증된 클라이언트에게 AES 키 및DRM(PlayReady, Widevine 및 FairPlay) 라이선스를 배달하는 서비스를 제공합니다. 콘텐츠가 AES 지우기 키로 암호화되고 HTTPS를 통해 전송되는 경우 클라이언트에 도달할 때까지 명확하지 않습니다. 

미디어 서비스 v3에서 콘텐츠 키는 스트리밍 로케이터와 [연결됩니다(이 예제](protect-with-aes128.md)참조). 미디어 서비스 키 배달 서비스를 사용하는 경우 Azure Media Services에서 콘텐츠 키를 생성하도록 할 수 있습니다. 키 배달 서비스를 소유하고 있거나 두 데이터 센터에서 동일한 콘텐츠 키가 있어야 하는 고가용성 시나리오를 처리해야 하는 경우 콘텐츠 키를 직접 생성해야 합니다.

플레이어가 스트림을 요청하면 Media Services는 지정된 키를 사용하고 AES 암호화되지 않은 키 또는 DRM 암호화를 사용하여 동적으로 사용자의 콘텐츠를 암호화합니다. 스트림을 해독하기 위해 플레이어는 Media Services 키 배달 서비스 또는 지정한 키 배달 서비스에서 키를 요청합니다. 사용자가 키를 받을 수 있는 권한이 있는지 여부를 결정하기 위해 서비스는 키에 대해 지정한 콘텐츠 키 정책을 평가합니다.

REST API 또는 Media Services 클라이언트 라이브러리를 사용하여 라이선스 및 키에 대한 권한 부여 및 인증 정책을 구성할 수 있습니다.

다음 이미지는 미디어 서비스 콘텐츠 보호를 위한 워크플로를 보여 줍니다.

![미디어 서비스 콘텐츠 보호를 위한 워크플로우](./media/content-protection/content-protection.svg)
  
&#42; *동적 암호화는 AES-128 클리어 키, CBCS 및 CENC를 지원합니다. 자세한 내용은 [지원 매트릭스를](#streaming-protocols-and-encryption-types)참조하십시오.*

이 문서에서는 Media Services의 콘텐츠 보호를 이해하는 데 도움이 되는 개념및 용어에 대해 설명합니다.

## <a name="main-components-of-a-content-protection-system"></a>콘텐츠 보호 시스템의 주요 구성 요소

콘텐츠 보호 시스템을 성공적으로 완료하려면 노력의 범위를 완전히 이해해야 합니다. 다음 섹션에서는 구현해야 하는 세 가지 부분에 대한 개요를 제공합니다.

> [!NOTE]
> 다음 부분으로 이동하기 전에 다음 섹션의 각 부분을 집중하고 완전히 테스트하는 것이 좋습니다. 콘텐츠 보호 시스템을 테스트하려면 섹션에 지정된 도구를 사용합니다.

### <a name="media-services-code"></a>미디어 서비스 코드
  
[DRM 샘플에서는](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) .NET을 사용하여 Media Services v3를 사용하여 다중 DRM 시스템을 구현하는 방법을 보여 주며 있습니다. 또한 미디어 서비스 라이센스/키 배달 서비스를 사용하는 방법도 보여 주어 있습니다.
  
여러 암호화 유형(AES-128, PlayReady, Widevine, FairPlay)을 사용하여 각 자산을 암호화할 수 있습니다. 결합하는 것이 타인이 무엇인지 알아보려면 [스트리밍 프로토콜 및 암호화 유형을](#streaming-protocols-and-encryption-types)참조하십시오.

예제에서는 다음을 수행하는 방법을 보여 줍니다.

1. 콘텐츠 키 [정책을](content-key-policy-concept.md)만들고 구성합니다.

   콘텐츠 키 정책을 만들어 자산에 대한 보안 액세스를 제공하는 콘텐츠 키가 최종 클라이언트에 전달되는 방식을 구성합니다.  

   * 라이센스 제공 권한을 정의합니다. JSON 웹 토큰(JWT)의 클레임을 기반으로 권한 부여 검사의 논리를 지정합니다.
   * [PlayReady,](playready-license-template-overview.md) [와이드바인](widevine-license-template-overview.md)및/또는 [페어플레이](fairplay-license-overview.md) 라이선스를 구성합니다. 템플릿을 사용하면 각 DRM에 대한 권한과 권한을 구성할 수 있습니다.

     ```
     ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
     ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
     ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
     ```

2. 암호화된 자산을 스트리밍하도록 구성된 [스트리밍 로케이터를](streaming-locators-concept.md) 만듭니다.
  
   스트리밍 로케이터는 [스트리밍 정책과](streaming-policy-concept.md)연결되어야 합니다. 예제에서는 "Predefined_MultiDrmCencStreaming" `StreamingLocator.StreamingPolicyName` 정책으로 설정 합니다.

   PlayReady 및 와이드바인 암호화가 적용되고 키는 구성된 DRM 라이선스에 따라 재생 클라이언트로 전달됩니다. CBCS(FairPlay)로 스트림을 암호화하려면 "Predefined_MultiDrmStreaming" 정책을 사용합니다.

   스트리밍 로케이터는 정의한 콘텐츠 키 정책과도 연결됩니다.

3. 테스트 토큰을 만듭니다.

   메서드는 `GetTokenAsync` 테스트 토큰을 만드는 방법을 보여 주며
4. 스트리밍 URL을 작성합니다.

   메서드는 `GetDASHStreamingUrlAsync` 스트리밍 URL을 빌드 하는 방법을 보여 주며 이 경우 URL은 DASH 콘텐츠를 스트림합니다.

### <a name="player-with-an-aes-or-drm-client"></a>AES 또는 DRM 클라이언트가 있는 플레이어

플레이어 SDK(기본 또는 브라우저 기반)에 따라 비디오 플레이어 앱은 다음 요구 사항을 충족해야 합니다.

* 플레이어 SDK는 필요한 DRM 클라이언트를 지원합니다.
* 플레이어 SDK는 필요한 스트리밍 프로토콜인 스무스, DASH 및/또는 HTTP 라이브 스트리밍(HLS)을 지원합니다.
* 플레이어 SDK는 라이센스 획득 요청에서 JWT 토큰 전달을 처리할 수 있습니다.

[Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/)를 사용하여 플레이어를 만들 수 있습니다. [Azure Media Player의 ProtectionInfo API](https://amp.azure.net/libs/amp/latest/docs/)를 통해 다양한 DRM 플랫폼에 사용할 DRM 기술을 지정할 수 있습니다.

AES 또는 CENC(Widevine 및/또는 PlayReady) 암호화된 콘텐츠를 테스트하려면 [Azure Media Player](https://aka.ms/azuremediaplayer)를 사용할 수 있습니다. **고급 옵션을** 선택하고 암호화 옵션을 확인하십시오.

FairPlay 암호화된 콘텐츠를 테스트하려는 경우 [이 테스트 플레이어](https://aka.ms/amtest)를 사용합니다. 플레이어는 AES-128 명확한 키 암호화와 함께 와이드 바인, 플레이 레디, 페어 플레이 드롬을 지원합니다.

다른 D램을 테스트할 올바른 브라우저를 선택하십시오.

* 와이드 바인에 대한 크롬, 오페라, 또는 파이어 폭스.
* 플레이 에지 또는 인터넷 익스플로러 11 플레이 준비.
* 페어 플레이에 대한 맥 OS에 사파리.

### <a name="security-token-service"></a>보안 토큰 서비스

STS(보안 토큰 서비스)는 JWT를 백 엔드 리소스 액세스를 위한 액세스 토큰으로 발급합니다. Azure Media 서비스 라이선스/키 배달 서비스를 백 엔드 리소스로 사용할 수 있습니다. STS는 다음과 같은 사항을 정의해야 합니다.

* 발행자 및 대상(또는 범위).
* 콘텐츠 보호의 비즈니스 요구 사항에 따라 달라지는 클레임입니다.
* 서명 확인을 위한 대칭 또는 비대칭 확인.
* 키 롤오버 지원(필요한 경우).

[이 STS 도구를](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt) 사용하여 STS를 테스트할 수 있습니다. 키 롤오버가 있는 대칭, 비대칭 또는 Azure Active Directory(Azure AD)의 세 가지 유형의 확인 키를 모두 지원합니다.

## <a name="streaming-protocols-and-encryption-types"></a>스트리밍 프로토콜 및 암호화 유형

Media Services를 사용하여 AES 암호화되지 않은 키 또는 PlayReady, Widevine 또는 FairPlay 등과 같은 DRM 암호화로 동적 암호화된 콘텐츠를 제공할 수 있습니다. 현재 HLS, MPEG DASH 및 부드러운 스트리밍 형식을 암호화할 수 있습니다. 각 프로토콜은 다음과 같은 암호화 방법을 지원합니다.

### <a name="hls"></a>HLS

HLS 프로토콜은 다음 컨테이너 형식 및 암호화 체계를 지원합니다.

|컨테이너 형식|암호화 체계|URL 예제|
|---|---|---|
|모두|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbc)`|
|MPG2-TS |CBCS (페어플레이) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbcs-aapl)`|
|CMAF(fmp4) |CBCS (페어플레이) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`|
|MPG2-TS |CENC(PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cenc)`|
|CMAF(fmp4) |CENC(PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cenc)`|

HLS/CMAF + 페어플레이(HEVC/H.265 포함)는 다음 장치에서 지원됩니다.

* iOS 11 이상.
* 아이폰 8 이상.
* 인텔 7 세대 CPU와 맥OS 높은 시에라.

### <a name="mpeg-dash"></a>MPEG-DASH

MPEG-DASH 프로토콜은 다음 컨테이너 형식 및 암호화 체계를 지원합니다.

|컨테이너 형식|암호화 체계|URL 예
|---|---|---|
|모두|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cbc)`|
|CSF(fmp4) |CENC(Widevine + PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cenc)`|
|CMAF(fmp4)|CENC(Widevine + PlayReady)|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-cmaf,encryption=cenc)`|

### <a name="smooth-streaming"></a>부드러운 스트리밍

부드러운 스트리밍 프로토콜은 다음 컨테이너 형식 및 암호화 체계를 지원합니다.

|프로토콜|컨테이너 형식|암호화 체계|
|---|---|---|
|fMP4|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cbc)`|
|fMP4 | CENC(PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cenc)`|

### <a name="browsers"></a>브라우저

일반적인 브라우저는 다음 DRM 클라이언트를 지원합니다.

|브라우저|암호화|
|---|---|
|Chrome|Widevine|
|마이크로소프트 지, 인터넷 익스플로러 11|PlayReady|
|Firefox|Widevine|
|Opera|Widevine|
|Safari|FairPlay|

## <a name="controlling-content-access"></a>콘텐츠 액세스 제어

콘텐츠 키 정책을 구성하여 콘텐츠에 액세스하는 사람을 제어할 수 있습니다. Media Services는 키를 요청 하는 사용자에 권한을 부여하는 여러 방법을 지원합니다. 키가 클라이언트에 배달되려면 먼저 클라이언트(플레이어)에서 정책을 충족해야 합니다. 콘텐츠 키 정책에는 *개방* 또는 *토큰* 제한이 있을 수 있습니다.

허가 없이 라이선스를 발급하려는 경우 개방형 콘텐츠 키 정책을 사용할 수 있습니다. 예를 들어 수익이 광고 기반이고 구독 기반이 아닌 경우입니다.  

토큰 제한 콘텐츠 키 정책을 사용하면 콘텐츠 키는 유효한 JWT 토큰 또는 라이선스/키 요청에서 간단한 웹 토큰(SWT)을 제공하는 클라이언트로만 전송됩니다. 이 토큰은 STS에서 발급해야 합니다.

Azure AD를 STS로 사용하거나 [사용자 지정 STS를](#using-a-custom-sts)배포할 수 있습니다. 지정된 키로 서명된 토큰을 만들고 토큰 제한 구성에서 지정한 클레임을 발급하려면 반드시 STS를 구성해야 합니다. 미디어 서비스 라이센스/키 배달 서비스는 다음과 같은 조건이 모두 있는 경우 요청된 라이센스 또는 키를 클라이언트에 반환합니다.

* 토큰이 유효합니다.
* 토큰의 클레임은 라이선스 또는 키에 대해 구성된 클레임과 일치합니다.

토큰 제한 정책을 구성하는 경우 기본 확인 키, 발급자 및 대상 매개 변수를 지정해야 합니다. 기본 확인 키에는 토큰 서명에 사용된 키가 포함됩니다. 발급자는 토큰을 발급하는 STS입니다. 범위라고도 하는 대상은 토큰의 의도 또는 토큰이 액세스 권한을 부여한 리소스에 대해 설명합니다. 미디어 서비스 라이센스/키 배달 서비스는 토큰의 이러한 값이 템플릿의 값과 일치하는지 확인합니다.

### <a name="token-replay-prevention"></a>토큰 재생 방지

*토큰 재생 방지* 기능을 사용하면 미디어 서비스 고객이 동일한 토큰을 사용하여 키 또는 라이선스를 요청할 수 있는 횟수에 대한 제한을 설정할 수 있습니다. 고객은 토큰에 형식의 `urn:microsoft:azure:mediaservices:maxuses` 클레임을 추가할 수 있으며, 여기서 값은 토큰이 라이선스 또는 키를 획득하는 데 사용할 수 있는 횟수입니다. 키 배달에 동일한 토큰을 가진 모든 후속 요청은 승인되지 않은 응답을 반환합니다. [DRM 샘플에서](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L601)클레임을 추가하는 방법을 알아보십시오.
 
#### <a name="considerations"></a>고려 사항

* 고객은 토큰 생성을 제어할 수 있어야 합니다. 클레임은 토큰 자체에 배치해야 합니다.
* 이 기능을 사용하는 경우 요청이 수신된 시점보다 1시간 이상 떨어진 토큰이 있는 요청은 승인되지 않은 응답으로 거부됩니다.
* 토큰은 서명으로 고유하게 식별됩니다. 페이로드를 변경하면(예: 만료 시간 또는 클레임업데이트) 토큰의 서명이 변경되고 키 배달이 이전에 는 들어오지 않은 새 토큰으로 계산됩니다.
* 토큰이 고객이 설정한 `maxuses` 값을 초과하면 재생이 실패합니다.
* 이 기능은 모든 기존 보호된 콘텐츠에 사용할 수 있습니다(발행된 토큰만 변경하면 됩니다).
* 이 기능은 JWT 및 SWT 모두에서 작동합니다.

## <a name="using-a-custom-sts"></a>사용자 지정 STS 사용

고객은 사용자 지정 STS를 사용하여 토큰을 제공하도록 선택할 수 있습니다. 그 이유는 다음과 같습니다.

* 고객이 사용하는 IDP(IDP)는 STS를 지원하지 않습니다. 이 경우 사용자 지정 STS가 옵션일 수 있습니다.
* 고객은 STS를 고객의 구독자 청구 시스템과 통합하는 데 보다 유연하고 긴밀한 제어가 필요할 수 있습니다.

   예를 들어 [OTT](https://en.wikipedia.org/wiki/Over-the-top_media_services) 서비스 운영자는 프리미엄, 기본 및 스포츠와 같은 여러 가입자 패키지를 제공할 수 있습니다. 이 작업자는 토큰의 클레임을 구독자의 패키지와 일치시켜 특정 패키지의 콘텐츠만 제공되도록 할 수 있습니다. 이 경우 사용자 지정 STS가 필요한 유연성 및 제어를 제공합니다.

* 토큰에 사용자 지정 클레임을 포함하려면 다른 DRM 라이센스 매개 변수(구독 라이선스 와 대여 라이센스) 중에서 선택할 수 있습니다.
* 토큰이 액세스 권한을 부여하는 키의 콘텐츠 키 식별자를 나타내는 클레임을 포함합니다.

사용자 지정 STS를 사용할 때는 다음 두 가지를 변경해야 합니다.

* 자산에 대한 라이선스 배달 서비스를 구성할 때 Azure AD의 현재 키 대신 사용자 지정 STS에서 확인에 사용한 보안 키를 지정해야 합니다.
* JWT 토큰이 생성되면 Azure AD의 현재 X509 인증서의 프라이빗 키 대신 보안 키가 지정됩니다.

두 가지 유형의 보안 키가 있습니다.

* 대칭 키: JWT를 생성 및 확인하는 데 동일한 키가 사용됩니다.
* 비대칭 키: JWT를 암호화/생성하는 데는 프라이빗 키가, 토큰을 확인하는 데는 공개 키가 사용되는 방식으로 X509 인증서에 공개-프라이빗 키 쌍이 사용됩니다.

개발 플랫폼으로 .NET Framework/C#을 사용하는 경우 비공개 보안 키에 사용된 X509 인증서에는 키 길이가 2048 이상이어야 합니다. 이 키 길이는 .NET 프레임워크에서 클래스 System.IdentityModel.Tokens.X509AsymmetricSecurityKey의 요구 사항입니다. 그렇지 않으면 다음과 같은 예외가 throw됩니다: IDX10630: 'System.IdentityModel.Tokens.X509AsymmetricSecurityKey' 서명을 위한 '2048' 비트보다 작을 수 없습니다.

## <a name="custom-key-and-license-acquisition-url"></a>사용자 지정 키 및 라이센스 획득 URL

미디어 서비스가 아닌 다른 라이선스/키 배달 서비스를 지정하려면 다음 템플릿을 사용합니다. 템플릿의 두 대체 필드는 자산당 스트리밍 정책을 만드는 대신 여러 자산에서 스트리밍 정책을 공유할 수 있습니다. 

* `EnvelopeEncryption.CustomKeyAcquisitionUrlTemplate`: 최종 사용자 플레이어에게 키를 제공하는 사용자 지정 서비스의 URL용 템플릿입니다. 키 발급에 Azure Media 서비스를 사용할 때는 필요하지 않습니다. 

   템플릿은 서비스가 런타임에 업데이트할 대체 가능한 토큰을 요청과 관련된 값으로 지원합니다.  현재 지원되는 토큰 값은 다음과 같습니다.
   * `{AlternativeMediaId}`, 스트리밍로카토르Id.AlternativeMediaId의 값으로 대체됩니다.
   * `{ContentKeyId}`을 사용하며 요청된 키의 식별자 값으로 대체됩니다.
* `StreamingPolicyPlayReadyConfiguration.CustomLicenseAcquisitionUrlTemplate`: 최종 사용자 플레이어에게 라이선스를 제공하는 사용자 지정 서비스의 URL용 템플릿입니다. 라이선스 발급을 위해 Azure Media 서비스를 사용할 때는 필요하지 않습니다.

   템플릿은 서비스가 런타임에 업데이트할 대체 가능한 토큰을 요청과 관련된 값으로 지원합니다. 현재 지원되는 토큰 값은 다음과 같습니다.  
   * `{AlternativeMediaId}`, 스트리밍로카토르Id.AlternativeMediaId의 값으로 대체됩니다.
   * `{ContentKeyId}`을 사용하며 요청된 키의 식별자 값으로 대체됩니다. 
* `StreamingPolicyWidevineConfiguration.CustomLicenseAcquisitionUrlTemplate`: 이전 템플릿과 동일하며 와이드바인에만 사용할 수 있습니다. 
* `StreamingPolicyFairPlayConfiguration.CustomLicenseAcquisitionUrlTemplate`: 이전 템플릿과 동일, 페어 플레이에 대해서만.  

예를 들어:

```csharp
streamingPolicy.EnvelopEncryption.customKeyAcquisitionUrlTemplate = "https://mykeyserver.hostname.com/envelopekey/{AlternativeMediaId}/{ContentKeyId}";
```

`ContentKeyId`요청된 키의 값을 가수합니다. 요청을 측의 엔터티에 매핑하려는 경우 사용할 `AlternativeMediaId` 수 있습니다. 예를 들어 `AlternativeMediaId` 사용 권한을 조회하는 데 사용할 수 있습니다.

사용자 지정 라이선스/키 획득 URL을 사용하는 REST 예에서는 [스트리밍 정책 - 만들기](https://docs.microsoft.com/rest/api/media/streamingpolicies/create)를 참조하십시오.

> [!NOTE]
> Widevine은 Google Inc.에서 제공하는 서비스로, Google Inc.의 서비스 약관 및 개인정보처리방침을 따릅니다.

## <a name="troubleshoot"></a>문제 해결

`MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY` 오류가 발생하면 적절한 스트리밍 정책을 지정해야 합니다.

`_NOT_SPECIFIED_IN_URL`로 끝나는 오류가 발생하면 URL에서 암호화 형식을 지정해야 합니다. 예제는 `…/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`입니다. [스트리밍 프로토콜 및 암호화 유형을](#streaming-protocols-and-encryption-types)참조하십시오.

## <a name="ask-questions-give-feedback-get-updates"></a>질문, 피드백 제공, 업데이트 받기

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [AES 암호화로 보호](protect-with-aes128.md)
* [DRM으로 보호](protect-with-drm.md)
* [액세스 제어를 통해 다중 DRM 콘텐츠 보호 시스템 설계](design-multi-drm-system-with-access-control.md)
* [스토리지 쪽 암호화](storage-account-concept.md#storage-side-encryption)
* [질문과 대답](frequently-asked-questions.md)
* [JSON 웹 토큰 처리기](https://docs.microsoft.com/dotnet/framework/security/json-web-token-handler)
