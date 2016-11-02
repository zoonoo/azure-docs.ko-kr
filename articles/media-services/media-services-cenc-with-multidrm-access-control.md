<properties 
    pageTitle="다중 DRM 및 Access Control이 포함된 CENC: Azure 및 Azure Media Services에서 참조 디자인 및 구현 | Microsoft Azure" 
    description="Microsoft® 부드러운 스트리밍 클라이언트 이식 키트 라이선스를 얻는 방법에 대해 알아보세요." 
    services="media-services" 
    documentationCenter="" 
    authors="willzhan"  
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"  
    ms.author="willzhan;kilroyh;yanmf;juliako"/>


#<a name="cenc-with-multi-drm-and-access-control:-a-reference-design-and-implementation-on-azure-and-azure-media-services"></a>다중 DRM 및 액세스 제어가 포함된 CENC: Azure 및 Azure 미디어 서비스에서 참조 디자인 및 구현

##<a name="key-words"></a>키워드
 
Azure Active Directory, Azure 미디어 서비스, Azure 미디어 플레이어, 동적 암호화, 라이선스 배달, PlayReady, Widevine, FairPlay, CENC(일반적인 암호화), 다중 DRM, Axinom, DASH, EME, MSE, JWT(JSON 웹 토큰), 클레임, 최신 브라우저, 키 롤오버, 대칭 키, 비대칭 키, OpenID Connect, X509 인증서. 

##<a name="in-this-article"></a>이 문서에서는 다음을 수행합니다.

이 문서에서 다루는 토픽은 다음과 같습니다.

- [소개](media-services-cenc-with-multidrm-access-control.md#introduction)
    - [이 문서의 개요](media-services-cenc-with-multidrm-access-control.md#overview-of-this-article)
- [참조 디자인](media-services-cenc-with-multidrm-access-control.md#a-reference-design)
- [디자인을 구현 기술에 매핑](media-services-cenc-with-multidrm-access-control.md#mapping-design-to-technology-for-implementation)
- [구현](media-services-cenc-with-multidrm-access-control.md#implementation)
    - [구현 절차](media-services-cenc-with-multidrm-access-control.md#implementation-procedures)
    - [구현에 대해 몇 가지 알려진 문제](media-services-cenc-with-multidrm-access-control.md#some-gotchas-in-implementation)
- [구현에 대한 추가 토픽](media-services-cenc-with-multidrm-access-control.md#additional-topics-for-implementation)
    - [HTTP 또는 HTTPS](media-services-cenc-with-multidrm-access-control.md#http-or-https)
    - [Azure Active Directory 서명 키 롤오버](media-services-cenc-with-multidrm-access-control.md#azure-active-directory-signing-key-rollover)
    - [액세스 토큰 위치](media-services-cenc-with-multidrm-access-control.md#where-is-the-access-token)
    - [라이브 스트리밍의 경우는 어떨까요?](media-services-cenc-with-multidrm-access-control.md#what-about-live-streaming)
    - [Azure 미디어 서비스 외부에서 라이선스 서버는 어떨까요?](media-services-cenc-with-multidrm-access-control.md#what-about-license-servers-outside-of-azure-media-services)
    - [사용자 지정 STS를 사용하려면 어떻게 하나요?](media-services-cenc-with-multidrm-access-control.md#what-if-i-want-to-use-a-custom-sts)
- [완료된 시스템 및 테스트](media-services-cenc-with-multidrm-access-control.md#the-completed-system-and-test)
    - [사용자 로그인](media-services-cenc-with-multidrm-access-control.md#user-login)
    - [PlayReady에 암호화된 미디어 확장 사용](media-services-cenc-with-multidrm-access-control.md#using-encrypted-media-extensipons-for-playready)
    - [Widevine에 EME 사용](media-services-cenc-with-multidrm-access-control.md#using-eme-for-widevine)
    - [자격이 없는 사용자](media-services-cenc-with-multidrm-access-control.md#not-entitled-users)
    - [사용자 지정 보안 토큰 서비스 실행](media-services-cenc-with-multidrm-access-control.md#running-custom-secure-token-service)
- [요약](media-services-cenc-with-multidrm-access-control.md#summary)

##<a name="introduction"></a>소개

잘 알다시피 OTT 또는 온라인 스트리밍 솔루션을 위한 DRM 하위 시스템을 디자인하고 구축하는 것은 복잡한 작업입니다. 일반적으로 운영자/온라인 비디오 공급자는 이러한 부분을 전문화된 DRM 서비스 공급자에게 아웃소싱합니다. 이 문서의 목표는 OTT 또는 온라인 스트리밍 솔루션에서 종단 간 DRM 하위 시스템의 참조 디자인 및 구현을 제공하는 것입니다.

이 문서는 OTT 또는 온라인 스트리밍/멀티 스크린 솔루션의 DRM 하위 시스템에서 작업 중인 엔지니어 또는 DRM 하위 시스템에 관심이 있는 모든 독자를 대상으로 합니다. 독자는 PlayReady, Widevine, FairPlay 또는 Adobe Access 등 한 가지 이상의 DRM 기술에 대해 잘 알고 있다고 가정합니다.

DRM에는 포함 다중 DRM의 CENC(일반적인 암호화)도 포함됩니다. 온라인 스트리밍 및 OTT 업계에서 주요 추세는 다양한 클라이언트 플랫폼에서 다중 기본 DRM의 CENC를 사용하는 것이며 이것은 다양한 클라이언트 플랫폼에서 단일 DRM과 해당 클라이언트 SDK를 사용하는 이전 추세로부터 변화된 것입니다. 다중 기본 DRM의 CENC를 사용할 때 PlayReady와 Widevine 모두 [일반적인 암호화(ISO/IEC 23001-7 CENC)](http://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) 사양에 따라 암호화됩니다.

다중 DRM의 CENC 이점은 다음과 같습니다.

1. 기본 DRM을 포함하는 다양한 플랫폼을 대상으로 단일 암호화 처리가 사용되므로 암호화 비용이 절감됩니다.
1. 암호화된 자산의 복사본이 하나만 필요하므로 암호화된 자산의 관리 비용이 절감됩니다.
1. 기본 플랫폼에서 기본 DRM 클라이언트는 일반적으로 무료로 제공되므로 DRM 클라이언트 라이선스 비용이 없습니다.

Microsoft는 몇몇 주요 기업들과 더불어 DASH 및 CENC의 적극적인 프로모터로 활동해왔습니다. Microsoft Azure 미디어 서비스에서 DASH 및CENC 지원을 제공하고 있습니다. Mingfei의 블로그의 [Azure Media Services에서 Google Widevine 라이선스 전달 서비스 발표](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/) 및 [다중 DRM 스트림을 배달하기 위해 Azure Media Services에서 Google Widevine 패키징 추가](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)에서 최근 소식을 참조하세요.  

### <a name="overview-of-this-article"></a>이 문서의 개요

이 문서의 목표는 다음과 같습니다.

1. 다중 DRM의 CENC를 사용하는 DRM 하위 시스템에 대한 참조 디자인을 제공합니다.
1. Microsoft Azure/Azure 미디어 서비스 플랫폼에 대한 참조 구현을 제공합니다.
1. 몇 가지 디자인 및 구현 토픽에 대해 설명합니다.

이 문서에서 "다중 DRM"은 다음을 나타냅니다.

1. Microsoft PlayReady
1. Google Widevine
1. Apple FairPlay(Azure 미디어 서비스에서 아직 지원되지 않음)

다음 표에서는 각 DRM에서 기본 플랫폼/기본 앱 및 지원되는 브라우저를 요약합니다.

**클라이언트 플랫폼**|**네이티브 DRM 지원**|**브라우저/앱**|**스트리밍 형식**
----|------|----|----
**스마트 TV, 연산자 STB, OTT STB**|주로 PlayReady 및/또는 Widevine 및/또는 기타|Linux, Opera, WebKit, 기타|다양한 형식
**Windows 10 장치(Windows PC, Windows 태블릿, Windows Phone, Xbox)**|PlayReady|MS Edge/IE11/EME<br/><br/><br/>UWP|DASH(HLS의 경우 PlayReady는 지원되지 않음)<br/><br/>DASH, 부드러운 스트리밍(HLS의 경우 PlayReady는 지원되지 않음) 
**Android 장치(전화, 태블릿, TV)**|Widevine|크롬/EME|DASH
**iOS(iPhone, iPad), OS X 클라이언트 및 Apple TV**|FairPlay|Safari 8+/EME|HLS
**플러그 인: Adobe Primetime**|Primetime 액세스|브라우저 플러그 인|HLS, HDS

각 DRM에 대한 배포의 현재 상태를 고려하면 서비스는 일반적으로 가장 좋은 방법으로 모든 유형의 끝점을 해결하도록 2 또는 3DRM을 구현해야 합니다.

다양한 클라이언트에서 사용자 환경의 특정 수준에 도달하는 데 서비스 논리의 복잡성과 클라이언트 쪽의 복잡성 사이의 장단점이 있습니다.

선택을 하려면 이러한 사실에 유의하세요.

- PlayReady는 모든 Windows 장치와 일부 Android 장치에서 고유하게 구현되며 거의 모든 플랫폼에서 소프트웨어 SDK를 통해 사용할 수 있습니다.
- Widevine은 모든 Android 장치, Chrome 및 일부 다른 장치에서 고유하게 구현됩니다.
- FairPlay는 iOS 및 Mac OS 클라이언트 또는 iTunes를 통해 사용할 수 있습니다.

따라서 일반적인 다중 DRM은 다음과 같습니다.

- 옵션 1: PlayReady 및 Widevine
- 옵션 2: PlayReady, Widevine 및 FairPlay


## <a name="a-reference-design"></a>참조 디자인

이 섹션에서는 구현에 사용되는 기술과 관계 없이 적용할 수 있는 참조 디자인을 제공합니다.

DRM 하위 시스템은 다음 구성 요소를 포함할 수 있습니다.

1. 키 관리
1. DRM 패키징
1. DRM 라이선스 배달
1. 자격 확인
1. 인증/권한 부여
1. 플레이어
1. 원본/CDN

다음 다이어그램에서는 DRM 하위 시스템에서 구성 요소 간의 상호 작용을 에 개략적으로 보여줍니다.

![CENC를 사용하는 DRM 하위 시스템](./media/media-services-cenc-with-multidrm-access-control/media-services-generic-drm-subsystem-with-cenc.png)
 
이 디자인에는 세 개의 기본 "계층"이 있습니다.

1. 외부적으로 노출되지 않는 백오피스 계층(검은색)
1. 공용으로 연결되는 모든 끝점을 포함하는 "DMZ" 계층(파란색)
1. 공용 인터넷을 통과하는 트래픽과 CDN 및 플레이어를 포함하는 공용 인터넷 계층(옅은 파란색)
 
정적 또는 동적 암호화에 관계 없이 DRM 보호를 제어하기 위한 콘텐츠 관리 도구가 있어야 합니다. DRM 암호화에 대한 입력은 다음을 포함해야 합니다.

1. MBR 비디오 콘텐츠
1. 콘텐츠 키
1. 라이선스 획득 URL

재생 시간 동안 개략적인 흐름은 다음과 같습니다.

1. 사용자가 인증됩니다.
1. 사용자에 대한 권한 부여 토큰이 만들어집니다.
1. DRM으로 보호된 콘텐츠(매니페스트)가 플레이어로 다운로드됩니다.
1. 플레이어는 라이선스 서버에 키 ID 및 권한 부여 토큰과 함께 라이선스 획득 요청을 제출합니다.

다음 토픽으로 이동하기 전에 키 관리의 디자인에 대한 몇 가지 단어에 대해 알아봅니다.

**콘텐츠 키 대 자산(ContentKey–to-Asset)**|**시나리오**
------|---------------------------
일대일(1–to-1)|가장 간단한 경우입니다. 가장 미세한 제어를 제공합니다. 하지만 일반적으로 이렇게 가장 높은 라이선스 배달 비용이 발생합니다. 보호된 각 자산에 대해 최소 하나의 라이선스 요청이 필요합니다.
일대다(1–to-Many)|여러 자산에 대해 동일한 콘텐츠 키를 사용할 수 있습니다. 예를 들어 장르 또는 장르의 하위 집합(또는 영화 유전자)과 같은 논리적 그룹의 모든 자산에 대해 단일 콘텐츠 키를 사용할 수 있습니다.
다대일(Many–to-1)|각 자산에 대해 여러 콘텐츠 키가 필요합니다. <br/><br/>예를 들어 MPEG DASH에 대해 다중 DRM의 동적 CENC 보호를 적용하고 HLS에 대해 AES-128 암호화를 적용해야 하는 경우 각각 고유한 ContentKeyType을 포함하는 두 가지 별도의 콘텐츠 키가 필요합니다. (동적 CENC 보호에 사용된 콘텐츠 키의 경우, ContentKeyType.CommonEncryption을 사용해야 하는 반면 동적 AES-128 암호화에 사용되는 콘텐츠 키의 경우, ContentKeyType.EnvelopeEncryption을 사용해야 합니다.)<br/><br/>또 다른 예로 DASH 콘텐츠의 CENC 보호에서는 이론적으로 비디오 스트림을 보호하는 데 하나의 콘텐츠 키를 사용할 수 있으며 오디오 스트림을 보호하는 데는 다른 콘텐츠 키를 사용할 수 있습니다. 
다대다(Many–to–Many)|위의 두 가지 시나리오를 조합한 것입니다. 동일한 자산 "그룹"의 여러 자산 각각에 대해 한 집합의 콘텐츠 키를 사용합니다.


또 다른 중요한 고려 사항은 영구 및 비영구 라이선스의 사용에 관한 것입니다.

이러한 고려 사항이 중요한 이유는 무엇인가요? 

라이선스 배달에 공용 클라우드를 사용하는 경우 라이선스 배달 비용에 직접적인 영향이 있습니다. 이를 설명하기 위해 다음 두 가지 다른 디자인 사례를 살펴보겠습니다.



1. 월간 구독: 영구 라이선스와 일대다(1–to-Many) 콘텐츠 키 대 자산 매핑을 사용합니다. 예: 예를 들어 모든 어린이용 영화의 경우 암호화에 단일 콘텐츠 키를 사용합니다. 이 경우 다음과 같습니다. 

    모든 어린이용 영화에 요청된 전체 라이선스 수/장치 = 1

1. 월간 구독: 비영구 라이선스와 콘텐츠 키 및 자산 간에 일대일(1–to-1) 매핑을 사용합니다. 이 경우 다음과 같습니다.

    모든 어린이용 영화에 요청된 전체 라이선스 수/장치 = [본 영화 수] x [세션 수]

쉽게 확인할 수 있듯이 두 가지 서로 다른 디자인으로 인해 라이선스 요청 패턴이 매우 달라지므로 라이선스 배달 서비스가 Azure 미디어 서비스와 같은 공용 클라우드에서 제공되는 경우 라이선스 배달 비용이 달라집니다.

## <a name="mapping-design-to-technology-for-implementation"></a>디자인을 구현 기술에 매핑

다음으로, 각 구성 요소에 사용할 기술을 지정하여 일반적인 디자인을 Microsoft Azure/Azure 미디어 서비스 플랫폼의 기술에 매핑합니다.

다음 표에서 이러한 매핑을 보여줍니다.

**구성 요소**|**Technology**
------|-------
**플레이어**|[Azure 미디어 플레이어](https://azure.microsoft.com/services/media-services/media-player/)
**IDP(ID 공급자)**|Azure Active Directory
**STS(보안 토큰 서비스)**|Azure Active Directory
**DRM 보호 워크플로**|Azure 미디어 서비스 동적 보호
**DRM 라이선스 배달**|1. Azure Media Services 라이선스 배달(PlayReady, Widevine, FairPlay) 또는 <br/>2. Axinom License Server, <br/>3. 사용자 지정 PlayReady 라이선스 서버
**원본**|Azure 미디어 서비스 스트리밍 끝점
**키 관리**|참조 구현에는 필요하지 않음
**콘텐츠 관리**|C# 콘솔 응용 프로그램

즉, IDP(ID 공급자) 및 STS(보안 토큰 서비스) 모두 Azure AD입니다. 플레이어로는 [Azure 미디어 플레이어 API](http://amp.azure.net/libs/amp/latest/docs/)를 사용합니다. Azure 미디어 서비스와 Azure 미디어 플레이어는 모두 다중 DRM의 DASH 및 CENC를 지원합니다.

다음 다이어그램에서는 위의 기술 매핑을 사용하는 전체적인 구조 및 흐름을 보여줍니다.

![AMS에서 CENC](./media/media-services-cenc-with-multidrm-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

동적 CENC 암호화를 설정하기 위해 콘텐츠 관리 도구는 다음 입력을 사용합니다.

1. 개방 콘텐츠
1. 키 생성/관리에서 콘텐츠 키
1. 라이선스 획득 URL
1. Azure AD의 정보 목록

콘텐츠 관리 도구의 출력은 다음과 같습니다.

1. ContentKeyAuthorizationPolicy - 라이선스 배달에서 JWT 토큰 및 DRM 라이선스 사양을 확인하는 방법에 대한 사양 포함
1. AssetDeliveryPolicy - 스트리밍 형식, DRM 보호 및 라이선스 획득 URL에 대한 사양 포함

런타임 중에 흐름은 다음과 같습니다.

1. 사용자 인증 시 JWT 토큰이 생성됩니다.
1. JWT 토큰에 포함된 클레임 중 하나는 "EntitledUserGroup"의 그룹 개체 ID를 포함하는 "groups" 클레임입니다. 이 클레임은 "자격 확인"을 전달하는 데 사용됩니다.
1. 플레이어는 CENC로 보호된 콘텐츠의 클라이언트 매니페스트를 다운로드하고 다음을 "확인"합니다.
    1. 키 ID 
    1. 콘텐츠가 CENC로 보호되는지
    1. 라이선스 획득 URL

1. 플레이어는 브라우저/DRM 지원에 따라 라이선스 획득 요청을 합니다. 라이선스 획득 요청에서 키 ID 및 JWT 토큰도 제출됩니다. 라이선스 배달 서비스는 필요한 라이선스를 발급하기 전에 포함된 JWT 토큰 및 클레임을 확인합니다.

##<a name="implementation"></a>구현

###<a name="implementation-procedures"></a>구현 절차

구현에는 다음 단계가 포함됩니다.

1. 테스트 자산 준비: Azure 미디어 서비스에서 테스트 비디오를 다중 비트 전송률 조각화된 MP4로 인코딩/패키지합니다. 이 자산은 DRM으로 보호되지 않습니다. DRM 보호는 나중에 동적 보호로 수행됩니다.
1. 키 ID 및 콘텐츠 키(필요한 경우 키 시드에서)를 만듭니다. 여기서는 여러 테스트 자산에 대해 단일 집합의 키 ID 및 콘텐츠 키만 다루므로 키 관리 시스템이 필요하지 않습니다.
1. AMS API를 사용하여 테스트 자산에 대한 다중 DRM 라이선스 배달 서비스를 구성합니다. 회사 또는 회사의 공급업체에서 Azure 미디어 서비스의 라이선스 서비스 대신 사용자 지정 라이선스 서버를 사용하는 경우 이 단계를 건너뛰고 라이선스 배달 구성 단계에 라이선스 획득 URL을 지정할 수 있습니다. AMS API는 권한 부여 정책 제한, 다양한 DRM 라이선스 서비스에 대한 라이선스 응답 템플릿 등과 같은 일부 세부적인 구성을 지정하는 데 필요합니다. 현재는 Azure 포털에서 이 구성에 필요한 UI를 제공하지 않습니다. Julia Kornich의 문서 [PlayReady 및/또는 Widevine 동적 일반 암호화 사용](media-services-protect-with-drm.md)에서 API 수준 정보 및 샘플 코드를 찾을 수 있습니다. 
1. AMS API를 사용하여 테스트 자산에 대한 자산 배달 정책을 구성합니다. Julia Kornich의 문서 [PlayReady 및/또는 Widevine 동적 일반 암호화 사용](media-services-protect-with-drm.md)에서 API 수준 정보 및 샘플 코드를 찾을 수 있습니다.
1. Azure에서 Azure Active Directory 테넌트를 만들고 구성합니다.
1. Azure Active Directory 테넌트에서 몇 가지 사용자 계정 및 그룹 만들기: "EntitledUser" 그룹 이상을 만들고 사용자를 이 그룹에 추가합니다. 이 그룹의 사용자는 라이선스 획득 시 자격 확인을 전달하고 이 그룹에 없는 사용자는 인증 확인 전달에 실패하며 라이선스를 획득할 수 없게 됩니다. 이 "EntitledUser" 그룹의 멤버가 되려면 Azure AD에서 발급한 JWT 토큰의 필수 "groups" 클레임이 필요합니다. 이 클레임 요구 사항은 다중 DRM 라이선스 배달 서비스 단계를 구성하는 데 지정해야 합니다.
1. 비디오 플레이어를 호스팅할 ASP.NET MVC 앱을 만듭니다. 이 ASP.NET 앱은 Azure Active Directory 테넌트에 대한 사용자 인증으로 보호됩니다. 적절한 클레임이 사용자 인증 후 얻은 액세스 토큰에 포함됩니다. 이 단계에는 OpenID Connect API가 권장됩니다. 다음 NuGet 패키지를 설치해야 합니다.
    - Install-Package Microsoft.Azure.ActiveDirectory.GraphClient
    - Install-Package Microsoft.Owin.Security.OpenIdConnect
    - Install-Package Microsoft.Owin.Security.Cookies
    - Install-Package Microsoft.Owin.Host.SystemWeb
    - Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
1. [Azure 미디어 플레이어 API](http://amp.azure.net/libs/amp/latest/docs/)를 사용하여 플레이어를 만듭니다. [Azure 미디어 플레이어의 ProtectionInfo API](http://amp.azure.net/libs/amp/latest/docs/) 를 통해 다양한 DRM 플랫폼에 사용할 DRM 기술을 지정할 수 있습니다.
1. 테스트 매트릭스:

**DRM**|**브라우저**|**자격이 있는 사용자에 대한 결과**|**자격이 없는 사용자에 대한 결과**
---|---|-----|---------
**PlayReady**|Windows 10의 MS Edge 또는 IE11|합격|불합격
**Widevine**|Windows 10의 Chrome|합격|불합격
**FairPlay** |TBD||

Azure 미디어 서비스 팀의 George Trifonov는 ASP.NET MVC 플레이어 앱에 대한 Azure Active Directory를 설정하는 자세한 단계를 제공하는 블로그, [Azure Active Directory와 Azure 미디어 서비스 OWIN MVC 기반 앱을 Azure Active Directory와 통합 및 JWT 클레임을 기반으로 하는 콘텐츠 키 배달 제한](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)을 작성했습니다.

또한 [Azure 미디어 서비스 및 동적 암호화의 JWT 토큰 인증](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)에 대한 블로그도 작성했습니다. 그리고 그의 [Azure 미디어 서비스 키 배달을 사용한 Azure AD 통합 샘플](https://github.com/AzureMediaServicesSamples/Key-delivery-with-AAD-integration/)을 여기에서 다룹니다.

Azure Active Directory에 대한 자세한 내용은 다음을 참조하세요.

- [Azure Active Directory 개발자 가이드](../active-directory/active-directory-developers-guide.md)에서 개발자 정보를 찾을 수 있습니다.
- [Azure AD 디렉터리 관리](../active-directory/active-directory-administer.md)에서 관리자 정보를 찾을 수 있습니다.

### <a name="some-gotchas-in-implementation"></a>구현에 대해 몇 가지 알려진 문제

구현에 대해 몇 가지 "알려진 문제"가 있습니다. 다행히 다음 "알려진 문제" 목록을 통해 문제가 발생하는 문제를 해결할 수 있습니다.

1. **발급자** URL은 **"/"**로 끝나야 합니다.  

    **대상**은 플레이어 응용 프로그램 클라이언트 ID여야 하며 발급자 URL 끝에 **"/"**를 추가해야 합니다.

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" /> 

    [JWT 디코더](http://jwt.calebb.net/)에서 JWT 토큰에 다음과 같이 **aud** 및 **iss**가 나타납니다.
    
    ![첫 번째 알려진 문제](./media/media-services-cenc-with-multidrm-access-control/media-services-1st-gotcha.png)


2. AAD의 응용 프로그램에 권한을 추가합니다(응용 프로그램의 구성 탭). 각 응용 프로그램(로컬 및 배포된 버전)에 필요합니다.

    ![두 번째 알려진 문제](./media/media-services-cenc-with-multidrm-access-control/media-services-perms-to-other-apps.png)


3. 동적 CENC 보호 설정에 올바른 발급자를 사용합니다.

        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>
    
    다음은 작동하지 않습니다.
    
        <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />
    
    GUID는 AAD 테넌트 ID입니다. Azure 포털의 끝점 팝업 창에서 GUID를 찾을 수 있습니다.

4. 그룹 멤버 자격 클레임 권한을 부여합니다. AAD 응용 프로그램 매니페스트 파일에서 다음이 있는지 확인합니다.

    "groupMembershipClaims": "All", (기본값은 null)

5. 제한 사항 요구 사항을 만들 때 적절한 TokenType을 설정합니다.

        objTokenRestrictionTemplate.TokenType = TokenType.JWT;

    SWT(ACS) 외에도 JWT(AAD)의 지원이 추가되었으므로 기본 TokenType은 TokenType.JWT입니다. SWT/ACS를 사용하는 경우 TokenType.SWT로 설정해야 합니다.

## <a name="additional-topics-for-implementation"></a>구현에 대한 추가 토픽
다음으로 디자인 및 구현에 대한 몇 가지 추가 토픽에 대해서 설명합니다.

###<a name="http-or-https?"></a>HTTP 또는 HTTPS

구축한 ASP.NET MVC 플레이어 응용 프로그램은 다음을 지원해야 합니다.

1. Azure AD를 통한 사용자 인증이 HTTPS를 사용해야 함
1. 클라이언트 및 Azure AD 간 JWT 토큰 교환이 HTTPS를 사용해야 함
1. Azure 미디어 서비스에서 라이선스 배달이 제공된 경우 클라이언트에 의한 DRM 라이선스 획득 시 HTTPS를 사용해야 함. 물론 PlayReady 제품군은 라이선스 배달에 대한 HTTPS를 위임하지 않습니다. PlayReady 라이선스 서버가 Azure 미디어 서비스 외부에 있는 경우 HTTP 또는 HTTPS를 사용할 수 있습니다.

따라서 ASP.NET 플레이어 응용 프로그램은 HTTPS를 사용하는 것이 가장 좋습니다. 즉, Azure 미디어 플레이어는 HTTPS를 사용하는 페이지에 있게 됩니다. 그러나 스트리밍의 경우 HTTP를 선호하므로 콘텐츠 혼합 문제를 고려해야 합니다.

1. 브라우저에서는 혼합 콘텐츠를 허용하지 않습니다. 하지만 Silverlight과 같은 플러그인, 부드러운 스트리밍을 위한 OSMF 플러그인 및 DASH는 허용합니다. 혼합 콘텐츠는 보안 문제가 발생할 수 있습니다. 악성 JS를 주입할 수 있는 위협으로 인해 고객 데이터가 위험할 수 있습니다.  브라우저는 기본적으로 이를 차단하며 지금까지는 이 문제를 해결하는 유일한 방법이 서버(원본) 쪽에서 https 또는 http에 관계 없이 모든 도메인을 허용하는 것입니다. 하지만 좋은 방법은 아닙니다.
1. 둘 다 HTTP를 사용하거나 둘 다 HTTPS를 사용해서 혼합 콘텐츠를 피해야 합니다. 혼합된 콘텐츠를 재생할 때 silverlightSS 기술은 혼합된 콘텐츠 경고를 지워야 합니다. flashSS 기술은 혼합된 콘텐츠 경고 없이 혼합된 콘텐츠를 처리합니다.
1. 스트리밍 끝점이 2014년 8월 전에 만들어진 경우 HTTPS를 지원하지 않습니다. 이 경우 HTTPS에 대한 새 스트리밍 끝점을 만들어 사용하세요.

참조 구현에서 DRM으로 보호된 콘텐츠에서는 응용 프로그램 및 스트리밍이 모두 HTTTPS를 사용합니다. 개방 콘텐츠의 경우 플레이어에서 인증 또는 라이선스가 필요하지 않으므로 HTTP 또는 HTTPS 중에 원하는 것을 사용할 수 있습니다.

### <a name="azure-active-directory-signing-key-rollover"></a>Azure Active Directory 서명 키 롤오버

구현 시 중요한 고려 사항입니다. 구현 시 이를 고려하지 않으면 완료된 시스템은 결국 6주 이내에 완전히 중지됩니다.

Azure AD에서는 업계 표준을 사용하여 Azure AD 자체와 Azure AD를 사용하는 응용 프로그램 간에 신뢰를 설정합니다. 특히, Azure AD는 공개 및 개인 키 쌍으로 구성된 서명 키를 사용합니다. Azure AD에서 사용자에 대한 정보가 포함된 보안 토큰을 만드는 경우 이 토큰은 응용 프로그램으로 다시 전송되기 전에 Azure AD에 의해 개인 키를 사용하여 서명됩니다. 해당 토큰이 유효하고 실제로 Azure AD에서 발생한 것인지 확인하기 위해 응용 프로그램은 테넌트의 페더레이션 메타데이터 문서에 포함된 Azure AD가 노출한 공개 키를 사용하여 토큰의 서명을 유효성 검사해야 합니다. 이 공개 키와 이로부터 파생된 서명 키는 Azure AD의 모든 테넌트에 사용된 것과 같습니다.

Azure AD 키 롤오버에 대한 자세한 정보는 [Azure AD의 서명 키 롤오버에 대한 중요한 정보](../active-directory/active-directory-signing-key-rollover.md)에서 확인할 수 있습니다.

[공개-개인 키 쌍](https://login.windows.net/common/discovery/keys/)중에서, 

- Azure Active Directory가 JWT 토큰을 생성하는 데 개인 키가 사용됩니다.
- AMS의 DRM 라이선스 배달 서비스와 같은 응용 프로그램에서 JWT 토큰을 확인하는 데 공개 키가 사용됩니다.
 
보안을 위해 Azure Active Directory는 이 인증서를 주기적(6주마다)으로 바꿉니다. 보안 침해가 발생하는 경우에는 언제든지 키 롤오버가 발생할 수 있습니다. 따라서 AMS의 라이선스 배달 서비스는 Azure AD에서 키 쌍을 바꾸므로 사용된 공개 키를 업데이트해야 하며 그렇지 않으면 AMS의 토큰 인증이 실패하고 라이선스가 발급되지 않습니다. 

이 작업은 DRM 라이선스 배달 서비스를 구성할 때 TokenRestrictionTemplate.OpenIdConnectDiscoveryDocument를 설정하여 수행합니다.

JWT 토큰 흐름은 다음과 같습니다.

1.  Azure AD가 인증된 사용자에 대한 최신 개인 키로 JWT 토큰을 발급합니다.
2.  플레이어가 다중 DRM으로 보호된 콘텐츠와 함께 CENC를 인식하면 먼저 Azure AD에서 발급한 JWT 토큰을 찾습니다.
3.  플레이어는 JWT 토큰과 함께 라이선스 획득 요청을 AMS의 라이선스 배달 서비스로 보냅니다.
4.  AMS의 라이선스 배달 서비스는 라이선스를 발급하기 전에 Azure AD의 최신/유효한 공개 키를 사용하여 JWT 토큰을 확인합니다.

DRM 라이선스 배달 서비스는 항상 Azure AD에서 최신/유효한 공개 키를 확인합니다. Azure AD에서 제공되는 공개 키는 Azure AD에서 발급한 JWT 토큰을 확인하는 데 사용되는 키가 됩니다.

AAD가 JWT 토큰을 생성한 후, 플레이어가 확인을 위해 JWT 토큰을 AMS의 DRM 라이선스 배달 서비스로 보내기 전에 키 롤오버가 발생한다면 어떻게 되나요? 

키는 언제든지 롤오버될 수 있으므로 페더레이션 메타데이터 문서에는 사용 가능한 공개 키가 항상 두 개 이상 있습니다. Azure 미디어 서비스 라이선스 배달에서는 하나의 키가 곧 롤오버되고 다른 키가 대체되는 방식으로 문서에 지정된 키를 사용할 수 있습니다.

### <a name="where-is-the-access-token?"></a>액세스 토큰 위치

웹앱에서 [OAuth 2.0 클라이언트 자격 증명 권한을 사용한 응용 프로그램 ID](active-directory-authentication-scenarios.md#web-application-to-web-api)로 API 앱을 호출하는 방식을 살펴보면 인증 흐름은 다음과 같습니다.

1.  사용자가 웹 응용 프로그램에서 Azure AD에 로그인합니다( [웹 브라우저-웹 응용 프로그램](active-directory-authentication-scenarios.md#web-browser-to-web-application)참조).
2.  Azure AD 권한 부여 끝점은 사용자 에이전트를 인증 코드와 함께 클라이언트 응용 프로그램으로 리디렉션합니다. 사용자 에이전트는 인증 코드를 클라이언트 응용 프로그램의 리디렉션 URI로 반환합니다.
3.  웹 응용 프로그램이 웹 API에 인증하고 원하는 리소스를 검색할 수 있도록 액세스 토큰을 획득해야 합니다. Azure AD의 토큰 끝점에 요청하여 자격 증명, 클라이언트 ID, 웹 API의 응용 프로그램 ID URI를 제공합니다. 사용자가 동의했음을 증명하는 인증 코드를 표시합니다.
4.  Azure AD가 응용 프로그램을 인증하고 웹 API를 호출하는 데 사용되는 JWT 액세스 토큰을 반환합니다.
5.  HTTPS를 통해 웹 응용 프로그램이 반환된 JWT 액세스 토큰을 사용해서 웹 API에 대한 요청의 권한 부여 헤더에 “전달자”를 지정한 JWT 문자열을 추가합니다. 그러면 웹 API에서 JWT 토큰의 유효성을 검사하여 유효성 검사가 성공하면 원하는 리소스를 반환합니다.

이 "응용 프로그램 ID" 흐름에서 웹 API는 웹 응용 프로그램이 해당 사용자를 인증했음을 신뢰합니다. 이런 이유로, 이 패턴을 신뢰할 수 있는 하위 시스템이라고 합니다. [이 페이지의 다이어그램](http://msdn.microsoft.com/library/azure/dn645542.aspx/) 은 인증 코드 부여 흐름이 어떻게 작동하는지를 설명합니다.

토큰 제한으로 라이선스 획득 시 동일한 신뢰할 수 있는 하위 시스템 패턴을 따릅니다. Azure 미디어 서비스의 라이선스 배달 서비스가 웹 API 리소스이고 웹 응용 프로그램이 액세스해야 하는 "백 엔드 리소스"입니다. 그렇다면 액세스 토큰은 어디에 있을까요?

실제로, Azure AD에서 액세스 토큰을 가져옵니다. 사용자 인증에 성공하면 인증 코드가 반환됩니다. 그런 다음 액세스 토큰 교환을 위해 인증 코드가 클라이언트 ID 및 앱 키와 함께 사용됩니다. 액세스 토큰은 Azure 미디어 서비스 라이선스 배달 서비스를 가리키거나 나타내는 "포인터" 응용 프로그램을 액세스하기 위한 것입니다.

다음 단계에 따라 Azure AD에서 "포인터" 앱을 등록 및 구성해야 합니다.

1.  Azure AD 테넌트에서

    - 로그온 URL과 함께 응용 프로그램(리소스)을 추가합니다. 

    https://[resource_name].azurewebsites.net/ 및 

    - 앱 ID URL: 
    
    https://[aad_tenant_name].onmicrosoft.com/[resource_name]; 
2.  리소스 앱에 대한 새 키를 추가합니다.
3.  groupMembershipClaims 속성이 "groupMembershipClaims": "All"을 포함하도록 앱 매니페스트 파일을 업데이트합니다.  
4.  플레이어 웹앱을 가리키는 Azure AD 앱의 "다른 응용 프로그램에 대한 권한" 섹션에서 위의 1단계에서 추가한 리소스 앱을 추가합니다. "위임된 권한"에서 "[resource_name] 액세스" 확인 표시를 선택합니다. 그러면 리소스 앱에 액세스하기 위한 액세스 토큰을 만드는 웹앱 권한이 제공됩니다. Visual Studio 및 Azure 웹앱으로 개발 중이라면 웹앱의 로컬 및 배포된 버전 모두에 대해 이 작업을 수행해야 합니다.
    
따라서 Azure AD에서 발급한 JWT 토큰이 실제로 이 "포인터" 리소스에 액세스하기 위한 액세스 토큰입니다.

### <a name="what-about-live-streaming?"></a>라이브 스트리밍의 경우는 어떨까요?

위의 설명에서는 주문형 자산에 초점을 두었습니다. 라이브 스트리밍의 경우는 어떨까요?

좋은 소식은 프로그램과 연결된 자산을 "VOD 자산"으로 처리하여 Azure 미디어 서비스에서 라이브 스트리밍을 보호하는 데 정확히 동일한 디자인 및 구현을 사용할 수 있다는 점입니다.

특히 잘 알려진 것처럼 Azure 미디어 서비스에서 라이브 스트리밍할 경우 채널을 만든 후 해당 채널 아래 프로그램을 만들어야 합니다. 프로그램을 만들려면 프로그램에 대한 라이브 아카이브를 포함할 자산을 만들어야 합니다. 라이브 콘텐츠의 다중 DRM 보호로 CENC를 제공하려면 프로그램 시작 전에, "VOD 자산"이었던 것처럼 자산에 동일한 설정/처리를 적용하기만 하면 됩니다.

### <a name="what-about-license-servers-outside-of-azure-media-services?"></a>Azure 미디어 서비스 외부에서 라이선스 서버는 어떨까요?

고객들은 대체로 고유한 데이터 센터 또는 DRM 서비스 공급자가 호스팅하는 형태로 라이선스 서버 팜에 투자할 수 있습니다. 다행스럽게도 Azure 미디어 서비스 콘텐츠 보호를 사용하면 Azure 미디어 서비스에서 호스팅 및 동적으로 보호되는 콘텐츠를 하이브리드 모드로 작업할 수 있습니다. 반면 DRM 라이선스는 Azure 미디어 서비스 외부 서버에서 제공됩니다. 이 경우 변경에 대한 다음 고려 사항이 있습니다.

1. 보안 토큰 서비스는 라이선스 서버 팜에서 허용되고 확인할 수 있는 토큰을 발급해야 합니다. 예를 들어 Axinom에서 제공하는 Widevine 라이선스 서버에는 "자격 메시지"를 포함하는 특정 JWT 토큰이 필요합니다. 따라서 이러한 JWT 토큰을 발급하는 STS가 있어야 합니다. 저자는 이러한 구현을 완료했으며 이에 대한 세부 사항은 [Azure 설명서 센터](https://azure.microsoft.com/documentation/): [Axinom을 사용하여 Azure Media Services에 Widevine 라이선스 제공](media-services-axinom-integration.md)에서 확인할 수 있습니다. 
1. 더 이상 Azure 미디어 서비스에서 라이선스 배달 서비스(ContentKeyAuthorizationPolicy)를 구성하지 않아도 됩니다. 수행해야 하는 작업은 다중 DRM으로 CENC 설정에서 AssetDeliveryPolicy를 구성할 때 라이선스 획득 URL(PlayReady, Widevine 및 FairPlay에 대해)을 제공하는 것입니다.
 
### <a name="what-if-i-want-to-use-a-custom-sts?"></a>사용자 지정 STS를 사용하려면 어떻게 하나요?

고객이 JWT 토큰을 제공하는 데 사용자 지정 STS(보안 토큰 서비스)를 사용하기로 선택하는 데는 몇 가지 이유가 있습니다. 다음과 같습니다.

1.  고객이 사용한 IDP(ID 공급자)에서 STS를 지원하지 않습니다. 이 경우 사용자 지정 STS가 옵션일 수 있습니다.
2.  고객은 STS를 고객의 구독자 청구 시스템과 통합하는 데 보다 유연하고 긴밀한 제어가 필요할 수 있습니다. 예를 들어 MVPD 작업자는 프리미엄, 기본, 스포츠 등과 같은 여러 OTT 구독자 패키지를 제공할 수 있습니다. 이 작업자는 토큰의 클레임을 구독자의 패키지와 일치시켜 올바른 패키지의 콘텐츠만 제공되도록 할 수 있습니다. 이 경우 사용자 지정 STS가 필요한 유연성 및 제어를 제공합니다.

사용자 지정 STS를 사용할 때는 두 가지를 변경해야 합니다.

1.  자산에 대한 라이선스 배달 서비스를 구성할 때 Azure Active Directory의 현재 키 대신 사용자 지정 STS(자세한 내용은 아래 참조)에서 확인에 사용한 보안 키를 지정해야 합니다.
2.  JWT 토큰이 생성되면 Azure Active Directory의 현재 X509 인증서의 개인 키 대신 보안 키가 지정됩니다.

두 가지 유형의 보안 키가 있습니다.

1.  대칭 키: JWT 토큰을 생성 및 확인하는 데 동일한 키가 사용됩니다.
2.  비대칭 키: JWT 토큰을 암호화/생성하는 데는 개인 키가, 토큰을 확인하는 데는 공개 키가 사용되는 방식으로 X509 인증서에 공개-개인 키 쌍이 사용됩니다.

####<a name="tech-note"></a>기술 참고 사항

개발 플랫폼으로 .NET Framework/C#을 사용하는 경우 비공개 보안 키에 사용된 X509 인증서에는 키 길이가 2048 이상이어야 합니다. 이는 .NET Framework에서 System.IdentityModel.Tokens.X509AsymmetricSecurityKey 클래스의 요구 사항입니다. 그렇지 않으면 다음 예외가 throw됩니다.

IDX10630: 서명을 위한 'System.IdentityModel.Tokens.X509AsymmetricSecurityKey'는 '2048'비트 이상이어야 합니다. 

## <a name="the-completed-system-and-test"></a>완료된 시스템 및 테스트

이 문서의 독자가 로그인 계정을 얻기 전 동작에 대한 기본적인 "그림"을 그려볼 수 있도록 완료된 종단 간 시스템에 대한 몇 가지 시나리오를 살펴보겠습니다.

플레이어 웹 응용 프로그램 및 해당 로그인은 [여기](https://openidconnectweb.azurewebsites.net/)에서 확인할 수 있습니다.

비디오 자산이 보호되지 않거나 토큰 인증 없이 DRM으로 보호되는(필요로 하는 모든 대상에 라이선스 발급) Azure 미디어 서비스에서 호스트되는 "통합되지 않은" 시나리오가 필요한 경우 로그인 없이(비디오 스트리밍이 HTTP를 통해 이루어지는 경우 HTTP로 전환) 테스트할 수 있습니다.

비디오 자산이 Azure 미디어 서비스에서 동적 DRM 보호 하에 있고 토큰 인증 및 Azure AD가 생성하는 JWT 토큰이 있는 종단 간 통합된 시나리오가 필요한 경우 로그인해야 합니다.

### <a name="user-login"></a>사용자 로그인

종단 간 통합된 DRM 시스템을 테스트하려면 "계정"을 만들거나 추가해야 합니다. 

계정이란?

원래 Microsoft 계정 사용자만 Azure에 액세스할 수 있었으나 이제는 이 두 가지 시스템의 사용자도 액세스할 수 있습니다. 이렇게 하기 위해 모든 Azure 속성이 인증을 위해 Azure AD를 신뢰하도록 하고, Azure AD가 조직 사용자를 인증하도록 하며, 소비자 사용자를 인증하는 Microsoft 계정 소비자 ID 시스템을 Azure AD가 신뢰하는 위치에 페더레이션 관계를 만들었습니다. 그 결과, Azure AD가 "기본" Azure AD 계정 뿐만 아니라 "게스트" Microsoft 계정도 인증할 수 있습니다.

Azure AD는 Microsoft 계정(MSA) 도메인을 신뢰하므로 다음 도메인에서 사용자 지정 Azure AD 테넌트로 어떤 계정이든 추가하여 로그인에 사용할 수 있습니다.

**도메인 이름**|**도메인**
-----------|----------
**사용자 지정 Azure AD 테넌트 도메인**|somename.onmicrosoft.com
**회사 도메인**|microsoft.com
**Microsoft 계정(MSA) 도메인**|outlook.com, live.com, hotmail.com

사용자를 위한 계정을 만들거나 추가한 작성자에게 연락할 수 있습니다. 

다음은 다양한 도메인 계정에서 사용하는 다양한 로그인 페이지의 스크린샷입니다.

**사용자 지정 Azure AD 테넌트 도메인 계정**: 이 경우 사용자 지정 Azure AD 테넌트 도메인의 사용자 지정된 로그인 페이지가 표시됩니다.

![사용자 지정 Azure AD 테넌트 도메인 계정](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain1.png)

**스마트 카드를 사용한 Microsoft 도메인 계정**: 이 경우 Microsoft 회사 IT에서 2단계 인증으로 사용자 지정한 로그인 페이지가 표시됩니다.

![사용자 지정 Azure AD 테넌트 도메인 계정](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain2.png)

**Microsoft 계정(MSA)**: 이 경우 소비자를 위한 Microsoft 계정의 로그인 페이지가 표시됩니다.

![사용자 지정 Azure AD 테넌트 도메인 계정](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain3.png)

### <a name="using-encrypted-media-extensions-for-playready"></a>PlayReady에 암호화된 미디어 확장 사용

Windows 8.1 이상의 IE 11, Windows 10의 Microsoft Edge 브라우저와 같이 PlayReady 지원에 대한 EME(암호화된 미디어 확장)를 지원하는 최신 브라우저에서는 PlayReady가 EME를 위한 기본 DRM이 됩니다.

![PlayReady에 EME 사용](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready1.png)

플레이어 영역에서 어두운 부분은 PlayReady 보호 기능이 보호된 비디오의 화면 캡처를 차단하기 때문입니다. 

다음 화면에는 플레이어 플러그인 및 MSE/EME 지원을 보여줍니다.

![PlayReady에 EME 사용](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready2.png)

Windows 10의 Microsoft Edge 및 IE 11에 있는 EME를 통해 이를 지원하는 Windows 10 장치에서 [PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/)을 호출할 수 있습니다. PlayReady SL3000은 향상된 프리미엄 콘텐츠(4K, HDR 등) 흐름 및 새 콘텐츠 배달 모델(향상된 콘텐츠에 대한 초기 창)의 잠금을 해제합니다.

Windows 장치에 집중: PlayReady는 Windows 장치(PlayReady SL3000)에서 사용 가능한 HW의 유일한 DRM입니다. 스트리밍 서비스는 EME 또는 UWP 응용 프로그램을 통해 PlayReady를 사용하고 PlayReady SL3000을 사용하여 다른 DRM보다 더 높은 화질을 제공할 수 있습니다. 일반적으로 2K 콘텐츠는 Chrome 또는 Firefox를 통해 이동하고 4K 콘텐츠는 동일한 장치의 Microsoft Edge/IE11 또는 UWP 응용 프로그램을 통해 이동합니다(서비스 설정 및 구현에 따라).


#### <a name="using-eme-for-widevine"></a>Widevine에 EME 사용

Windows 10, Windows 8.1, Mac OSX Yosemite의 Chrome 41 이상, Android 4.4.4의 Chrome과 같이 EME/Widevine을 지원하는 최신 브라우저에서는 Google Widevine이 EME 뒤의 DRM입니다.

![Widevine에 EME 사용](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine1.png)

Widevine은 보호된 비디오의 화면 캡처 만들기를 차단하지 않습니다.

![Widevine에 EME 사용](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine2.png)

### <a name="not-entitled-users"></a>자격이 없는 사용자

사용자가 "자격이 있는 사용자" 그룹의 멤버가 아닌 경우 사용자는 "자격 확인"을 전달할 수 없고 다중 DRM 라이선스 서비스가 아래 표시된 것처럼 요청된 라이선스의 발급을 거부합니다. 자세한 설명은 설계된 대로 "라이선스 획득 실패"입니다.

![자격이 없는 사용자](./media/media-services-cenc-with-multidrm-access-control/media-services-unentitledusers.png)

### <a name="running-custom-secure-token-service"></a>사용자 지정 보안 토큰 서비스 실행

사용자 지정 STS(보안 토큰 서비스)를 실행하는 시나리오에서는 사용자 지정 STS에서 대칭 또는 비대칭 키를 사용하여 JWT 토큰을 발급합니다. 

대칭 키를 사용하는 경우(Chrome 사용):

![사용자 지정 STS 실행](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts1.png)

X509 인증서를 통해 비대칭 키를 사용하는 경우(Microsoft 최신 브라우저 사용)

![사용자 지정 STS 실행](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts2.png)

위의 두 경우 모두 Azure AD를 통한다는 점에서 사용자 인증은 동일하게 유지됩니다. 유일한 차이점은 JWT 토큰이 Azure AD 대신 사용자 지정 STS에 의해 발급된다는 점입니다. 물론, 동적 CENC 보호를 구성할 때 라이선스 배달 서비스 제한으로 JWT 토큰 유형을 대칭 또는 비대칭 키로 지정합니다.

## <a name="summary"></a>요약

이 문서에서는 다중 원시 DRM 및 토큰 인증을 통한 액세스 제어가 포함된 CENC와 Azure, Azure 미디어 서비스 및 Azure 미디어 플레이어를 사용한 디자인 및 구현에 대해 설명했습니다.

- DRM/CENC 하위 시스템에 필요한 모든 구성 요소를 포함하는 참조 디자인이 표시됩니다.
- Azure, Azure 미디어 서비스 및 Azure 미디어 플레이어에 대한 참조 구현입니다.
- 디자인 및 구현에 직접 관련된 몇 가지 토픽도 설명합니다.


##<a name="media-services-learning-paths"></a>미디어 서비스 학습 경로

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>피드백 제공

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

###<a name="acknowledgments"></a>승인 

William Zhang, Mingfei Yan, Roland Le Franc, Kilroy Hughes, Julia Kornich



<!--HONumber=Oct16_HO2-->


