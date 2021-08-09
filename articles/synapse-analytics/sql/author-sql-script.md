---
title: Synapse Studio의 SQL 스크립트
description: Azure synapse Analytics의 Synapse Studio SQL 스크립트에 대해 소개합니다.
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: omafnan
ms.openlocfilehash: 4ed02901aa0d6948e9c6443e5bbcf4ebfbc872f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98118696"
---
# <a name="synapse-studio-sql-scripts-in-azure-synapse-analytics"></a>Azure Synapse Analytics의 Synapse Studio SQL 스크립트 

Synapse Studio는 SQL 쿼리를 작성하는 데 사용할 수 있는 SQL 스크립트 웹 인터페이스를 제공합니다. 

## <a name="begin-authoring-in-sql-script"></a>SQL 스크립트에서 작성 시작 

SQL 스크립트에서 작성 환경을 시작하는 방법에는 여러 가지가 있습니다. 다음 방법 중 하나를 통해 새 SQL 스크립트를 만들 수 있습니다.

1. 개발 메뉴에서 **“+”** 아이콘을 선택하고 **SQL 스크립트** 를 선택합니다.

2. **작업** 메뉴에서 **새 SQL 스크립트** 를 선택합니다.

3. SQL 스크립트 개발의 **작업** 메뉴에서 **가져오기** 를 선택합니다. 로컬 스토리지에서 기존 SQL 스크립트를 선택합니다.
![새 SQL 스크립트 3 작업](media/author-sql-script/new-sql-script-3-actions.png)

## <a name="create-your-sql-script"></a>SQL 스크립트 만들기

1. **속성** 단추를 선택하고 SQL 스크립트에 할당된 기본 이름을 바꿔 SQL 스크립트의 이름을 선택합니다. 
![새 SQL 스크립트 이름 바꾸기](media/author-sql-script/new-sql-script-rename.png)

2. **연결 대상** 드롭다운 메뉴에서 특정 전용 SQL 풀 또는 서버리스 SQL 풀을 선택합니다. 또는 필요한 경우 **데이터베이스 사용** 에서 데이터베이스를 선택합니다. 
![새 SQL 풀 선택](media/author-sql-script/new-sql-choose-pool.png)

3. intellisense 기능을 사용하여 SQL 스크립트 작성을 시작합니다.

## <a name="run-your-sql-script"></a>SQL 스크립트 실행

**실행** 단추를 선택하여 SQL 스크립트를 실행합니다. 결과는 기본적으로 테이블에 표시됩니다.

![새 SQL 스크립트 결과 테이블](media/author-sql-script/new-sql-script-results-table.png)

## <a name="export-your-results"></a>결과 내보내기

“결과 내보내기”를 선택하고 확장을 선택하여 다양한 형식(CSV, Excel, JSON, XML 포함)으로 결과를 로컬 스토리지로 내보낼 수 있습니다.

**차트** 단추를 선택하여 차트에서 SQL 스크립트 결과를 시각화할 수도 있습니다. “차트 유형” 및 **범주 열** 을 선택합니다. **이미지로 저장** 을 선택하여 차트를 그림으로 내보낼 수 있습니다. 

![새 SQL 스크립트 결과 차트](media/author-sql-script/new-sql-script-results-chart.png)

## <a name="explore-data-from-a-parquet-file"></a>Parquet 파일에서 데이터 검색

SQL 스크립트를 사용하여 스토리지 계정에서 Parquet 파일을 살펴보고 파일 내용을 미리 볼 수 있습니다.

![새 스크립트 sqlod parquet](media/author-sql-script/new-script-sqlod-parquet.png)

## <a name="sql-tables-external-tables-views"></a>SQL 테이블, 외부 테이블, 뷰

데이터 아래에서 **작업** 메뉴를 선택하여 다음과 같은 몇 가지 작업을 선택할 수 있습니다.

- 새 SQL 스크립트
- 상위 1000개의 행 선택
- CREATE
- DROP 및 CREATE 
 
SQL 데이터베이스의 노드를 마우스 오른쪽 단추로 클릭하여 사용 가능한 제스처를 살펴봅니다.
 
![새 스크립트 데이터베이스](media/author-sql-script/new-script-database.png)

## <a name="create-folders-and-move-sql-scripts-into-a-folder"></a>폴더 만들기 및 SQL 스크립트를 폴더로 이동

SQL 스크립트 개발의 “작업” 메뉴에서 “새 폴더”를 선택합니다. 팝업 창에 새 폴더의 이름을 입력합니다. 

> [!div class="mx-imgBorder"] 
> ![‘새 폴더’가 선택된 SQL 스크립트의 예제를 보여 주는 스크린샷](./media/author-sql-script/new-sql-script-create-folder.png)

SQL 스크립트를 폴더로 이동하려면 SQL 스크립트를 선택하고 작업 메뉴에서 “이동 대상”을 선택하면 됩니다. 그런 다음, 새 창에서 대상 폴더를 찾고 SQL 스크립트를 선택한 폴더로 이동합니다. SQL 스크립트를 신속하게 끌어 폴더에 놓을 수도 있습니다.  

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/new-sql-script-move-folder.png)

## <a name="next-steps"></a>다음 단계

SQL 스크립트를 작성하는 방법에 대한 자세한 내용은 [Azure Synapse Analytics](../index.yml)를 참조하세요.