---
title: Azure 시냅스 스튜디오의 SQL 스크립트(미리 보기)
description: 소개 Azure 시냅스 스튜디오(미리 보기) SQL 스크립트
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: omafnan
ms.openlocfilehash: 940c6d6d96c5c1aa062397d21ea96dace2c09bae
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431073"
---
# <a name="using-sql-script-in-azure-synapse-studio-preview"></a>Azure 시냅스 스튜디오에서 SQL 스크립트 사용(미리 보기)

Azure Synapse Studio(미리 보기)는 SQL 쿼리를 작성할 수 있는 SQL 스크립트 웹 인터페이스를 제공합니다. SQL 풀(미리 보기) 또는 주문형 SQL(미리 보기)에 연결할 수 있습니다. 

## <a name="begin-authoring-in-sql-script"></a>SQL 스크립트작성 시작 

SQL 스크립트에서 작성 환경을 시작하는 방법에는 여러 가지가 있습니다. 다음 방법 중 하나를 통해 새 SQL 스크립트를 만들 수 있습니다.

1. "+" 아이콘을 선택하고 SQL 스크립트를 선택합니다.

    > [!div class="mx-imgBorder"] 
    >![뉴sql스크립트](./media/author-sql-script/newsqlscript.png)

2. SQL 스크립트 개발 아래의 작업 메뉴에서 SQL 스크립트 개발 아래의 "작업" 메뉴에서 "새 SQL 스크립트"를 선택합니다. 

    > [!div class="mx-imgBorder"] 
    > ![뉴sql스크립트](./media/author-sql-script/newsqlscript2actions.png)

또는 

3. SQL 스크립트 개발에서 "작업" 메뉴에서 "가져오기"를 선택하고 로컬 저장소에서 기존 SQL 스크립트를 선택합니다.

    > [!div class="mx-imgBorder"] 
    > ![뉴sql스크립트](./media/author-sql-script/newsqlscript3actions.png)

## <a name="create-your-sql-script"></a>SQL 스크립트 만들기

1. "속성" 단추를 선택하고 SQL 스크립트에 할당된 기본 이름을 대체하여 SQL 스크립트의 이름을 선택합니다.

    > [!div class="mx-imgBorder"] 
    > ![뉴sql스크립트](./media/author-sql-script/newsqlscriptrename.png)

1. "연결" 드롭다운 메뉴에서 특정 SQL 풀 또는 주문형 SQL을 선택합니다. 또는 필요한 경우 "데이터베이스 사용"에서 데이터베이스를 선택합니다.

    > [!div class="mx-imgBorder"] 
    > ![뉴sql스크립트](./media/author-sql-script/newsqlchoosepool.png)

1. intellisense 기능을 사용하여 SQL 스크립트 작성을 시작합니다.

    > [!div class="mx-imgBorder"] 
    > ![뉴sql스크립트](./media/author-sql-script/newsqlintellisense.png)

## <a name="run-your-sql-script"></a>SQL 스크립트 실행

"실행" 단추를 선택하여 SQL 스크립트를 실행합니다. 결과는 기본적으로 테이블에 표시됩니다.

> [!div class="mx-imgBorder"] 
> ![뉴sql스크립트](./media/author-sql-script/newsqlscriptresultstable.png)

## <a name="export-your-results"></a>결과 내보내기

"내보내기 결과"를 선택하고 확장을 선택하여 결과를 CSV, Excel, JSON, XML 등 다양한 형식의 로컬 저장소로 내보낼 수 있습니다.

"차트" 단추를 선택하여 차트에서 SQL 스크립트 결과를 시각화할 수도 있습니다. "차트 유형" 및 "범주 열"을 선택합니다. "이미지로 저장"을 선택하여 차트를 그림으로 내보낼 수 있습니다. 

> [!div class="mx-imgBorder"] 
> ![뉴sql스크립트](./media/author-sql-script/newsqlscriptresultschart.png)

## <a name="explore-data-from-a-parquet-file"></a>마루 파일에서 데이터를 탐색합니다.

SQL 스크립트를 사용하여 저장소 계정에서 Parquet 파일을 탐색하여 파일 내용을 미리 볼 수 있습니다. 

> [!div class="mx-imgBorder"] 
> ![뉴sql스크립트](./media/author-sql-script/newscriptsqlodparquet.png)

## <a name="sql-tables-external-tables-views"></a>SQL 테이블, 외부 테이블, 보기

데이터 에서 "작업" 메뉴를 선택 하 여 "새 SQL 스크립트", "상위 1000 행 선택", "만들기", "삭제 및 만들기"와 같은 몇 가지 작업을 선택할 수 있습니다. SQL 풀 및 주문형 SQL의 노드를 마우스 오른쪽 단추로 클릭하여 사용 가능한 제스처를 탐색합니다.

> [!div class="mx-imgBorder"] 
> ![뉴sql스크립트](./media/author-sql-script/newscriptdatabase.png)

## <a name="next-steps"></a>다음 단계

SQL 스크립트 작성에 대한 자세한 내용은 [Azure Synapse 분석을](https://docs.microsoft.com/azure/synapse-analytics)참조하십시오.