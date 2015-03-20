<properties 
	pageTitle="포털을 사용하여 콘텐츠 키 인증 정책을 구성" 
	description="콘텐츠 키에 대한 인증 정책을 구성하는 방법에 대해 알아봅니다." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="juliako"/>



#콘텐츠 키 인증 정책을 구성하기 
[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../includes/media-services-selector-content-key-auth-policy.md)]

이 문서는 [워크플로 주문형 미디어 서비스 비디오] 시리즈의(../media-services-video-on-demand-workflow) 및 [미디어 서비스 라이브 스트리밍 워크플로](../media-services-live-streaming-workflow) 일부입니다. 


##개요

Microsoft Azure 미디어 서비스를 사용하면 128 비트 암호화 키를 사용하는 AES(Advanced Encryption Standard) 및 PlayReady DRM로 암호화된 콘텐츠를 제공할 수 있습니다. 또한 미디어 서비스는 **키\라이선스 배달 서비스**를 제공하여 클라이언트가 암호화된 콘텐츠를 재생할 수 있는 키 또는 라이선스를 받을 수 있습니다. 

이 항목에서는 **Azure 관리 포털**을 사용하여 콘텐츠 키 인증 정책을 구성하는 방법을 보여 줍니다. 키는 나중에 동적으로 콘텐츠를 암호화하는데 사용할 수 있습니다. 현재 암호화할 수 있는 스트리밍 형식: HLS, MPEG DASH 및 부드러운 스트리밍. HDS 스트리밍 형식 또는 점진적 다운로드는 암호화할 수 없습니다.
 
플레이어가 동적으로 암호화되도록 설정된 스트림을 요청하는 경우, 미디어 서비스는 구성된 키를 사용하고 AES 또는 PlayReady 암호화를 사용하여 동적으로 사용자의 콘텐츠를 암호화합니다. 스트림을 해독하기 위해 플레이어는 키 배달 서비스에서 키를 요청합니다. 사용자에게 키를 얻을 수 있는 권한이 있는지 여부를 결정하기 위해 서비스는 키에 지정된 권한 부여 정책을 평가합니다.


여러 콘텐츠 키를 가지거나 미디어 서비스 키 배달 서비스가 아닌 **키\라이선스 배달 서비스** URL을 지정하려면 미디어 서비스 키 배달 서비스 미디어 서비스 .NET SDK 또는 REST API를 사용하십시오.

[미디어 서비스 .NET SDK를 사용하여 콘텐츠 키 권한 부여 정책 구성](../media-services-dotnet-configure-content-key-auth-policy/)

[미디어 서비스 REST API를 사용하여 콘텐츠 키 권한 부여 정책 구성](../media-services-rest-configure-content-key-auth-policy/)

###다음과 같은 몇 가지 고려 사항이 적용됩니다.

- 동적 패키징 및 동적 암호화를 사용할 수 있으려면 하나 이상의 배율 단위(스트리밍 단위)가 있어야 합니다. 자세한 내용은 [미디어 서비스를 확장하는 방법]을 참조하세요(../media-services-manage-origins#scale_streaming_endpoints). 
- 사용자의 자산은 적응 비트 전송률 MP4 또는 적응 비트 전송률 부드러운 스트리밍 파일 집합을 포힘해야 합니다. 자세한 내용은 [자산 인코딩]을 참조하세요(../media-services-encode-asset/).  
- 키 배달 서비스는 ContentKeyAuthorizationPolicy 및 관련 개체(정책 옵션 및 제한 사항)를 15분 동안 캐시합니다.  ContentKeyAuthorizationPolicy를 만들고 "Token" 제한을 사용하도록 지정 및 테스트하고 정책의 제한을 "개방"으로 업데이트 하는 경우, 해당 정책이 "개방" 버전으로 전환하는 데 약 15분이 소요됩니다.


##키 인증 정책을 구성하는 방법

키 인증 정책을 구성 하려면 **콘텐츠 보호** 페이지를 선택하십시오.
	
미디어 서비스는 키를 요청 하는 사용자를 인증 하는 여러 방법을 지원합니다. 콘텐츠 키 인증 정책은 **개방**, **토큰** 또는 **IP** 권한 부여 제한을 가질 수 있습니다(REST 또는 .NET SDK로 **IP** 구성 가능). 

###열기 제한

**개방** 제한은 시스템이 키를 요청하는 사람에게 키를 제공하는 것을 의미합니다. 이 제한은 테스트 목적으로 유용할 수 있습니다.

![OpenPolicy][open_policy]

###토큰 제한

토큰 제한 정책을 선택하려면 **TOKEN** 단추를 누릅니다.

**토큰** 제한 정책은 **보안 토큰 서비스**(STS)에 의해 발급된 토큰이 수반되어야 합니다. 미디어 서비스 지원 토큰에는 **간단한 웹 토큰**([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) 형식 및 **JSON 웹 토큰 **(JWT) 형식의 토큰을 지원합니다. 자세한 내용은 [JWT 토큰 인증](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)(영문)을 참조하세요.

미디어 서비스는 **보안 토큰 서비스**를 제공하지 않습니다. 사용자 지정 STS를 만들거나 Microsoft Azure ACS를 활용하여 토큰을 발급할 수 있습니다. 지정된 키로 서명된 토큰을 만들고 토큰 제한 구성에서 지정한 클레임을 발급하려면 반드시 STS를 구성해야 합니다. 토큰이 유효하고 해당 토큰의 클레임이 콘텐츠 키에 대해 구성된 클레임과 일치하는 경우 미디어 서비스 키 배달 서비스는 암호화 키를 클라이언트에게 반환합니다. 자세한 내용은 [Azure ACS를 사용하여 토큰 발급](http://mingfeiy.com/acs-with-key-services)을 참조하세요.

**토큰** 제한 정책을 구성 하는 경우 제한된 **확인 키**, **발급자** 및 **청중**에 대한 값을 설정해야 합니다. 기본 확인 키는 토큰이 서명된 키를 포함하며 발급자는 토큰을 발행하는 보안 토큰 서비스입니다. 청중(범위 라고도 함)은 토큰의 의도 또는 토큰이 접근을 인증하는 대상 리소스를 설명합니다. 미디어 서비스 키 배달 서비스는 이러한 토큰의 값이 템플릿 파일에 있는 값과 일치하는지 확인합니다.  

###PlayReady

**PlayReady**로 콘텐츠를 보호 하는 경우, 인증 정책에서 지정해야하는 것 중 하나는 PlayReady 라이선스 템플릿을 정의하는 XML 문자열입니다. 기본적으로 설정되는 정책:
		
	<PlayReadyLicenseResponseTemplate xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1">
	  <LicenseTemplates>
	    <PlayReadyLicenseTemplate><AllowTestDevices>true</AllowTestDevices>
	      <ContentKey i:type="ContentEncryptionKeyFromHeader" />
	      <LicenseType>Nonpersistent</LicenseType>
	      <PlayRight>
	        <AllowPassingVideoContentToUnknownOutput>Allowed</AllowPassingVideoContentToUnknownOutput>
	      </PlayRight>
	    </PlayReadyLicenseTemplate>
	  </LicenseTemplates>
	</PlayReadyLicenseResponseTemplate>

**정책 xml 가져오기** 단추를 클릭하고 [여기](https://msdn.microsoft.com/library/azure/dn783459.aspx)에 정의된 XML 스키마를 준수하는 다른 XML을 제공할 수 있습니다.

##다음 단계
이제 사용자가 콘텐츠 키 인증 정책을 구성을 완료하였으므로, [다음으로 이동하십시오. Azure 관리 포털을 사용하여 암호화를 사용하도록 설정](../media-services-manage-content#encrypt/) 항목으로 이동합니다.


[open_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png


<!--HONumber=47-->
