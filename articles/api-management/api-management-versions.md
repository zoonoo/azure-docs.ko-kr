---
title: Azure API Management의 버전 Microsoft Docs
description: Azure API Management 버전의 개념에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: johndowns
ms.service: api-management
ms.topic: article
ms.date: 06/12/2020
ms.author: jodowns
ms.custom: fasttrack-new
ms.openlocfilehash: 578bb511175d88a1507af9520265a1acd068b27c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87097999"
---
# <a name="versions-in-azure-api-management"></a>Azure API Management의 버전

버전을 사용 하면 개발자에 게 관련 Api 그룹을 제공할 수 있습니다. 버전을 사용 하 여 API의 주요 변경 내용을 안전 하 게 처리할 수 있습니다. 클라이언트는 준비가 되었을 때 새 API 버전을 사용 하도록 선택할 수 있지만 기존 클라이언트는 이전 버전을 계속 사용 합니다. 버전은 버전 식별자 (선택한 문자열 값)를 통해 구분 되며 버전 관리 체계를 사용 하면 클라이언트에서 사용 하려는 API 버전을 식별할 수 있습니다.

대부분의 경우 각 API 버전은 자체의 독립적인 API로 간주할 수 있습니다. 두 개의 다른 API 버전에는 서로 다른 작업과 정책 집합이 있을 수 있습니다.

버전의 경우 다음을 수행할 수 있습니다.

- 여러 버전의 API를 동시에 게시 합니다.
- 경로, 쿼리 문자열 또는 헤더를 사용 하 여 버전을 구분할 수 있습니다.
- 숫자, 날짜 또는 이름일 수 있는 버전을 식별 하는 문자열 값을 사용 합니다.
- 개발자 포털에서 API 버전을 함께 그룹화 하 여 표시 합니다.
- 기존 (버전이 없는) API를 사용 하 고 기존 클라이언트를 손상 시 키 지 않고 새 버전의 API를 만듭니다.

[연습을 수행 하 여 버전을 시작 하세요.](./api-management-get-started-publish-versions.md)

## <a name="versioning-schemes"></a>버전 관리 체계

다른 API 개발자는 버전 관리에 대 한 요구 사항이 다릅니다. Azure API Management는 버전 관리에 대 한 단일 접근 방식을 규정 하지 않으며 대신 몇 가지 옵션을 제공 합니다.

### <a name="path-based-versioning"></a>경로 기반 버전 관리

경로 버전 관리 체계를 사용 하는 경우에는 모든 API 요청에 대해 버전 식별자를 URL 경로에 포함 해야 합니다.

예를 들어 `https://apis.contoso.com/products/v1` 및는 `https://apis.contoso.com/products/v2` 동일한 API를 참조 `products` 하지만, 버전 및는 각각에 해당 `v1` `v2` 합니다.

헤더 기반 버전 관리를 사용 하는 경우 API 요청 URL의 형식은 `https://{yourDomain}/{apiName}/{versionIdentifier}/{operationId}` 입니다.

### <a name="header-based-versioning"></a>헤더 기반 버전 관리

헤더 버전 관리 체계를 사용 하는 경우 모든 API 요청에 대 한 HTTP 요청 헤더에 버전 식별자를 포함 해야 합니다. HTTP 요청 헤더의 이름을 지정할 수 있습니다.

예를 들어 라는 사용자 지정 헤더를 만들 수 `Api-Version` 있으며, 클라이언트는 `v1` `v2` 이 헤더의 값에 또는를 지정할 수 있습니다.

### <a name="query-string-based-versioning"></a>쿼리 문자열 기반 버전 관리

쿼리 문자열 버전 관리 체계를 사용 하는 경우에는 모든 API 요청에 대해 버전 식별자를 쿼리 문자열 매개 변수에 포함 해야 합니다. 쿼리 문자열 매개 변수의 이름을 지정할 수 있습니다.

쿼리 문자열 기반 버전 관리를 사용 하는 경우 API 요청 URL의 형식은 `https://{yourDomain}/{apiName}/{operationId}?{queryStringParameterName}={versionIdentifier}` 입니다.

예를 들어 `https://apis.contoso.com/products?api-version=v1` 및는 `https://apis.contoso.com/products/api-version=v2` 동일한 API를 참조 `products` 하지만, 버전 및는 각각에 해당 `v1` `v2` 합니다.

## <a name="original-versions"></a>원본 버전

버전이 없는 API에 버전을 추가 하는 경우 버전 식별자가 `Original` 지정 되지 않은 상태로 버전이 자동으로 만들어지고 기본 URL에 응답 합니다. 버전을 사용 하면 `Original` 기존 호출자가 버전을 추가 하는 프로세스에 의해 손상 되지 않습니다. 시작 시 사용 하도록 설정 된 버전을 사용 하 여 새 API를 만들면 `Original` 버전이 생성 되지 않습니다.

## <a name="how-versions-are-represented"></a>버전 표현 방법

Azure API Management는 단일 논리적 API에 대 한 버전 집합을 나타내는 *버전 집합*이라는 리소스를 유지 관리 합니다. Azure Portal 사용 하 여 버전을 관리할 때 버전 집합이 표시 되지 않지만 PowerShell, 리소스 관리자 템플릿 또는 Azure Resource Manager API를 사용 하 여 API Management 서비스와 상호 작용 하는 경우 버전 집합을 직접 보고 관리할 수 있습니다. 버전 집합에는 버전이 지정 된 API의 표시 이름과 지정 된 버전에 대 한 요청을 전달 하 [는 데 사용 되는 버전 관리 체계가](#versioning-schemes) 포함 됩니다.

각 버전의 API는 자체 API 리소스로 유지 관리 되며, 그러면 버전 집합과 연결 됩니다. 버전 집합에는 API의 버전 간에 상당한 변경이 발생할 수 있다는 사실을 반영 하는 매우 다양 한 작업 또는 정책을 포함 하는 Api가 포함 될 수 있습니다.

### <a name="migrating-a-non-versioned-api-to-a-versioned-api"></a>버전이 없는 API를 버전이 지정 된 API로 마이그레이션

Azure Portal를 사용 하 여 기존 API에 대 한 버전 관리를 사용 하는 경우 API Management 리소스가 다음과 같이 변경 됩니다.

 * 새 버전 집합이 만들어집니다.
 * 기존 버전은 [ `Original` API 버전으로](#original-versions)유지 되 고 구성 됩니다. API는 버전 집합에 연결 되어 있지만 버전 식별자를 지정 하지 않아도 됩니다.
 * 새 버전은 새 API로 생성 되 고 버전 집합에 연결 됩니다. 이 새 API는 버전 관리 체계 및 식별자를 사용 하 여 액세스 해야 합니다.

## <a name="versions-and-revisions"></a>버전 및 수정 버전

버전 및 수정 버전은 고유 기능입니다. 각 버전에는 버전이 없는 API와 마찬가지로 여러 개의 수정이 있을 수 있습니다. 버전을 사용 하지 않고 수정 버전을 사용 하거나 다른 방법을 사용할 수 있습니다. 일반적으로 버전은 api 버전을 주요 변경 내용과 분리 하는 데 사용 되 고, 수정 버전은 API에 대 한 사소한 변경 내용에 사용할 수 있습니다.

수정 버전의 변경 내용이 포함 되어 있거나, 정식 버전을 베타/테스트 버전으로 변환 하려는 경우 수정 버전에서 버전을 만들 수 있습니다. Azure Portal를 사용 하 여 수정 버전 탭의 수정 상황에 맞는 메뉴에서 ' 수정 버전에서 버전 만들기 '를 클릭 합니다.

## <a name="developer-portal"></a>개발자 포털

[개발자 포털](./api-management-howto-developer-portal.md) 은 API의 각 버전을 별도로 나열 합니다.

![API Management 개발자 포털에서 버전이 지정 된 Api 목록을 표시 합니다.](media/api-management-versions/portal-list.png)

API의 세부 정보에는 해당 API의 모든 버전 목록이 표시 됩니다. 버전 `Original` 식별자 없이 버전이 표시 됩니다.

![API의 세부 정보 및 해당 API에 대 한 버전 목록을 표시 하 API Management 개발자 포털](media/api-management-versions/portal-details.png)

> [!TIP]
> API 버전은 개발자 포털에 표시 되기 전에 제품에 추가 해야 합니다.
