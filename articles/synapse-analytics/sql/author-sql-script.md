---
title: Azure Synapse Studio의 SQL 스크립트 (미리 보기)
description: Azure Synapse Studio (미리 보기) SQL 스크립트 소개
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: omafnan
ms.openlocfilehash: 3f3009799889bd6b118f586676e22338d821d37c
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2020
ms.locfileid: "94635292"
---
# <a name="using-sql-scripts-in-azure-synapse-studio-preview"></a>Azure Synapse Studio에서 SQL 스크립트 사용 (미리 보기)

Azure Synapse Studio (미리 보기)는 SQL 쿼리를 작성 하는 데 사용할 수 있는 SQL 스크립트 웹 인터페이스를 제공 합니다. SQL 풀 (미리 보기)에 연결할 수 있습니다. 

## <a name="begin-authoring-in-sql-script"></a>SQL 스크립트에서 작성 시작 

SQL 스크립트에서 제작 환경을 시작 하는 방법에는 여러 가지가 있습니다. 다음 방법 중 하나를 통해 새 SQL 스크립트를 만들 수 있습니다.

1. 개발 메뉴에서 **"+"** 아이콘을 선택 하 고 **SQL 스크립트** 를 선택 합니다.

2. **작업** 메뉴에서 **새 SQL 스크립트** 를 선택 합니다.

3. SQL 스크립트 개발의 **작업** 메뉴에서 **가져오기** 를 선택 합니다. 로컬 저장소에서 기존 SQL 스크립트를 선택 합니다.
![새 sql 스크립트 3 동작](media/author-sql-script/new-sql-script-3-actions.png)

## <a name="create-your-sql-script"></a>SQL 스크립트 만들기

1. **속성** 단추를 선택 하 고 sql 스크립트에 할당 된 기본 이름을 대체 하 여 sql 스크립트의 이름을 선택 합니다. 
![새 sql 스크립트 이름 바꾸기](media/author-sql-script/new-sql-script-rename.png)

2. **연결 대상** 드롭다운 메뉴에서 특정 전용 sql 풀 또는 서버를 사용 하지 않는 sql 풀을 선택 합니다. 또는 필요한 경우 데이터베이스 **사용** 에서 데이터베이스를 선택 합니다. 
![새 sql 풀 선택](media/author-sql-script/new-sql-choose-pool.png)

3. Intellisense 기능을 사용 하 여 SQL 스크립트 제작을 시작 합니다.

## <a name="run-your-sql-script"></a>SQL 스크립트 실행

**실행** 단추를 선택 하 여 SQL 스크립트를 실행 합니다. 결과는 기본적으로 테이블에 표시 됩니다.

![새 sql 스크립트 결과 테이블](media/author-sql-script/new-sql-script-results-table.png)

## <a name="export-your-results"></a>결과 내보내기

"결과 내보내기"를 선택 하 고 확장을 선택 하 여 다른 형식 (CSV, Excel, JSON, XML 등)으로 결과를 로컬 저장소로 내보낼 수 있습니다.

**차트** 단추를 선택 하 여 차트에서 SQL 스크립트 결과를 시각화할 수도 있습니다. "차트 종류" 및 **범주 열** 을 선택 합니다. **이미지로 저장** 을 선택 하 여 차트를 그림으로 내보낼 수 있습니다. 

![새 sql 스크립트 결과 차트](media/author-sql-script/new-sql-script-results-chart.png)

## <a name="explore-data-from-a-parquet-file"></a>Parquet 파일에서 데이터 탐색

SQL 스크립트를 사용 하 여 저장소 계정의 Parquet 파일을 탐색 하 여 파일 콘텐츠를 미리 볼 수 있습니다.

![새 스크립트 sqlod parquet](media/author-sql-script/new-script-sqlod-parquet.png)

## <a name="sql-tables-external-tables-views"></a>SQL 테이블, 외부 테이블, 뷰

데이터에서 **작업** 메뉴를 선택 하 여 다음과 같은 여러 가지 작업을 선택할 수 있습니다.

- 새 SQL 스크립트
- 상위 1000 행 선택
- CREATE
- DROP 및 CREATE 
 
SQL 데이터베이스의 노드를 마우스 오른쪽 단추로 클릭 하 여 사용 가능한 제스처를 탐색 합니다.
 
![새 스크립트 데이터베이스](media/author-sql-script/new-script-database.png)

## <a name="create-folders-and-move-sql-scripts-into-a-folder"></a>폴더 만들기 및 SQL 스크립트를 폴더로 이동

SQL 스크립트 개발의 동작 메뉴에서 SQL 스크립트 개발의 "작업" 메뉴에서 "새 폴더"를 선택 합니다. 을 입력 하 고 팝업 창에서 새 폴더의 이름을 입력 합니다. 

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/new-sql-script-create-folder.png)

SQL 스크립트를 폴더로 이동 하려면 sql 스크립트를 선택 하 고 동작 메뉴에서 "이동"을 선택 하면 됩니다. 그런 다음 새 창에서 대상 폴더를 찾고 sql 스크립트를 선택한 폴더로 이동 합니다. Sql 스크립트를 신속 하 게 끌어 폴더에 놓을 수도 있습니다.  

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/new-sql-script-move-folder.png)

## <a name="next-steps"></a>다음 단계

SQL 스크립트를 작성 하는 방법에 대 한 자세한 내용은 [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)를 참조 하세요.
