---
title: 포함 파일
description: 포함 파일
services: logic-apps
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.author: estfan
ms.custom: include file
ms.date: 05/15/2018
ms.openlocfilehash: d60d7727e0674298fa6da7e7330221318da23efd
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161635"
---
* Azure SQL Database를 사용하는 경우 [Azure SQL Database에 연결](#connect-azure-sql-db)의 단계를 따릅니다.

* SQL Server를 사용하는 경우 [SQL Server에 연결](#connect-sql-server)의 단계를 따릅니다.

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database"></a>Azure SQL Database에 연결

SQL 트리거 또는 작업에서 연결 정보를 묻는 메시지가 표시 되 면 트리거와 동작 모두에 대해 작동 하는 다음 단계를 수행 합니다.

1. **연결 이름**에 연결 이름을 만듭니다.

1. **SQL Server 이름**에서 Azure SQL Server를 선택 합니다. **SQL Database 이름** 목록이 표시 되 면 데이터베이스를 선택 합니다. Azure SQL server에 대 한 사용자 이름 및 암호를 제공 합니다.

   SQL database 속성의 Azure Portal 또는 연결 문자열에서이 정보를 찾을 수도 있습니다.

   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![Azure SQL Database에 대 한 연결 만들기](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)

1. 완료되면 **만들기**를 선택합니다.

1. 연결을 만든 후 [sql 트리거 추가](#add-sql-trigger) 또는 [sql 작업 추가](#add-sql-action)를 계속 합니다.

<a name="connect-sql-server"></a>

### <a name="connect-to-sql-server"></a>SQL Server에 연결

SQL 트리거 또는 작업에서 연결 정보를 묻는 메시지가 표시 되 면 트리거와 동작 모두에 대해 작동 하는 다음 단계를 수행 합니다. 그러나 시작 하기 전에 [온-프레미스 데이터 게이트웨이](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection)를 이미 설정 했는지 확인 합니다. 그렇지 않으면 연결을 만들 때 게이트웨이가 게이트웨이 목록에 표시 되지 않습니다.

1. **연결 이름**에 연결 이름을 만듭니다.

1. 트리거 또는 작업에서 **온-프레미스 데이터 게이트웨이를 통해 연결**을 선택하여 SQL Server 옵션을 표시합니다.

1. Sql **Server 이름** 및 **sql 데이터베이스 이름**에 대해 sql server의 주소와 데이터베이스 이름을 제공 합니다. 사용자 **이름** 및 **암호**에 서버에 대 한 사용자 이름 및 암호를 제공 합니다.

   연결 문자열에서이 정보를 찾을 수도 있습니다.

   * `Server=<your-server-address>`
   * `Database=<your-database-name>`
   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![SQL Server에 대 한 연결 만들기](./media/connectors-create-api-sqlazure/sql-server-create-connection.png)

1. SQL server에서 Windows 또는 기본 인증을 사용 하는 경우 **인증 유형을**선택 합니다.

1. **게이트웨이**아래에서 이전에 만든 온-프레미스 데이터 게이트웨이와 연결 된 Azure 구독을 선택 하 고 온-프레미스 데이터 게이트웨이의 이름을 선택 합니다.

   게이트웨이가 목록에 나타나지 않으면 올바르게 [게이트웨이를 설정](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection)했는지 확인합니다.

   ![SQL Server 연결 만들기 완료](./media/connectors-create-api-sqlazure/sql-server-create-connection-complete.png)

1. 완료되면 **만들기**를 선택합니다.

1. 연결을 만든 후 [SQL 트리거 추가](#add-sql-trigger) 또는 [SQL 작업 추가](#add-sql-action)를 계속 진행합니다.
