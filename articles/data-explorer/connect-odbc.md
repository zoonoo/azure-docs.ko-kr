---
title: ODBC 사용 하 여 Azure 데이터 탐색기로 연결
description: 이 방법에서는 Azure 데이터 탐색기로 ODBC 연결을 설정한 다음 해당 연결을 사용 하 여 Tableau 사용 하 여 데이터를 시각화 하는 방법을 배웁니다.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/21/2019
ms.openlocfilehash: ad00ad247b047d4acf97eb5d0e96229949181ecf
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/23/2019
ms.locfileid: "56740624"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>ODBC 사용 하 여 Azure 데이터 탐색기로 연결

Open Database Connectivity ([ODBC](/sql/odbc/reference/odbc-overview))는 데이터베이스 액세스를 위해 널리 사용 되는 응용 프로그램 프로그래밍 인터페이스 (API). 전용된 커넥터 없는 응용 프로그램에서 Azure 데이터 탐색기에 연결할 ODBC를 사용 합니다.

응용 프로그램 내부적으로 호출 하는 데이터베이스 관련 모듈에서 구현 된 ODBC 인터페이스에서 함수를 호출할 *드라이버*합니다. Azure 데이터 탐색기는 SQL Server 통신 프로토콜의 하위 집합을 지원 합니다 ([MS TDS](/azure/kusto/api/tds/)); 따라서 해당 SQL Server에 대 한 ODBC 드라이버를 사용할 수 있습니다.

이 문서에서는 ODBC를 지 원하는 응용 프로그램에서 Azure 데이터 탐색기로 연결할 수 있도록 SQL Server ODBC 드라이버를 사용 하는 방법을 알아봅니다. 그런 다음 필요에 따라 Tableau에서 데이터 탐색기를 Azure에 연결 하 샘플 클러스터에서 데이터를 가져옵니다.

## <a name="prerequisites"></a>필수 조건

이 방법을 완료하려면 다음 사항이 필요합니다.

* [Microsoft ODBC Driver for SQL Server 버전 17.2.0.1 이상](/sql/connect/odbc/download-odbc-driver-for-sql-server) 운영 체제에 대 한 합니다.

* Tableau 예제를 수행 하려는 경우 또한 필요 합니다.

  * Tableau Desktop을 전체 또는 [평가판](https://www.tableau.com/products/desktop/download) 버전입니다.

  * StormEvents 샘플 데이터가 포함된 클러스터입니다. 자세한 내용은 [빠른 시작: Azure Data Explorer 클러스터 및 데이터베이스 만들기](create-cluster-database-portal.md) 및 [Azure Data Explorer로 샘플 데이터 수집](ingest-sample-data.md)을 참조하세요.

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="configure-the-odbc-data-source"></a>ODBC 데이터 원본 구성

SQL Server 용 ODBC 드라이버를 사용 하는 ODBC 데이터 원본을 구성 하려면 다음이 단계를 따릅니다.

1. Windows에서 검색할 *ODBC 데이터 원본*, ODBC 데이터 원본 데스크톱 앱을 엽니다.

1. **추가**를 선택합니다.

    ![데이터 원본 추가](media/connect-odbc/add-data-source.png)

1. 선택 **SQL Server 용 ODBC 드라이버 17** 한 다음 **마침**합니다.

    ![드라이버 선택](media/connect-odbc/select-driver.png)

1. 연결 및 클러스터에 연결 하려면 클릭에 대 한 이름 및 설명 입력 **다음**합니다. 클러스터 URL 형식에서 이어야 합니다  *\<ClusterName\>.\< 지역\>. kusto.windows.net*합니다.

    ![서버 선택](media/connect-odbc/select-server.png)

1. 선택 **Active Directory 통합** 한 다음 **다음**합니다.

    ![Active Directory 통합](media/connect-odbc/active-directory-integrated.png)

1. 샘플 데이터를 사용 하 여 데이터베이스를 선택한 다음 **다음**합니다.

    ![기본 데이터베이스 변경](media/connect-odbc/change-default-database.png)

1. 다음 화면에서 모든 옵션 선택 기본값으로 둡니다 **완료**합니다.

1. 선택 **데이터 원본을 테스트**합니다.

    ![데이터 원본 테스트](media/connect-odbc/test-data-source.png)

1. 다음 테스트에 성공 했는지 확인 **확인**합니다. 테스트 성공 하지 않은 경우 이전 단계에서 지정한 값을 확인 하 고 클러스터에 연결할 수 있는 권한이 확인 합니다.

    ![테스트 성공](media/connect-odbc/test-succeeded.png)

## <a name="visualize-data-in-tableau-optional"></a>(선택 사항) Tableau에서 데이터 시각화

ODBC 구성을 완료 했다면, 이제 Tableau에 샘플 데이터를 가져올 수 있습니다.

1. Tableau Desktop의 왼쪽된 메뉴에서 선택 **다른 데이터베이스 (ODBC)** 합니다.

    ![ODBC로 연결](media/connect-odbc/connect-odbc.png)

1. 에 대 한 **DSN**odbc의 경우 만든 데이터 원본을 선택한 다음 선택 **로그인**합니다.

    ![ODBC 로그인](media/connect-odbc/odbc-sign-in.png)

1. 에 대 한 **데이터베이스**와 같은 샘플 클러스터에서 데이터베이스를 선택 *TestDatabase*합니다. 에 대 한 **스키마**를 선택 *dbo*, 및 **테이블**를 선택 합니다 *StormEvents* 샘플 테이블.

    ![데이터베이스 및 테이블 선택](media/connect-odbc/select-database-table.png)

1. Tableau에는 이제 샘플 데이터에 대 한 스키마를 보여 줍니다. 선택 **지금 업데이트** Tableau에 데이터를 가져올 수 있습니다.

    ![데이터 업데이트](media/connect-odbc/update-data.png)

    데이터를 가져올 때 Tableau 개의 데이터 행 표시 다음 이미지와 유사 합니다.

    ![결과 집합](media/connect-odbc/result-set.png)

1. 이제 Azure 데이터 탐색기에서 가져온 데이터를 기반으로 하는 Tableau에서 시각화를 만들 수 있습니다. 자세한 내용은 [Tableau Learning](https://www.tableau.com/learn)합니다.

## <a name="next-steps"></a>다음 단계

[Azure 데이터 탐색기용 쿼리 작성](write-queries.md)

[자습서: ](visualize-power-bi.md) Power BI에서 Azure Data Explorer의 데이터 시각화