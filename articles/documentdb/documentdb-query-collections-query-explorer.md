---
title: "DocumentDB 쿼리 탐색기: SQL 쿼리 편집기 | Microsoft Docs"
description: "DocumentDB 컬렉션에 대해 SQL 쿼리를 작성하고 실행하기 위해 Azure 포털의 SQL 쿼리 편집기인 DocumentDB 쿼리 탐색기에 대해 알아봅니다."
keywords: "SQL 쿼리 작성, SQL 쿼리 편집기"
services: documentdb
author: kirillg
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: ac378240-b11f-4522-ae9f-09da3a6f9c16
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2016
ms.author: kirillg
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4a4e66ff9022b153fe09fba004ff50aed053b392


---
# <a name="write-edit-and-run-sql-queries-for-documentdb-using-query-explorer"></a>쿼리 탐색기를 사용하여 DocumentDB에 대한 SQL 쿼리 작성, 편집 및 실행
이 문서에서는 [DocumentDB 컬렉션](documentdb-create-collection.md)에 대해 SQL 쿼리를 작성하고 편집하고 실행할 수 있게 해주는 Azure Portal 도구인 [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) 쿼리 탐색기에 대해 개괄적으로 설명합니다.

1. Azure 포털의 이동 표시줄에서 **DocumentDB(NoSQL)**를 클릭합니다. **DocumentDB(NoSQL)**가 표시되지 않으면 **서비스 더 보기**를 클릭한 다음 **DocumentDB(NoSQL)**를 클릭합니다.
2. 리소스 메뉴에서 **쿼리 탐색기**를 클릭합니다. 
   
    ![쿼리 탐색기가 강조 표시된 Azure 포털 스크린샷](./media/documentdb-query-collections-query-explorer/queryexplorercommand.png)
3. **쿼리 탐색기** 블레이드의 드롭다운 목록에서 쿼리할 **데이터베이스** 및 **컬렉션**을 선택하고 실행할 쿼리를 입력합니다. 
   
    **데이터베이스** 및 **컬렉션** 드롭다운 목록은 쿼리 탐색기를 실행하는 컨텍스트에 따라 미리 채워집니다. 
   
    기본 쿼리 `SELECT TOP 100 * FROM c` 가 제공됩니다.  기본 쿼리를 적용하거나 [SQL 쿼리 치트 시트](documentdb-sql-query-cheat-sheet.md) 또는 [SQL 쿼리 및 SQL 구문](documentdb-sql-query.md) 문서에서 설명하는 SQL 쿼리 언어를 사용하여 쿼리를 직접 생성할 수 있습니다.
   
    결과를 보려면 **쿼리 실행** 을 클릭합니다.
   
    ![SQL 쿼리 편집기인 쿼리 탐색기에서 SQL 쿼리 작성 스크린 샷](./media/documentdb-query-collections-query-explorer/queryexplorerinitial.png)
4. **결과** 블레이드에 쿼리의 출력이 표시됩니다. 
   
    ![쿼리 탐색기에서 SQL 쿼리를 작성한 결과의 스크린샷](./media/documentdb-query-collections-query-explorer/queryresults1.png)

## <a name="work-with-results"></a>결과 작업
기본적으로 쿼리 탐색기는 100개 단위로 결과를 반환합니다.  쿼리로 생성되는 결과가 100개 이상이면 단순히 **다음 페이지** 및 **이전 페이지** 명령을 사용하여 결과 집합을 탐색할 수 있습니다.

![쿼리 탐색기 페이지 매김 스크린샷](./media/documentdb-query-collections-query-explorer/queryresultspagination.png)

쿼리가 정상적으로 실행된 경우 **정보** 창에는 요청 비용, 쿼리의 왕복 횟수, 현재 표시된 결과 집합 및 결과가 더 있는지(앞에서 설명한 대로 **다음 페이지** 명령을 사용하여 액세스할 수 있음)와 같은 메트릭이 있습니다.

![쿼리 탐색기 쿼리 정보 스크린샷](./media/documentdb-query-collections-query-explorer/queryinformation.png)

## <a name="use-multiple-queries"></a>여러 쿼리 사용
여러 쿼리를 사용 중이며 쿼리 간을 빠르게 전환하려는 경우 **쿼리 탐색기** 블레이드의 쿼리 텍스트 상자에 모든 쿼리를 입력하고 실행하려는 쿼리를 강조 표시한 다음 **쿼리 실행**을 클릭하여 결과를 확인하면 됩니다.

![쿼리 탐색기(SQL 쿼리 편집기)에서 여러 SQL 쿼리를 작성하고 개별 쿼리를 강조 표시하여 실행한 스크린 샷](./media/documentdb-query-collections-query-explorer/queryexplorerhighlightandrun.png)

## <a name="add-queries-from-a-file-into-the-sql-query-editor"></a>파일의 쿼리를 SQL 쿼리 편집기에 추가
**파일 로드** 명령을 사용하여 기존 파일의 내용을 로드할 수 있습니다.

![파일 로드를 사용하여 파일의 SQL 쿼리를 쿼리 탐색기에 로드하는 방법을 보여주는 스크린 샷](./media/documentdb-query-collections-query-explorer/loadqueryfile.png)

## <a name="troubleshoot"></a>문제 해결
쿼리가 완료될 때 오류가 발생하면, 쿼리 탐색기에 문제 해결에 도움이 될 수 있는 오류 목록이 표시됩니다.

![쿼리 탐색기 쿼리 오류 스크린샷](./media/documentdb-query-collections-query-explorer/queryerror.png)

## <a name="run-documentdb-sql-queries-outside-the-portal"></a>포털 외부 DocumentDB SQL 쿼리 실행
Azure 포털의 쿼리 탐색기는 DocumentDB에 대해 SQL 쿼리를 실행하는 방법 중 한 가지입니다. [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) 또는 [클라이언트 SDK](documentdb-sdk-dotnet.md)를 사용하여 SQL 쿼리를 실행할 수도 있습니다. 이러한 다른 방법에 대한 자세한 내용은 [SQL 쿼리 실행](documentdb-sql-query.md#executing-sql-queries)

## <a name="next-steps"></a>다음 단계
쿼리 탐색기에서 지원되는 DocumentDB SQL 문법에 대해 자세히 알아보려면 [SQL 쿼리 및 SQL 구문](documentdb-sql-query.md) 문서를 참조하거나 [SQL 쿼리 치트 시트](documentdb-sql-query-cheat-sheet.md)를 인쇄합니다.
샘플 데이터 집합을 사용하여 쿼리를 온라인으로 테스트할 수 있는 [Query Playground](https://www.documentdb.com/sql/demo) 를 통해 다양한 쿼리를 실행해 볼 수도 있습니다.




<!--HONumber=Nov16_HO3-->


