<properties
   pageTitle="Azure 검색의 API 버전 | Microsoft Azure | 검색 API"
   description="Azure 검색 REST API의 버전 정책 및 .NET SDK의 클라이언트 라이브러리."
   services="search"
   documentationCenter=""
   authors="brjohnstmsft"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="dotnet"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="08/16/2016"
   ms.author="brjohnst"/>

# Azure 검색의 API 버전

Azure 검색은 정기적으로 기능 업데이트를 공개합니다. 항상 그렇지는 않지만 경우에 따라 이전 버전과 호환성을 유지하기 위해 API의 새 버전을 게시하기 위해 이러한 업데이트가 필요하기도 합니다. 새 버전을 게시하면 코드에서 검색 서비스 업데이트를 통합 하는 시기와 방법을 제어할 수 있습니다.

일반적으로 새 API 버전을 사용하여 코드를 업그레이드하기 위해 다소의 작업이 필요므로, 꼭 필요한 경우에만 새 버전을 게시하려고 합니다. API의 일부를 변경하여 더 이상 이전 버전과 호환되지 않는 경우에만 새 버전을 게시합니다. 이러한 경우는 기존 기능의 수정 또는 기존 API 노출 영역을 변경하는 새로운 기능으로 인해 발생할 수 있습니다.

SDK 업데이트와 동일한 규칙을 따릅니다. Azure 검색 SDK는 버전이 주, 부, 빌드 번호 등의 세 부분으로 구성되는 [의미 체계 버전 관리](http://semver.org/) 규칙을 따릅니다(예: 1.1.0). 이전 버전과 호환되지 않는 변경의 경우에만 SDK의 새로운 주 버전이 출시됩니다. 호환성을 유지하는 기능 업데이트의 경우 부 버전이, 버그 수정의 경우 빌드 버전이 증가합니다.

##현재 버전의 스냅숏 

아래는 Azure 검색에 대한 모든 프로그래밍 인터페이스의 현재 버전의 스냅숏입니다. 로드맵 및 기타 세부 정보는 이 문서의 다음 섹션에 있습니다.

인터페이스|가장 최근의 주 버전|가동 상태
----------|-------------------------|------
[.NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx)|1\.1|일반 공급, 2016년 2월 릴리스됨
[.NET SDK 미리 보기](https://msdn.microsoft.com/library/mt761536%28v=azure.103%29.aspx)|2\.0-preview|미리 보기, 2016년 8월 릴리스
[서비스 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx)|2015-02-28|일반 공급
[서비스 REST API 미리 보기](search-api-2015-02-28-preview.md)|2015-02-28-Preview|미리 보기
[관리 REST API](https://msdn.microsoft.com/library/azure/dn832684.aspx)|2015-08-19|일반 공급

REST API의 경우 각 호출의 `api-version`을 포함해야 합니다. 이렇게 하면 API 미리 보기 등 특정 버전을 대상으로 하기 쉽습니다. 다음 예제에서는 `api-version` 매개 변수를 지정하는 방법을 보여 줍니다.

    GET https://adventure-works.search.windows.net/indexes/bikes?api-version=2015-02-28

> [AZURE.NOTE] 각 요청에는 `api-version`이 있지만, 모든 API 요청에 대해 동일한 버전을 사용하는 것이 좋습니다. 새 API 버전이 이전 버전에서 인식 하지 못하는 특성 또는 작업을 도입하는 경우 특히 유용합니다. 혼합 API 버전에는 의도하지 않은 결과가 있을 수 있기 때문에 사용하지 말아야 합니다.
> 
서비스 REST API 및 관리 REST API는 따로따로 버전이 지정됩니다. 버전 번호 체계는 동일합니다.

일반 공급(또는 GA) API는 프로덕션에서 사용할 수 있으며 Azure 서비스 수준 계약에 적용을 받습니다. 미리 보기 버전에는 GA 버전으로 항상 마이그레이션되지 않는 실험적 기능이 있습니다. **프로덕션 응용 프로그램에서 미리 보기 API를 사용하지 않는 것이 좋습니다.**

##SDK 버전 로드맵

.NET SDK의 각 버전은 서비스 REST API의 특정 버전을 대상으로 합니다. 기능은 REST API에 먼저 공개된 다음 SDK에서 구현됩니다.

.NET SDK는 이제 일반 공급되며 작업은 다음 버전에서 이미 진행 중입니다. 다음 테이블에서 SDK의 향후 버전을 볼 수 있어 앞으로의 내용을 대략적으로 파악할 수 있습니다.

.NET SDK 버전|REST API 버전|기능|ETA
----------------|----------------|--------|---
1\.1|2015-02-28|Lucene 쿼리 구문|2016년 2월
2\.0-preview|2015-02-28-Preview|사용자 지정 분석기, Azure Blob 및 테이블 인덱서, 필드 매핑, Etag|2016년 8월
2\.x|새 GA API 버전|2\.0-preview와 동일|2016년 4사분기 초

##미리 보기 및 일반 공급 버전 정보

Azure 검색은 항상 REST API를 통해 실험 기능을 미리 공개한 다음, .NET SDK의 시험판 버전을 통해 공개합니다.

미리 보기 기능은 GA 릴리스로 마이그레이션되지 않을 수 있습니다. 반면 GA 버전의 기능은 안정적인 것으로 간주되며 이전 버전과의 사소한 호환 수정 및 향상을 제외하고는 변경되지 않으며, 미리 보기 기능은 테스트와 실험을 통해 기능 설계 및 구현에 대한 피드백을 수집할 수 있습니다.

그러나 미리 보기 기능이 변경될 수 있으므로 미리 보기 버전에 종속된 프로덕션 코드를 작성하지 않는 것이 좋습니다. 이전 미리 보기 버전을 사용하는 경우 일반 공급(GA) 버전으로 마이그레이션하는 것이 좋습니다.

.NET SDK의 코드 마이그레이션에 대한 지침은 [.NET SDK 업그레이드](search-dotnet-sdk-migration.md)에서 찾을 수 있습니다.

일반 공급은 Azure 검색이 현재 SLA(서비스 수준 계약) 하에 있다는 의미입니다. SLA는 [Azure 검색 서비스 수준 계약](https://azure.microsoft.com/support/legal/sla/search/v1_0/)에서 찾을 수 있습니다.

<!---HONumber=AcomDC_0817_2016-->