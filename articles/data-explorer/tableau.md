---
title: Azure 데이터 탐색기를 열고 데이터베이스 연결 (ODBC) 연결을 사용 하 여 Tableau 사용 하 여 데이터 시각화
description: 이 문서에서는 Azure 데이터 탐색기 연결에는 개방형 데이터베이스 연결 (ODBC) 연결을 사용 하 여 Tableau 사용 하 여 데이터를 시각화 하는 방법을 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: a0948ae35a5c23768df117979db819861ac64529
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66499086"
---
# <a name="visualize-data-from-azure-data-explorer-in-tableau"></a>Tableau의 Azure 데이터 탐색기에서 데이터 시각화

 [Tableau](https://www.tableau.com/) 는 비즈니스 인텔리전스 시각적 분석 플랫폼입니다. Tableau에서 Azure 데이터 탐색기로 연결한 샘플 클러스터에서 데이터를 가져와서, SQL Server 열린 데이터베이스 연결 (ODBC) 드라이버를 사용 합니다. 

## <a name="prerequisites"></a>필수 조건

이 문서를 완료 하려면 다음이 필요 합니다.

* [ODBC 사용 하 여 Azure 데이터 탐색기를 연결할](connect-odbc.md) Tableau에서 데이터 탐색기를 Azure에 연결할 SQL Server ODBC 드라이버를 사용 합니다. 

* Tableau Desktop을 전체 또는 [평가판](https://www.tableau.com/products/desktop/download) 버전입니다.

* StormEvents 샘플 데이터가 포함된 클러스터입니다. 자세한 내용은 [Azure 데이터 탐색기 클러스터 및 데이터베이스를 만드는](create-cluster-database-portal.md) 하 고 [Azure 데이터 탐색기로 샘플 데이터를 수집](ingest-sample-data.md)합니다.

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="visualize-data-in-tableau"></a>Tableau에서 데이터 시각화 

ODBC 구성 마친 후에 Tableau에 샘플 데이터를 가져올 수 있습니다.

1. Tableau Desktop의 왼쪽된 메뉴에서 선택 **다른 데이터베이스 (ODBC)** 합니다.

    ![ODBC로 연결](media/tableau/connect-odbc.png)

1. 에 대 한 **DSN**odbc의 경우 만든 데이터 원본을 선택한 다음 선택 **로그인**합니다.

    ![ODBC 로그인](media/tableau/odbc-sign-in.png)

1. 에 대 한 **데이터베이스**와 같은 샘플 클러스터에서 데이터베이스를 선택 *TestDatabase*합니다. 에 대 한 **스키마**를 선택 *dbo*, 및 **테이블**를 선택 합니다 *StormEvents* 샘플 테이블.

    ![데이터베이스 및 테이블 선택](media/tableau/select-database-table.png)

1. Tableau에는 이제 샘플 데이터에 대 한 스키마를 보여 줍니다. 선택 **지금 업데이트** Tableau에 데이터를 가져올 수 있습니다.

    ![데이터 업데이트](media/tableau/update-data.png)

    데이터를 가져올 때 Tableau 개의 데이터 행 표시 다음 이미지와 유사 합니다.

    ![결과 집합](media/tableau/result-set.png)

1. 이제 Azure 데이터 탐색기에서 가져온 데이터를 기반으로 하는 Tableau에서 시각화를 만들 수 있습니다. 자세한 내용은 [Tableau Learning](https://www.tableau.com/learn)합니다.

## <a name="next-steps"></a>다음 단계

* [Azure 데이터 탐색기용 쿼리 작성](write-queries.md)