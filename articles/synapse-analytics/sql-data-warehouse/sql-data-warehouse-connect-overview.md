---
title: 시냅스 SQL 풀에 연결
description: SQL 풀에 연결합니다.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 70dd1ae883ee1f44672dccb802c0e770d7676440
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619076"
---
# <a name="connect-to-synapse-sql-pool"></a>시냅스 SQL 풀에 연결
SQL 풀에 연결합니다.

## <a name="find-your-server-name"></a>서버 이름 찾기
다음 예제의 서버 이름은 sqlpoolservername.database.windows.net. 정규화된 서버 이름을 찾으려면:

1. [Azure 포털](https://portal.azure.com)로 이동합니다.
2. Azure **시냅스 분석을 클릭합니다.**
3. 연결하려는 SQL 풀을 클릭합니다.
4. 전체 서버 이름을 찾습니다.
   
    ![전체 서버 이름](media/sql-data-warehouse-connect-overview/server-connect.PNG)

## <a name="supported-drivers-and-connection-strings"></a>지원되는 드라이버 및 연결 문자열
SQL 풀은 [ADO.NET,](https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx) [ODBC,](https://msdn.microsoft.com/library/jj730314.aspx) [PHP](https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396)및 [JDBC를](https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx)지원합니다. 최신 버전 및 설명서를 찾으려면 이전 드라이버 중 하나를 클릭합니다. 

Azure Portal에서 사용 중인 드라이버에 대한 연결 문자열을 자동으로 생성하려면 앞의 예제에서 **데이터베이스 연결 문자열 표시**를 클릭합니다. 또한 각 드라이버에 대한 연결 문자열의 모양에 대한 몇 가지 예는 다음과 같습니다.

> [!NOTE]
> 사용 불가능한 짧은 시간 동안 연결이 효력을 유지하려면 연결 제한 시간을 300초로 설정하는 것이 좋습니다.
> 
> 

### <a name="adonet-connection-string-example"></a>ADO.NET 연결 문자열 예제
```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### <a name="odbc-connection-string-example"></a>ODBC 연결 문자열 예제
```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### <a name="php-connection-string-example"></a>PHP 연결 문자열 예제
```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### <a name="jdbc-connection-string-example"></a>JDBC 연결 문자열 예제
```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="connection-settings"></a>연결 설정
SQL 풀은 연결 및 개체 를 만드는 동안 일부 설정을 표준화합니다. 이러한 설정은 재정의되거나 다음을 포함할 수 없습니다.

| 데이터베이스 설정 | 값 |
|:--- |:--- |
| [ANSI_NULLS](https://msdn.microsoft.com/library/ms188048.aspx) |켜기 |
| [QUOTED_IDENTIFIERS](https://msdn.microsoft.com/library/ms174393.aspx) |켜기 |
| [DATEFORMAT](https://msdn.microsoft.com/library/ms189491.aspx) |mdy |
| [날짜 우선](https://msdn.microsoft.com/library/ms181598.aspx) |7 |

## <a name="next-steps"></a>다음 단계
Visual Studio를 사용하여 연결하고 쿼리하려면 [Visual Studio를 사용하여 쿼리](sql-data-warehouse-query-visual-studio.md)를 참조하세요. 인증 옵션에 대한 자세한 내용은 [Azure Synapse 분석에 대한 인증을](sql-data-warehouse-authentication.md)참조하십시오.

