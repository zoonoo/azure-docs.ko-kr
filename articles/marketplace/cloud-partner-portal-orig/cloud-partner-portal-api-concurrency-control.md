---
title: 동시성 제어 | Azure 마켓플레이스
description: 클라우드 파트너 포털 게시 API의 동시성 제어 전략에 대해 설명합니다.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 302ba8d550f5e91efe12c620d766550958d3bf68
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81256402"
---
# <a name="concurrency-control"></a>동시성 제어

> [!NOTE]
> 클라우드 파트너 포털 API는 파트너 센터와 통합되며 오퍼가 파트너 센터로 마이그레이션된 후에도 계속 작동합니다. 통합은 작은 변화를 도입합니다. [Cloud 파트너 포털 API 참조에](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) 나열된 변경 내용을 검토하여 파트너 센터로 마이그레이션한 후에도 코드가 계속 작동하는지 확인합니다.

클라우드 파트너 포털 게시 API를 호출할 때는 항상 사용할 동시성 제어 전략을 명시적으로 지정해야 합니다. **If-Match** 헤더를 제공하지 않으면 HTTP 400 오류 응답이 반환됩니다. 동시성 제어를 위해 제공되는 두 가지 전략은 다음과 같습니다.

-   **낙관적** - 업데이트를 수행하는 클라이언트가 데이터를 마지막으로 읽은 후 데이터가 변경되었는지를 확인합니다.
-   **마지막 데이터 적용** - 데이터를 마지막으로 읽은 시간 이후 다른 애플리케이션이 데이터를 수정했는지에 관계없이 클라이언트가 데이터를 직접 업데이트합니다.

<a name="optimistic-concurrency-workflow"></a>낙관적 동시성 워크플로
-------------------------------

리소스가 예기치 않게 편집되지 않도록 보장하려면 다음 워크플로를 통해 낙관적 동시성 전략을 사용하는 것이 좋습니다.

1.  API를 사용하여 엔터티를 검색합니다. 응답에는 응답 시에 현재 저장되어 있는 엔터티 버전을 식별하는 ETag 값이 포함됩니다.
2.  업데이트 시에는 필수 **If-Match** 요청 헤더에 이와 동일한 ETag 값을 포함합니다.
3.  API는 요청에서 수신된 ETag 값과 엔터티의 현재 ETag 값을 원자성 트랜잭션에서 비교합니다.
    *   ETag 값이 서로 다르면 API는 `412 Precondition Failed` HTTP 응답을 반환합니다. 이 오류는 클라이언트가 엔터티를 마지막으로 검색한 이후 다른 프로세스가 엔터티를 업데이트했거나, 요청에 지정된 ETag 값이 잘못되었음을 나타냅니다.
    *  ETag 값이 같거나 **If-match** 헤더에 와일드카드 별표 문자(`*`)가 포함되어 있으면 API는 요청된 작업을 수행합니다. API 작업에서는 엔터티의 저장된 ETag 값도 업데이트합니다.


> [!NOTE]
> **If-Match** 헤더에서 와일드카드 문자(*)를 지정하면 API는 마지막 데이터 적용 동시성 전략을 사용합니다. 이 경우 ETag 비교는 수행되지 않으며 확인 없이 리소스가 업데이트됩니다. 
