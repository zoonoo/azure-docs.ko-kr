---
title: 'Azure Front 도어: 끝점 관리자'
description: 이 문서에서는 Azure 프런트 도어 끝점 관리자의 개요를 제공 합니다.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: c916be9a54d62e16f488c94f4fa88a2207fb8788
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101100101"
---
# <a name="what-is-azure-front-door-standardpremium-preview-endpoint-manager"></a>Azure Front 도어 표준/프리미엄 (미리 보기) 끝점 관리자 란?

> [!NOTE]
> * 이 설명서는 Azure Front Door 표준/프리미엄(미리 보기)용입니다. Azure Front Door에 대한 정보를 찾고 있나요? [Azure 전방 도어 문서](../front-door-overview.md)를 봅니다.

끝점 관리자는 Azure Front 문에 대해 구성한 끝점에 대 한 개요를 제공 합니다. 끝점은 도메인 및 연결 된 구성의 논리적 그룹화입니다. Endpoint Manager를 사용 하면 CRUD (만들기, 읽기, 업데이트 및 삭제) 작업을 위한 끝점 컬렉션을 관리할 수 있습니다. 끝점 관리자를 통해 끝점에 대 한 다음 요소를 관리할 수 있습니다.

* 도메인
* 원본 그룹
* 경로
* 보안

:::image type="content" source="../media/concept-endpoint-manager/endpoint-manager-1.png" alt-text="구성이 없는 끝점 관리자의 스크린샷" lightbox="../media/concept-endpoint-manager/endpoint-manager-1-expanded.png":::

끝점 관리자는 끝점 내에서 생성 되는 각 요소의 인스턴스 수를 나열 합니다. 각 요소의 연결 상태도 표시 됩니다. 예를 들어 여러 도메인 및 원본 그룹을 만들고 서로 다른 경로를 사용 하 여 연결을 할당할 수 있습니다.

> [!IMPORTANT]
> * Azure 전면 도어 표준/프리미엄 (미리 보기)은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [**Microsoft Azure Preview에 대한 추가 사용 약관**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="linked-view"></a>연결 된 뷰

끝점 관리자 내에서 연결 된 보기를 사용 하 여 다음과 같은 Azure Front 도어 요소 간의 연결을 쉽게 식별할 수 있습니다.

* 현재 끝점과 연결 된 도메인은 무엇 인가요?
* 어떤 원본 그룹이 어떤 도메인에 연결 되나요?
* 어떤 WAF 정책이 어떤 도메인에 연결 되어 있나요?

:::image type="content" source="../media/concept-endpoint-manager/endpoint-manager-2.png" alt-text="구성이 포함 된 끝점 관리자의 스크린샷" lightbox="../media/concept-endpoint-manager/endpoint-manager-2-expanded.png":::

## <a name="next-steps"></a>다음 단계

[전면 도어 표준/프리미엄을 만드는](create-front-door-portal.md)방법에 대해 알아봅니다.
