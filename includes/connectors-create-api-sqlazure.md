---
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.topic: include
ms.date: 11/08/2019
ms.openlocfilehash: ea0ae1b1527aa1f527c8ac8fbcd3b4e4f6b6fe2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789202"
---
* Azure SQL Database를 사용하는 경우 [Azure SQL Database에 연결](#connect-azure-sql-db)의 단계를 따릅니다.

* SQL Server를 사용하는 경우 [SQL Server에 연결](#connect-sql-server)의 단계를 따릅니다.

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database"></a>Azure SQL Database에 연결

SQL 트리거 또는 작업이 연결 정보를 묻는 메시지가 표시되면 트리거와 작업 모두에 대해 작동하는 다음 단계를 따릅니다.

1. **연결 이름에**대해 연결에 대한 이름을 만듭니다.

1. **SQL 서버 이름에서**Azure SQL 서버를 선택합니다. SQL **데이터베이스 이름** 목록이 나타나면 데이터베이스를 선택합니다. Azure SQL 서버에 대한 사용자 이름과 암호를 제공합니다.

   또한 SQL 데이터베이스 속성 아래의 Azure 포털또는 연결 문자열에서 이 정보를 찾을 수 있습니다.

   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![Azure SQL 데이터베이스에 대한 연결 만들기](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)

1. 완료되면 **만들기**를 선택합니다.

1. 연결을 만든 후 SQL [트리거 추가](#add-sql-trigger) 또는 [SQL 작업 추가](#add-sql-action)를 계속합니다.

<a name="connect-sql-server"></a>

### <a name="connect-to-sql-server"></a>SQL Server에 연결

SQL 트리거 또는 작업이 연결 정보를 묻는 메시지가 표시되면 트리거와 작업 모두에 대해 작동하는 다음 단계를 따릅니다. 로컬 컴퓨터에 [온-프레미스 데이터 게이트웨이를](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-install) 설치하고 Azure [데이터 게이트웨이 리소스를 만들어야](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection)하는 시나리오의 경우 먼저 이러한 요구 사항을 완료해야 합니다. 그렇지 않으면 연결을 만들 때 게이트웨이 리소스가 게이트웨이 목록에 나타나지 않습니다.

또한 [통합 서비스 환경(ISE)에서](https://docs.microsoft.com/azure/logic-apps/connect-virtual-network-vnet-isolated-environment-overview)SQL Server 커넥터와 Windows 인증을 사용하려면 커넥터의 비ISE 버전과 온-프레미스 데이터 게이트웨이를 사용합니다. ISE 레이블이 지정된 버전은 Windows 인증을 지원하지 않습니다.

1. **연결 이름에**대해 연결에 대한 이름을 만듭니다.

1. 트리거 또는 작업에서 **온-프레미스 데이터 게이트웨이를 통해 연결**을 선택하여 SQL Server 옵션을 표시합니다.

1. **SQL 서버 이름** 및 SQL 데이터베이스 **이름의**경우 SQL 서버의 주소와 데이터베이스 이름을 제공합니다. **사용자 이름** 및 **암호의**경우 서버의 사용자 이름과 암호를 제공합니다.

   연결 문자열에서 이 정보를 찾을 수도 있습니다.

   * `Server=<your-server-address>`
   * `Database=<your-database-name>`
   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![SQL 서버에 대한 연결 만들기](./media/connectors-create-api-sqlazure/sql-server-create-connection.png)

1. SQL 서버에서 Windows 또는 기본 인증을 사용하는 경우 **인증 유형을**선택합니다.

1. **게이트웨이에서**이전에 만든 온-프레미스 데이터 게이트웨이와 연결된 Azure 구독을 선택하고 온-프레미스 데이터 게이트웨이의 이름을 선택합니다.

   게이트웨이가 목록에 나타나지 않으면 올바르게 [게이트웨이를 설정](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection)했는지 확인합니다.

   ![완료된 SQL Server 연결 만들기](./media/connectors-create-api-sqlazure/sql-server-create-connection-complete.png)

1. 완료되면 **만들기**를 선택합니다.

1. 연결을 만든 후 [SQL 트리거 추가](#add-sql-trigger) 또는 [SQL 작업 추가](#add-sql-action)를 계속 진행합니다.
