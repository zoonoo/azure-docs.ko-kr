---
title: Azure Front 도어 표준/프리미엄 SKU 비교
description: 이 문서에서는 Azure Front 도어 Standard 및 Premium SKU에 대 한 개요와 이러한 기능 간의 기능 차이를 제공 합니다.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: amsriva
ms.openlocfilehash: 0f5ecef1716a503b7d27d0f5cdde15a4560c0e61
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101100248"
---
# <a name="overview-of-azure-front-door-standardpremium-sku-preview"></a>Azure 전면 도어 표준/프리미엄 SKU 개요 (미리 보기)

> [!Note]
> 이 설명서는 Azure Front 도어 Standard/Premium (미리 보기)에 대 한 것입니다. Azure Front 문에 대 한 정보를 찾고 있나요? [여기](../front-door-overview.md)에서 봅니다.

Azure Front 도어가 3 개의 다른 Sku, [Azure Front 도어](../front-door-overview.md), Azure Front 도어 Standard (미리 보기) 및 Azure Front 도어 프리미엄 (미리 보기)에 대해 제공 됩니다. Azure Front 도어 Standard/Premium Sku는 Azure 프런트 도어의 기능, Microsoft Azure WAF의 기능 Azure CDN을 지능형 위협 보호를 갖춘 단일 보안 클라우드 CDN 플랫폼으로 결합 합니다.

> [!IMPORTANT]
> Azure 전면 도어 표준/프리미엄 (미리 보기)은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [**Microsoft Azure Preview에 대한 추가 사용 약관**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

* **Azure Front 도어 표준 SKU** 는 다음과 같습니다.

    * 콘텐츠 배달 최적화
    * 정적 및 동적 콘텐츠 가속 모두 제공
    * 전역 부하 분산
    * SSL 오프 로드
    * 도메인 및 인증서 관리
    * 향상 된 트래픽 분석 
    * 기본 보안 기능

* **Azure Front 도어 PREMIUM sku** 는 표준 SKU의 기능을 기반으로 하며 다음을 추가 합니다.

    * WAF를 통한 광범위 한 보안 기능
    * BOT 보호
    * 개인 링크 지원
    * Microsoft 위협 인텔리전스 및 보안 분석과의 통합. 

![Front 도어 Sku 간의 비교를 보여 주는 다이어그램](../media/tier-comparison/tier-comparison.png)

## <a name="feature-comparison"></a>기능 비교

| 기능 |      Standard      |  Premium |
|----------|:-------------:|------:|
| 사용자 지정 도메인 | 예 | 예 |
| SSL 오프 로드 | 예 | 예 |
| 캐싱 |  예  | 예 |
| 압축 | 예 | 예   |
| 전역 부하 분산 | 예  | 예 |
| 계층 7 라우팅 | 예 | 예 |
| URL 재작성 | 예 | 예 |
| 규칙 엔진 | 예 | 예 |
| 개인 원본 (개인 링크) | 예 | 예 |
| WAF | 예 | 예 |
| Bot 보호 | 예 | 예 |
| 향상 된 메트릭 및 진단 | 예 | 예 |
| 트래픽 보고서 | 예 | 예 |
| 보안 보고서 | 예 | 예 | 

## <a name="next-steps"></a>다음 단계

[프런트 도어를 만드는](create-front-door-portal.md) 방법 알아보기
