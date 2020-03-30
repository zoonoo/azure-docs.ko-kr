---
title: Azure Cognitive Search를 사용하여 모바일 애플리케이션 개발을 위한 AI 기반 클라우드 검색 서비스
description: 모바일 애플리케이션 개발을 위해 AI 기반 클라우드 검색 서비스를 사용하는 서비스에 대해 알아봅니다.
author: codemillmatt
ms.assetid: 34a8a070-0123-8982-8345-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 48703001317533981f360baacb330b6878a3857e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240994"
---
# <a name="ai-powered-cloud-service-with-azure-cognitive-search"></a>Azure 인지 검색을 이용한 AI 기반 클라우드 서비스
[Azure Cognitive Search는](https://azure.microsoft.com/services/search/) 개발자에게 웹, 모바일 및 엔터프라이즈 응용 프로그램의 개인 이기종 콘텐츠에 대한 풍부한 검색 환경을 추가하기 위한 API 및 도구를 제공하는 서비스형 검색 클라우드 솔루션입니다. 사용자 지정 코드는 데이터 수집(인덱싱)을 호출하여 인덱스를 만들고 로드합니다. 다른 쪽에서는 애플리케이션 코드가 쿼리 요청을 발행하고 응답을 처리합니다. 검색 환경은 Azure Cognitive Search의 기능을 사용하여 클라이언트에서 정의되며, 서비스에서 생성, 소유 및 저장하는 지속된 인덱스에 대한 쿼리 실행이 있습니다.

## <a name="azure-cognitive-search-features"></a>Azure 인지 검색 기능
- **자유형 텍스트 검색**: 전체 텍스트 검색, 간단한 쿼리 구문 및 Lucene 쿼리 구문을 제공합니다.
- **단순 점수 매기기 프로파일**: 문서 자체의 값 함수로 관련성을 모델링합니다.
- **지오서치**: 검색 결과가 실제 위치와 근접한 것을 기반으로 데이터를 탐색합니다.
- **사용자 경험 기능**: 자동 완성, 검색 제안, 정렬 및 페이징을 제공합니다.
- **인지 검색**: 이미지 및 텍스트 분석을 위한 원시 콘텐츠에서 텍스트 정보를 추출하기 위해 인덱싱 파이프라인에 적용할 수 있습니다.
- **지식 저장소**: 인덱싱 중에 생성된 보강을 저장합니다.
- **데이터 원본**: Azure SQL Database, Azure Cosmos DB 및 Azure Blob 저장소에 연결하면 기본 데이터 저장소에서 검색 가능한 콘텐츠를 추출합니다.
- **데이터 가져오기 마법사**: 인덱서를 구성합니다. 
- **인덱스 디자이너**: 인덱스를 일어섭게 합니다. 
- **검색 탐색기**: 쿼리를 테스트하고 점수 매기기 프로필을 구체화합니다.

Azure 인지 검색 [기능에](/azure/search/search-what-is-azure-search#feature-descriptions)대한 자세한 설명을 찾을 수 있습니다.

## <a name="references"></a>참조
- [Azure 포털](https://portal.azure.com) 
- [Azure 인지 검색 설명서](/azure/search/)
- [개발자 가이드](https://azure.microsoft.com/resources/iot-developers-guide/)
- [Azure Cognitive Search를 사용하는 방법](/azure/search/search-what-is-azure-search#how-to-use-azure-cognitive-search)
- [빠른 시작](/azure/search/search-create-service-portal)

 