---
title: Azure Cognitive Search을 사용 하 여 모바일 응용 프로그램을 개발 하기 위한 AI 기반 클라우드 검색 서비스
description: 모바일 응용 프로그램 개발을 위해 AI 기반 클라우드 search 서비스를 사용 하는 서비스에 대해 알아봅니다.
author: elamalani
ms.assetid: 34a8a070-0123-8982-8345-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 335df16e6a6436485521611aee0f0a62edca8a3b
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123458"
---
# <a name="ai-powered-cloud-service-with-azure-cognitive-search"></a>Azure Cognitive Search를 사용 하는 AI 지원 클라우드 서비스
[Azure Cognitive Search](https://azure.microsoft.com/services/search/) 는 웹, 모바일 및 엔터프라이즈 응용 프로그램에 있는 개인, 다른 유형의 콘텐츠에 대해 풍부한 검색 환경을 추가 하기 위한 개발자 api 및 도구를 제공 하는 서비스를 제공 하는 서비스 클라우드 솔루션입니다. 사용자 지정 코드는 데이터 수집(인덱싱)을 호출하여 인덱스를 만들고 로드합니다. 다른 쪽에서는 애플리케이션 코드가 쿼리 요청을 발행하고 응답을 처리합니다. 검색 환경은 Azure Cognitive Search의 기능을 사용하여 클라이언트에 정의되며, 서비스에서 만들고, 소유하고, 저장하는 지속형 인덱스에 대한 쿼리를 실행합니다.

## <a name="azure-cognitive-search-features"></a>Azure Cognitive Search 기능
- 전체 텍스트 검색, 단순 쿼리 구문 및 lucene 쿼리 구문을 사용 하 여 **자유 형식 텍스트 검색**
- **간단한 점수 매기기 프로필** 을 통해 문서 자체에서 값의 함수로 관련성을 모델링할 수 있습니다.
- **지리적 검색** 을 통해 사용자는 검색 결과의 근접성을 기반으로 하는 데이터를 실제 위치에 탐색할 수 있습니다.
- **사용자** 는 자동 완성, 검색 제안, 정렬 및 페이징을 통해 기능을 제공 합니다.
- 이미지 및 텍스트 분석에 대 한 **인식 검색** 을 인덱싱 파이프라인에 적용 하 여 원시 콘텐츠에서 텍스트 정보를 추출할 수 있습니다.
- 인덱싱 중에 생성 되는 강화를 저장 하는 **기술 자료 저장소** 입니다.
- Azure SQL Database, Azure Cosmos DB 또는 Azure Blob storage를 **데이터 원본** 으로 연결 하 여 기본 데이터 저장소에서 검색 가능한 콘텐츠를 추출 합니다.
- **데이터 가져오기 마법사** 를 사용 하 여 인덱서를 구성 하 고 인덱스 **디자이너** 를 사용 하 여 인덱스를 구성 하 고 **검색 탐색기** 를 사용 하 여 쿼리를 테스트 하 고 점수 매기기 프로필을 구체화할

Azure Cognitive Search [기능](/azure/search/search-what-is-azure-search#feature-descriptions)에 대 한 자세한 설명을 확인 하세요.

## <a name="references"></a>참조
- [Azure Portal](https://portal.azure.com) 
- [설명서](/azure/search/)
- [개발자 가이드](https://azure.microsoft.com/resources/iot-developers-guide/)
- [Azure Cognitive Search를 사용 하는 방법](/azure/search/search-what-is-azure-search#how-to-use-azure-cognitive-search)
- [빠른 시작](/azure/search/search-create-service-portal)

  