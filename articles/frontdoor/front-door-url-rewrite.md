---
title: Azure 전면 도어-URL 재작성 | Microsoft Docs
description: 이 문서는 Azure Front 도어가 구성 된 경우 경로에 대 한 URL 재작성을 수행 하는 방법을 이해 하는 데 도움이 됩니다.
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: eb5b4ab8a23a374aec54d65dd5390ab3fec3e905
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91445481"
---
# <a name="url-rewrite-custom-forwarding-path"></a>URL 다시 쓰기(사용자 지정 전달 경로)
Azure 전면 도어는 백 엔드로 전달 되는 요청을 생성할 때 사용할 선택적 **사용자 지정 전달 경로** 를 구성 하 여 URL 다시 쓰기를 지원 합니다. 기본적으로 사용자 지정 전달 경로가 제공 되지 않으면 Front 도어가 전달 된 요청에 사용 된 URL에 들어오는 URL 경로를 복사 합니다. 전달된 요청의 호스트 헤더는 선택한 백 엔드에 맞게 구성됩니다. 호스트 헤더의 역할 및 구성 방법은 [백 엔드 호스트 헤더](front-door-backend-pool.md#hostheader)를 참조하세요.

URL 재작성의 강력한 부분은 사용자 지정 전달 경로가 와일드 카드 경로와 일치 하는 들어오는 경로의 일부를 전달 된 경로에 복사 한다는 것입니다. 이러한 경로 세그먼트는 아래 예제의 **녹색** 세그먼트입니다.
</br>

:::image type="content" source="./media/front-door-url-rewrite/front-door-url-rewrite-example.jpg" alt-text="Azure Front Door URL 다시 쓰기":::

## <a name="url-rewrite-example"></a>URL 다시 쓰기 예제
구성 된 프런트 엔드 호스트와 경로를 다음과 같이 조합 하 여 라우팅 규칙을 고려 합니다.

| 호스트      | 경로       |
|------------|-------------|
| www\.contoso.com | /\*   |
|            | /foo        |
|            | /foo/\*     |
|            | /foo/bar/\* |

아래 표의 첫 번째 열은 들어오는 요청을 보여주고, 두 번째 열은 "가장 구체적으로" 일치하는 '경로'를 보여줍니다.  테이블의 세 번째 및 결과 열은 구성 된 **사용자 지정 전달 경로의**예입니다.

예를 들어 두 번째 행을 보시면, 들어오는 요청 `www.contoso.com/sub`와 관련하여 사용자 지정 전달 경로가 `/`이면 전달된 경로는 `/sub`입니다. 사용자 지정 전달 경로가 `/fwd/`이면 전달된 경로는 `/fwd/sub`입니다. 나머지 열에도 마찬가지 원리가 적용됩니다. 아래 경로에서 **강조된** 부분은 와일드 카드에서 일치하는 부분을 나타냅니다.

| 들어오는 요청       | 가장 구체적으로 일치하는 경로 | /          | /fwd/          | /foo/          | /foo/bar/          |
|------------------------|--------------------------|------------|----------------|----------------|--------------------|
| www \. contoso.com/            | /\*                      | /          | /fwd/          | /foo/          | /foo/bar/          |
| www \. contoso.com/**sub**     | /\*                      | /**부분**   | /fwd/**sub**   | /foo/**sub**   | /foo/bar/**sub**   |
| www \. contoso.com/**a/b/c**   | /\*                      | /**a/b/c** | /fwd/**a/b/c** | /foo/**a/b/c** | /foo/bar/**a/b/c** |
| www \. contoso.com/foo         | /foo                     | /          | /fwd/          | /foo/          | /foo/bar/          |
| www \. contoso.com/foo/        | /foo/\*                  | /          | /fwd/          | /foo/          | /foo/bar/          |
| www \. contoso.com/foo/**표시줄** | /foo/\*                  | /**스페이스바**   | /fwd/**bar**   | /foo/**bar**   | /foo/bar/**bar**   |

## <a name="optional-settings"></a>선택적 설정
특정 회람 규칙 설정에 대해 지정할 수 있는 추가 옵션 설정이 있습니다.

* **캐시 구성** -사용 하지 않도록 설정 하거나 지정 하지 않으면이 라우팅 규칙과 일치 하는 요청이 캐시 된 콘텐츠를 사용 하려고 시도 하지 않고 대신 항상 백 엔드에서 인출 됩니다. 자세한 내용은 [Front Door를 통한 캐싱](front-door-caching.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Front Door를 만드는](quickstart-create-front-door.md) 방법을 알아봅니다.
- [Front Door의 작동 원리](front-door-routing-architecture.md)를 알아봅니다.
