---
title: Azure Search의 비디오, 샘플 및 자습서 | Microsoft Docs
description: Microsoft Azure에서 호스팅되는 클라우드 서비스인 Azure Search에 대해 만들어진 모든 비디오, 샘플, 데모 및 자습서의 중앙 목록입니다.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: heidist
ms.openlocfilehash: 197d54480d987b3826f129e9e1264caf4a1998b8
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757590"
---
# <a name="azure-search-code-samples-demos-videos-and-tutorials"></a>Azure Search: 코드 샘플, 데모, 비디오 및 자습서
다음 비디오, 데모, 자습서 및 샘플 앱으로 빠르게 기능을 활용해 보세요.

## <a name="learn"></a>유용한 정보

Azure Search를 처음 사용하는 경우 여기에서 시작합니다. 약 1시간 동안 이러한 모든 리소스를 검토할 수 있습니다.

| 제목 | 미디어 | 설명 | Date |
|-------|-------|-------------|------|
| [이 소개 비디오 시청](https://channel9.msdn.com/Events/Connect/2016/138) | 비디오 | 기능, 서비스 만들기 및 쿼리에 대한 9m38s의 소개 내용입니다. | 2016년 11월 |
| [ 서비스 만들기](search-get-started-portal.md) | 문서 | 포털에서 Azure Search를 설정하기 위한 단계입니다. | 2017년 2월 |
| [인덱스 쿼리](search-get-started-portal.md) | 문서 |기본 제공 샘플 데이터를 사용하여 만들고 쿼리합니다. 포털의 도구는 쿼리 형식 및 검색 매개 변수에 대해 알아볼 수 있는 빠른 방법입니다. | 2017년 2월 |
| [다음 단계:<br/> .NET의 코드](search-howto-dotnet-sdk.md) | 문서 |C# 연습에서는 Azure Search .NET 라이브러리를 사용하여 연결, 인덱싱 및 쿼리하는 방법을 보여 줍니다. | 2016년 11월 |
| [다음 단계:<br/> REST의 코드](search-create-index-rest-api.md) | 문서 |임의 플랫폼 및 프로그래밍 언어에로 Azure Search 인덱스를 만들기 위한 REST 호출 및 JSON 페이로드를 검토합니다. | 2016년 12월 |
| [Azure Search의 전체 텍스트 검색 작동 방식](search-lucene-query-architecture.md) | 문서 | Azure Search 전체 텍스트 검색 워크로드의 아키텍처 및 개요 | 2017년 3월 |

## <a name="code-samples"></a>코드 샘플

샘플 및 연습을 통한 코드 기반 탐색입니다.

| 코드 샘플 | 설명 |
|-------|-------------|
| [.NET 클라이언트 라이브러리 시작](https://github.com/Azure-Samples/search-dotnet-getting-started) | .NET 클라이언트 라이브러리를 사용하는 ASP.NET MVC 5 응용 프로그램입니다. |
| [REST API 시작](https://github.com/Azure-Samples/search-rest-api-getting-started) | REST API 코드 샘플입니다. |
| [소스: NYC 작업 웹 사이트 데모](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) | ASP.NET MVC를 사용하는 NYC 작업 데이터에 대해 라이브 데모 Azure Search 사이트의 소스입니다. |
| [소스: Tate Art Gallery 데모](https://github.com/liamca/azure-search-tate-art-gallery/) | 아트 갤러리 데이터를 통한 라이브 데모 Azure Search 사이트에 대한 소스를 제공하며 컬렉션 데이터 형식 및 중첩된 데이터 구조, 패싯 및 필터, 사용자 이름 및 미리보기 이미지를 보여줍니다.|

특정 기능 조합 및 시나리오에 대한 추가 코드 샘플은 [ **Azure 샘플 > Azure Search**](https://github.com/azure-samples?utf8=%E2%9C%93&query=search)에서 찾을 수 있습니다.

## <a name="online-demos"></a>온라인 데모
검색 환경에 대한 Azure Search를 사용하는 모든 기능을 갖춘 웹 응용 프로그램입니다. 이러한 링크는 Azure Search를 사용하는 앱을 실제로 사용해볼 수 있는 환경을 제공합니다.

| 링크 | 설명 |
|-------|-------------|
|[검색 분석기 데모](http://alice.unearth.ai) | 표준 Lucene 분석기, Lucene 영어 분석기 및 Microsoft의 자연어 프로세서의 쿼리 결과를 보여주는 라이브 데모 웹 사이트입니다. 각 검색 용어 입력에 대해 각 분석기의 출력을 나란히 비교하여 볼 수 있습니다. |
|[Azure Search에 대한 NYC(뉴욕시) 구직 포털 데모](http://aka.ms/azjobsdemo) | NYC 오픈 데이터 이니셔티브의 데이터를 사용하여 Azure Search 기능을 설명하는 라이브 ASP.NET 웹 사이트입니다. 이 앱은 [단순 쿼리 구문](https://msdn.microsoft.com/library/azure/dn798920.aspx)을 사용합니다. 코드를 수정하여 [Lucene 구문](https://msdn.microsoft.com/library/azure/mt589323.aspx)을 사용할 수 있습니다. |
|[Tate Art Gallery 데모](https://rawgit.com/liamca/azure-search-tate-art-gallery/master/Demo/index.html) | Tate Art Gallery의 샘플 데이터를 사용하여 호스트된 ASP.NET 웹 사이트입니다. 먼저 검색어로 "modern", "photo" 및 "portrait" 등을 사용해 보세요.
|[Azure Search 데모 사이트](https://searchsamples.azurewebsites.net/#/) | Azure Search 데모 앱의 모음입니다. Azure Search를 사용하여 미디어 검색, 지리적 검색 및 제품 재고 앱을 시험 사용합니다. |

## <a name="online-courseware"></a>온라인 교육 과정

Azure Search에 대한 다중 세션 데모 및 실습입니다.

| 링크 | 공급자| 설명 | Date
|------|-------------|------|------|
[Xamarin 및 Azure Search를 사용하여 지능형 플랫폼 간 모바일 응용 프로그램 빌드](https://mva.microsoft.com/training-courses/building-intelligent-crossplatform-mobile-applications-using-xamarin-and-azure-search-16890) | Microsoft Virtual Academy | 자동 완성, 전체 텍스트 검색 및 페이징, 필터링, 조회, moreLikeThis, Machine Learning, 점수 매기기 및 튜닝, 분석 | 2016년 12월 |
[Azure 데이터 시리즈 > 3부: Azure Search](https://mva.microsoft.com/training-courses/data-series-platform-azure-data-services-17696) | Microsoft Virtual Academy | Azure SQL Database 및 Azure Cosmos DB와 함께 Azure Search를 사용하는 방법을 알아봅니다. | 2017년 7월 | 
[Visual Studio 2017 및 Azure를 사용하여 웹 및 데이터 응용 프로그램 개발 > 5부: Azure Search 및 Language Understanding Intelligent Service](https://mva.microsoft.com/training-courses/web-and-data-application-development-with-visual-studio-2017-and-azure-16931) | Microsoft Virtual Academy | 개발자를 위한 비디오 및 강좌 시리즈. 5부에서는 기본 검색 환경을 구축한 다음, 자연어를 처리할 수 있도록 LUIS로 확장하는 방법을 알아봅니다. | 2016년 11월| 
[Azure Search를 사용하여 앱에 검색 기능 추가](https://www.pluralsight.com/courses/azure-adding-search-abilities-apps)| Pluralsight | REST API를 안내하는 고급 온라인 강좌입니다. | 2016년 9월  | 
