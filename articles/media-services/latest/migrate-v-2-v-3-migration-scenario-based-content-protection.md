---
title: 콘텐츠 보호 마이그레이션 지침
description: 이 문서에서는 Azure Media Services v 2에서 v3으로 마이그레이션하는 데 도움이 되는 콘텐츠 보호 기반 시나리오 기반 지침을 제공합니다.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 04/05/2021
ms.author: inhenkel
ms.openlocfilehash: 59f74740117ba9f549133c4ca9bcb510928eb105
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108138880"
---
# <a name="content-protection-scenario-based-migration-guidance"></a>콘텐츠 보호 시나리오 기반 마이그레이션 지침

![마이그레이션 가이드 로고](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![마이그레이션 2단계](./media/migration-guide/steps-4.svg)

이 문서에서는 v2 API에서 새 Azure Media Services v3 API로 콘텐츠 보호 사용 사례를 마이그레이션하는 방법에 대한 세부 정보 및 지침을 제공합니다.

## <a name="protect-content-in-v3-api"></a>v3 API에서 콘텐츠 보호

새 v3 API에서 [다중 키](architecture-design-multi-drm-system.md) 기능에 대한 지원을 사용합니다.

구체적인 단계는 이 문서의 끝에 있는 콘텐츠 보호 개념, 자습서 및 방법 가이드를 참조하세요.

> [!NOTE]
> 이 문서의 나머지 부분에서는 .NET을 사용하여 v2 콘텐츠 보호를 v3으로 마이그레이션하는 방법에 대해 설명합니다.  다른 언어나 방법에 대한 지침 또는 샘플 코드가 필요한 경우 이 페이지에 대한 GitHub 문제를 만드세요.

## <a name="v3-visibility-of-v2-assets-streaminglocators-and-properties"></a>v2 자산, 스트리밍 로케이터 및 속성의 v3 표시 여부

v2 API에서 `Assets`, `StreamingLocators` 및 `ContentKeys`는 스트리밍 콘텐츠를 보호하는 데 사용되었습니다. v3 API로 마이그레이션할 때 v2 API `Assets`, `StreamingLocators` 및 `ContentKeys`는 모두 v3 API에 자동으로 노출되며 모든 데이터에 액세스할 수 있습니다.

그러나 v2에서 생성된 v2 항목의 속성은 v3 API를 통해 *업데이트* 할 수 없습니다.

v2 엔터티에 저장된 콘텐츠를 업데이트하거나 변경해야 하는 경우 v2 API를 사용하여 업데이트하거나 새 v3 API 엔터티를 만들어 마이그레이션해야 합니다.

## <a name="asset-identifier-differences"></a>자산 식별자 차이점

마이그레이션하려면 v2 자산에서 속성 또는 콘텐츠 키에 액세스해야 합니다.  v2 API는 `AssetId`를 기본 식별 키로 사용하지만 새로운 v3 API는 엔터티의 *Azure Resource Management 이름* 을 기본 식별자로 사용한다는 점을 이해하는 것이 중요합니다.  (v2 `Asset.Name` 속성은 고유 식별자로 사용되지 않습니다.) v3 API를 사용하면 v2 자산 이름이 이제 `Asset.Description`으로 표시됩니다.

예를 들어 이전에 ID가 `nb:cid:UUID:8cb39104-122c-496e-9ac5-7f9e2c2547b8`인 v2 자산이 있었다면 이제 식별자가 GUID `8cb39104-122c-496e-9ac5-7f9e2c2547b8` 끝에 있습니다. V3 API를 통해 v2 자산을 나열할 때 이를 확인할 수 있습니다.

v2 API를 사용하여 생성 및 게시된 모든 자산에는 `StreamingPolicy`의 기본 콘텐츠 키 정책 대신 v3 API의 `ContentKeyPolicy` 및 `ContentKey`가 있습니다.

자세한 내용은 [콘텐츠 키 정책](./drm-content-key-policy-concept.md) 문서 및 [스트리밍 정책](./stream-streaming-policy-concept.md) 문서를 참조하세요.

## <a name="use-azure-media-services-explorer-amse-v2-and-amse-v3-tools-side-by-side"></a>AMSE(Azure Media Services 탐색기) v2 및 AMSE v3 도구를 나란히 사용

[v3 Azure Media Services 탐색기 도구](https://github.com/Azure/Azure-Media-Services-Explorer)와 함께 [v2 Azure Media Services 탐색기 도구](https://github.com/Azure/Azure-Media-Services-Explorer/releases/tag/v4.3.15.0)를 사용하여 v2 API를 통해 생성 및 게시된 자산의 데이터를 나란히 비교할 수 있습니다. 속성은 모두 다른 위치에 표시되어야 합니다.

## <a name="use-the-net-content-protection-migration-sample"></a>.NET 콘텐츠 보호 마이그레이션 샘플 사용

Media Services 코드 샘플의 ContentProtection에서 [v2tov3MigrationSample](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/v2tov3Migration)을 사용하여 자산 식별자의 차이점을 비교하는 코드 샘플을 찾을 수 있습니다.

## <a name="list-the-streaming-locators"></a>스트리밍 로케이터 나열

자산 항목에서 새로운 v3 메서드 [ListStreamingLocators](/rest/api/media/assets/liststreaminglocators)를 사용하여 v2 API에서 생성된 자산과 연결된 `StreamingLocators`를 쿼리할 수 있습니다.  [ListStreamingLocatorsAsync](/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocatorsasync?preserve-view=true&view=azure-dotnet)의 .NET 클라이언트 SDK 버전도 참조하세요.

`ListStreamingLocators` 메서드의 결과는 `StreamingPolicyName`과 함께 로케이터의 `Name` 및 `StreamingLocatorId`를 제공합니다.

## <a name="find-the-content-keys"></a>콘텐츠 키 찾기

`StreamingLocators`와 함께 사용된 `ContentKeys`를 찾으려면 [StreamingLocator.ListContentKeysAsync](/dotnet/api/microsoft.azure.management.media.streaminglocatorsoperationsextensions.listcontentkeysasync?preserve-view=true&view=azure-dotnet) 메서드를 호출하면 됩니다.  

v3 API의 콘텐츠 보호에 대한 자세한 내용은 [Media Services 동적 암호화를 사용하여 콘텐츠 보호](./drm-content-protection-concept.md) 문서를 참조하세요.

## <a name="change-the-v2-contentkeypolicy-keeping-the-same-contentkey"></a>동일한 ContentKey를 유지하는 v2 ContentKeyPolicy 변경

먼저 v2 SDK를 통해 자산의 게시를 취소(모든 스트리밍 로케이터 제거)해야 합니다. 방법은 다음과 같습니다.

1. 로케이터를 삭제합니다.
1. `ContentKeyAuthorizationPolicy`의 연결을 해제합니다.
1. `AssetDeliveryPolicy`의 연결을 해제합니다.
1. `ContentKey`의 연결을 해제합니다.
1. `ContentKey`를 삭제합니다.
1. v3 `StreamingPolicy` 및 `ContentKeyPolicy`를 사용하여 v3에서 새 `StreamingLocator`를 생성하고 필요한 특정 콘텐츠 키 식별자와 키 값을 지정합니다.

> [!NOTE]
> v3 API를 사용하여 v2 로케이터를 삭제할 수 있지만, v2 API에서 만들어진 콘텐츠 키 또는 콘텐츠 키 정책은 제거되지 않습니다.

## <a name="content-protection-concepts-tutorials-and-how-to-guides"></a>콘텐츠 보호 개념, 자습서 및 방법 가이드

### <a name="concepts"></a>개념

- [Media Services 동적 암호화를 사용하여 콘텐츠 보호](drm-content-protection-concept.md)
- [액세스 제어가 포함된 다중 DRM 콘텐츠 보호 시스템 설계](architecture-design-multi-drm-system.md)
- [PlayReady 라이선스 템플릿을 사용하는 Media Services v3](drm-playready-license-template-concept.md)
- [Widevine 라이선스 템플릿을 사용하는 Media Services v3 개요](drm-widevine-license-template-concept.md)
- [Apple FairPlay 라이선스 요구 사항 및 구성](drm-fairplay-license-overview.md)
- [스트리밍 정책](stream-streaming-policy-concept.md)
- [콘텐츠 키 정책](drm-content-key-policy-concept.md)

### <a name="tutorials"></a>자습서

[빠른 시작: 포털을 사용한 콘텐츠 암호화](drm-encrypt-content-how-to.md)

### <a name="how-to-guides"></a>방법 가이드

- [기존 정책에서 서명 키 가져오기](drm-get-content-key-policy-dotnet-how-to.md)
- [Media Services v3를 사용하는 iOS용 오프라인 FairPlay 스트리밍](drm-offline-fairplay-for-ios-concept.md)
- [Media Services v3를 사용하는 Android용 오프라인 Widevine 스트리밍](drm-offline-widevine-for-android.md)
- [Media Services v3를 사용하는 Windows 10용 오프라인 PlayReady 스트리밍](drm-offline-playready-streaming-for-windows-10.md)

## <a name="samples"></a>샘플

- [v2tov3MigrationSample](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/v2tov3Migration)
- [코드 샘플에서 V2와 V3 코드를 비교](migrate-v-2-v-3-migration-samples.md)할 수도 있습니다.

## <a name="tools"></a>도구

- [v3 Azure Media Services 탐색기 도구](https://github.com/Azure/Azure-Media-Services-Explorer)
- [v2 Azure Media Services 탐색기 도구](https://github.com/Azure/Azure-Media-Services-Explorer/releases/tag/v4.3.15.0)