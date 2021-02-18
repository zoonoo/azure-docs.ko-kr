---
title: Azure 전면 도어 표준/프리미엄 (미리 보기)의 캐시 제거
description: 이 문서는 Azure Front 도어 Standard/Premium에서 캐시를 제거 하는 방법을 이해 하는 데 도움이 됩니다.
services: frontdoor
author: duongau
manager: KumudD
ms.service: frontdoor
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: aacbf2ceab8580727b1885bf6533cd74a7c4e60a
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101099812"
---
# <a name="cache-purging-in-azure-front-door-standardpremium-preview"></a>Azure 전면 도어 표준/프리미엄 (미리 보기)의 캐시 제거

> [!Note]
> 이 설명서는 Azure Front 도어 Standard/Premium (미리 보기)에 대 한 것입니다. Azure Front 문에 대 한 정보를 찾고 있나요? [여기](../front-door-overview.md)에서 봅니다.

Azure 전면 도어 표준/프리미엄은 자산의 TTL (time-to-live)이 만료 될 때까지 자산을 캐시 합니다. 클라이언트에서 TTL이 만료 된 자산을 요청할 때마다 Azure Front 도어 환경에서는 요청을 처리 하는 새로운 자산의 업데이트 된 복사본을 검색 한 후 새로 고친 캐시를 저장 합니다.

사용자가 항상 자산의 최신 복사본을 얻을 수 있도록 하는 것이 가장 좋습니다. 이 작업을 수행 하는 방법은 각 업데이트에 대 한 자산을 버전으로 만들고 새 Url로 게시 하는 것입니다. Azure Front 도어 Standard/Premium은 다음 클라이언트 요청에 대 한 새 자산을 즉시 검색 합니다. 경우에 따라 모든에 지 노드에서 캐시 된 콘텐츠를 제거 하 고 새로운 업데이트 된 자산을 검색 하도록 강제할 수도 있습니다. 캐시 된 콘텐츠를 제거 하는 이유는 응용 프로그램에 대 한 새 업데이트를 수행 했거나 잘못 된 정보를 포함 하는 자산을 업데이트 하려고 하기 때문입니다.

> [!IMPORTANT]
> Azure 전면 도어 표준/프리미엄 (미리 보기)은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

[Azure Front 도어 캐싱을](concept-caching.md) 검토 하 여 캐싱의 작동 방식을 이해 합니다.

## <a name="configure-cache-purge"></a>캐시 제거 구성

1. 제거 하려는 자산이 있는 Azure Front 도어 프로필의 개요 페이지로 이동한 다음 **캐시 제거** 를 선택 합니다.

   :::image type="content" source="../media/how-to-cache-purge/front-door-cache-purge-1.png" alt-text="개요 페이지의 캐시 제거 스크린샷":::

1. 에 지 노드에서 제거할 끝점 및 도메인을 선택 합니다. *하나 이상의 도메인을 선택할 수 있습니다.*

   :::image type="content" source="../media/how-to-cache-purge/front-door-cache-purge-2.png" alt-text="캐시 제거 페이지의 스크린샷":::

1. 모든 자산을 지우려면 **선택한 도메인에 대 한 모든 자산 제거** 를 선택 합니다. 그렇지 않고 **경로** 에 제거 하려는 각 자산의 경로를 입력 합니다.

제거할 경로 목록에서 지원 되는 형식은 다음과 같습니다.

* **단일 경로 제거**:/pictures/strasbourg.png와 같은 파일 확장명을 사용 하 여 자산의 전체 경로 (프로토콜 및 도메인 제외)를 지정 하 여 개별 자산을 제거 합니다.
* **루트 도메인 제거**: 경로에서 "/*"를 사용 하 여 끝점의 루트를 제거 합니다.

Azure Front 도어 Standard/Preium의 캐시 제거는 대/소문자를 구분 하지 않습니다. 또한 쿼리 문자열은 독립적 이므로 URL을 제거 하면 해당 변수의 모든 쿼리 문자열 변형이 제거 됩니다. 

## <a name="next-steps"></a>다음 단계

[전면 도어 표준/프리미엄을 만드는](create-front-door-portal.md)방법에 대해 알아봅니다.
