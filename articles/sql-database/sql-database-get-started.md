---
title: "빠른 시작: 첫 번째 Azure SQL Database | Microsoft Docs"
description: "Azure Portal에서 SQL Database 논리 서버, 서버 수준 방화벽 규칙 및 데이터베이스를 만드는 방법을 알아봅니다. 또한 Azure SQL Database와 SQL Server Management Studio를 사용하는 방법에 대해 알아봅니다."
keywords: "SQL 데이터베이스 자습서, SQL 데이터베이스 만들기"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: aeb8c4c3-6ae2-45f7-b2c3-fa13e3752eed
ms.service: sql-database
ms.custom: single databases
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 02/17/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 166a9d7032bb75188a790bea1724aefd194dcefa
ms.openlocfilehash: 36afd5c8bccb080ae3aaf1b4975d317b9087a3b3
ms.lasthandoff: 02/18/2017


---
# <a name="create-connect-to-and-query-your-first-azure-sql-databases-in-the-azure-portal-and-using-ssms"></a>Azure Portal에서 SSMS를 사용하여 첫 번째 Azure SQL 데이터베이스를 만들고, 연결하고, 쿼리

이 자습서에서는 Azure Portal에서 SQL Server Management Studio를 사용하여 Azure SQL 데이터베이스를 만들고, 연결하고, 쿼리하는 방법을 알아봅니다. 이 자습서를 완료할 때 얻을 수 있는 결과는 다음과 같습니다.

* 논리 서버, 서버 수준 방화벽 규칙, 두 개의 데이터베이스가 포함된 리소스 그룹이 생성됩니다.
* Azure Portal에서 SQL Server Management Studio를 사용하여 서버 및 데이터베이스 속성을 확인하는 방법을 익힙니다.
* Azure Portal에서 SQL Server Management Studio를 사용하여 데이터베이스를 쿼리하는 방법을 익힙니다.

**예상 시간**: 이 자습서의 경우 약 30분 소요됩니다(이미 필수 조건을 충족한다고 가정).

> [!TIP]
> [PowerShell](sql-database-get-started-powershell.md) 또는 [C#](sql-database-get-started-csharp.md)을 사용하여 Azure SQL 데이터베이스를 만들고, 연결하고, 쿼리하는 방법도 배울 수 있습니다.
>

> [!NOTE]
> 이 자습서에서는 [SQL Database 서버 개요](sql-database-server-overview.md), [SQL Database 개요](sql-database-overview.md) 및 [Azure SQL Database 방화벽 규칙 개요](sql-database-firewall-configure.md) 학습 항목의 콘텐츠를 학습할 수 있습니다. SQL Database 서비스에 대한 개요는 [SQL Database란?](sql-database-technical-overview.md)을 참조하세요.
>  

## <a name="prerequisites"></a>필수 조건

* **Azure 계정**. [무료 Azure 계정을 열거나](https://azure.microsoft.com/free/) 또는 [Visual Studio 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/)할 수 있습니다. 

* **Azure는 사용 권한을 만듭니다**. 구독 소유자 또는 참가자 역할의 구성원인 계정을 사용하여 Azure Portal에 연결할 수 있어야 합니다. RBAC(역할 기반 액세스 제어)에 대한 자세한 내용은 [Azure Portal에서 액세스 관리 시작](../active-directory/role-based-access-control-what-is.md)을 참조하세요.

* **SQL Server Management Studio**. [SQL Server Management Studio 다운로드](https://msdn.microsoft.com/library/mt238290.aspx)에서 SSMS(SQL Server Management Studio) 최신 버전을 다운로드하여 설치할 수 있습니다. 새로운 기능이 지속적으로 출시되고 있으므로 Azure SQL Database에 연결할 때에는 항상 최신 버전의 SSMS를 사용하세요.

### <a name="sign-in-to-the-azure-portal"></a>Azure 포털에 로그인합니다.

이 절차의 단계에서는 [Azure 계정](https://account.windowsazure.com/Home/Index)을 사용하여 Azure Portal에 연결하는 방법을 보여줍니다.

1. 선택한 브라우저를 열고 [Azure Portal](https://portal.azure.com/)에 연결합니다.
2. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
3. **로그인** 페이지에서 구독에 대한 자격 증명을 제공합니다.
   
   ![로그인](./media/sql-database-get-started/login.png)


<a name="create-logical-server-bk"></a>

## <a name="create-a-new-logical-sql-server"></a>새로운 SQL 논리 서버 만들기

이 절차의 단계에서는 사용자가 선택한 지역에서 Azure Portal을 사용하여 논리 서버를 만드는 방법을 보여줍니다. 논리 서버는 SQL 데이터베이스를 만드는 개체이자 사용자가 Azure SQL Database 방화벽을 통해 연결 하도록 허용하는 방화벽 규칙을 만드는 개체입니다. 

1. **새로 만들기**를 클릭하고 **sql server**를 입력한 다음 **Enter** 키를 클릭합니다.

    ![논리 SQL 서버](./media/sql-database-get-started/logical-sql-server.png)
2. **SQL 서버(논리 서버)**를 클릭합니다.
   
    ![논리 SQL 서버 만들기](./media/sql-database-get-started/create-logical-sql-server.png)
3. **만들기**를 클릭하여 새 SQL Server(논리 서버) 블레이드를 엽니다.

    ![새 논리 SQL 서버](./media/sql-database-get-started/new-logical-sql-server.png)
3. **서버 이름** 텍스트 상자에서 새 논리 서버의 유효한 이름을 입력합니다. 녹색 확인 표시가 유효한 이름을 제공한 것을 나타냅니다.
    
    ![새 서버 이름](./media/sql-database-get-started/new-server-name.png)

    > [!IMPORTANT]
    > 새 서버의 정규화된 이름의 형식은 전역적으로 고유해야 하며 **<your_server_name>.database.windows.net** 형식입니다. 이 자습서의 뒷부분의 정규화된 서버 이름을 사용하여 서버와 데이터베이스에 연결합니다.
    >
    
4. **서버 관리자 로그인** 텍스트 상자에서 이 서버의 SQL 인증 로그인을 위한 사용자 이름을 입력합니다. 이 로그인을 서버 보안 주체 로그인이라고 합니다. 녹색 확인 표시가 유효한 이름을 제공한 것을 나타냅니다.
    
    ![SQL 관리자 로그인](./media/sql-database-get-started/sql-admin-login.png)
5. **암호** 및 **암호 확인** 텍스트 상자에서 서버 보안 주체 로그인 계정에 대한 암호를 제공합니다. 녹색 확인 표시가 유효한 암호를 제공한 것을 나타냅니다.
    
    ![SQL 관리자 암호](./media/sql-database-get-started/sql-admin-password.png)
6. **구독** 드롭다운 상자에서 개체를 만들 권한을 갖고 있는 구독을 선택합니다.

    ![subscription](./media/sql-database-get-started/subscription.png)
7. **리소스 그룹** 텍스트 상자에서 **새로 만들기**를 선택한 다음 새 리소스 그룹의 올바른 이름을 입력합니다. 녹색 확인 표시가 유효한 이름을 제공한 것을 나타냅니다.

    ![새 리소스 그룹](./media/sql-database-get-started/new-resource-group.png)

8. **위치** 텍스트 상자에서 논리 서버를 만들 데이터 센터를 선택합니다.
    
    ![서버 위치](./media/sql-database-get-started/server-location.png)
    
    > [!TIP]
    > **Azure 서비스의 서버 액세스 허용** 확인란은 이 블레이드에서 변경할 수 없습니다. [서버 방화벽] 블레이드에서 이 설정을 변경할 수 있습니다. 자세한 내용은 [보안 시작](sql-database-control-access-sql-authentication-get-started.md)을 참조하세요.
    >
    
9. **대시보드에 고정** 확인란을 선택합니다.

10. **만들기**를 클릭하고 이 스크립트를 Azure에 배포하여 논리 서버를 만듭니다.

    ![만들기 단추](./media/sql-database-get-started/create.png)

11. 서버를 만든 후 기본적으로 표시되는 서버 속성을 검토합니다. 

    ![SQL 서버 블레이드](./media/sql-database-get-started/sql-server-blade.png)
12. **속성**을 클릭하여 논리적 SQL server의 추가 속성을 봅니다.

    ![SQL 서버 속성](./media/sql-database-get-started/sql-server-properties.png)
13. 이 자습서의 뒷부분에서 사용할 정규화된 서버 이름을 클립보드에 복사합니다.

    ![정규화된 SQL 서버 이름](./media/sql-database-get-started/sql-server-full-name.png)

## <a name="create-a-server-level-firewall-rule"></a>서버 수준 방화벽 규칙 만들기

이 절차의 단계에서는 Azure Portal에서 서버 수준 방화벽 규칙을 만드는 방법을 보여 줍니다. 기본적으로 Azure SQL Database 방화벽은 논리 서버 및 해당 데이터베이스에 대한 외부 연결을 차단합니다. 서버에 연결하려면 다음 절차에서 연결하는 컴퓨터의 IP 주소에 대한 방화벽 규칙을 만들어야 합니다. 자세한 내용은 [Azure SQL Database 방화벽 규칙 개요](sql-database-firewall-configure.md)를 참조하세요.

1. SQL 서버 블레이드에서 **방화벽**을 클릭하여 서버의 방화벽 블레이드를 엽니다. 클라이언트 컴퓨터의 IP 주소가 표시됩니다.

    ![SQL 서버 방화벽](./media/sql-database-get-started/sql-server-firewall.png)

2. 도구 모음에서 **클라이언트 IP 추가**를 클릭하여 현재 IP 주소의 방화벽 규칙을 만듭니다.

    ![클라이언트 IP 추가](./media/sql-database-get-started/add-client-ip.png)

    > [!NOTE]
    > 단일 IP 주소 또는 전체 주소 범위에 대한 방화벽 규칙을 만들 수 있습니다. 방화벽을 열면 SQL 관리자와 사용자가 유효한 자격 증명이 있는 서버의 모든 데이터베이스에 로그인할 수 있습니다.
    >

4. 도구 모음에서 **저장**을 클릭하여 이 서버 수준 방화벽 규칙을 저장한 다음 **확인**을 클릭하여 성공 대화 상자를 닫습니다.

    ![성공](./media/sql-database-get-started/save-firewall-rule.png)

## <a name="connect-to-the-server-with-ssms"></a>SSMS로 서버에 연결

이 절차의 단계에서는 SQL Server Management Studio를 사용하여 SQL 논리 서버에 연결하는 방법을 보여줍니다. SSMS는 DBA에서 SQL 서버 및 데이터베이스 관리에 사용하는 기본 도구입니다.

1. SQL Server Management Studio를 엽니다(Windows 검색 상자에서 **Microsoft SQL Server Management Studio**를 입력하고 **Enter** 키를 클릭하여 SSMS 열기).

    ![SQL Server Management Studio](./media/sql-database-get-started/ssms.png)
3. **서버에 연결** 대화 상자에서 이전 절차의 정규화된 서버 이름을 입력하고, SQL Server 인증을 선택하고, 서버를 프로비전하는 동안 지정한 로그인 및 암호를 입력합니다.

    ![서버 연결](./media/sql-database-get-started/connect-to-server.png)
4. **연결**을 클릭하여 연결을 시작하고 SSMS에서 개체 탐색기를 엽니다.

    ![연결된 서버](./media/sql-database-get-started/connected-to-server.png)
5. [개체 탐색기]에서 **데이터베이스**, **시스템 데이터베이스**, **master**를 차례로 확장하여 master 데이터베이스의 개체를 봅니다.

    ![master 데이터베이스](./media/sql-database-get-started/master-database.png)
6. **로그인**을 마우스 오른쪽 단추로 클릭한 다음 **새 쿼리**를 클릭합니다.

    ![master 데이터베이스 쿼리](./media/sql-database-get-started/query-master-database.png)

8. 쿼리 창에서 다음 쿼리를 입력합니다.

   ```select * from sys.objects```

9.  도구 모음에서 **실행**을 클릭하여 master 데이터베이스의 모든 시스템 개체 목록을 반환합니다.

    ![master 데이터베이스 시스템 개체를 쿼리합니다.](./media/sql-database-get-started/query-master-database-system-objects.png)

    > [!NOTE]
    > SQL 보안을 시작하려면 [SQL 인증 시작](sql-database-control-access-sql-authentication-get-started.md)을 참조하세요.
    >

## <a name="create-a-database-with-sample-data"></a>샘플 데이터로 데이터베이스 만들기

이 절차의 단계에서는 이전에 만든 논리 서버에 연결된 Azure Portal의 샘플 데이터를 사용하여 데이터베이스를 만드는 방법을 보여 줍니다. 

1. Azure Portal에서 **SQL 데이터베이스**를 클릭합니다.

    ![SQL 데이터베이스](./media/sql-database-get-started/new-sql-database.png)
2. SQL 데이터베이스 블레이드에서 **추가**를 클릭합니다. 

    ![SQL 데이터베이스 추가](./media/sql-database-get-started/add-sql-database.png)

    ![SQL 데이터베이스 블레이드](./media/sql-database-get-started/sql-database-blade.png)
3. **데이터베이스 이름** 텍스트 상자에서 유효한 데이터베이스 이름을 입력합니다.

    ![SQL 데이터베이스 이름](./media/sql-database-get-started/sql-database-name.png)
4. **원본 선택**에서 **샘플(AdventureWorksLT)**을 선택합니다.
   
    ![Adventure Works LT](./media/sql-database-get-started/adventureworkslt.png)
5. **서버**에서 서버가 선택되었는지 확인합니다. 서버에 데이터베이스를 추가할 때 데이터베이스를 단일 데이터베이스(기본값)로 추가하거나 탄력적 풀에 추가할 수 있습니다. 탄력적 풀에 대한 자세한 내용은 [탄력적 풀](sql-database-elastic-pool.md)을 참조하세요.

6. **가격 책정 계층**에서 가격 책정 계층을 **기본**으로 변경하고 **선택**을 클릭합니다. 원한다면 나중에 가격 책정 계층을 높일 수 있지만 여기서는 학습 목적으로 최저 비용 계층을 사용하는 것이 좋습니다.

    ![가격 책정 계층](./media/sql-database-get-started/pricing-tier.png)
7. **대시보드에 고정** 확인란을 선택하고 **만들기**를 클릭합니다.

    ![만들기 단추](./media/sql-database-get-started/create.png)

8. 데이터베이스를 만든 후 Azure Portal에서 해당 속성을 봅니다. 이후 자습서에서는 이 블레이드에서 사용할 수 있는 옵션을 이해할 수 있도록 합니다. 

    ![새 샘플 데이터베이스 블레이드](./media/sql-database-get-started/new-sample-db-blade.png)

## <a name="query-the-database-in-the-azure-portal"></a>Azure Portal에서 데이터베이스 쿼리

이 절차의 단계에서는 Azure Portal에서 직접 데이터베이스를 쿼리하는 방법을 보여줍니다. 

1. SQL Database 블레이드의 도구 모음에서 **도구**를 클릭합니다.

    ![도구](./media/sql-database-get-started/tools.png)
2. 도구 블레이드에서 **쿼리 편집기(미리 보기)**를 클릭합니다.

    ![쿼리 편집기](./media/sql-database-get-started/query-editor.png)
3. 이 확인란을 클릭하여 쿼리 편집기가 미리 보기 기능인지 확인한 다음 **확인**을 클릭합니다.
4. **쿼리 편집기** 블레이드에서 **로그인**을 클릭합니다.

    ![쿼리 편집기 블레이드](./media/sql-database-get-started/query-editor-blade.png)
5. 인증 유형 및 로그인을 검토한 다음 이 로그인에 대한 암호를 제공합니다. 

    ![쿼리 편집기 로그인](./media/sql-database-get-started/query-editor-login.png)
6. **확인**을 클릭하여 로그인하려고 합니다.
7. 인증되면 쿼리 창에 다음 쿼리를 입력하고 **실행**을 클릭합니다.

   ```select * from sys.objects```

    ![쿼리 편집기 쿼리](./media/sql-database-get-started/query-editor-query.png)

8. **결과** 창에서 쿼리 결과를 검토합니다.

    ![쿼리 편집기 결과](./media/sql-database-get-started/query-editor-results.png)

## <a name="query-the-database-with-ssms"></a>SSMS로 데이터베이스 쿼리

이 절차의 단계에서는 SQL Server Management Studio를 사용하여 데이터베이스에 연결한 다음 샘플 데이터를 쿼리하여 데이터베이스의 개체를 보는 방법을 보여줍니다.

1. SQL Server Management Studio로 전환하고 [개체 탐색기]에서 도구 모음의 **데이터베이스**를 클릭한 다음 **새로 고침**을 클릭하여 샘플 데이터베이스를 봅니다.

    ![SSMS를 통한 새 샘플 데이터베이스](./media/sql-database-get-started/new-sample-db-ssms.png)
2. [개체 탐색기]에서 새 데이터베이스를 확장하여 해당 개체를 봅니다.

    ![SSMS를 통한 새 샘플 데이터베이스 개체](./media/sql-database-get-started/new-sample-db-objects-ssms.png)
3. 샘플 데이터베이스를 마우스 오른쪽 단추로 클릭한 다음 **새 쿼리**를 클릭합니다.

    ![SSMS를 통한 새 샘플 데이터베이스 쿼리](./media/sql-database-get-started/new-sample-db-query-ssms.png)
4. 쿼리 창에서 다음 쿼리를 입력합니다.

   ```select * from sys.objects```
   
9.  도구 모음에서 **실행**을 클릭하여 샘플 데이터베이스의 모든 시스템 개체 목록을 반환합니다.

    ![SSMS를 통한 새 샘플 데이터베이스 쿼리 시스템 개체](./media/sql-database-get-started/new-sample-db-query-objects-ssms.png)

## <a name="create-a-blank-database-with-ssms"></a>SSMS로 빈 데이터베이스 만들기

이 절차의 단계에서는 SQL Server Management Studio를 사용하여 새 데이터베이스를 만드는 방법을 보여줍니다.

1. [개체 탐색기]에서 **데이터베이스**를 마우스 오른쪽 단추로 클릭한 다음 **새 데이터베이스**를 클릭합니다.

    ![SSMS를 통한 새 빈 데이터베이스](./media/sql-database-get-started/new-blank-database-ssms.png)

2. **새 데이터베이스** 대화 상자에서 데이터베이스 이름 텍스트 상자에 데이터베이스 이름을 입력합니다. 

    ![SSMS를 통한 새 빈 데이터베이스 이름](./media/sql-database-get-started/new-blank-database-name-ssms.png)

3. [새 데이터베이스] 대화 상자에서 **옵션**을 클릭한 다음 버전을 **기본**으로 변경합니다.

    ![SSMS를 통한 새 빈 데이터베이스 옵션](./media/sql-database-get-started/new-blank-database-options-ssms.png)

    > [!TIP]
    > Azure SQL Database에 대해 수정할 수 있는 이 대화 상자의 다른 옵션을 검토합니다. 이러한 옵션에 대한 자세한 내용은 [Create Database](https://msdn.microsoft.com/library/dn268335.aspx)를 참조하세요.
    >

4. **확인**을 클릭하여 빈 데이터베이스를 만듭니다.
5. 작업이 완료되면 [개체 탐색기]에서 데이터베이스 노드를 새로 고쳐 새로 만든 빈 데이터베이스를 봅니다. 

    ![개체 탐색기의 새 빈 데이터베이스](./media/sql-database-get-started/new-blank-database-object-explorer.png)

## <a name="troubleshoot-connectivity"></a>연결 문제 해결

Azure SQL Database에 대한 연결이 실패하면 오류 메시지가 표시됩니다. SQL Azure 데이터베이스 재구성, 방화벽 설정, 연결 시간 제한 또는 잘못된 로그인 정보로 인해 연결 문제가 발생할 수 있습니다. 연결 문제 해결 도구에 대한 내용은 [Microsoft Azure SQL Database 연결 문제 해결](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database)을 참조하세요.

## <a name="delete-a-single-database-in-the-azure-portal"></a>Azure Portal에서 단일 데이터베이스 삭제

이 절차의 단계에서는 Azure Portal을 사용하여 단일 데이터베이스를 삭제하는 방법을 보여줍니다.

1. Azure Portal의 SQL 데이터베이스 블레이드에서 삭제하려는 데이터베이스를 클릭합니다. 
2.  해당 SQL 데이터베이스에 대해 **삭제**를 클릭합니다.

    ![delete-database](./media/sql-database-get-started/delete-database.png)
2. **예**를 클릭하여 이 데이터베이스를 영구적으로 삭제한다고 확인합니다.

    ![delete-database-yes](./media/sql-database-get-started/delete-database-yes.png)

> [!TIP]
> 서버 자체를 삭제하지 않은 경우 데이터베이스의 보존 기간 중에 서비스 시작 자동 백업에서 데이터베이스를 복원할 수 있습니다. 기본 버전의 데이터베이스인 경우&7;일 이내에 복원할 수 있습니다. 그 외의 버전은 35일 이내에 복원할 수 있습니다. 서버 자체를 삭제하면 서버 또는 해당 서버의 삭제된 데이터베이스를 복구할 수 없습니다. 데이터베이스 백업에 대한 자세한 내용은 [SQL Database 백업에 대해 알아보기](sql-database-automated-backups.md)를 참조하고 백업에서 데이터베이스를 복원하는 방법에 대한 정보는 [데이터베이스 복구](sql-database-recovery-using-backups.md)를 참조하세요. 삭제된 데이터베이스를 복원하는 방법에 대한 방법 문서는 [삭제된 Azure SQL Database 복원 - Azure Portal](sql-database-restore-deleted-database-portal.md)을 참조하세요.
>


## <a name="next-steps"></a>다음 단계
이제 이 자습서를 완료했으므로 여기서 학습한 내용을 빌드하기 위해 탐색할 수 있는 추가 자습서가 다음과 같이 다양하게 준비되어 있습니다. 

- SQL Server 인증 자습서를 시작하려면 [SQL 인증 및 권한 부여](sql-database-control-access-sql-authentication-get-started.md)를 참조하세요.
- Azure Active Directory 인증 자습서를 시작하려면 [AAD 인증 및 권한 부여](sql-database-control-access-aad-authentication-get-started.md)를 참조하세요.
* Azure Portal에서 샘플 데이터베이스를 쿼리하려는 경우 [공개 미리 보기: SQL 데이터베이스에 대한 대화형 쿼리 경험](https://azure.microsoft.com/updates/azure-sql-database-public-preview-t-sql-editor/)을 참조하세요.
* Excel을 알고 있는 경우 [Azure에서 Excel로 SQL Database에 연결](sql-database-connect-excel.md)하는 방법에 대해 알아보세요.
* 코딩을 시작할 준비가 되었다면 [SQL Database 및 SQL Server에 대한 연결 라이브러리](sql-database-libraries.md)에서 프로그래밍 언어를 선택합니다.
* 온-프레미스 SQL Server 데이터베이스를 Azure로 이동하려면 [SQL Database로 데이터베이스 마이그레이션](sql-database-cloud-migrate.md)을 참조하세요.
* BCP 명령줄 도구를 사용하여 CSV 파일에서 새 테이블로 일부 데이터를 로드하려는 경우 [BCP를 사용하여 CSV 파일에서 SQL Database로 데이터 로드](sql-database-load-from-csv-with-bcp.md)를 참조하세요.
* 테이블 및 다른 개체를 만들려면 [테이블 만들기](https://msdn.microsoft.com/library/ms365315.aspx)에서 "테이블을 만들려면" 항목을 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- 기술적 개요는 [SQL Database 정의](sql-database-technical-overview.md)를 참조하세요.
- 가격 정보는 [Azure SQL Database 가격 책정](https://azure.microsoft.com/pricing/details/sql-database/)을 참조하세요.


