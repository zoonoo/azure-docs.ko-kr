---
title: Azure Media Services v3 질문과 대답 | Microsoft Docs
description: 이 문서에서는 Azure Media Services v3에 대해 자주 묻는 질문과 대답을 제공합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 06/21/2019
ms.author: juliako
ms.openlocfilehash: c9da29ad288811bbed225fd906f2a7eb1fd9edf7
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977729"
---
# <a name="media-services-v3-frequently-asked-questions"></a>Media Services v3 질문과 대답

이 문서에서는 AMS(Azure Media Services) v3에 대해 자주 묻는 질문과 대답을 제공합니다.

## <a name="general"></a>일반

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Azure Media Services 리소스에 대 한 작업을 수행할 수 있는 Azure 역할은 무엇 인가요? 

[Media Services 계정에 대 한 RBAC (역할 기반 액세스 제어)를](rbac-overview.md)참조 하세요.

### <a name="how-do-i-configure-media-reserved-units"></a>미디어 예약 단위를 구성하려면 어떻게 할까요?

Media Services v3 또는 Video Indexer에 의해 트리거되는 오디오 분석 및 비디오 분석 작업의 경우 10개의 S3 MRU를 사용하여 계정을 프로비전하는 것이 좋습니다. 10개가 넘는 S3 MRU가 필요한 경우 [Azure Portal](https://portal.azure.com/)을 사용하여 지원 티켓을 엽니다.

자세한 내용은 [CLI를 사용하여 미디어 처리 크기 조정](media-reserved-units-cli-how-to.md)을 참조하세요.

### <a name="what-is-the-recommended-method-to-process-videos"></a>비디오 처리에 권장하는 방법은 무엇입니까?

[Transforms](https://docs.microsoft.com/rest/api/media/transforms)는 비디오 인코딩 또는 분석에 대한 일반적인 작업을 구성하는 데 사용할 수 있습니다. 각 **변환**은 비디오 또는 오디오 파일을 처리하는 작업의 작성법 또는 워크플로를 설명합니다. [작업](https://docs.microsoft.com/rest/api/media/jobs) 은 지정 된 입력 비디오 또는 오디오 콘텐츠에 **변환을** 적용 하는 Media Services에 대 한 실제 요청입니다. 변환을 만든 후에는 Media Services API 또는 게시된 SDK를 사용하여 작업을 제출할 수 있습니다. 자세한 내용은 [Transform 및 Jobs](transforms-jobs-concept.md)를 참조하세요.

### <a name="how-does-pagination-work"></a>페이지 매김은 어떻게 작동하나요?

페이지 매김을 사용할 때는 항상 다음 링크를 사용하여 컬렉션을 열거하고, 특정 페이지 크기에 따라 달라지지 않아야 합니다. 자세한 내용과 예제는 [필터링, 정렬, 페이징](entities-overview.md)을 참조하세요.

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>Azure Media Services v3에서 아직 사용할 수 없는 기능은 무엇입니까?

자세한 내용은 [V2 api에 대 한 기능 간격](migrate-from-v2-to-v3.md#feature-gaps-with-respect-to-v2-apis)을 참조 하세요.

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>구독 간에 Media Services 계정을 이동 하는 프로세스는 무엇 인가요?  

자세한 내용은 [구독 간 Media Services 계정 이동](media-services-account-concept.md)을 참조 하세요.

## <a name="live-streaming"></a>라이브 스트리밍 

###  <a name="how-to-insert-breaksvideos-and-image-slates-during-live-stream"></a>라이브 스트리밍 도중에 중단/비디오 및 이미지 슬레이트를 삽입하는 방법은 무엇입니까?

Media Services v3 라이브 인코딩은 아직 라이브 스트리밍 도중에 비디오 또는 이미지 슬레이트를 삽입하는 기능을 지원하지 않습니다. 

[라이브 온-프레미스 인코더](recommended-on-premises-live-encoders.md)를 사용하여 원본 비디오를 바꿀 수 있습니다. Telestream Wirecast, Switcher Studio(iOS), OBS Studio(무료 앱) 등 여러 앱에서 원본을 바꾸는 기능을 제공합니다.

## <a name="content-protection"></a>콘텐츠 보호

### <a name="should-i-use-an-aes-128-clear-key-encryption-or-a-drm-system"></a>AES-128 암호화 되지 않은 키 암호화 또는 DRM 시스템을 사용 해야 하나요?

고객들은 종종 AES 암호화 또는 DRM 시스템을 사용해야 할지 여부를 궁금해 합니다. 두 시스템 간의 주요 차이점은 AES 암호화를 사용 하는 경우 키가 전송 중에 암호화 되지만 추가 암호화 ("clear") 없이 암호화 되도록 콘텐츠 키가 TLS를 통해 클라이언트에 전송 된다는 것입니다. 따라서 콘텐츠를 해독 하는 데 사용 되는 키는 클라이언트 플레이어에서 액세스할 수 있으며, 클라이언트의 네트워크 추적에서 일반 텍스트로 볼 수 있습니다. AES-128 암호화 되지 않은 키 암호화는 뷰어가 신뢰할 수 있는 당사자 (예: 직원이 볼 수 있도록 회사 내에 배포 되는 회사 비디오 암호화) 인 사용 사례에 적합 합니다.

PlayReady, Widevine 및와 같은 DRM 시스템은 모두 AES-128 암호화 되지 않은 키와 비교 하 여 콘텐츠를 암호 해독 하는 데 사용 되는 키에 대 한 추가 수준의 암호화를 제공 합니다. 콘텐츠 키는 TLS에서 제공 하는 전송 수준 암호화에 추가 하 여 DRM 런타임에 의해 보호 되는 키로 암호화 됩니다. 또한 암호 해독은 악의적인 사용자가 공격하기에 좀 더 어려운 운영 체제 수준의 보안 환경에서 처리됩니다. DRM은 뷰어가 신뢰할 만한 당사자가 아니고 가장 높은 수준의 보안이 필요한 사용 사례에 권장됩니다.

### <a name="how-to-show-a-video-only-to-users-who-have-a-specific-permission-without-using-azure-ad"></a>Azure AD를 사용 하지 않고 특정 권한이 있는 사용자 에게만 비디오를 표시 하는 방법

특정 토큰 공급자 (예: Azure AD)를 사용할 필요가 없습니다. 비대칭 키 암호화를 사용 하 여 사용자 고유의 [JWT](https://jwt.io/) 공급자 (따라서 STS, 보안 토큰 서비스)를 만들 수 있습니다. 사용자 지정 STS에서 비즈니스 논리를 기준으로 클레임을 추가할 수 있습니다.

발급자, 대상 그룹 및 클레임이 모두 JWT와 ContentKeyPolicy에서 사용 되는 ContentKeyPolicyRestriction 사이에서 정확 하 게 일치 하는지 확인 합니다.

자세한 내용은 [Media Services 동적 암호화를 사용 하 여 콘텐츠 보호](content-protection-overview.md)를 참조 하세요.

### <a name="how-and-where-to-get-jwt-token-before-using-it-to-request-license-or-key"></a>라이선스 또는 키를 요청하는 데 사용하기 전에 JWT 토큰을 가져올 수 있는 방법과 위치는 어떻게 되나요?

1. 프로덕션의 경우 HTTPS 요청 시 JWT 토큰을 발급 하는 STS (보안 토큰 서비스) (웹 서비스)가 있어야 합니다. 테스트를 위해 **Program.cs**에 정의된 [GetTokenAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) 메서드에 표시된 코드를 사용할 수 있습니다.
2. 사용자가 인증되면 플레이어에서 이러한 토큰에 대해 STS에 요청하고 토큰의 값으로 할당해야 합니다. [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/)를 사용할 수 있습니다.

* 대칭 및 비대칭 키를 사용하여 STS를 실행하는 예제는 [https://aka.ms/jwt](https://aka.ms/jwt)를 참조하세요. 
* 이러한 JWT 토큰을 사용하는 Azure Media Player를 기반으로 하는 플레이어의 예제는 [https://aka.ms/amtest](https://aka.ms/amtest)를 참조하세요("player_settings" 링크를 펼쳐 토큰 입력을 확인함).

### <a name="how-do-you-authorize-requests-to-stream-videos-with-aes-encryption"></a>AES 암호화를 사용하여 비디오를 스트림할 수 있도록 요청에 권한을 부여하려면 어떻게 할까요?

올바른 방법은 다음과 같이 STS(보안 토큰 서비스)를 활용하는 것입니다.

STS에서 사용자 프로필에 따라 서로 다른 클레임(예: "프리미엄 사용자", "기본 사용자", "평가판 사용자")을 추가합니다. JWT에서 서로 다른 클레임을 사용하면 사용자가 각각의 콘텐츠를 볼 수 있습니다. 물론 ContentKeyPolicyRestriction에는 서로 다른 콘텐츠/자산에 해당하는 RequiredClaims가 있습니다.

[이 샘플](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)에 나와 있는 것 처럼 라이선스/키 제공을 구성 하 고 자산을 암호화 하는 Azure Media Services api를 사용 합니다.

자세한 내용은 다음을 참조하세요.

- [콘텐츠 보호 개요](content-protection-overview.md)
- [액세스 제어가 포함된 다중 DRM 콘텐츠 보호 시스템 설계](design-multi-drm-system-with-access-control.md)

### <a name="http-or-https"></a>HTTP 또는 HTTPS
ASP.NET MVC 플레이어 애플리케이션은 다음을 지원해야 합니다.

* Azure AD를 통한 사용자 인증이 HTTPS를 사용해야 함
* 클라이언트 및 Azure AD 간 JWT 교환이 HTTPS를 사용해야 함
* Media Services에서 라이선스 배달이 제공된 경우 클라이언트에 의한 DRM 라이선스 획득 시 HTTPS를 사용해야 함. PlayReady 제품군은 라이선스 배달에 대한 HTTPS를 위임하지 않습니다. PlayReady 라이선스 서버가 Media Services 외부에 있는 경우 HTTP 또는 HTTPS를 사용할 수 있습니다.

ASP.NET 플레이어 애플리케이션은 HTTPS를 사용하는 것이 가장 좋으므로 Media Player는 HTTPS를 사용하는 페이지에 있게 됩니다. 그러나 스트리밍의 경우 HTTP를 선호하므로 콘텐츠 혼합 문제를 고려해야 합니다.

* 브라우저에서는 혼합 콘텐츠를 허용하지 않습니다. 하지만 Silverlight과 같은 플러그인, 부드러운 스트리밍을 위한 OSMF 플러그인 및 DASH는 허용합니다. 악성 JavaScript를 주입할 수 있는 위협으로 인해 고객 데이터가 위험해질 수 있으므로 혼합 콘텐츠는 보안 문제를 발생할 수 있습니다. 브라우저는 기본적으로 이 기능을 차단합니다. 이 문제를 해결하는 유일한 방법이 서버(원본) 쪽에서 HTTPS 또는 HTTP에 관계없이 모든 도메인을 허용하는 것입니다. 하지만 좋은 방법은 아닙니다.
* 혼합 콘텐츠를 피해야 합니다. 플레이어 애플리케이션과 Media Player 둘 다 HTTP 또는 HTTPS를 사용해야 합니다. 혼합된 콘텐츠를 재생할 때 silverlightSS 기술은 혼합된 콘텐츠 경고를 지워야 합니다. flashSS 기술은 혼합된 콘텐츠 경고 없이 혼합된 콘텐츠를 처리합니다.
* 스트리밍 엔드포인트가 2014년 8월 전에 만들어진 경우 HTTPS를 지원하지 않습니다. 이 경우 HTTPS에 대한 새 스트리밍 엔드포인트를 만들어 사용하세요.

### <a name="what-about-live-streaming"></a>라이브 스트리밍의 경우는 어떨까요?

프로그램과 연결된 자산을 VOD 자산으로 처리하여 Media Services에서 라이브 스트리밍을 보호하는 데 정확히 동일한 디자인 및 구현을 사용할 수 있습니다. 라이브 콘텐츠의 다중 DRM 보호를 제공 하려면 자산을 라이브 출력과 연결 하기 전에 VOD 자산과 동일한 설정/처리를 자산에 적용 합니다.

### <a name="what-about-license-servers-outside-media-services"></a>Media Services 외부에서 라이선스 서버는 어떨까요?

고객들은 대체로 고유한 데이터 센터 또는 DRM 서비스 공급자가 호스트하는 형태로 라이선스 서버 팜에 투자할 수 있습니다. Media Services 콘텐츠 보호를 사용하면 하이브리드 모드로 작업할 수 있습니다. DRM 라이선스는 Media Services 외부 서버에서 제공되지만 콘텐츠는 Media Services에서 호스트되고 동적으로 보호될 수 있습니다. 이 경우 다음과 같은 변경을 고려하세요.

* STS는 라이선스 서버 팜에서 허용되고 확인할 수 있는 토큰을 발급해야 합니다. 예를 들어 Axinom에서 제공하는 Widevine 라이선스 서버에는 자격 메시지를 포함하는 특정 JWT가 필요합니다. 따라서 이러한 JWT를 발급하는 STS가 있어야 합니다. 
* 더 이상 Media Services에서 라이선스 배달 서비스를 구성하지 않아도 됩니다. ContentKeyPolicies를 구성할 때 라이선스 획득 URL(PlayReady, Widevine 및 FairPlay에 대해)을 제공해야 합니다.

> [!NOTE]
> Widevine은 Google Inc.에서 제공하는 서비스로, Google Inc.의 서비스 약관 및 개인정보처리방침을 따릅니다.

## <a name="media-services-v2-vs-v3"></a>Media Services v2와 v3 비교 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Azure Portal을 사용하여 v3 리소스를 관리할 수 있나요?

현재는 Azure Portal을 사용하여 v3 리소스를 관리할 수 없습니다. [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref) 또는 지원되는 [SDK](media-services-apis-overview.md#sdks) 중 하나를 사용하세요.

### <a name="is-there-an-assetfile-concept-in-v3"></a>v3에 AssetFile 개념이 있나요?

Media Services를 Storage SDK 종속성과 분리하기 위해 AssetFiles가 AMS API에서 제거되었습니다. 이제 Media Services가 아닌 Storage에서 해당 Storage에 속한 정보를 보관합니다. 

자세한 내용은 [Media Services v3로 마이그레이션](migrate-from-v2-to-v3.md)을 참조하세요.

### <a name="where-did-client-side-storage-encryption-go"></a>클라이언트 쪽 스토리지 암호화는 어디에 있나요?

이제 서버 쪽 스토리지 암호화(기본적으로 설정됨)를 사용하는 것이 좋습니다. 자세한 내용은 [미사용 데이터에 대한 Azure Storage 서비스 암호화](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)를 참조하세요.

## <a name="next-steps"></a>다음 단계

[Media Services v3 개요](media-services-overview.md)
