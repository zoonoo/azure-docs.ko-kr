---
title: 콘텐츠 보호 개요 | Microsoft Docs
description: 이 기사는 미디어 서비스 콘텐츠 보호에 대한 개요를 제공합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: erikre
editor: ''

ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: juliako

---
# <a name="protecting-content-overview"></a>콘텐츠 보호 개요
Microsoft Azure 미디어 서비스를 사용하면 컴퓨터를 떠날 때부터 저장, 처리 및 배달에 이르는 과정 내내 미디어를 보호할 수 있습니다. Media Services를 사용하면 128비트 암호화 키를 사용하는 AES(Advanced Encryption Standard) 또는 Microsoft PlayReady, Google Widevine 및 Apple FairPlay 등 주요 DRM 중 하나로 동적 암호화된 라이브 및 주문형 콘텐츠를 제공할 수 있습니다. 또한 Media Services는 인증된 클라이언트에게 AES 키 및DRM(PlayReady, Widevine 및 FairPlay) 라이선스를 배달하는 서비스를 제공합니다. 

다음 이미지는 AMS가 지원하는 콘텐츠 보호 워크플로를 보여 줍니다. 

![PlayReady로 보호](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

> [!NOTE]
> 동적 암호화를 사용할 수 있으려면 먼저 암호화된 콘텐츠를 스트리밍하려는 스트리밍 끝점에서 하나 이상의 스트리밍 예약 단위를 가져와야 합니다.
> 
> 

이 토픽에서는 AMS를 사용한 콘텐츠 보호를 이해하는 것과 관련된 [개념 및 용어](media-services-content-protection-overview.md) 를 설명합니다. 토픽에는 콘텐츠 보호 태스크의 수행 방법을 보여 주는 토픽에 대한 [링크](media-services-content-protection-overview.md#common-scenarios) 도 포함되어 있습니다. 

## <a name="dynamic-encryption"></a>동적 암호화
Microsoft Azure Media Services를 사용하면 AES 암호화되지 않은 키 또는 Microsoft PlayReady, Google Widevine 및 Apple FairPlay 등과 같은 DRM 암호화로 동적 암호화된 콘텐츠를 제공할 수 있습니다.

현재 암호화할 수 있는 스트리밍 형식은 HLS, MPEG DASH 및 부드러운 스트리밍입니다. HDS 스트리밍 형식 또는 점진적 다운로드는 암호화할 수 없습니다.

미디어 서비스에서 자산을 암호화하려는 경우 암호화 키(CommonEncryption 또는 EnvelopeEncryption)를 자산에 연결하고 해당 키에 대해 권한 부여 정책도 구성해야 합니다.

또한 자산의 배달 정책을 구성해야 합니다. 저장소에서 암호화된 자산을 스트리밍하려면 자산 배달 정책을 구성하여 배달 방법을 지정해야 합니다.

플레이어가 스트림을 요청하면 Media Services는 지정된 키를 사용하고 AES 암호화되지 않은 키 또는 DRM 암호화를 사용하여 동적으로 사용자의 콘텐츠를 암호화합니다. 스트림을 해독하기 위해 플레이어는 키 배달 서비스에서 키를 요청합니다. 사용자에게 키를 얻을 수 있는 권한이 있는지 여부를 결정하기 위해 서비스는 키에 지정된 권한 부여 정책을 평가합니다.

> [!NOTE]
> 동적 암호화를 이용하려면 먼저 암호화된 콘텐츠를 배달할 계획인 스트리밍 끝점에 대한 주문형 스트리밍 단위를 하나 이상 가져와야 합니다. 자세한 내용은 [미디어 서비스 크기를 조정하는 방법](media-services-portal-manage-streaming-endpoints.md)을 참조하세요.
> 
> 

## <a name="storage-encryption"></a>저장소 암호화
AES 256비트 암호화를 사용하여 암호화되지 않은 콘텐츠를 로컬에서 암호화한 다음에 암호화되어 저장된 Azure Storage에 업로드하려면 저장소 암호화를 사용합니다. 저장소 암호화로 보호된 자산은 자동으로 암호 해제되어 인코딩되기 전에 암호화된 파일 시스템에 배치됩니다. 그리고 필요에 따라 새 출력 자산으로 다시 업로드되기 전에 다시 암호화됩니다. 저장소 암호화를 사용하는 기본적인 사례는 디스크에 저장된 상태일 때 강력한 암호화로 고품질의 입력 미디어 파일을 보호하려는 경우입니다.

저장소에서 암호화된 자산을 배달하려면 미디어 서비스에서 콘텐츠 배달 방법을 알 수 있도록 자산의 배달 정책을 구성해야 합니다. 자산을 스트리밍하기 전에 스트리밍 서버에서 저장소 암호화를 제거하고 지정된 배달 정책(예: AES, 일반 암호화 또는 암호화 없음)을 사용하여 콘텐츠를 스트리밍합니다.

## <a name="common-encryption-(cenc)"></a>CENC(일반 암호화)
일반 암호화는 PlayReady 또는/및 Widewine으로 콘텐츠를 암호화하는 경우에 사용합니다.

## <a name="using-cbcs-aapl-encryption"></a>cbcs-aapl 암호화 사용
cbcs-aapl은 FairPlay로 콘텐츠를 암호화하는 경우 사용합니다.

## <a name="envelope-encryption"></a>봉투 암호화
AES-128 암호화되지 않은 키로 콘텐츠를 보호하려는 경우 이 옵션을 사용합니다. 이보다 안전한 옵션을 원하는 경우 이 토픽에 나열된 DRM 중 하나를 선택합니다. 

## <a name="licenses-and-keys-delivery-service"></a>라이선스 및 키 배달 서비스
Media Services는 DRM(PlayReady, Widevine, FairPlay) 라이선스 및 AES 암호화되지 않은 키를 인증된 클라이언트에 배달하는 서비스를 제공합니다. [Azure Portal](media-services-portal-protect-content.md), REST API 또는 .NET용 Media Services SDK를 사용하여 라이선스 및 키에 대한 권한 부여 및 인증 정책을 구성할 수 있습니다.

## <a name="token-restriction"></a>토큰 제한
콘텐츠 키 권한 부여 정책에는 열기 또는 토큰 제한과 같은 하나 이상의 권한 부여 제한이 있을 수 있습니다. 토큰 제한 정책은 보안 토큰 서비스(STS)에 의해 발급된 토큰이 수반되어야 합니다. 미디어 서비스 지원 토큰에는 간단한 웹 토큰(SWT) 형식 및 JSON 웹 토큰(JWT) 형식의 토큰을 지원합니다. 미디어 서비스는 보안 토큰 서비스를 제공하지 않습니다. 사용자 지정 STS를 만들거나 Microsoft Azure ACS를 활용하여 토큰을 발급할 수 있습니다. 지정된 키로 서명된 토큰을 만들고 토큰 제한 구성에서 지정한 클레임을 발급하려면 반드시 STS를 구성해야 합니다. 토큰이 유효하고 해당 토큰의 클레임이 키(또는 라이선스)에 대해 구성된 클레임과 일치하는 경우 미디어 서비스 키 배달 서비스는 요청된 키(또는 라이선스)를 클라이언트에 반환합니다.

토큰 제한 정책을 구성하는 경우 기본 확인 키, 발급자 및 대상 매개 변수를 지정해야 합니다. 기본 확인 키는 토큰이 서명된 키를 포함하며 발급자는 토큰을 발행하는 보안 토큰 서비스입니다. 청중(범위) 라고도 함)은 토큰의 의도 또는 토큰이 접근을 인증하는 대상 리소스를 설명합니다. 미디어 서비스 키 배달 서비스는 이러한 토큰의 값이 템플릿 파일에 있는 값과 일치하는지 확인합니다.

## <a name="streaming-urls"></a>스트리밍 URL
자산이 하나 이상의 DRM으로 암호화되어 있는 경우 스트리밍 URL에서 암호화 태그를 사용해야 합니다(형식='m3u8-aapl', 암호화='xxx').

고려 사항은 다음과 같습니다.

* 1개 이하의 암호화 형식만 지정할 수 있습니다.
* 하나의 암호화가 자산에 적용되었다면 암호화 형식을 URL에 지정할 필요는 없습니다.
* 암호화 형식은 대/소문자를 구분하지 않습니다.
* 다음과 같은 암호화 형식을 지정할 수 있습니다.  
  * **cenc**: 일반 암호화(Playready 또는 Widevine)
  * **cbcs-aapl**: Fairplay
  * **cbc**: AES 봉투 암호화

## <a name="common-scenarios"></a>일반적인 시나리오
다음 토픽에서는 저장소의 콘텐츠를 보호하고, 암호화된 스트리밍 미디어를 동적으로 배달하며, AMS 키/라이선스 배달 서비스를 사용하는 방법을 보여 줍니다.

* [AES로 보호](media-services-protect-with-aes128.md) 
* [PlayReady 및/또는 Widevine으로 보호 ](media-services-protect-with-drm.md)
* [Apple FairPlay 및/또는 PlayReady로 보호되는 HLS 콘텐츠 스트림](media-services-protect-hls-with-fairplay.md)

### <a name="additional-scenarios"></a>추가 시나리오
* [암호기/스트리밍 서버와 함께 Azure PlayReady License 서비스를 통합하는 방법](http://mingfeiy.com/integrate-azure-playready-license-service-encryptorstreaming-server)
* [castLabs를 사용하여 Azure 미디어 서비스에 DRM 라이선스 제공](media-services-castlabs-integration.md)

## <a name="media-services-learning-paths"></a>미디어 서비스 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>관련 링크
[Azure 미디어 서비스를 사용하여 AES 동적 암호화로 PlayReady 발표](http://mingfeiy.com/playready)

[Azure 미디어 서비스 PlayReady 라이선스 배달 가격 설명](http://mingfeiy.com/playready-pricing-explained-in-azure-media-services)

[Azure 미디어 서비스에서 AES 암호화된 스트림에 대한 디버깅 방법](http://mingfeiy.com/debug-aes-encrypted-stream-azure-media-services)

[JWT 토큰 인증을 참조하세요.](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[Azure Active Directory와 Azure 미디어 서비스 OWIN MVC 기반 앱을 Azure Active Directory와 통합하고 JWT 클레임을 기반으로 하는 콘텐츠 키 배달을 제한합니다](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

[Azure ACS를 사용하여 토큰을 발급합니다](http://mingfeiy.com/acs-with-key-services).

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png



<!--HONumber=Oct16_HO2-->


