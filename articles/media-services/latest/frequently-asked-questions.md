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
ms.date: 04/24/2019
ms.author: juliako
ms.openlocfilehash: d99e3661fd51b481a20404cfeb55157ac803c220
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64706158"
---
# <a name="azure-media-services-v3-frequently-asked-questions"></a>Azure Media Services v3 질문과 대답

이 문서에서는 AMS(Azure Media Services) v3에 대해 자주 묻는 질문과 대답을 제공합니다.

## <a name="v3-apis"></a>v3 API

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Azure 역할 Azure Media Services 리소스에서 작업을 수행할 수 있습니까? 

참조 [Media Services 계정에 대 한 역할 기반 액세스 제어 (RBAC)](rbac-overview.md)합니다.

### <a name="how-do-i-configure-media-reserved-units"></a>미디어 예약 단위를 구성하려면 어떻게 할까요?

Media Services v3 또는 Video Indexer에 의해 트리거되는 오디오 분석 및 비디오 분석 작업의 경우 10개의 S3 MRU를 사용하여 계정을 프로비전하는 것이 좋습니다. 10개가 넘는 S3 MRU가 필요한 경우 [Azure Portal](https://portal.azure.com/)을 사용하여 지원 티켓을 엽니다.

자세한 내용은 [CLI를 사용하여 미디어 처리 크기 조정](media-reserved-units-cli-how-to.md)을 참조하세요.

### <a name="what-is-the-recommended-method-to-process-videos"></a>비디오 처리에 권장하는 방법은 무엇입니까?

[Transforms](https://docs.microsoft.com/rest/api/media/transforms)는 비디오 인코딩 또는 분석에 대한 일반적인 작업을 구성하는 데 사용할 수 있습니다. 각 **변환**은 비디오 또는 오디오 파일을 처리하는 작업의 작성법 또는 워크플로를 설명합니다. A [작업](https://docs.microsoft.com/rest/api/media/jobs) 적용 하려면 Media Services에 대 한 실제 요청입니다 합니다 **변환** 주어진된 입력된 비디오 또는 오디오 콘텐츠를 합니다. 변환을 만든 후에는 Media Services API 또는 게시된 SDK를 사용하여 작업을 제출할 수 있습니다. 자세한 내용은 [Transform 및 Jobs](transforms-jobs-concept.md)를 참조하세요.

### <a name="how-does-pagination-work"></a>페이지 매김은 어떻게 작동하나요?

페이지 매김을 사용할 때는 항상 다음 링크를 사용하여 컬렉션을 열거하고, 특정 페이지 크기에 따라 달라지지 않아야 합니다. 자세한 내용과 예제는 [필터링, 정렬, 페이징](entities-overview.md)을 참조하세요.

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>어떤 기능은 아직 Azure Media Services v3에서 사용할 수 있습니까?

세부 정보를 참조 하세요 [v2 Api와 관련 하 여 간격을 기능](migrate-from-v2-to-v3.md#feature-gaps-with-respect-to-v2-apis)합니다.

## <a name="live-streaming"></a>라이브 스트리밍 

###  <a name="how-to-insert-breaksvideos-and-image-slates-during-live-stream"></a>라이브 스트리밍 도중에 중단/비디오 및 이미지 슬레이트를 삽입하는 방법은 무엇입니까?

Media Services v3 라이브 인코딩은 아직 라이브 스트리밍 도중에 비디오 또는 이미지 슬레이트를 삽입하는 기능을 지원하지 않습니다. 

[라이브 온-프레미스 인코더](recommended-on-premises-live-encoders.md)를 사용하여 원본 비디오를 바꿀 수 있습니다. Telestream Wirecast, Switcher Studio(iOS), OBS Studio(무료 앱) 등 여러 앱에서 원본을 바꾸는 기능을 제공합니다.

## <a name="content-protection"></a>콘텐츠 보호

### <a name="how-and-where-to-get-jwt-token-before-using-it-to-request-license-or-key"></a>라이선스 또는 키를 요청하는 데 사용하기 전에 JWT 토큰을 가져올 수 있는 방법과 위치는 어떻게 되나요?

1. 프로덕션 환경의 경우 HTTPS 요청 시 JWT 토큰을 발급하는 STS(보안 토큰 서비스)(웹 서비스)가 있어야 합니다. 테스트를 위해 [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs)에 정의된 **GetTokenAsync** 메서드에 표시된 코드를 사용할 수 있습니다.
2. 사용자가 인증되면 플레이어에서 이러한 토큰에 대해 STS에 요청하고 토큰의 값으로 할당해야 합니다. [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/)를 사용할 수 있습니다.

* 대칭 및 비대칭 키를 사용하여 STS를 실행하는 예제는 [https://aka.ms/jwt](https://aka.ms/jwt)를 참조하세요. 
* 이러한 JWT 토큰을 사용하는 Azure Media Player를 기반으로 하는 플레이어의 예제는 [https://aka.ms/amtest](https://aka.ms/amtest)를 참조하세요("player_settings" 링크를 펼쳐 토큰 입력을 확인함).

### <a name="how-do-you-authorize-requests-to-stream-videos-with-aes-encryption"></a>AES 암호화를 사용하여 비디오를 스트림할 수 있도록 요청에 권한을 부여하려면 어떻게 할까요?

올바른 방법은 다음과 같이 STS(보안 토큰 서비스)를 활용하는 것입니다.

STS에서 사용자 프로필에 따라 서로 다른 클레임(예: "프리미엄 사용자", "기본 사용자", "평가판 사용자")을 추가합니다. JWT에서 서로 다른 클레임을 사용하면 사용자가 각각의 콘텐츠를 볼 수 있습니다. 물론 ContentKeyPolicyRestriction에는 서로 다른 콘텐츠/자산에 해당하는 RequiredClaims가 있습니다.

구성 하는 데 사용 하 여 Azure Media Services Api 라이선스/키 배달 및 자산 암호화 (에서처럼 [이 샘플](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)).

자세한 내용은 다음을 참조하세요.

- [콘텐츠 보호 개요](content-protection-overview.md)
- [액세스 제어가 포함된 다중 DRM 콘텐츠 보호 시스템 설계](design-multi-drm-system-with-access-control.md)

## <a name="media-services-v2-vs-v3"></a>Media Services v2와 v3 비교 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Azure Portal을 사용하여 v3 리소스를 관리할 수 있나요?

현재는 Azure Portal을 사용하여 v3 리소스를 관리할 수 없습니다. [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref) 또는 지원되는 [SDK](developers-guide.md) 중 하나를 사용하세요.

### <a name="is-there-an-assetfile-concept-in-v3"></a>v3에 AssetFile 개념이 있나요?

Media Services를 Storage SDK 종속성과 분리하기 위해 AssetFiles가 AMS API에서 제거되었습니다. 이제 Media Services가 아닌 Storage에서 해당 Storage에 속한 정보를 보관합니다. 

자세한 내용은 [Media Services v3로 마이그레이션](migrate-from-v2-to-v3.md)을 참조하세요.

### <a name="where-did-client-side-storage-encryption-go"></a>클라이언트 쪽 저장소 암호화는 어디에 있나요?

이제 서버 쪽 스토리지 암호화(기본적으로 설정됨)를 사용하는 것이 좋습니다. 자세한 내용은 [미사용 데이터에 대한 Azure Storage 서비스 암호화](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)를 참조하세요.

## <a name="next-steps"></a>다음 단계

[Media Services v3 개요](media-services-overview.md)
