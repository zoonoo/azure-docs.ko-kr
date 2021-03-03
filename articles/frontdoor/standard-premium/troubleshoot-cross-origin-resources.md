---
title: 크로스-원본 리소스 공유에 Azure Front 도어 표준/프리미엄 사용
description: CORS (크로스-원본 자원 공유)를 사용 하 여 Azure Front 도어 (AFD)를 사용 하는 방법에 대해 알아봅니다.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: ee8f19aca62d2e331fcf59551d47c2dac93783b1
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101099368"
---
# <a name="using-azure-front-door-standardpremium-with-cross-origin-resource-sharing-cors"></a>CORS (원본 간 리소스 공유)에서 Azure Front 도어 Standard/Premium 사용

> [!Note]
> 이 설명서는 Azure Front 도어 Standard/Premium (미리 보기)에 대 한 것입니다. Azure Front 문에 대 한 정보를 찾고 있나요? [여기](../front-door-overview.md)에서 봅니다.

## <a name="what-is-cors"></a>CORS는 무엇인가요?

CORS(크로스 원본 자원 공유)는 특정 도메인에서 실행되는 웹 애플리케이션이 다른 도메인의 자원에 액세스할 수 있도록 하는 HTTP 기능입니다. 사이트 간 스크립팅 공격의 가능성을 줄이기 위해 모든 최신 웹 브라우저는 [동일 원본 정책](https://www.w3.org/Security/wiki/Same_Origin_Policy)이라는 보안 제한을 구현 합니다. 이 경우 웹 페이지는 다른 도메인의 API를 호출할 수 없습니다.  CORS는 한 도메인(원본 도메인)에서 다른 도메인의 API를 호출할 수 있는 안전한 방법을 제공합니다.

> [!IMPORTANT]
> Azure 전면 도어 표준/프리미엄 (미리 보기)은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="how-it-works"></a>작동 방법

CORS 요청에는 *간단한 요청* 과 *복잡한 요청* 의 두 가지 유형이 있습니다.

### <a name="for-simple-requests"></a>간단한 요청:

1. 브라우저는 다른 **원본** HTTP 요청 헤더를 사용 하 여 CORS 요청을 보냅니다. 이 헤더의 값은 부모 페이지를 제공한 원본입니다. 이 값은 *프로토콜* *도메인* 및 *포트* 로 정의됩니다.  Https \: //www.contoso.com의 페이지가 fabrikam.com 원본의 사용자 데이터에 액세스 하려고 하면 다음 요청 헤더가 fabrikam.com으로 전송 됩니다.

   `Origin: https://www.contoso.com`

2. 서버는 다음으로 응답할 수 있습니다.

   * 허용되는 원본 사이트를 나타내는 응답의 **Access-Control-Allow-Origin** 헤더 다음은 그 예입니다. 

     `Access-Control-Allow-Origin: https://www.contoso.com`

   * 원본 헤더를 확인 한 후 서버에서 원본 간 요청을 허용 하지 않는 경우 403와 같은 HTTP 오류 코드

   * 모든 원본을 허용하는 와일드카드를 사용한 **Access-Control-Allow-Origin** 헤더

     `Access-Control-Allow-Origin: *`

### <a name="for-complex-requests"></a>복잡한 요청:

복잡한 요청은 실제 CORS 요청을 전송하기 전에 브라우저가 *실행 전 요청*(즉, 예비 프로브)을 전송해야 하는 CORS 요청입니다. 원본 CORS 요청을 계속할 수 있고 `OPTIONS` 동일한 URL에 대 한 요청인 경우 실행 전 요청에서 서버 사용 권한을 요청 합니다.

> [!TIP]
> CORS 흐름 및 일반적인 문제에 대한 자세한 내용은 [REST API에 대한 CORS 가이드](https://www.moesif.com/blog/technical/cors/Authoritative-Guide-to-CORS-Cross-Origin-Resource-Sharing-for-REST-APIs/)를 참조하세요.
>
>

## <a name="wildcard-or-single-origin-scenarios"></a>와일드카드 또는 단일 원본 시나리오
**액세스 제어-허용-원본** 헤더가 와일드 카드 (*) 또는 단일 원본으로 설정 된 경우 Azure Front 도어의 CORS는 추가 구성 없이 자동으로 작동 합니다.  Azure 전면 도어는 첫 번째 응답을 캐시 하 고 결과 요청은 동일한 헤더를 사용 합니다.

원본에 CORS를 설정 하기 전에 Azure Front 문이 이미 요청 되었으면 끝점 콘텐츠에서 콘텐츠를 제거 하 여 **액세스 제어 허용 원본** 헤더를 사용 하 여 콘텐츠를 다시 로드 해야 합니다.

## <a name="multiple-origin-scenarios"></a>여러 원본 시나리오
특정 목록의 원본이 CORS에 대해 허용되도록 해야 할 경우 문제가 좀 더 복잡해집니다. CDN이 첫 번째 CORS 원본에 대해 **Access-Control-Allow-Origin** 헤더를 캐시할 때 이러한 문제가 발생합니다.  다른 CORS 원본이 다른 요청을 수행 하면 CDN은 캐시 된 **액세스 제어 허용 원본** 헤더를 제공 합니다 .이 헤더는 일치 하지 않습니다. 여러 가지 방법으로이 문제를 해결할 수 있습니다.

### <a name="azure-front-door-rule-set"></a>Azure Front 도어 규칙 집합

Azure Front 도어의 Azure Front 도어 [규칙 집합](concept-rule-set.md) 에서 요청에 대 한 **원본** 헤더를 확인 하는 규칙을 만들 수 있습니다. 유효한 원본인 경우 규칙은 **액세스 제어-허용-원본** 헤더를 올바른 값으로 설정 합니다. 이 경우 파일의 원본 서버에 있는 **액세스 제어 허용 원본** 헤더가 무시 되 고 AFD의 규칙 엔진이 허용 된 CORS 원본을 완전히 관리 합니다.

:::image type="content" source="../media/troubleshooting-cross-origin-resource-sharing/cross-origin-resource.png" alt-text="규칙 집합을 사용 하는 규칙 예의 스크린샷":::

> [!TIP]
> 규칙에 추가 작업을 추가 하 여 **액세스 제어-허용 메서드와** 같은 추가 응답 헤더를 수정할 수 있습니다.
> 

## <a name="next-steps"></a>다음 단계

* [Front Door를 만드는](create-front-door-portal.md) 방법을 알아봅니다.
