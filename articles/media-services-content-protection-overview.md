<properties 
	pageTitle="콘텐츠 보호 개요" 
	description="이 기사는 미디어 서비스 콘텐츠 보호에 대한 개요를 제공합니다." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/04/2015" 
	ms.author="juliako"/>

# 콘텐츠 보호 개요

Microsoft Azure 미디어 서비스를 사용하면 128비트 암호화 키를 사용하는 AES(Advanced Encryption Standard) 및 PlayReady DRM로 동적 암호화된 콘텐츠를 제공할 수 있습니다. 또한 미디어 서비스는 인증된 클라이언트에게 키 및 PlayReady 라이선스를 배달하는 서비스를 제공합니다. 

![content-protection][content-protection]

동적 암호화를 사용할 수 있으려면 먼저 암호화된 콘텐츠를 스트리밍하려는 스트리밍 끝점에서 하나 이상의 스트리밍 예약 단위를 가져와야 합니다.

## 개념

### 동적 암호화

Microsoft Azure 미디어 서비스를 사용하면 128비트 암호화 키를 사용하는 AES(Advanced Encryption Standard) 및 PlayReady DRM로 동적 암호화된 콘텐츠를 제공할 수 있습니다. 

현재는 다음과 같은 스트리밍 형식을 암호화할 수 있습니다. HLS, MPEG DASH 및 부드러운 스트리밍. HDS 스트리밍 형식 또는 점진적 다운로드는 암호화할 수 없습니다.

미디어 서비스에서 자산을 암호화하려는 경우 암호화 키(CommonEncryption 또는 EnvelopeEncryption)를 자산에 연결하고 해당 키에 대해 권한 부여 정책도 구성해야 합니다.

또한 자산의 배달 정책을 구성해야 합니다. 저장소에서 암호화된 자산을 스트리밍하려면 자산 배달 정책을 구성하여 배달 방법을 지정해야 합니다.  

플레이어가 스트림을 요청하면 미디어 서비스는 지정된 키를 사용하고 AES 또는 PlayReady 암호화를 사용하여 동적으로 사용자의 콘텐츠를 암호화합니다. 스트림을 해독하기 위해 플레이어는 키 배달 서비스에서 키를 요청합니다. 사용자에게 키를 얻을 수 있는 권한이 있는지 여부를 결정하기 위해 서비스는 키에 지정된 권한 부여 정책을 평가합니다.

### PlayReady DRM 라이선스 및 AES 암호화되지 않은 키 배달 서비스

미디어 서비스는 PlayReady 라이선스 및 AES 암호화되지 않은 키를 인증된 클라이언트에 배달하는 서비스를 제공합니다. Azure 관리 포털, REST API 또는 Media Services SDK for .NET을 사용하여 라이선스 및 키에 대한 권한 부여 및 인증 정책을 구성할 수 있습니다.

포털을 사용하는 경우 하나의 AES 정책(모든 AES 암호화 콘텐츠에 적용됨)과 하나의 PlayReady 정책(모든 PlayReady 암호화 콘텐츠에 적용됨)을 구성할 수 있습니다. 보다 세밀하게 구성을 제어하려면 Media Services SDK for .NET을 사용하세요.

### PlayReady 라이선스 템플릿

미디어 서비스는 PlayReady 라이선스를 배달하는 서비스를 제공합니다. 최종 사용자 플레이어(예: Silverlight)가 PlayReady로 보호된 콘텐츠를 재생하려고 하면 라이선스 배달 서비스로 요청을 보내 라이선스를 얻습니다. 라이선스 서비스에서 요청을 승인하면 클라이언트로 전송하여 지정된 콘텐츠의 암호를 해독하고 재생하는 데 사용할 수 있는 라이선스가 발급됩니다.

라이선스에는 사용자가 보호된 콘텐츠를 재생하려고 할 때 PlayReady DRM 런타임에서 적용하도록 하려는 권한 및 제한이 포함됩니다. 미디어 서비스는 PlayReady 라이선스를 구성할 수 있는 API를 제공합니다. 자세한 내용은 [미디어 서비스 PlayReady 라이선스 템플릿 개요](https://msdn.microsoft.com/library/azure/dn783459.aspx)를 참조하세요.

### 토큰 제한

콘텐츠 키 권한 부여 정책에는 열기, 토큰 제한 또는 IP 제한과 같은 하나 이상의 권한 부여 제한이 있을 수 있습니다. 토큰 제한 정책은 보안 토큰 서비스(STS)에 의해 발급된 토큰이 수반되어야 합니다. 미디어 서비스는 SWT(단순 웹 토큰) 형식 및 JWT(JSON 웹 토큰) 형식의 토큰을 지원합니다. 미디어 서비스는 보안 토큰 서비스를 제공하지 않습니다. 사용자 지정 STS를 만들거나 Microsoft Azure ACS를 활용하여 토큰을 발급할 수 있습니다. 지정된 키로 서명된 토큰을 만들고 토큰 제한 구성에서 지정한 클레임을 발급하려면 반드시 STS를 구성해야 합니다. 토큰이 유효하고 해당 토큰의 클레임이 키(또는 라이선스)에 대해 구성된 클레임과 일치하는 경우 미디어 서비스 키 배달 서비스는 요청된 키(또는 라이선스)를 클라이언트에 반환합니다.
토큰 제한 정책을 구성할 때 기본 확인 키, 발급자 및 대상 매개 변수를 지정해야 합니다. 기본 확인 키는 토큰이 서명된 키를 포함하며 발급자는 토큰을 발행하는 보안 토큰 서비스입니다. 청중(범위라고도 함)은 토큰의 의도 또는 토큰이 접근을 인증하는 대상 리소스를 설명합니다. 미디어 서비스 키 배달 서비스는 이러한 토큰의 값이 템플릿 파일에 있는 값과 일치하는지 확인합니다.

## 일반적인 시나리오

### 저장소에서 콘텐츠를 보호하고 암호화된 스트리밍 미디어를 동적으로 배달합니다.  

1. 자산에 고품질 mezzanine 파일을 수집합니다. 저장소 암호화 옵션을 자산에 적용합니다.
2. 스트리밍 끝점을 구성합니다.
1. 적응 비트 전송률 MP4 집합을 인코딩합니다. 저장소 암호화 옵션을 출력 자산에 적용합니다.
1. 재생하는 동안 동적으로 암호화하려는 경우 자산에 대한 암호화 콘텐츠 키를 만듭니다.
2. 콘텐츠 키 인증 정책을 구성합니다.
1. 자산 배달 정책(동적 패키징 및 동적 암호화에서 사용)을 구성합니다.
1. 주문형 로케이터를 만들어 자산을 게시합니다.
1. 게시된 콘텐츠를 스트리밍합니다.

### 사용자 고유의 암호화 및 스트리밍 서비스와 함께 미디어 서비스 키 및 라이선스 배달 서비스를 사용합니다.

자세한 내용은 [암호기/스트리밍 서버와 함께 Azure PlayReady 라이선스 서비스를 통합하는 방법](http://mingfeiy.com/integrate-azure-playready-license-service-encryptorstreaming-server)을 참조하세요.

## 일반적인 콘텐츠 암호화 작업

>[AZURE.NOTE]콘텐츠가 저장소 암호화된 경우 자산 배달 정책을 구성해야 합니다.

### 스트리밍 끝점 구성

스트리밍 끝점 개요 및 관리 방법에 대한 정보는 [미디어 서비스 계정에서 스트리밍 끝점을 관리하는 방법](media-services-manage-origins.md)을 참조하세요.

### 콘텐츠 키 만들기

**.NET** 또는 **REST API**를 사용하여 자산을 암호화하려는 콘텐츠 키를 만듭니다. 

[AZURE.INCLUDE [media-services-selector-create-contentkey](../includes/media-services-selector-create-contentkey.md)]

### 콘텐츠 키 인증 정책 구성하기 

**.NET** 또는 **REST API**를 사용하여 콘텐츠 보호 및 키 인증 정책을 구성합니다.

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../includes/media-services-selector-content-key-auth-policy.md)]

### 자산 배달 정책 구성

**.NET** 또는 **REST API**를 사용하여 자산 배달 정책을 구성합니다.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]


## 관련 링크

[Azure Media Services를 사용하여 AES 동적 암호화로 PlayReady 발표](http://mingfeiy.com/playready)

[Azure Media Service PlayReady 라이선스 배달 가격 설명](http://mingfeiy.com/playready-pricing-explained-in-azure-media-services)

[Azure 미디어 서비스에서 AES 암호화된 스트림에 대한 디버깅 방법](http://mingfeiy.com/debug-aes-encrypted-stream-azure-media-services)

[JWT 토큰 인증(영문)을 참조하세요](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).

[Azure Active Directory와 Azure 미디어 서비스 OWIN MVC 기반 앱을 Azure Active Directory와 통합하고 JWT 클레임을 기반으로 하는 콘텐츠 키 배달을 제한합니다](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

[토큰을 발급 하도록 Azure ACS를 사용](http://mingfeiy.com/acs-with-key-services).

[content-protection]: ./media/media-services-content-protection/media-services-content-protection.png

<!--HONumber=52-->