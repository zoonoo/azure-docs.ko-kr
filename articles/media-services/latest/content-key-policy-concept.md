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
ms.date: 05/28/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: a597ab3519f4ba1696e111622541bcab89488558
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66425426"
---
# <a name="content-key-policies"></a>콘텐츠 키 정책

Media Services를 사용하면 Advanced Encryption Standard(AES-128) 또는 Microsoft PlayReady, Google Widevine 및 Apple FairPlay 등 세 가지 주요 DRM(디지털 권한 관리) 시스템 중 하나로 동적 암호화된 라이브 콘텐츠 및 주문형 콘텐츠를 제공할 수 있습니다. 또한 Media Services는 인증된 클라이언트에게 AES 키 및DRM(PlayReady, Widevine 및 FairPlay) 라이선스를 배달하는 서비스를 제공합니다. 

만들려는 프로그램 스트림에서 암호화 옵션을 지정 하려면를 [스트리밍 정책](streaming-policy-concept.md) 와 연결 프로그램 [스트리밍 로케이터](streaming-locators-concept.md)합니다. 만든를 [콘텐츠 키 정책](https://docs.microsoft.com/rest/api/media/contentkeypolicies) 구성 하려면 어떻게 콘텐츠 키 (에 대 한 보안 액세스를 제공 하는 프로그램 [자산](assets-concept.md)) 최종 클라이언트에 배달 됩니다. 클라이언트에 전달 되도록 지정 된 구성을 사용 하 여 키를 위해 충족 해야 하는 콘텐츠 키 정책에서 요구 사항 (제한)를 설정 하려면 필요 합니다. 이 콘텐츠 키 정책 지우기 스트리밍 또는 다운로드에 대 한 필요 하지 않습니다. 

연결 하는 일반적으로 프로그램 **콘텐츠 키 정책** 사용 하 여 프로그램 [스트리밍 로케이터](streaming-locators-concept.md)합니다. 내에서 콘텐츠 키 정책을 지정할 수는 또는 [스트리밍 정책](streaming-policy-concept.md) (경우에 대 한 사용자 지정 스트리밍 정책을 만드는 고급 시나리오). 

Media Services를 통해 콘텐츠 키 자동 생성 하는 것이 좋습니다. 일반적으로 장기적인된 키를 사용 하는 사용 하 여 정책 있는지 확인할 **가져올**합니다. 키를 가져오려면 별도의 동작 메서드를 호출하여 비밀 또는 자격 증명을 가져와야 합니다. 아래 예제를 참조하세요.

**콘텐츠 키 정책**은 업데이트할 수 있습니다. 키 배달 캐시를 업데이트하고 업데이트된 정책을 선택하는 데 최대 15분까지 걸릴 수 있습니다. 

> [!IMPORTANT]
> * 날짜/시간 형식의 **콘텐츠 키 정책** 속성은 언제나 UTC 형식입니다.
> * Media Services 계정에 대한 제한된 정책 세트를 설계하고 동일한 옵션이 필요할 때마다 스트리밍 로케이터에 해당 세트를 다시 사용하는 것이 좋습니다. 자세한 내용은 [할당량 및 제한 사항](limits-quotas-constraints.md)을 참조하세요.

### <a name="example"></a>예

키를 가져오려면 **GetPolicyPropertiesWithSecretsAsync**에 나와 있는 것 처럼 합니다 [기존 정책에서 서명 키를 가져온](get-content-key-policy-dotnet-howto.md#get-contentkeypolicy-with-secrets) 예제입니다.

## <a name="filtering-ordering-paging"></a>필터링, 정렬, 페이징

[Media Services 엔터티 필터링, 순서 지정, 페이징](entities-overview.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [AES-128 동적 암호화 및 키 전달 서비스 사용](protect-with-aes128.md)
* [DRM 동적 암호화 및 라이선스 배달 서비스 사용](protect-with-drm.md)
* [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted)
