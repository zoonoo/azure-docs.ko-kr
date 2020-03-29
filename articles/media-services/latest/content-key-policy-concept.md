---
title: Media Services의 콘텐츠 키 정책 - Azure | Microsoft Docs
description: 이 문서에서는 콘텐츠 키 정책의 개념과 Azure Media Services에서 이러한 정책을 사용하는 방법을 설명합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/26/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 7ddef1e78b4f8f62145e10b4cabc4537e28aba2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74969907"
---
# <a name="content-key-policies"></a>콘텐츠 키 정책

Microsoft Azure Media Services를 사용하면 Advanced Encryption Standard (AES-128) 또는 Microsoft PlayReady, Google Widevine 및 Apple FairPlay 등 세 가지 주요 DRM(디지털 권한 관리) 시스템 중 하나로 동적 암호화된 라이브 및 주문형 콘텐츠를 제공할 수 있습니다. 또한 Media Services는 인증된 클라이언트에게 AES 키 및DRM(PlayReady, Widevine 및 FairPlay) 라이선스를 배달하는 서비스를 제공합니다. 

스트림에서 암호화 옵션을 지정하려면 [스트리밍 정책을](streaming-policy-concept.md) 만들고 [스트리밍 로케이터](streaming-locators-concept.md)에 연결해야 합니다. [콘텐츠 키 정책을](https://docs.microsoft.com/rest/api/media/contentkeypolicies) 만들어 [자산에](assets-concept.md)대한 보안 액세스를 제공하는 콘텐츠 키가 최종 클라이언트에 전달되는 방식을 구성합니다. 지정된 구성의 키가 클라이언트에 전달되려면 충족해야 하는 콘텐츠 키 정책에 대한 요구 사항(제한)을 설정해야 합니다. 명확한 스트리밍 또는 다운로드에는 콘텐츠 키 정책이 필요하지 않습니다. 

일반적으로 콘텐츠 키 정책을 스트리밍 [로케이터](streaming-locators-concept.md)와 연결합니다. 또는 고급 시나리오에 대한 사용자 지정 스트리밍 정책을 만들 때 [스트리밍 정책](streaming-policy-concept.md) 내에서 콘텐츠 키 정책을 지정할 수 있습니다. 

## <a name="best-practices-and-considerations"></a>모범 사례 및 고려 사항

> [!IMPORTANT]
> 다음 권장 사항을 검토하십시오.

* Media Service 계정에 대한 제한된 정책 집합을 디자인하고 동일한 옵션이 필요할 때마다 스트리밍 로케이터에 다시 사용해야 합니다. 자세한 내용은 [할당량 및 제한 사항](limits-quotas-constraints.md)을 참조하세요.
* 콘텐츠 키 정책은 업데이터로 설정됩니다. 키 배달 캐시가 업데이트되고 업데이트된 정책을 선택하는 데 최대 15분이 걸릴 수 있습니다. 

   정책을 업데이트하면 캐시된 콘텐츠를 사용하는 고객에게 재생 문제가 발생할 수 있는 기존 CDN 캐시를 덮어쓰게 됩니다.  
* 각 자산에 대해 새 콘텐츠 키 정책을 만들지 않는 것이 좋습니다. 동일한 정책 옵션이 필요한 자산 간에 동일한 콘텐츠 키 정책을 공유하는 주요 이점은 다음과 같습니다.
   
   * 소수의 정책을 관리하는 것이 더 쉽습니다.
   * 콘텐츠 키 정책을 업데이트해야 하는 경우 변경 사항은 거의 즉시 모든 새 라이선스 요청에 적용됩니다.
* 새 정책을 만들어야 하는 경우 자산에 대한 새 스트리밍 로케이터를 만들어야 합니다.
* 미디어 서비스에서 콘텐츠 키를 자동으로 생성하도록 하는 것이 좋습니다. 

   일반적으로 수명이 긴 키를 사용하고 [Get](https://docs.microsoft.com/rest/api/media/contentkeypolicies/get)을 사용하여 콘텐츠 키 정책이 있는지 확인합니다. 키를 가져오려면 별도의 동작 메서드를 호출하여 비밀 또는 자격 증명을 가져와야 합니다. 아래 예제를 참조하세요.

## <a name="example"></a>예제

키로 이동하려면 기존 `GetPolicyPropertiesWithSecretsAsync` [정책 예제에서 서명 키 를 가져옵니다에](get-content-key-policy-dotnet-howto.md#get-contentkeypolicy-with-secrets) 표시된 대로 에서 를 사용합니다.

## <a name="filtering-ordering-paging"></a>필터링, 정렬, 페이징

[Media Services 엔터티 필터링, 순서 지정, 페이징](entities-overview.md)을 참조하세요.

## <a name="additional-notes"></a>추가적인 참고 사항

* `Datetime` 형식의 콘텐츠 키 정책의 속성은 항상 UTC 형식으로 되어 있습니다.
* Widevine은 Google Inc.에서 제공하는 서비스로, Google Inc.의 서비스 약관 및 개인정보처리방침을 따릅니다.

## <a name="next-steps"></a>다음 단계

* [AES-128 동적 암호화 및 키 전달 서비스 사용](protect-with-aes128.md)
* [DRM 동적 암호화 및 라이선스 배달 서비스 사용](protect-with-drm.md)
* [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted)
