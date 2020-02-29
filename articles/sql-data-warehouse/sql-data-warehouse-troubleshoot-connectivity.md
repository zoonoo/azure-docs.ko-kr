---
title: 연결 문제 해결
description: SQL Analytics의 연결 문제 해결
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: supportability
ms.date: 03/27/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 003366a6d88e018090475b6fb22d9042a97af823
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78192254"
---
# <a name="troubleshooting-connectivity-issues"></a>연결 문제 해결

이 문서에서는 SQL Analytics 데이터베이스에 연결 하는 방법에 대 한 일반적인 문제 해결 방법을 보여 줍니다.
- [서비스 가용성 확인](./sql-data-warehouse-troubleshoot-connectivity.md#check-service-availability)
- [일시 중지 또는 크기 조정 작업 확인](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-paused-or-scaling-operation)
- [방화벽 설정 확인](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-firewall-settings)
- [VNet/서비스 엔드포인트 설정 확인](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-vnetservice-endpoint-settings)
- [최신 드라이버 확인](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-the-latest-drivers)
- [연결 문자열 확인](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-connection-string)
- [일시적 연결 문제](./sql-data-warehouse-troubleshoot-connectivity.md#intermittent-connection-issues)
- [일반적인 오류 메시지](./sql-data-warehouse-troubleshoot-connectivity.md#common-error-messages)

## <a name="check-service-availability"></a>서비스 가용성 확인

서비스가 사용 가능한지 확인합니다. Azure Portal에서 연결 하려는 SQL 분석 데이터베이스로 이동 합니다. 왼쪽 TOC 패널에서 **문제 진단 및 해결**을 클릭 합니다.

![리소스 상태 선택](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

SQL Analytics의 상태는 여기에 표시 됩니다. 서비스가 **사용 가능한**것으로 표시 되지 않으면 추가 단계를 확인 합니다.

![서비스 사용 가능](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

리소스 상태에서 SQL 분석 인스턴스가 일시 중지 또는 확장 되는 것으로 표시 되는 경우 지침에 따라 인스턴스를 다시 시작 합니다.

![서비스가 일시 중지](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png) Resource Health에 대 한 추가 정보는 여기에서 확인할 수 있습니다.

## <a name="check-for-paused-or-scaling-operation"></a>일시 중지 또는 크기 조정 작업 확인

포털에서 SQL 분석 인스턴스가 일시 중지 되었는지 또는 크기를 조정 하는지 확인 합니다.

![서비스가 일시 중지 됨](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

서비스가 일시 중지 또는 크기 조정이라고 확인되는 경우, 유지 관리 일정 동안에 없는지 확인합니다. SQL Analytics *개요*에 대 한 포털에서 선택 된 유지 관리 일정을 볼 수 있습니다.

![개요 유지 관리 일정](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

그렇지 않으면, 이 유지 관리는 예정된 일정이 아닌 것인지 IT 관리자에게 확인합니다. SQL Analytics 인스턴스를 다시 시작 하려면 [여기](https://docs.microsoft.com/azure/sql-data-warehouse/pause-and-resume-compute-portal#resume-compute)에 설명 된 단계를 따르세요.

## <a name="check-your-firewall-settings"></a>방화벽 설정 확인

SQL Analytics 데이터베이스는 1433 포트를 통해 통신 합니다.   회사 네트워크 내에서 연결 하려는 경우 1433 포트를 통한 아웃 바운드 트래픽이 네트워크 방화벽에서 허용 되지 않을 수 있습니다. 이 경우 IT 부서에서 1433 포트를 열지 않으면 Azure SQL Database 서버에 연결할 수 없습니다. 방화벽 구성에 대 한 추가 정보는 [여기](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#create-and-manage-ip-firewall-rules)를 참조 하세요.

## <a name="check-your-vnetservice-endpoint-settings"></a>VNet/서비스 끝점 설정 확인

40914 및 40615 오류를 수신 하는 경우 [여기에서 오류 설명 및 해결](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615)을 참조 하세요.

## <a name="check-for-the-latest-drivers"></a>최신 드라이버에 대한 확인

### <a name="software"></a>소프트웨어

SQL Analytics 데이터베이스에 연결 하는 데 최신 도구를 사용 하 고 있는지 확인 합니다.

* SSMS
* Azure Data Studio
* SQL Server Data Tools(Visual Studio)

### <a name="drivers"></a>드라이버

최신 드라이버 버전을 사용하는지 확인합니다.  이전 버전의 드라이버를 사용 하면 이전 드라이버가 새 기능을 지원 하지 않을 수 있으므로 예기치 않은 동작이 발생할 수 있습니다.

* [ODBC](https://docs.microsoft.com/sql/connect/odbc/download-odbc-driver-for-sql-server)
* [JDBC](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)
* [OLE DB](https://docs.microsoft.com/sql/connect/oledb/download-oledb-driver-for-sql-server)
* [PHP](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server)

## <a name="check-your-connection-string"></a>연결 문자열 확인

연결 문자열이 올바르게 설정되어 있는지 확인합니다.  다음은 몇 가지 샘플입니다.  [연결 문자열에 대한 추가 정보는 여기](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-connection-strings)에서 찾을 수 있습니다.

ADO.NET 연결 문자열

```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

ODBC 연결 문자열

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

PHP 연결 문자열

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

JDBC 연결 문자열

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="intermittent-connection-issues"></a>간헐적 연결 문제

대기 중인 요청이 많아 서버의 부하가 발생하는 경우를 확인합니다. 추가 리소스에 대 한 SQL 분석 인스턴스를 확장 해야 할 수도 있습니다.

## <a name="common-error-messages"></a>일반적인 오류 메시지

오류 40914 및 40615에 대 한 자세한 내용은 [여기에서 오류 설명 및 해결](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615)을 참조 하세요.

## <a name="still-having-connectivity-issues"></a>여전히 연결 문제가 있나요?
엔지니어링 팀이 지원할 수 있도록 [지원 티켓](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) 을 만듭니다.
