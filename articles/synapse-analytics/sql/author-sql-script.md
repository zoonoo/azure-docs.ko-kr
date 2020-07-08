---
title: Azure Synapse Studio의 SQL 스크립트 (미리 보기)
description: Azure Synapse Studio (미리 보기) SQL 스크립트 소개
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: omafnan
ms.openlocfilehash: ee384d6095ccbf25225a435fe8afe4281c5d62df
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85921394"
---
# <a name="using-sql-script-in-azure-synapse-studio-preview"></a>Azure Synapse Studio에서 SQL 스크립트 사용 (미리 보기)

Azure Synapse Studio (미리 보기)는 SQL 쿼리를 작성 하는 데 사용할 수 있는 SQL 스크립트 웹 인터페이스를 제공 합니다. SQL 풀 (미리 보기) 또는 SQL 주문형 (미리 보기)에 연결할 수 있습니다. 

## <a name="begin-authoring-in-sql-script"></a>SQL 스크립트에서 작성 시작 

SQL 스크립트에서 제작 환경을 시작 하는 방법에는 여러 가지가 있습니다. 다음 방법 중 하나를 통해 새 SQL 스크립트를 만들 수 있습니다.

1. 개발 메뉴에서 **"+"** 아이콘을 선택 하 고 **SQL 스크립트**를 선택 합니다.

    ![newsqlscript](media/author-sql-script/newsqlscript.png)

2. **작업** 메뉴에서 **새 SQL 스크립트**를 선택 합니다.
    
    ![newsqlscript2actions](media/author-sql-script/newsqlscript2actions.png)

또는 

3. SQL 스크립트 개발의 **작업** 메뉴에서 **가져오기** 를 선택 하 고 로컬 저장소에서 기존 sql 스크립트를 선택 합니다.
 
    ![newsqlscript3actions](media/author-sql-script/newsqlscript3actions.png)

## <a name="create-your-sql-script"></a>SQL 스크립트 만들기

1. **속성** 단추를 선택 하 고 sql 스크립트에 할당 된 기본 이름을 대체 하 여 sql 스크립트의 이름을 선택 합니다.
  
    ![newsql스크립트가 trename](media/author-sql-script/newsqlscriptrename.png)

2. **연결 대상** 드롭다운 메뉴에서 특정 sql 풀 또는 주문형 sql을 선택 합니다. 또는 필요한 경우 데이터베이스 **사용**에서 데이터베이스를 선택 합니다.
 
    ![newsqlchoosepool](media/author-sql-script/newsqlchoosepool.png)

3. Intellisense 기능을 사용 하 여 SQL 스크립트 제작을 시작 합니다.

    ![newsqlintellisense](media/author-sql-script/newsqlintellisense.png)

## <a name="run-your-sql-script"></a>SQL 스크립트 실행

**실행** 단추를 선택 하 여 SQL 스크립트를 실행 합니다. 결과는 기본적으로 테이블에 표시 됩니다.

![newsqlscriptresultstable](media/author-sql-script/newsqlscriptresultstable.png)

## <a name="export-your-results"></a>결과 내보내기

"결과 내보내기"를 선택 하 고 확장을 선택 하 여 다른 형식 (CSV, Excel, JSON, XML 등)으로 결과를 로컬 저장소로 내보낼 수 있습니다.

**차트** 단추를 선택 하 여 차트에서 SQL 스크립트 결과를 시각화할 수도 있습니다. "차트 종류" 및 **범주 열**을 선택 합니다. **이미지로 저장**을 선택 하 여 차트를 그림으로 내보낼 수 있습니다. 

![newsqlscriptresultschart](media/author-sql-script/newsqlscriptresultschart.png)

## <a name="explore-data-from-a-parquet-file"></a>Parquet 파일에서 데이터 탐색

SQL 스크립트를 사용 하 여 저장소 계정의 Parquet 파일을 탐색 하 여 파일 콘텐츠를 미리 볼 수 있습니다.

![newscriptsqlodparquet](media/author-sql-script/newscriptsqlodparquet.png)

## <a name="sql-tables-external-tables-views"></a>SQL 테이블, 외부 테이블, 뷰

데이터에서 **작업** 메뉴를 선택 하 여 다음과 같은 여러 가지 작업을 선택할 수 있습니다.

- 새 SQL 스크립트
- 상위 1000 행 선택
- CREATE
- DROP 및 CREATE 
 
SQL 풀 및 SQL 주문형 노드를 마우스 오른쪽 단추로 클릭 하 여 사용 가능한 제스처를 살펴보세요.
 
![newscriptdatabase](media/author-sql-script/newscriptdatabase.png)

## <a name="next-steps"></a>다음 단계

SQL 스크립트를 작성 하는 방법에 대 한 자세한 내용은 [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)를 참조 하세요.
