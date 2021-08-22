---
title: Media Services의 콘텐츠 키 정책 - Azure
description: 이 문서에서는 콘텐츠 키 정책의 개념과 Azure Media Services에서 이러한 정책을 사용하는 방법을 설명합니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: d8bff86065e4849723ab4001bd19218b89a2c8a2
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2021
ms.locfileid: "122633733"
---
# <a name="content-key-policies"></a>콘텐츠 키 정책

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Media Services를 사용하면 Advanced Encryption Standard(AES-128) 또는 Microsoft PlayReady, Google Widevine 및 Apple FairPlay 등 세 가지 주요 DRM(디지털 권한 관리) 시스템 중 하나로 동적 암호화된 라이브 콘텐츠 및 주문형 콘텐츠를 제공할 수 있습니다. 또한 Media Services는 인증된 클라이언트에게 AES 키 및DRM(PlayReady, Widevine 및 FairPlay) 라이선스를 배달하는 서비스를 제공합니다. 

스트림에 대한 암호화 옵션을 지정하려면 [스트리밍 정책](stream-streaming-policy-concept.md)을 만들고 해당 정책을 [스트리밍 로케이터](stream-streaming-locators-concept.md)에 연결해야 합니다. [콘텐츠 키 정책](/rest/api/media/contentkeypolicies)을 만들어 [자산](assets-concept.md)에 대한 보안 액세스를 제공하는 콘텐츠 키가 최종 클라이언트에 전달되는 방식을 구성합니다. 키를 지정된 구성을 사용하여 클라이언트에게 전달하기 위해 충족되어야 하는 콘텐츠 키 정책에 대한 요구 사항(제한)을 설정해야 합니다. 콘텐츠 키 정책은 투명한 스트리밍 또는 다운로드에 필요하지 않습니다. 

일반적으로 콘텐츠 키 정책을 [스트리밍 로케이터](stream-streaming-locators-concept.md)와 연결합니다. 또는 고급 시나리오에 대한 사용자 지정 스트리밍 정책을 만들 때 [스트리밍 정책](stream-streaming-policy-concept.md) 내에서 콘텐츠 키 정책을 지정할 수 있습니다. 

## <a name="best-practices-and-considerations"></a>모범 사례 및 고려 사항

> [!IMPORTANT]
> 다음 권장 사항을 검토하세요.

* Media Services 계정에 대한 제한된 정책 세트를 설계하고 동일한 옵션이 필요할 때마다 스트리밍 로케이터에 해당 세트를 다시 사용하는 것이 좋습니다. 자세한 내용은 [할당량 및 한도](limits-quotas-constraints-reference.md)를 참조하세요.
* 콘텐츠 키 정책은 업데이트할 수 있습니다. 키 전달 캐시를 업데이트하고 업데이트된 정책을 선택하는 데 최대 15분까지 걸릴 수 있습니다. 

   정책을 업데이트하여 캐시된 콘텐츠를 사용하는 고객에게 재생 문제를 일으킬 수 있는 기존 CDN 캐시를 덮어씁니다.  
* 각 자산에 대해 새 콘텐츠 키 정책을 만들지 않는 것이 좋습니다. 동일한 정책 옵션이 필요한 자산 간에 동일한 콘텐츠 키 정책을 공유할 경우의 주요 이점은 다음과 같습니다.
   
   * 적은 수의 정책을 관리하는 것이 더 쉽습니다.
   * 콘텐츠 키 정책에 대한 업데이트를 수행해야 하는 경우 변경 내용이 모든 새 라이선스 요청에 거의 바로 적용됩니다.
* 새 정책을 만들어야 하는 경우 자산에 대해 새 스트리밍 로케이터를 만들어야 합니다.
* Media Services에서 콘텐츠 키를 자동으로 생성하도록 하는 것이 좋습니다. 

   일반적으로 수명이 긴 키를 사용하고 [Get](/rest/api/media/contentkeypolicies/get)을 사용하여 콘텐츠 키 정책이 있는지 확인합니다. 키를 가져오려면 별도의 동작 메서드를 호출하여 비밀 또는 자격 증명을 가져와야 합니다. 아래 예제를 참조하세요.

## <a name="example"></a>예제

키를 가져오려면 [기존 정책에서 서명 키 가져오기](drm-get-content-key-policy-dotnet-how-to.md#get-contentkeypolicy-with-secrets) 예제에 표시된 대로 `GetPolicyPropertiesWithSecretsAsync`를 사용합니다.

## <a name="filtering-ordering-paging"></a>필터링, 정렬, 페이징

[Media Services 엔터티 필터링, 순서 지정, 페이징](filter-order-page-entities-how-to.md)을 참조하세요.

## <a name="additional-notes"></a>추가적인 참고 사항

* `Datetime` 형식의 콘텐츠 키 정책 속성은 언제나 UTC 형식입니다.
* Widevine은 Google Inc.에서 제공하는 서비스로, Google Inc.의 서비스 약관 및 개인정보처리방침을 따릅니다.

## <a name="next-steps"></a>다음 단계

* [AES-128 동적 암호화 및 키 전달 서비스 사용](drm-playready-license-template-concept.md)
* [DRM 동적 암호화 및 라이선스 배달 서비스 사용](drm-protect-with-drm-tutorial.md)
* [기본 AES 암호화되지 않은 키 암호화 및 스트리밍 샘플 코드](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicAESClearKey)
