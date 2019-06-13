---
title: ODBC 사용 하 여 Azure 데이터 탐색기로 연결
description: 이 문서에서는 Azure 데이터 탐색기를 열고 데이터베이스 연결 (ODBC) 연결을 설정 하는 방법을 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 02ae9673f1dc402ee1500b466d7e259263ef3262
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66494842"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>ODBC 사용 하 여 Azure 데이터 탐색기로 연결

Open Database Connectivity ([ODBC](/sql/odbc/reference/odbc-overview))는 데이터베이스 액세스를 위해 널리 사용 되는 응용 프로그램 프로그래밍 인터페이스 (API). 전용된 커넥터 없는 응용 프로그램에서 Azure 데이터 탐색기에 연결할 ODBC를 사용 합니다.

응용 프로그램 내부적으로 호출 하는 데이터베이스 관련 모듈에서 구현 된 ODBC 인터페이스에서 함수를 호출할 *드라이버*합니다. Azure 데이터 탐색기는 SQL Server 통신 프로토콜의 하위 집합을 지원 합니다 ([MS TDS](/azure/kusto/api/tds/)) 이므로 SQL Server 용 ODBC 드라이버를 사용할 수 있습니다.

이 문서에서는 ODBC를 지 원하는 응용 프로그램에서 Azure 데이터 탐색기로 연결할 수 있도록 SQL Server ODBC 드라이버를 사용 하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 조건

다음이 필요합니다.

* [Microsoft ODBC Driver for SQL Server 버전 17.2.0.1 이상](/sql/connect/odbc/download-odbc-driver-for-sql-server) 운영 체제에 대 한 합니다.

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

## <a name="next-steps"></a>다음 단계

* [Tableau에서 Azure 데이터 탐색기에 연결](tableau.md)