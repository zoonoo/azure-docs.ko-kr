---
title: Azure Front Door 표준/프리미엄(미리 보기)의 캐시 제거
description: 이 문서는 Azure Front Door 표준/프리미엄에서 캐시를 삭제하는 방법을 이해하는 데 도움을 줍니다.
services: frontdoor
author: duongau
manager: KumudD
ms.service: frontdoor
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: aacbf2ceab8580727b1885bf6533cd74a7c4e60a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101099812"
---
# <a name="cache-purging-in-azure-front-door-standardpremium-preview"></a>Azure Front Door 표준/프리미엄(미리 보기)의 캐시 제거

> [!Note]
> 이 설명서는 Azure Front Door 표준/프리미엄(미리 보기)용입니다. Azure Front Door에 대한 정보를 찾고 있나요? [여기](../front-door-overview.md)에서 봅니다.

Azure Front Door 표준/프리미엄은 자산의 time-to-live(TTL)이 만료될 때까지 자산을 캐시합니다. 클라이언트가 만료된 TTL를 사용하여 자산을 요청할 때마다 Azure Front Door 환경에서는 요청을 처리할 자산의 업데이트된 새 복사본이 있는지 검색한 후 새로 고친 캐시를 저장합니다.

최상의 방법은 사용자가 항상 자산의 최신 복사본을 얻는 것입니다. 이렇게 하는 방법은 각 업데이트에 대한 자산을 버전으로 만들고 새 URL로 게시하는 것입니다. Azure Front Door 표준/프리미엄은 다음 클라이언트 요청에 대한 새 자산을 즉시 검색합니다. 경우에 따라 모든 에지 노드에서 캐시된 콘텐츠를 삭제하고 해당 콘텐츠가 모두 새로 업데이트된 자산을 검색하도록 강제하려 할 수 있습니다. 캐시된 콘텐츠를 삭제하는 이유는 애플리케이션에 업데이트를 새로 했거나 잘못된 정보를 포함한 자산을 업데이트하려고 하기 때문입니다.

> [!IMPORTANT]
> Azure Front Door 표준/프리미엄(미리 보기)은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

[Azure Front Door 캐싱](concept-caching.md)을 검토하여 캐싱의 작동 방식을 이해합니다.

## <a name="configure-cache-purge"></a>캐시 삭제 구성

1. 삭제하려는 자산이 있는 Azure Front Door 프로필의 개요 페이지로 이동한 다음 **캐시 삭제** 를 선택합니다.

   :::image type="content" source="../media/how-to-cache-purge/front-door-cache-purge-1.png" alt-text="개요 페이지의 캐시 삭제 스크린샷":::

1. 에지 노드에서 삭제할 엔드포인트 및 도메인을 선택합니다. *(하나 이상의 도메인을 선택할 수 있습니다.)*

   :::image type="content" source="../media/how-to-cache-purge/front-door-cache-purge-2.png" alt-text="캐시 삭제 페이지의 스크린샷":::

1. 모든 자산을 지우려면 **선택한 도메인에 대한 모든 자산 삭제** 를 선택합니다. 아니면 **경로** 에 삭제하려는 각 자산의 경로를 입력합니다.

삭제할 경로 목록에서 지원되는 형식은 다음과 같습니다.

* **단일 경로 삭제**: /pictures/strasbourg.png와 같은 파일 확장명을 사용하여 자산의 전체 경로(프로토콜 및 도메인 제외)를 지정하여 개별 자산을 삭제합니다.
* **루트 도메인 삭제**: 경로에 “/*”가 포함된 엔드포인트의 루트를 삭제합니다.

Azure Front Door 표준/프리미엄의 캐시 삭제는 대/소문자를 구분하지 않습니다. 또한 쿼리 문자열은 독립적이므로 URL을 삭제하면 해당 URL의 모든 쿼리 문자열 변형이 삭제됩니다. 

## <a name="next-steps"></a>다음 단계

[Front Door 표준/프리미엄 만드는](create-front-door-portal.md) 방법 알아보기.
