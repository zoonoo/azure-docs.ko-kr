---
title: API 버전
titleSuffix: Azure Cognitive Search
description: .NET SDK의 Azure Cognitive Search REST API 및 클라이언트 라이브러리에 대한 버전 정책입니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/05/2020
ms.openlocfilehash: d2308d065755df834a224ef3b1df202d7b7dc5ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100518949"
---
# <a name="api-versions-in-azure-cognitive-search"></a>Azure Cognitive Search의 API 버전

Azure Cognitive Search는 정기적으로 기능 업데이트를 배포합니다. 항상 그렇지는 않지만 경우에 따라 이러한 업데이트에는 이전 버전과 호환성을 유지하기 위해 API의 새 버전이 필요하기도 합니다. 새 버전을 게시하면 코드에서 검색 서비스 업데이트를 통합 하는 시기와 방법을 제어할 수 있습니다.

새 API 버전을 사용하여 코드를 업그레이드하려면 다소의 작업이 필요하므로, Azure Cognitive Search 팀은 필요한 경우에만 새 버전을 게시하는 것을 규칙으로 합니다. API의 일부를 변경하여 더 이상 이전 버전과 호환되지 않는 경우에만 새 버전이 필요합니다. 그러한 변경은 기존 기능의 수정 또는 기존 API 노출 영역을 변경하는 새로운 기능으로 인해 발생할 수 있습니다.

SDK 업데이트와 동일한 규칙이 적용됩니다. Azure Cognitive Search SDK는 해당 버전의 세 부분, 즉 주, 부, 빌드 번호(예: 1.1.0)를 포함하는 [의미 체계 버전 관리](https://semver.org/) 규칙을 따릅니다. 이전 버전과 호환되지 않는 변경의 경우에만 SDK의 새로운 주 버전이 릴리스됩니다. 호환성을 유지하는 기능 업데이트의 경우 부 버전이, 버그 수정의 경우 빌드 버전이 증가합니다.

> [!Important]
> .NET, Java, Python, JavaScript용 Azure SDK는 Azure Cognitive Search에 대한 새 클라이언트 라이브러리를 배포합니다. 현재 Azure SDK 라이브러리는 최신 검색 REST API(2020-06-30) 또는 관리 REST API(2020-03-13)를 완전히 지원하지 않지만 이는 시간이 지남에 따라 변경됩니다. 기능 향상에 대한 알림을 이 페이지 또는 [새로운 내용](whats-new.md)에서 주기적으로 확인할 수 있습니다.

<a name="unsupported-versions"></a>

## <a name="unsupported-versions"></a>지원되지 않는 버전

2020년 10월 15일까지 최신 버전의 REST API에 대한 기존 검색 솔루션을 업그레이드합니다. 해당 시점에 Azure Cognitive Search REST API의 다음 버전은 사용 중지되며 더 이상 지원되지 않습니다.

+ **2015-02-28**
+ **2015-02-28-Preview**
+ **2014-07-31-미리 보기**
+ **2014-10-20-미리 보기**

또한 [**3.0.0-rc**](https://www.nuget.org/packages/Microsoft.Azure.Search/3.0.0-rc)보다 오래된 Azure Cognitive Search .NET SDK 버전은 해당 REST API 버전 중 하나를 대상으로 하기 때문에 사용이 중지됩니다. 

이 날짜 이후에는 더 이상 사용되지 않는 REST API 또는 SDK 버전을 사용하는 애플리케이션이 더 이상 작동하지 않으므로 업그레이드해야 합니다. 이 유형을 변경하는 경우와 마찬가지로 12개월의 통지를 제공하므로 적절한 시간을 조정할 수 있습니다.

Azure Cognitive Search를 계속 사용하려면 [REST API](search-api-migration.md)를 대상으로 하는 기존 코드를 [REST API 버전 2020-06-30](/rest/api/searchservice/) 또는 최신 SDK 버전으로 2020년 10월 15일까지 마이그레이션하세요.  최신 버전으로 업데이트하는 방법에 대한 질문이 있는 경우 코드를 업데이트할 충분한 시간이 생기도록 2020년 5월 15일까지 azuresearch_contact@microsoft.com으로 메일을 보내세요.

## <a name="rest-apis"></a>REST API

Azure Cognitive Search 서비스 인스턴스는 최신 버전을 포함하여 여러 REST API 버전을 지원합니다. 더 이상 최신 버전이 아닌 버전을 계속 사용할 수는 있지만 [코드를 마이그레이션](search-api-migration.md)하여 최신 버전을 사용하는 것이 좋습니다. REST API를 사용하는 경우 api-version 매개 변수를 통해 모든 요청에 API 버전을 지정해야 합니다. .NET SDK를 사용하는 경우 사용 중인 SDK의 버전에 따라 해당하는 REST API 버전이 결정됩니다. 이전 버전의 SDK를 사용하는 경우 최신 API 버전을 지원하기 위해 서비스가 업그레이드된 경우에도 변경 내용 없이 해당 코드를 계속 실행할 수 있습니다.

다음 표에서는 Search Service REST API의 현재 버전과 이전에 릴리스된 버전의 버전 기록을 제공합니다. 설명서는 가장 최근의 안정적인 미리 보기 버전에만 게시됩니다.

### <a name="search-service-apis"></a>Search Service API

검색 서비스에서 콘텐츠를 만들고 관리합니다.

| 버전&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   | 상태 | Description |
|-------------------------|--------|------------------------------|
| [검색 2020-06-30](/rest/api/searchservice/index)| Stable | 검색 REST API의 안정적인 최신 릴리스로, 관련 채점 및 일반적 지식 저장소에 대한 가용성을 면에서 진보를 이루었습니다.|
| [검색 2020-06-30-Preview](/rest/api/searchservice/index-preview)| 미리 보기 | 안정적인 버전과 연결된 미리 보기 버전입니다. 여러 [미리 보기 기능](search-api-preview.md)이 포함되어 있습니다. |
| 검색 2019-05-06 | Stable  | [복합 형식](search-howto-complex-data-types.md)을 추가합니다. |
| 검색 2019-05-06-미리 보기 | 미리 보기 | 안정적인 버전과 연결된 미리 보기 버전입니다. |
| 검색 2017-11-11 | Stable | 기술 세트 및 [AI 보강](cognitive-search-concept-intro.md)을 추가합니다. |
| 검색 2017-11-11-미리 보기 | 미리 보기 | 안정적인 버전과 연결된 미리 보기 버전입니다. |
| 검색 2016-09-01 |Stable | [인덱서](search-indexer-overview.md)를 추가합니다. |
| 검색 2016-09-01-미리 보기 | 미리 보기 | 안정적인 버전과 연결된 미리 보기 버전입니다.|
| 검색 2015-02-28 | 10-10-2020 이후 지원되지 않음   | 첫 번째 일반 공급 릴리스.  |
| 검색 2015-02-28-미리 보기 | 10-10-2020 이후 지원되지 않음  | 안정적인 버전과 연결된 미리 보기 버전입니다. |
| 검색 2014-10-20-미리 보기 | 10-10-2020 이후 지원되지 않음 | 두 번째 공개 미리 보기입니다. |
| 검색 2014-07-31-미리 보기 | 10-10-2020 이후 지원되지 않음  | 첫 번째 공개 미리 보기입니다. |

### <a name="management-rest-apis"></a>관리 REST API

검색 서비스를 만들고 구성하며 API 키를 관리합니다.

| 버전&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   | 상태 | Description |
|-------------------------|--------|------------------------------|
| [관리 2020-08-01](/rest/api/searchmanagement/) | Stable | 관리 REST API의 안정적인 최신 릴리스. 미리 보기 버전에 명시된 리소스를 제외하고, 모든 아웃바운드 액세스된 리소스에 대해 일반적으로 사용 가능한 공유 프라이빗 링크 리소스 지원을 추가합니다. |
| [관리 2020-08-01-미리 보기](/rest/api/searchmanagement/index-preview) | 미리 보기  | 현재 미리 보기 상태: Azure Functions 및 Azure Database for MySQL에 대한 공유 프라이빗 링크 리소스를 지원합니다. |
| 관리 2020-03-13  | Stable | 프라이빗 링크를 통해 [프라이빗 엔드포인트](service-create-private-endpoint.md)를, 그리고 새 서비스에 대한 [네트워크 IP 규칙](service-configure-firewall.md)을 추가합니다. 자세한 내용은 이 [Swagger 사양](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/resource-manager/Microsoft.Search/stable/2020-08-01)을 참조하세요. |
| 관리 2019-10-01-미리 보기 | 미리 보기  | 이 목록에는 미리 보기 기능이 도입되지 않았습니다. 이 미리 보기는 2020-03-13과 기능적으로 동일합니다. 자세한 내용은 이 [Swagger 사양](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/resource-manager/Microsoft.Search/preview/2019-10-01-preview)을 참조하세요. |
| 관리 2015-08-19  | Stable | 관리 REST API의 일반적으로 사용할 수 있는 첫 번째 버전입니다. 서비스 프로비저닝, 스케일 업, API 키 관리를 제공합니다. 자세한 내용은 이 [Swagger 사양](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/resource-manager/Microsoft.Search/stable/2015-08-19)을 참조하세요. |
| 관리 2015-08-19-미리 보기  | 미리 보기 | 관리 REST API의 첫 번째 미리 보기 버전입니다. 자세한 내용은 이 [Swagger 사양](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/resource-manager/Microsoft.Search/stable/2015-08-19)을 참조하세요. |

## <a name="azure-sdk-for-net"></a>Azure SDK for .NET

다음 표에서는 최신 SDK 버전에 대한 링크를 제공합니다. 

| SDK 버전 | 상태 | Description |
|-------------|--------|------------------------------|
| [Azure.Search.Documents 11](/dotnet/api/overview/azure/search.documents-readme) | Stable | 2020년 7월에 릴리스된 Azure .NET SDK의 새 클라이언트 라이브러리입니다. 검색 REST api-version = 2020-06-30 REST API를 대상으로 하지만, 지역 필터에 대한 기본 지원을 아직 제공하지 않습니다. 지리적 작업에는 [Microsoft.Spatial](https://www.nuget.org/packages/Microsoft.Spatial/) 패키지를 권장합니다. [System.Text.Json](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Microsoft.Azure.Core.Spatial/README.md) 및 [Newtonsoft.Json](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Microsoft.Azure.Core.Spatial.NewtonsoftJson/README.md)에 대한 예제를 볼 수 있습니다. |
| [Microsoft.Azure.Search 10](https://www.nuget.org/packages/Microsoft.Azure.Search/) | Stable | 2019년 5월에 릴리스됨. 검색 REST api-version=2019-05-06을 대상으로 합니다.|
| [Microsoft.Azure.Management.Search 4.0.0](/dotnet/api/overview/azure/search/management) | Stable | 관리 REST api-버전=2020-08-01을 대상으로 합니다.  |
| Microsoft.Azure.Management.Search 3.0.0 | Stable | 관리 REST api-version=2015-08-19을 대상으로 합니다.  |

## <a name="azure-sdk-for-java"></a>Java용 Azure SDK

| SDK 버전 | 상태 | Description  |
|-------------|--------|------------------------------|
| [Java azure-search-documents 11](https://newreleases.io/project/github/Azure/azure-sdk-for-java/release/azure-search-documents_11.1.0) | Stable | Azure Java SDK의 새 클라이언트 라이브러리가 2020년 7월에 릴리스되었습니다. 검색 REST api-version=2019-05-06을 대상으로 합니다. |
| [Java 관리 클라이언트 1.35.0](/java/api/overview/azure/search/management) | Stable | 관리 REST api-version=2015-08-19을 대상으로 합니다. |

## <a name="azure-sdk-for-javascript"></a>JavaScript용 Azure SDK

| SDK 버전 | 상태 | Description  |
|-------------|--------|------------------------------|
| [JavaScript @azure/search-documents 11.0](https://www.npmjs.com/package/@azure/search-documents) | Stable | Azure JavaScript & TypesScript SDK의 새 클라이언트 라이브러리가 2020년 7월에 릴리스되었습니다. 검색 REST api-version = 2016-09-01을 대상으로 합니다. |
| [JavaScript @azure/arm-search](https://www.npmjs.com/package/@azure/arm-search) | Stable | 관리 REST api-version=2015-08-19을 대상으로 합니다. |

## <a name="azure-sdk-for-python"></a>Python용 Azure SDK

| SDK 버전 | 상태 | Description  |
|-------------|--------|------------------------------|
| [Python azure-search-documents 11.0](https://pypi.org/project/azure-search-documents/) | Stable | Azure Python SDK의 새 클라이언트 라이브러리가 2020년 7월에 릴리스되었습니다. 검색 REST api-version=2019-05-06을 대상으로 합니다. |
| [Python azure-mgmt-search 8.0](https://pypi.org/project/azure-mgmt-search/) | Stable | 관리 REST api-version=2015-08-19을 대상으로 합니다. |
