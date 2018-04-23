---
title: Azure Search 서비스 REST API 버전 2016-09-01로 업그레이드 | Microsoft Docs
description: Azure Search 서비스 REST API 버전 2016-09-01로 업그레이드
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 10/27/2016
ms.author: brjohnst
ms.openlocfilehash: ea901462677d42d90007a2130825bd3b382407f2
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="upgrading-to-the-azure-search-service-rest-api-version-2016-09-01"></a>Azure Search 서비스 REST API 버전 2016-09-01로 업그레이드
[Azure Search 서비스 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx)의 2015-02-28 또는 2015-02-28-Preview 버전을 사용하는 경우 이 문서를 통해 일반적으로 다음 API 버전인 2016-09-01을 사용하기 위해 응용 프로그램을 업그레이드할 수 있습니다.

REST API의 2016-09-01 버전에는 이전 버전에서 변경된 몇 가가지 포함되어 있습니다. 이는 대부분 이전 버전과 호환되기 때문에 이전에 사용하던 버전에 따라 간단히 코드를 변경할 수 있습니다. 새 API 버전을 사용하는 코드를 변경하는 방법에 대한 지침은 [업그레이드 단계](#UpgradeSteps) 를 참조하세요.

> [!NOTE]
> Azure Search 서비스 인스턴스는 최신 버전을 포함한 여러 REST API 버전을 지원합니다. 더 이상 최신 버전이 아닌 버전을 계속 사용할 수는 있지만 코드를 마이그레이션하여 최신 버전을 사용하는 것이 좋습니다.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2016-09-01"></a>2016-09-01 버전의 새로운 기능
2016-09-01 버전은 Azure Search 서비스 REST API의 두 번째 일반 릴리스입니다. 이 API 버전의 새로운 기능은 다음과 같습니다.

* [사용자 지정 분석기](https://aka.ms/customanalyzers)를 사용하여 텍스트를 인덱싱 가능하고 검색 가능한 토큰으로 변환하는 프로세스를 제어할 수 있습니다
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) 및 [Azure Table Storage](search-howto-indexing-azure-tables.md) 인덱서를 사용하여 일정에 따라 또는 요청 시 Azure 저장소에서 Azure Search로 데이터를 쉽게 가져올 수 있습니다.
* [필드 매핑](search-indexer-field-mappings.md)을 사용하여 인덱서가 Azure Search로 데이터를 가져오는 방법을 사용자 지정할 수 있습니다.
* ETag를 사용하여 동시에 안전하게 인덱스, 인덱서 및 데이터 원본의 정의를 업데이트할 수 있습니다. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>업그레이드 단계
2015-02-28 버전에서 업그레이드하는 경우 버전 번호를 제외하고 코드를 변경하지 않아도 됩니다. 코드를 변경해야 하는 유일한 경우는 다음과 같습니다.

* 인식할 수 없는 속성이 API 응답에 반환되는 경우 코드가 실패합니다. 기본적으로 응용 프로그램은 이해하지 못하는 속성을 무시합니다.
* 코드는 API 요청을 보관하고 새 API 버전으로 다시 전송하려 합니다. 예를 들어 응용 프로그램이 검색 API에서 반환된 연속 토큰을 보관하는 경우 이런 현상이 발생할 수 있습니다(자세한 내용은 [검색 API 참조](https://msdn.microsoft.com/library/azure/dn798927.aspx#Anchor_1)의 `@search.nextPageParameters`를 참조).

이러한 경우에 해당하는 경우 코드를 적절하게 변경해야 합니다. 2016-09-01 버전의 [새로운 기능](#WhatsNew)을 사용하여 시작하지 않으면 코드를 변경할 필요가 없습니다.

2015-02-28-Preview 버전에서 업그레이드하는 경우, 위 내용이 적용되지만 일부 미리 보기 기능은 2016-09-01 버전에서 사용할 수 없다는 점을 알아야 합니다.

* Azure Blob Storage 인덱서는 CSV 파일과 JSON 배열을 포함하는 Blob을 지원합니다.
* 동의어
* "이보다 더 나은" 쿼리

코드가 이러한 기능을 사용하는 경우 기능을 사용하지 않고는 2016-09-01로 업그레이드할 수 없습니다.

> [!IMPORTANT]
> 미리 보기 API는 테스트 및 평가 용도로 제공되며 프로덕션 환경에는 사용되지 않는다는 점을 유념하세요.
> 
> 

## <a name="conclusion"></a>결론
Azure Search 서비스 REST API 사용에 대한 자세한 정보가 필요한 경우 MSDN의 최근에 업데이트된 [API 참조](https://msdn.microsoft.com/library/azure/dn798935.aspx)를 참조하세요.

Azure Search에 대한 귀하의 피드백을 환영합니다. 문제가 발생하면 [Azure Search MSDN 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch) 또는 [StackOverflow](http://stackoverflow.com/)를 통해 자유롭게 도움을 요청하세요. StackOverflow에서 Azure Search에 대한 질문이 있는 경우, `azure-search`를 사용하여 태그하세요.

Azure Search를 이용해 주셔서 감사합니다!

