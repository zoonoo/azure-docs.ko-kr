---
title: Azure Search 서비스 REST API 버전 2016-09-01-Preview | Microsoft Docs
description: Azure Search 서비스 REST API 버전 2016-09-01-Preview에는 동의어 및 moreLikeThis 검색과 같은 실험적 기능이 포함되어 있습니다.
author: mhko
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: reference
ms.date: 10/25/2017
ms.author: nateko
ms.openlocfilehash: 034c7c9c6e97ebb128860b6041089b311ffaf528
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="azure-search-service-rest-api-version-2016-09-01-preview"></a>Azure Search 서비스 REST API 버전: 2016-09-01-Preview
이 문서는 `api-version=2016-09-01-Preview`에 대한 참조 설명서입니다. 이 미리 보기는 다음과 같은 실험적 기능을 제공하여 현재 일반적으로 사용할 수 있는 버전인 [api-version=2016-09-01](https://msdn.microsoft.com/library/dn798935.aspx)을 확장합니다.

* [동의어 API](search-synonyms.md): 동의어 지도를 업로드하고 검색을 확장합니다.
* [`moreLikeThis` 쿼리 매개 변수](search-more-like-this.md): 특정 문서와 관련된 문서를 찾습니다.

이러한 실험적 기능을 테스트하려면 미리 보기 API 버전 `api-version=2016-09-01-Preview`를 대상으로 지정하세요. 다음 예제에서는 more-like-this 쿼리를 만들 때 미리 보기 API 버전을 지정하는 방법을 보여줍니다.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?moreLikeThis=a1&api-version=2016-09-01-Preview

> [!NOTE]
> 미리 보기 기능은 사용자 의견을 수집하고 변경하기 위해 테스트 및 실험에 사용할 수 있습니다. **프로덕션 응용 프로그램에서 미리 보기 API를 사용하지 않는 것이 좋습니다.**

Azure Search 서비스는 여러 버전으로 제공됩니다. 자세한 내용은 [Search 서비스 버전 관리](http://msdn.microsoft.com/library/azure/dn864560.aspx) 를 참조하세요.
