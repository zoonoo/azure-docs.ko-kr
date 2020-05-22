---
title: 관리 ID를 사용하여 Azure SQL Database에 액세스 - Azure Stream Analytics
description: 이 문서에서는 관리 ID를 사용하여 Azure SQL DB 출력에 대해 Azure Stream Analytics 작업을 인증하는 방법을 설명합니다.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 70ad69c1a34f656347b0cf53b28a1c35ac6ad043
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595842"
---
# <a name="use-managed-identities-to-access-azure-sql-database-from-an-azure-stream-analytics-job-preview"></a>관리 ID를 사용하여 Azure Stream Analytics 작업에서 Azure SQL Database에 액세스(미리 보기)

Azure Stream Analytics는 Azure SQL Database 출력 싱크에 대한 [관리 ID 인증](../active-directory/managed-identities-azure-resources/overview.md)을 지원합니다. 관리 ID를 사용하면 암호를 변경할 때 재인증해야 하거나 90일마다 사용자 토큰이 만료되는 등의 사용자 기반 인증 방법의 제한이 사라집니다. 수동으로 인증할 필요가 없어지면 Stream Analytics 배포를 완전히 자동화할 수 있습니다.

관리 ID는 지정된 Stream Analytics 작업을 나타내는 Azure Active Directory에 등록된 관리형 애플리케이션입니다. 관리형 애플리케이션은 대상 리소스에 인증하는 데 사용됩니다. 이 문서에서는 Azure Portal을 통해 Stream Analytics 작업의 Azure SQL Database 출력에 관리 ID를 사용하도록 설정하는 방법을 보여줍니다.

## <a name="prerequisites"></a>사전 요구 사항

이 기능을 사용하려면 다음 항목이 필요합니다.

- Azure Stream Analytics 작업

- Azure SQL Database 리소스

## <a name="create-a-managed-identity"></a>관리 ID 만들기

먼저 Azure Stream Analytics 작업의 관리 ID를 만듭니다.

1. [Azure Portal](https://portal.azure.com)에서 Stream Analytics 작업을 엽니다.

1. 왼쪽 탐색 메뉴의 **구성** 아래에서 **관리 ID**를 선택합니다. 그런 다음, **시스템 할당 관리 ID 사용** 옆에 있는 확인란을 선택하고 **저장**을 선택합니다.

   ![시스템 할당 관리 ID 선택](./media/sql-db-output-managed-identity/system-assigned-managed-identity.png)


   Azure Active Directory에서 Stream Analytics 작업의 ID에 대한 서비스 주체가 생성됩니다. 새로 생성된 ID의 수명 주기는 Azure에서 관리합니다. Stream Analytics 작업을 삭제하면 연결된 ID(즉, 서비스 주체)는 Azure에서 자동으로 삭제합니다. 

1. 구성을 저장하면 서비스 주체의 OID(개체 ID)가 아래와 같이 보안 주체 ID로 나열됩니다. 

   ![보안 주체 ID로 표시되는 개체 ID](./media/sql-db-output-managed-identity/principal-id.png)

   서비스 주체에는 Stream Analytics 작업과 동일한 이름이 사용됩니다. 예를 들어 작업 이름이 *MyASAJob*이면 서비스 주체 이름도 *MyASAJob*이 됩니다.

## <a name="select-an-active-directory-admin"></a>Active Directory 관리자 선택

관리 ID를 만든 후에는 Active Directory 관리자를 선택합니다.

1. Azure SQL Database 리소스로 이동하여 데이터베이스가 속해 있는 SQL Server를 선택합니다. 리소스 개요 페이지의 *서버 이름* 옆에서 SQL Server 이름을 찾을 수 있습니다. 

1. **설정**에서 **Active Directory 관리자**를 선택합니다. 그런 다음, **관리자 설정**을 선택합니다. 

   ![Active Directory 관리자 페이지](./media/sql-db-output-managed-identity/active-directory-admin-page.png)
 
1. Active Directory 관리자 페이지에서 SQL Server의 관리자가 될 사용자 또는 그룹을 검색하고 **선택**을 클릭합니다.  

   ![Active Directory 관리자 추가](./media/sql-db-output-managed-identity/add-admin.png)

1. **Active Directory 관리자** 페이지에서 **저장**을 선택합니다. 관리자를 변경하는 프로세스는 몇 분 정도 걸립니다.  

## <a name="create-a-database-user"></a>데이터베이스 사용자 만들기

다음으로, Azure Active Directory ID에 매핑되는 포함된 데이터베이스 사용자를 SQL Database에 만듭니다. 포함된 데이터베이스 사용자는 master 데이터베이스에 로그인할 수 없지만, 데이터베이스와 연결된 디렉터리의 ID에 매핑됩니다. Azure Active Directory ID는 개별 사용자 계정 또는 그룹일 수 있습니다. 이 경우 Stream Analytics 작업에 대한 포함된 데이터베이스 사용자를 만들어야 합니다. 

1. SQL Server Management Studio를 사용하여 SQL 데이터베이스에 연결합니다. **사용자 이름**은 **ALTER ANY USER** 권한이 있는 Azure Active Directory 사용자입니다. SQL Server에서 설정하는 관리자를 예로 들 수 있습니다. **Azure Active Directory - MFA가 지원되는 유니버설** 인증을 사용합니다. 

   ![SQL Server에 연결](./media/sql-db-output-managed-identity/connect-sql-server.png)

   서버 이름 `<SQL Server name>.database.windows.net`은 지역에 따라 달라질 수 있습니다. 예를 들어 중국 지역은 `<SQL Server name>.database.chinacloudapi.cn`을 사용해야 합니다.
 
   **옵션 > 연결 속성 > 데이터베이스에 연결**로 이동하여 특정 SQL Database를 지정할 수 있습니다.  

   ![SQL Server 연결 속성](./media/sql-db-output-managed-identity/sql-server-connection-properties.png)

1. 처음으로 연결하면 다음 창이 표시될 수 있습니다.

   ![새 방화벽 규칙 창](./media/sql-db-output-managed-identity/new-firewall-rule.png)

   1. 창의 내용이 맞으면 Azure Portal에서 SQL 서버 리소스로 이동합니다. **보안** 섹션에서 **방화벽 및 가상 네트워크** 페이지를 엽니다. 
   1. 원하는 규칙 이름을 사용하여 새 규칙을 추가합니다.
   1. **새 방화벽 규칙** 창의 *시작* IP 주소를 *시작 IP*로 사용합니다.
   1. **새 방화벽 규칙** 창의 *끝* IP 주소를 *끝 IP*로 사용합니다. 
   1. **저장**을 선택하고 SQL Server Management Studio에서 다시 연결을 시도합니다. 

1. 연결되면 포함된 데이터베이스 사용자를 만듭니다. 다음 SQL 명령은 Stream Analytics 작업과 동일한 이름을 사용하는 포함된 데이터베이스 사용자를 만듭니다. *ASA_JOB_NAME*을 대괄호를 묶어야 합니다. 다음 T-SQL 구문을 사용하여 쿼리를 실행합니다. 

   ```sql
   CREATE USER [ASA_JOB_NAME] FROM EXTERNAL PROVIDER; 
   ```

## <a name="grant-stream-analytics-job-permissions"></a>Stream Analytics 작업 권한 부여

Stream Analytics 작업은 SQL Database 리소스에 **CONNECT**하는 관리 ID의 권한을 갖고 있습니다. 대부분 Stream Analytics 작업에서 **SELECT** 같은 명령을 실행하도록 허용하는 것이 효율적입니다. SQL Server Management Studio를 사용하여 Stream Analytics 작업에 이러한 권한을 부여할 수 있습니다. 자세한 내용은 [GRANT(Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql?view=sql-server-ver15) 참조를 확인하세요.

또는 SQL Server Management Studio에서 SQL 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **속성 > 권한**을 선택할 수도 있습니다. 권한 메뉴에서 이전에 추가한 Stream Analytics 작업을 볼 수 있으며, 필요에 따라 수동으로 권한을 부여하거나 거부할 수 있습니다.

## <a name="create-an-azure-sql-database-output"></a>Azure SQL Database 출력 만들기

관리 ID를 구성했으므로, 이제 Azure SQL Database를 Stream Analytics 작업에 출력으로 추가할 수 있습니다.

1. Stream Analytics 작업으로 돌아가서 **작업 토폴로지**에서 **출력** 페이지로 이동합니다. 

1. **추가 > SQL Database**를 선택합니다. SQL Database 출력 싱크의 출력 속성 창에서 인증 모드 드롭다운을 클릭하고 **관리 ID**를 선택합니다.

1. 나머지 속성을 입력합니다. SQL Database 출력을 만드는 방법에 대한 자세한 내용은 [Stream Analytics를 사용하여 SQL Database 출력 만들기](stream-analytics-define-outputs.md#sql-database)를 참조하세요. 작업을 마쳤으면 **저장**을 선택합니다. 

## <a name="next-steps"></a>다음 단계

* [Azure Stream Analytics의 출력 이해](stream-analytics-define-outputs.md)
* [Azure SQL Database에 Azure Stream Analytics 출력](stream-analytics-sql-output-perf.md)
