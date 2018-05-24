---
title: Azure Search Service REST API 버전 2017-11-11-Preview | Microsoft Docs
description: Azure Search Service REST API 버전 2017-11-11-Preview에는 동의어 및 moreLikeThis 검색과 같은 실험적 기능이 포함되어 있습니다.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 05/01/2018
ms.author: HeidiSteen
ms.openlocfilehash: 3a9ff6697357dec443691b261ae6fc0477603a9c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33786722"
---
# <a name="azure-search-service-rest-api-version-2017-11-11-preview"></a>Azure Search Service REST API 버전: 2017-11-11-Preview
이 문서에서는 `api-version=2017-11-11-Preview` 버전의 Azure Search Service REST API를 설명하면서 다음의 실험적 기능을 제공합니다.

+ 지금 공개 미리 보기 상태인 [인식 검색](cognitive-search-concept-intro.md)은 텍스트가 아닌 원본 및 구분되지 않는 텍스트에서 잠재적인 정보를 찾아 이를 Azure Search에서 전체 텍스트 검색 가능한 콘텐츠로 변환하는 Azure Search 인덱싱의 새 보강 기능입니다.

미리 보기 API는 다음 두 작업을 제외하고 일반적으로 사용할 수 있는 API에 해당합니다.

+ [기술 집합 만들기(api-version=2017-11-11-Preview)](ref-create-skillset.md)
+ [인덱서 만들기(api-version=2017-11-11-Preview)](ref-create-indexer.md)

시험판 기능을 평가 하는 경우 미리 보기 API 버전 `api-version=2017-11-11-Preview`을 대상으로 하는지 확인합니다. 다음 예제 구문은 미리 보기 API 버전에 대한 호출을 설명합니다.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2017-11-11-Preview

> [!NOTE]
> 미리 보기 기능은 사용자 의견을 수집하고 변경하기 위해 테스트 및 실험에 사용할 수 있습니다. **프로덕션 응용 프로그램에서 미리 보기 API를 사용하지 않는 것이 좋습니다.**

Azure Search 서비스는 여러 버전으로 제공됩니다. 자세한 내용은 [API 버전](search-api-versions.md)을 참조하세요.
