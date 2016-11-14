---
title: "Azure Portal을 사용하여 Azure 검색 인덱스 쿼리 | Microsoft Docs"
description: "Azure 포털의 검색 탐색기에서 검색 쿼리를 실행합니다."
services: search
manager: jhubbard
documentationcenter: 
author: ashmaka
ms.assetid: 8e524188-73a7-44db-9e64-ae8bf66b05d3
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 08/29/2016
ms.author: ashmaka
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a23372112e17703a3399e1bdc9eaf73b85a1f80d


---
# <a name="query-your-azure-search-index-using-the-azure-portal"></a>Azure 포털을 사용하여 Azure 검색 인덱스 쿼리
> [!div class="op_single_selector"]
> * [개요](search-query-overview.md)
> * [포털](search-explorer.md)
> * [.NET](search-query-dotnet.md)
> * [REST (영문)](search-query-rest-api.md)
> 
> 

이 가이드에서는 Azure 포털에서 Azure 검색 인덱스를 쿼리하는 방법을 보여 줍니다.

이 연습을 시작하기 전에 [Azure 검색 인덱스를 만들고](search-what-is-an-index.md) [데이터로 채워야](search-what-is-data-import.md) 합니다.

## <a name="i-go-to-your-azure-search-blade"></a>I. Azure 검색 블레이드로 이동
1.  [Azure 포털](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)
2. Azure 검색 서비스 선택

## <a name="ii-select-the-index-you-would-like-to-search"></a>II. 검색하려는 인덱스 선택
1. "인덱스" 타일에서 검색하려는 인덱스를 선택합니다.

![](./media/search-explorer/pick-index.png)

## <a name="iii-click-on-the-search-explorer-tile"></a>III. "검색 탐색기" 타일 클릭
![](./media/search-explorer/search-explorer-tile.png)

## <a name="iii-start-searching"></a>III. 검색 시작
1. Azure 검색 인덱스를 검색하려면 "*쿼리 문자열*" 필드에 입력을 시작한 후 "**검색**"을 누릅니다.
   
   * 검색 탐색기를 사용하는 경우 [쿼리 매개 변수](https://msdn.microsoft.com/library/dn798927.aspx)
2. "*결과*" 섹션에서 쿼리 결과가 Azure 검색 REST API에 대해 검색 요청을 실행할 때 HTTP 응답 본문에 수신하는 원시 JSON에 표시됩니다.
3. 쿼리 문자열은 적절한 요청 URL로 자동으로 구문 분석되어 Azure 검색 REST API에 대해 HTTP 요청을 제출합니다.

![](./media/search-explorer/search-bar.png)




<!--HONumber=Nov16_HO2-->


