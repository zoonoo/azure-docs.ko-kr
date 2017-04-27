---
title: "SQL Server DB를 Azure SQL Database로 마이그레이션 | Microsoft Docs"
description: "SQL Server 데이터베이스를 Azure SQL Database로 마이그레이션하는 방법을 알아봅니다."
services: sql-database
documentationcenter: 
author: janeng
manager: jstrauss
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial-migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 04/04/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: 842ca29e46aefbd58638ac642f000ef39c1202d1
ms.lasthandoff: 04/13/2017


---

# <a name="migrate-your-sql-server-database-to-azure-sql-database"></a>SQL Server 데이터베이스를 Azure SQL Database로 마이그레이션

이 자습서에서는 Microsoft Data Migration Assistant를 사용하여 기존 SQL Server 데이터베이스를 Azure SQL Database로 마이그레이션하고, 실제 데이터 마이그레이션을 수행하고 마이그레이션 완료 후 마이그레이션된 데이터베이스에 연결하기 위해 마이그레이션 준비에 필요한 단계를 살펴봅니다. 

> [!IMPORTANT]
> 호환성 문제를 해결하려면 [Visual Studio Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)를 사용합니다. 
>

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

이 자습서를 완료하려면 다음이 설치되어 있어야 합니다.

- 최신 버전의 SSMS([SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms))를 설치합니다. SSMS를 설치하면 다양한 데이터베이스 개발 작업을 자동화하는 데 사용할 수 있는 명령줄 유틸리티인 SQLPackage의 최신 버전도 설치됩니다. 
- DMA([Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595)).
- 마이그레이션할 데이터베이스. 이 자습서에서는 SQL Server 2008R2 이상의 인스턴스에서 [SQL Server 2008R2 AdventureWorks OLTP 데이터베이스](https://msftdbprodsamples.codeplex.com/releases/view/59211)를 사용하지만 사용자가 선택한 모든 데이터베이스를 사용할 수 있습니다. 

## <a name="step-1---prepare-for-migration"></a>1단계 - 마이그레이션 준비

마이그레이션할 준비가 되었습니다. **[Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595)**를 사용하여 Azure SQL Database에 마이그레이션하기 위한 데이터베이스의 준비 상태를 평가하려면 다음 단계를 수행합니다.

1. **Data Migration Assistant**를 엽니다. 마이그레이션하려는 데이터베이스가 포함된 SQL Server 인스턴스에 연결된 모든 컴퓨터에서 DMA를 실행할 수 있으며 SQL Server 인스턴스를 호스트하는 컴퓨터에는 설치할 필요가 없습니다.

     ![Data Migration Assistant 열기](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-open.png)

2. 왼쪽 메뉴에서 **+ 새로 만들기**를 클릭하여 **평가** 프로젝트를 만듭니다. 양식에 **프로젝트 이름**을 입력하고(다른 값은 모두 기본값으로 유지) **만들기**를 클릭합니다. **옵션** 페이지를 엽니다.

     ![새 Data Migration Assistant 프로젝트](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-new-project.png)

3. **옵션** 페이지에서 **다음**을 클릭합니다. **Select sources**(소스 선택) 페이지가 열립니다.

     ![새 데이터 마이그레이션 옵션](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-options.png) 

4. **Select sources**(소스 선택) 페이지에서 마이그레이션할 서버가 포함된 SQL Server 인스턴스의 이름을 입력합니다. 필요한 경우 이 페이지에서 다른 값을 변경합니다. **Connect**를 클릭합니다.

     ![새 데이터 마이그레이션 소스 선택](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-sources.png)

5. **Select sources**(소스 선택) 페이지의 **Add sources**(소스 추가) 부분에서 호환성을 테스트할 데이터베이스의 확인란을 선택합니다. **추가**를 클릭합니다.

     ![새 데이터 마이그레이션 소스 선택](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-sources-add.png)

6. **Start Assessment**(평가 시작)를 클릭합니다.

     ![새 데이터 마이그레이션 평가 시작](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-start-assessment.png)

7. 평가가 완료되면 먼저 데이터베이스가 마이그레이션하는 데 충분히 호환되는지 확인합니다. 녹색 원에 있는 확인 표시를 찾습니다.

     ![새 데이터 마이그레이션 호환 평가 결과](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-assessment-results-compatible.png)

8. **SQL Server feature parity**(SQL Server 기능 패리티)부터 결과를 검토합니다. 특히 지원되지 않는 기능, 부분적으로 지원되는 기능 및 권장 작업에 대해 제공된 정보를 검토합니다. 

     ![새 데이터 마이그레이션 평가 패리티](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-assessment-results-parity.png)

9. **호환성 문제**를 클릭합니다. 특히 마이그레이션 차단, 동작 변경 내용 및 각 호환성 수준에 대해 사용되지 않는 기능에 대한 정보를 검토합니다. AdventureWorks2008R2 데이터베이스의 경우 SQL Server 2008 이후 전체 텍스트 검색의 변경 내용 및 SQL Server 2000 이후 SERVERPROPERTY('LCID')의 변경 내용을 검토합니다. 이러한 변경 사항의 자세한 내용에 대한 링크가 제공됩니다. 변경된 다양한 검색 옵션 및 전체 텍스트 검색에 대한 설정 

   > [!IMPORTANT] 
   > Azure SQL Database에 데이터베이스를 마이그레이션한 후에 데이터베이스의 현재 호환성 수준(AdventureWorks2008R2 데이터베이스의 경우 100 수준) 또는 더 높은 수준에서 작동하도록 선택할 수 있습니다. 특정 호환성 수준에서 데이터베이스를 운영하기 위한 옵션 및 영향에 대한 자세한 내용은 [ALTER DATABASE 호환성 수준](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)을 참조하세요. 또한 호환성 수준과 관련된 추가 데이터베이스 수준 설정에 대한 자세한 내용은 [ALTER DATABASE SCOPED CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)을 참조하세요.
   >

10. 필요에 따라 JSON 파일로 보고서를 저장하려면 **보고서 내보내기**를 클릭합니다.
11. Data Migration Assistant를 닫습니다.

## <a name="step-2---export-to-bacpac-file"></a>2단계 - BACPAC 파일로 내보내기 

BACPAC 파일은 메타데이터 및 SQL Server 데이터베이스의 데이터를 포함하는 BACPAC의 확장명을 가진 ZIP 파일입니다. BACPAC 파일은 보관이나 마이그레이션(예: SQL Server에서 Azure SQL Database로)을 위해 Azure Blob Storage 또는 로컬 저장소에 저장할 수 있습니다. 트랜잭션 측면에서 일관되도록 내보내려는 경우 내보내기 중에 쓰기 작업이 발생하지 않아야 합니다.

SQLPackage 명령줄 유틸리티를 사용하여 로컬 저장소로 AdventureWorks2008R2 데이터베이스를 내보내려면 다음 단계를 수행합니다.

1. Windows 명령 프롬프트를 열고 디렉터리를 **130** 버전의 SQLPackage가 있는 폴더(예: **C:\Program Files (x86)\Microsoft SQL Server\130\DAC\bin**)로 변경합니다.

2. 명령 프롬프트에서 다음 SQLPackage 명령을 실행하여 **localhost**의 **AdventureWorks2008R2** 데이터베이스를 **AdventureWorks2008R2.bacpac**로 내보냅니다. 이러한 값을 사용자 환경에 맞는 적절한 값으로 변경합니다.

    ```SQLPackage
    sqlpackage.exe /Action:Export /ssn:localhost /sdn:AdventureWorks2008R2 /tf:AdventureWorks2008R2.bacpac
    ```

    ![sqlpackage 내보내기](./media/sql-database-migrate-your-sql-server-database/sqlpackage-export.png)

실행이 완료되면 생성된 BCPAC 파일이 sqlpackage 실행 파일이 있는 디렉터리에 저장됩니다. 이 예제에서는 C:\Program Files (x86)\Microsoft SQL Server\130\DAC\bin에 저장됩니다. 

## <a name="step-3-log-in-to-the-azure-portal"></a>3단계: Azure Portal에 로그인

[Azure 포털](https://portal.azure.com/)에 로그인합니다. SQLPackage 명령줄 유틸리티를 실행하고 있는 컴퓨터에서 로그온하면 5단계의 방화벽 규칙 만들기가 용이해집니다.

## <a name="step-4-create-a-sql-database-logical-server"></a>4단계: SQL Database 논리 서버에 연결

[Azure SQL Database 논리 서버](sql-database-features.md)는 여러 데이터베이스에 대한 중앙 관리 지점의 역할을 합니다. 마이그레이션된 Adventure Works OLTP SQL Server 데이터베이스를 포함하도록 SQL Database 논리 서버를 만들려면 다음 단계를 수행합니다. 

1. Azure Portal의 왼쪽 위에 있는 **새로 만들기** 단추를 클릭합니다.

2. **새로 만들기** 페이지에서 검색 창에 **서버**를 입력하고 필터링된 목록에서 **SQL Database(논리 서버)**를 선택합니다.

    ![논리 서버 선택](./media/sql-database-migrate-your-sql-server-database/logical-server.png)

3. **모두** 페이지에서 **SQL server(논리 서버)**를 클릭한 다음 **SQL Server(논리 서버)** 페이지에서 **만들기**를 클릭합니다.

    ![논리 서버 만들기](./media/sql-database-migrate-your-sql-server-database/logical-server-create.png)

4. 위의 이미지에 표시된 대로 다음과 같은 정보를 사용하여 SQL Server(논리 서버) 형식을 작성합니다.     

   - 서버 이름: 전역적으로 고유한 서버 이름을 지정합니다.
   - 서버 관리자 로그인: 서버 관리자 로그인 이름을 제공합니다.
   - 암호: 사용자가 선택한 암호를 지정합니다.
   - 리소스 그룹: **새로 만들기**를 선택하고 **myResourceGroup**을 지정합니다.
   - 위치: 데이터 센터 위치를 선택합니다.

    ![논리 서버 만들기 양식 완료](./media/sql-database-migrate-your-sql-server-database/logical-server-create-completed.png)

5. **만들기**를 클릭하여 논리 서버를 프로비전합니다. 프로비전하는 데 몇 분이 걸립니다. 

## <a name="step-5-create-a-server-level-firewall-rule"></a>5단계: 서버 수준 방화벽 규칙 만들기

방화벽 규칙을 만들어서 특정 IP 주소에 대한 방화벽을 열지 않으면 SQL Database 서비스는 외부 응용 프로그램 및 도구가 서버 또는 서버의 데이터베이스에 연결되지 않도록 방지하는 [서버 수준에 방화벽](sql-database-firewall-configure.md)을 만듭니다. SQLPackage 명령줄 유틸리티를 실행하는 컴퓨터의 IP 주소에 대한 SQL Database 서버 수준 방화벽 규칙을 만들려면 다음 단계를 수행합니다. 이렇게 하면 SQLPackage를 Azure SQL Database 방화벽을 통해 SQL Database 논리 서버에 연결할 수 있습니다. 

1. 왼쪽 메뉴에서 **모든 리소스**를 클릭하고 **모든 리소스** 페이지에서 새 서버를 클릭합니다. 서버에 대한 개요 페이지가 열리고 이 페이지에 정규화된 서버 이름(예: **mynewserver20170403.database.windows.net**)이 표시되며 추가 구성을 위한 옵션도 제공됩니다.

     ![논리 서버 개요](./media/sql-database-migrate-your-sql-server-database/logical-server-overview.png)

2. 개요 페이지의 왼쪽 메뉴에서 **설정** 아래 **방화벽**을 클릭합니다. SQL Database 서버에 대한 **방화벽 설정** 페이지가 열립니다. 

3. 도구 모음에서 **클라이언트 IP 추가**를 클릭하여 현재 사용 중인 컴퓨터의 IP 주소를 추가한 다음 **저장**을 클릭합니다. 이 IP 주소에 대한 서버 수준 방화벽 규칙이 생성됩니다.

     ![set server firewall rule](./media/sql-database-migrate-your-sql-server-database/server-firewall-rule-set.png)

4. **확인**을 클릭합니다.

이제 SQL Server Management Studio 또는 이전에 만든 서버 관리자 계정을 사용하여 이 IP 주소에서 원하는 다른 도구를 사용하여 모든 데이터베이스 및 이 서버에 연결할 수 있습니다.

> [!NOTE]
> SQL Database는 포트 1433을 통해 통신합니다. 회사 네트워크 내에서 연결을 시도하는 경우 포트 1433을 통한 아웃바운드 트래픽이 네트워크 방화벽에서 허용되지 않을 수 있습니다. 이 경우 IT 부서에서 포트 1433을 열지 않으면 Azure SQL Database 서버에 연결할 수 없습니다
>

## <a name="step-6---import-bacpac-file-to-azure-sql-database"></a>6단계 - Azure SQL Database에 BACPAC 파일 가져오기 

최신 버전의 SQLPackage 명령줄 유틸리티를 사용하면 지정한 [서비스 계층 및 성능 수준](sql-database-service-tiers.md)에서 Azure SQL Database를 만들 수 있습니다. 가져오기 작업 동안 최상의 성능을 위해 높은 서비스 계층 및 성능 수준을 선택한 다음 서비스 계층 및 성능 수준이 즉시 필요한 것보다 높은 경우 가져오기 작업 후에 규모를 축소합니다.

SQLPackage 명령줄 유틸리티를 사용하여 Azure SQL Database에 AdventureWorks2008R2 데이터베이스를 가져오려면 다음 단계를 수행합니다.

- 로컬 저장소의 **AdventureWorks2008R2** 데이터베이스를 이전에 데이터베이스 이름 **myMigratedDatabase**, 서비스 계층 **Premium** 및 서비스 목표 **P6**을 사용하여 만든 Azure SQL Database 논리 서버로 가져오려면 명령 프롬프트에서 다음 SQLPackage 명령을 실행합니다. 이 세 값을 사용자 환경에 맞게 변경합니다.

    ```
    SqlPackage.exe /a:import /tcs:"Data Source=mynewserver20170403.database.windows.net;Initial Catalog=myMigratedDatabase;User Id=<change_to_your_admin_user_account>;Password=<change_to_your_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
    ```

   ![sqlpackage 가져오기](./media/sql-database-migrate-your-sql-server-database/sqlpackage-import.png)

> [!IMPORTANT]
> Azure SQL Database 논리 서버는 포트 1433에서 수신 대기합니다. 회사 방화벽 내에서 Azure SQL Database 논리 서버로 연결을 시도하면 성공적인 연결을 위해 회사 방화벽에서 이 포트가 열려야 합니다.
>

## <a name="step-7---connect-using-sql-server-management-studio-ssms"></a>7단계 - SSMS(SQL Server Management Studio)를 사용하여 연결

SQL Server Management Studio를 사용하여 Azure SQL Database 서버 및 새롭게 마이그레이션된 데이터베이스에 연결합니다. SQLPackage를 실행한 다른 컴퓨터에서 SQL Server Management Studio를 실행하는 경우 이전 절차의 단계를 사용하여 이 컴퓨터에 대한 방화벽 규칙을 만듭니다.

1. SQL Server Management Studio를 엽니다.

2. **서버에 연결** 대화 상자에서 다음 정보를 입력합니다.
   - **서버 유형**: 데이터베이스 엔진을 지정합니다.
   - **서버 이름**: **mynewserver20170403.database.windows.net**과 같은 정규화된 서버 이름을 입력합니다.
   - **인증**: SQL Server 인증을 지정합니다.
   - **로그인**: 서버 관리자 계정을 입력합니다.
   - **암호**: 서버 관리자 계정의 암호를 입력합니다.
 
    ![ssms로 연결](./media/sql-database-migrate-your-sql-server-database/connect-ssms.png)

3. **Connect**를 클릭합니다. 개체 탐색기 창이 열립니다. 

4. 개체 탐색기에서 **데이터베이스**를 확장한 다음 **myMigratedDatabase**를 확장하여 샘플 데이터베이스에 있는 개체를 봅니다.

## <a name="step-8---change-database-properties"></a>8단계 - 데이터베이스 속성 변경

SQL Server Management Studio를 사용하여 서비스 계층, 성능 수준 및 호환성 수준을 변경할 수 있습니다.

1. 개체 탐색기에서 **myMigratedDatabase**를 마우스 오른쪽 단추로 클릭하고 **새 쿼리**를 클릭합니다. 데이터베이스에 연결된 쿼리 창이 열립니다.

2. 다음 명령을 실행하여 서비스 계층을 **Standard**로, 성능 수준을 **S1**로 설정합니다.

    ```
    ALTER DATABASE myMigratedDatabase 
    MODIFY 
        (
        EDITION = 'Standard'
        , MAXSIZE = 250 GB
        , SERVICE_OBJECTIVE = 'S1'
    );
    ```

    ![서비스 계층 변경](./media/sql-database-migrate-your-sql-server-database/service-tier.png)

3. 다음 명령을 실행하여 데이터베이스 호환성 수준을 **130**으로 변경합니다.

    ```
    ALTER DATABASE myMigratedDatabase  
    SET COMPATIBILITY_LEVEL = 130;
    ```

   ![호환성 수준 변경](./media/sql-database-migrate-your-sql-server-database/compat-level.png)

## <a name="next-steps"></a>다음 단계 

- 마이그레이션 개요는 [데이터베이스 마이그레이션](sql-database-cloud-migrate.md)을 참조하세요.
- T-SQL 차이점에 대한 자세한 내용은 [SQL Database로의 마이그레이션 중 Transact-SQL 차이점 해결](sql-database-transact-sql-information.md)을 참조하세요.
- Visual Studio 코드를 사용하여 연결 및 쿼리하려면 [Visual Studio 코드를 사용하여 연결 및 쿼리](sql-database-connect-query-vscode.md)를 참조하세요.
- .NET을 사용하여 연결 및 쿼리하려면 [.NET을 사용하여 연결 및 쿼리](sql-database-connect-query-dotnet.md)를 참조하세요.
- PHP를 사용하여 연결 및 쿼리하려면 [PHP를 사용하여 연결 및 쿼리](sql-database-connect-query-php.md)를 참조하세요.
- Node.js를 사용하여 연결 및 쿼리하려면 [Node.js를 사용하여 연결 및 쿼리](sql-database-connect-query-nodejs.md)를 참조하세요.
- Java를 사용하여 연결 및 쿼리하려면 [Java를 사용하여 연결 및 쿼리](sql-database-connect-query-java.md)를 참조하세요.
- Python을 사용하여 연결 및 쿼리하려면 [Python을 사용하여 연결 및 쿼리](sql-database-connect-query-python.md)를 참조하세요.
- Ruby를 사용하여 연결 및 쿼리하려면 [Ruby를 사용하여 연결 및 쿼리](sql-database-connect-query-ruby.md)를 참조하세요.


