---
title: 콘텐츠 보호 마이그레이션 지침
description: 이 문서는 Azure Media Services v 2에서 v 2로의 마이그레이션에 도움이 되는 콘텐츠 보호 시나리오 기반 지침을 제공 합니다.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 9c0040c0ad34b019eaa90bbd1571377ad3539578
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98940122"
---
# <a name="content-protection-scenario-based-migration-guidance"></a>콘텐츠 보호 시나리오 기반 마이그레이션 지침

![마이그레이션 가이드 로고](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![마이그레이션 2 단계](./media/migration-guide/steps-4.svg)

이 문서는 Azure Media Services v 2에서 v 2로의 마이그레이션에 도움이 되는 콘텐츠 보호 시나리오 기반 지침을 제공 합니다.

## <a name="protect-content-in-v3-api"></a>V3 API에서 콘텐츠 보호

새 v3 API에서 [다중 키](design-multi-drm-system-with-access-control.md) 기능에 대 한 지원을 사용 합니다.

특정 단계는 콘텐츠 보호 개념, 자습서 및 아래 가이드를 참조 하세요.

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

## <a name="next-steps"></a>다음 단계

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]