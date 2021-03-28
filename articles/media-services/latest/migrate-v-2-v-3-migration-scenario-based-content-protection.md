---
title: 콘텐츠 보호 마이그레이션 지침
description: 이 문서에서는 Azure Media Services v 2에서 v3로 마이그레이션하는 데 도움이 되는 콘텐츠 보호 시나리오 기반 지침을 제공 합니다.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/26/2021
ms.author: inhenkel
ms.openlocfilehash: 7ef41b76f343d8997feebc4a366deda7ce6a2afa
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644066"
---
# <a name="content-protection-scenario-based-migration-guidance"></a>콘텐츠 보호 시나리오 기반 마이그레이션 지침

![마이그레이션 가이드 로고](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![마이그레이션 2 단계](./media/migration-guide/steps-4.svg)

이 문서에서는 v2 API에서 새 Azure Media Services v3 API로 콘텐츠 보호 사용 사례를 마이그레이션하는 방법에 대 한 세부 정보 및 지침을 제공 합니다.

## <a name="protect-content-in-v3-api"></a>V3 API에서 콘텐츠 보호

새 v3 API에서 [다중 키](design-multi-drm-system-with-access-control.md) 기능에 대 한 지원을 사용 합니다.

특정 단계는 콘텐츠 보호 개념, 자습서 및 아래 가이드를 참조 하세요.

## <a name="visibility-of-v2-assets-streaminglocators-and-properties-in-the-v3-api-for-content-protection-scenarios"></a>콘텐츠 보호 시나리오에 대 한 v3 API의 v2 자산, StreamingLocators 및 속성의 표시 여부

V3 API로 마이그레이션하는 동안 v2 자산에서 일부 속성 또는 콘텐츠 키에 액세스 해야 하는 것을 알 수 있습니다. 한 가지 주요 차이점은 v2 API는 기본 id 키로 **AssetId** 를 사용 하 고 새 v3 api는 엔터티의 Azure 리소스 관리 이름을 기본 식별자로 사용 한다는 것입니다.  V2 **Asset.Name** 속성은 일반적으로 고유 식별자로 사용 되지 않으므로 v3로 마이그레이션할 때 이제 v2 자산 이름이 자산에 표시 되는 것을 확인할 수 있습니다 **. 설명** 필드.

예를 들어 이전에 ID가 **"nb: cid: UUID: 8cb39104-122c-496e-9ac5-7f9e2c2547b8"** 인 v2 자산이 있는 경우 v3 API를 통해 이전 v2 자산을 나열할 때 이름이 끝에 있는 GUID 부분 (이 경우 **"8cb39104-122c-496e-9ac5-7f9e2c2547b8"**)이 됩니다.

Asset 엔터티에서 새로운 v3 메서드 [Liststreaminglocators](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators) 를 사용 하 여 v2 API에서 만든 자산과 연결 된 **streaminglocators** 를 쿼리할 수 있습니다.  또한 [ListStreamingLocatorsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocatorsasync?view=azure-dotnet) 의 .NET 클라이언트 SDK 버전을 참조 합니다.

**Liststreaminglocators** 메서드의 결과는 **StreamingPolicyName** 와 함께 로케이터의 **이름** 및 **streaminglocidid** 를 제공 합니다.

콘텐츠 보호를 위해 **streaminglocators** 에서 사용 되는 **contentkeys** 를 찾으려면 [ListContentKeysAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.streaminglocatorsoperationsextensions.listcontentkeysasync?view=azure-dotnet) 메서드를 호출 하면 됩니다.  

V2 API를 사용 하 여 생성 및 게시 된 모든 **자산** 에는 [스트리밍 정책](https://docs.microsoft.com/azure/media-services/latest/streaming-policy-concept)에 대 한 기본 콘텐츠 키 정책을 사용 하는 대신, V3 Api에서 [콘텐츠 키 정책과](https://docs.microsoft.com/azure/media-services/latest/content-key-policy-concept) 콘텐츠 키가 정의 됩니다.

V3 API의 콘텐츠 보호에 대 한 자세한 내용은 [Media Services 동적 암호화를 사용 하 여 콘텐츠 보호](https://docs.microsoft.com/azure/media-services/latest/content-protection-overview) 문서를 참조 하세요.

## <a name="how-to-list-your-v2-assets-and-content-protection-settings-using-the-v3-api"></a>V3 API를 사용 하 여 v2 자산 및 콘텐츠 보호 설정을 나열 하는 방법

V2 API에서는 일반적으로 **자산**, **Streaminglocators** 및 **contentkeys** 를 사용 하 여 스트리밍 콘텐츠를 보호 합니다.
V3 api로 마이그레이션할 때 v2 api 자산, StreamingLocators 및 ContentKeys는 모두 v3 API에서 자동으로 노출 되며이에 대 한 모든 데이터를 액세스할 수 있습니다.

## <a name="can-i-update-v2-properties-using-the-v3-api"></a>V3 API를 사용 하 여 v2 속성을 업데이트할 수 있나요?

아니요, v 2의 StreamingLocators, Streaminglocators, 콘텐츠 키 정책 및 콘텐츠 키를 사용 하 여 만든 v3 API를 통해 v2 엔터티에 대 한 속성을 업데이트할 수 없습니다.
V2 엔터티에 저장 된 콘텐츠를 업데이트, 변경 또는 변경 해야 하는 경우 v2 API를 통해 업데이트 하거나 새 v3 API 엔터티를 만들어 앞으로 마이그레이션해야 합니다.

## <a name="how-do-i-change-the-contentkeypolicy-used-for-a-v2-asset-that-is-published-and-keep-the-same-content-key"></a>게시 되 고 동일한 콘텐츠 키를 유지 하는 v2 자산에 사용 되는 ContentKeyPolicy 변경 어떻게 할까요??

이 경우 먼저 v2 SDK를 통해 자산에서 모든 스트리밍 로케이터를 게시 취소 (로케이터 삭제, 콘텐츠 키 권한 부여 정책 연결 해제, 자산 배달 정책 연결 해제, 콘텐츠 키 연결 해제, 콘텐츠 키 삭제, v3 [Streaminglocator](https://docs.microsoft.com/azure/media-services/latest/streaming-policy-concept) 및 [ContentKeyPolicy](https://docs.microsoft.com/azure/media-services/latest/content-key-policy-concept)를 사용 하 여 V3에서 새 **[streaminglocator](https://docs.microsoft.com/azure/media-services/latest/streaming-locators-concept)** 만들기) 해야 합니다.

**[Streaminglocator](https://docs.microsoft.com/azure/media-services/latest/streaming-locators-concept)** 를 만들 때 필요한 특정 콘텐츠 키 식별자 및 키 값을 지정 해야 합니다.

V3 API를 사용 하 여 v2 로케이터를 삭제할 수는 있지만이 경우 v2 API에서 만든 콘텐츠 키 또는 콘텐츠 키 정책이 제거 되지 않습니다.  

## <a name="using-amse-v2-and-amse-v3-side-by-side"></a>AMSE v2 및 AMSE v3 사용

V 2에서 v3로 콘텐츠를 마이그레이션하는 경우 v2 Api를 통해 생성 및 게시 되는 자산에 대해 함께 표시 되는 데이터를 비교 하기 위해 [v3 Azure Media Services 탐색기 도구](https://github.com/Azure/Azure-Media-Services-Explorer) 와 함께 [v2 Azure Media Services 탐색기 도구](https://github.com/Azure/Azure-Media-Services-Explorer/releases/tag/v4.3.15.0) 를 설치 하는 것이 좋습니다. 속성은 모두 표시 되지만 지금은 약간 다른 위치에 있습니다.  


## <a name="content-protection-concepts-tutorials-and-how-to-guides"></a>콘텐츠 보호 개념, 자습서 및 방법 가이드

### <a name="concepts"></a>개념

- [Media Services 동적 암호화를 사용 하 여 콘텐츠 보호](content-protection-overview.md)
- [액세스 제어가 포함된 다중 DRM 콘텐츠 보호 시스템 설계](design-multi-drm-system-with-access-control.md)
- [PlayReady 라이선스 템플릿을 사용 하 여 v3 Media Services](playready-license-template-overview.md)
- [Widevine 사용 하는 Media Services v3 라이선스 템플릿 개요](widevine-license-template-overview.md)
- [Apple FairPlay 라이선스 요구 사항 및 구성](fairplay-license-overview.md)
- [스트리밍 정책](streaming-policy-concept.md)
- [콘텐츠 키 정책](content-key-policy-concept.md)

### <a name="tutorials"></a>자습서

[빠른 시작: 포털을 사용한 콘텐츠 암호화](encrypt-content-quickstart.md)

### <a name="how-to-guides"></a>방법 가이드

- [기존 정책에서 서명 키 가져오기](get-content-key-policy-dotnet-howto.md)
- [Media Services v3를 사용 하는 iOS 용 오프 라인 FairPlay 스트리밍](offline-fairplay-for-ios.md)
- [Media Services v3을 사용 하 여 Android 용 오프 라인 Widevine 스트리밍](offline-widevine-for-android.md)
- [Media Services v3를 사용 하는 Windows 10 용 오프 라인 PlayReady 스트리밍](offline-plaready-streaming-for-windows-10.md)

## <a name="samples"></a>샘플

[코드 샘플에서 V2 및 V3 코드도 비교할](migrate-v-2-v-3-migration-samples.md)수 있습니다.

## <a name="tools"></a>도구

- [v3 Azure Media Services 탐색기 도구](https://github.com/Azure/Azure-Media-Services-Explorer)
- [v2 Azure Media Services 탐색기 도구](https://github.com/Azure/Azure-Media-Services-Explorer/releases/tag/v4.3.15.0)
