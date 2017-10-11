---
title: "Azure Portal을 사용하여 콘텐츠 키 인증 정책 구성 | Microsoft 문서"
description: "콘텐츠 키에 대한 인증 정책을 구성하는 방법에 대해 알아봅니다."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: ee82a3fa-c34b-48f2-a108-8ba321f1691e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako
ms.openlocfilehash: 5a35c7255a1c30a693862589c14f6a22a1900790
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/29/2017
---
# <a name="configure-content-key-authorization-policy"></a>콘텐츠 키 인증 정책 구성
[!INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>개요
Microsoft Azure 미디어 서비스를 사용하면 AES(Advanced Encryption Standard)(128비트 암호화 키 사용) 또는 [Microsoft PlayReady DRM](https://www.microsoft.com/playready/overview/)으로 보호되는 MPEG-DASH, 부드러운 스트리밍 및 HTTP-Live-Streaming(HLS) 스트림을 배달할 수 있습니다. 또한 AMS를 사용하면 Widevine DRM으로 암호화된 DASH 스트림을 배달할 수 있습니다. PlayReady와 Widevine 모두 일반적인 암호화(ISO/IEC 23001-7 CENC) 사양에 따라 암호화됩니다.

또한 미디어 서비스는 **키/라이선스 배달 서비스** 를 제공하여 클라이언트가 암호화된 콘텐츠를 재생할 수 있는 AES 키 또는 PlayReady/Widevine 라이선스를 받을 수 있습니다.

이 항목에서는 Azure Portal을 사용하여 콘텐츠 키 인증 정책을 구성하는 방법을 보여 줍니다. 키는 나중에 동적으로 콘텐츠를 암호화하는데 사용할 수 있습니다. 현재 암호화할 수 있는 스트리밍 형식은 HLS, MPEG DASH 및 부드러운 스트리밍입니다. 점진적 다운로드를 암호화할 수 없습니다.

플레이어가 동적으로 암호화되도록 설정된 스트림을 요청하는 경우, 미디어 서비스는 구성된 키를 사용하고 AES 또는 DRM 암호화를 사용하여 동적으로 사용자의 콘텐츠를 암호화합니다. 스트림을 해독하기 위해 플레이어는 키 배달 서비스에서 키를 요청합니다. 사용자에게 키를 얻을 수 있는 권한이 있는지 여부를 결정하기 위해 서비스는 키에 지정된 권한 부여 정책을 평가합니다.

여러 콘텐츠 키를 가지거나 미디어 서비스 키 배달 서비스가 아닌 **키/라이선스 배달 서비스** URL을 지정하려면 미디어 서비스 키 배달 서비스 미디어 서비스 .NET SDK 또는 REST API를 사용하세요.

[미디어 서비스 .NET SDK를 사용하여 콘텐츠 키 권한 부여 정책 구성](media-services-dotnet-configure-content-key-auth-policy.md)

[미디어 서비스 REST API를 사용하여 콘텐츠 키 권한 부여 정책 구성](media-services-rest-configure-content-key-auth-policy.md)

### <a name="some-considerations-apply"></a>다음과 같은 몇 가지 고려 사항이 적용됩니다.
* AMS 계정이 만들어질 때 **기본** 스트리밍 끝점은 **중지됨** 상태에서 계정에 추가됩니다. 콘텐츠 스트리밍을 시작하고 동적 패키징 및 동적 암호화를 활용하려면 스트리밍 끝점이 **실행** 상태에 있어야 합니다. 
* 사용자의 자산은 적응 비트 전송률 MP4 또는 적응 비트 전송률 부드러운 스트리밍 파일 집합을 포함해야 합니다. 자세한 내용은 [자산 인코딩](media-services-encode-asset.md)을 참조하세요.
* 키 배달 서비스는 ContentKeyAuthorizationPolicy 및 관련 개체(정책 옵션 및 제한 사항)를 15분 동안 캐시합니다.  ContentKeyAuthorizationPolicy를 만들고 "Token" 제한을 사용하도록 지정 및 테스트하고 정책의 제한을 "개방"으로 업데이트 하는 경우, 해당 정책이 "개방" 버전으로 전환하는 데 약 15분이 소요됩니다.

## <a name="how-to-configure-the-key-authorization-policy"></a>키 인증 정책을 구성하는 방법
키 인증 정책을 구성하려면 **콘텐츠 보호** 페이지를 선택하세요.

미디어 서비스는 키를 요청 하는 사용자를 인증 하는 여러 방법을 지원합니다. 콘텐츠 키 인증 정책에는 **개방**, **토큰** 또는 **IP** 인증 제한이 있을 수 있습니다(**IP**는 REST 또는 .NET SDK로 구성 가능).

### <a name="open-restriction"></a>열기 제한
**개방** 제한은 시스템이 키를 요청하는 사람에게 키를 제공하는 것을 의미합니다. 이 제한은 테스트 목적으로 유용할 수 있습니다.

![OpenPolicy][open_policy]

### <a name="token-restriction"></a>토큰 제한
토큰 제한 정책을 선택하려면 **TOKEN** 단추를 누릅니다.

**토큰** 제한 정책에는 **보안 토큰 서비스**(STS)에서 발급한 토큰이 수반되어야 합니다. Media Services에서는 **단순 웹 토큰**([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) 형식 및 **JSON Web Token**(JWT) 형식의 토큰을 지원합니다. 자세한 내용은 [JWT 토큰 인증](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)(영문)을 참조하세요.

미디어 서비스는 **보안 토큰 서비스**를 제공하지 않습니다. 사용자 지정 STS를 만들거나 Microsoft Azure ACS를 활용하여 토큰을 발급할 수 있습니다. 지정된 키로 서명된 토큰을 만들고 토큰 제한 구성에서 지정한 클레임을 발급하려면 반드시 STS를 구성해야 합니다. 토큰이 유효하고 해당 토큰의 클레임이 콘텐츠 키에 대해 구성된 클레임과 일치하는 경우 미디어 서비스 키 배달 서비스는 암호화 키를 클라이언트에게 반환합니다. 자세한 내용은 [Azure ACS를 사용하여 토큰 발급](http://mingfeiy.com/acs-with-key-services)(영문)을 참조하세요.

**토큰** 제한 정책을 구성하는 경우 **확인 키**, **발급자** 및 **대상**에 대한 값을 설정해야 합니다. 기본 확인 키는 토큰이 서명된 키를 포함하며 발급자는 토큰을 발행하는 보안 토큰 서비스입니다. 청중(범위) 라고도 함)은 토큰의 의도 또는 토큰이 접근을 인증하는 대상 리소스를 설명합니다. 미디어 서비스 키 배달 서비스는 이러한 토큰의 값이 템플릿 파일에 있는 값과 일치하는지 확인합니다.

### <a name="playready"></a>PlayReady
**PlayReady**로 콘텐츠를 보호하려는 경우 권한 부여 정책에서 지정해야 하는 항목 중 하나는 PlayReady 라이선스 템플릿을 정의하는 XML 문자열입니다. 기본적으로 설정되는 정책:

<PlayReadyLicenseResponseTemplate xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1"> <LicenseTemplates> <PlayReadyLicenseTemplate><AllowTestDevices>true</AllowTestDevices> <ContentKey i:type="ContentEncryptionKeyFromHeader" /> <LicenseType>비영구</LicenseType> <PlayRight> <AllowPassingVideoContentToUnknownOutput>허용</AllowPassingVideoContentToUnknownOutput> </PlayRight> </PlayReadyLicenseTemplate> </LicenseTemplates> </PlayReadyLicenseResponseTemplate>

**정책 xml 가져오기** 단추를 클릭하고, [여기](media-services-playready-license-template-overview.md)에 정의된 XML 스키마를 준수하는 별도의 XML을 제공할 수 있습니다.

## <a name="next-step"></a>다음 단계
미디어 서비스 학습 경로를 검토합니다.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png

