---
title: 조건부 액세스
description: Azure SQL 데이터베이스 및 Azure 시냅스에 대한 조건부 액세스를 구성하는 방법에 대해 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.custom: sql-data-warehouse
ms.date: 02/06/2020
tag: azure-synpase
ms.openlocfilehash: cd56ccf2e6a4ceb0d81c25b5b9e795176be66b77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124901"
---
# <a name="conditional-access-mfa-with-azure-sql-database-and-azure-synapse-analytics"></a>Azure SQL 데이터베이스 및 Azure 시냅스 분석을 사용하여 MFA(조건부 액세스)

Azure [SQL 데이터베이스](sql-database-technical-overview.md), [관리되는 인스턴스](sql-database-managed-instance.md)및 Azure [Synapse는](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) Microsoft 조건부 액세스를 지원합니다. 

> [!NOTE]
> 이 항목은 Azure SQL 서버및 Azure SQL 서버에서 생성된 SQL 데이터베이스 및 Azure 시냅스 모두에 적용됩니다. 간단히 하기 위해 SQL 데이터베이스는 SQL 데이터베이스와 Azure Synapse를 모두 참조할 때 사용됩니다.

다음 단계에서는 조건부 액세스 정책을 적용하기 위해 SQL Database를 구성하는 방법을 보여 줍니다.  

## <a name="prerequisites"></a>사전 요구 사항  
- Azure Active Directory 인증을 지원하려면 Azure Synapse에서 SQL Database 또는 SQL 풀을 구성해야 합니다. 특정 단계는 [SQL Database 또는 Azure Synapse를 사용하여 Azure Active Directory 인증 구성 및 관리를](sql-database-aad-authentication-configure.md)참조하세요.  
- 다단계 인증을 사용하는 경우 최신 SSMS와 같은 지원되는 도구에 연결해야 합니다. 자세한 내용은 [SQL Server Management Studio에 대한 Azure SQL Database 다단계 인증 구성](sql-database-ssms-mfa-authentication-configure.md)을 참조하세요.  

## <a name="configure-ca-for-azure-sql-dbdw"></a>Azure SQL DB/DW에 대한 CA 구성  
1. 포털에 로그인하고 **Azure Active Directory를**선택한 다음 **조건부 액세스를**선택합니다. 자세한 내용은 [Azure Active Directory 조건부 액세스 기술 참조](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference)를 참조하세요.  
   ![조건부 액세스 블레이드](./media/sql-database-conditional-access/conditional-access-blade.png) 
     
2. **조건부 액세스 정책** 블레이드에서 **새 정책**을 클릭하고 이름을 입력한 다음 **규칙 구성**을 클릭합니다.  
3. **할당에서**사용자 **및 그룹을**선택하고 **사용자 및 그룹 선택을**선택한 다음 조건부 액세스에 대한 사용자 또는 그룹을 선택합니다. **선택**을 클릭한 다음 **완료**를 클릭하여 선택 사항을 적용합니다.  
   ![사용자 및 그룹 선택](./media/sql-database-conditional-access/select-users-and-groups.png)  

4. **클라우드 앱**을 선택하고 **앱 선택**을 클릭합니다. 조건부 액세스에 사용할 수 있는 모든 앱이 표시됩니다. **Azure SQL Database**를 선택하고 아래쪽에서 **선택**을 클릭한 다음, **완료**를 클릭합니다.  
   ![SQL Database 선택](./media/sql-database-conditional-access/select-sql-database.png)  
   다음 세 번째 스크린샷에 나열된 **Azure SQL Database를** 찾을 수 없는 경우 다음 단계를 완료합니다.   
   - SSMS를 사용하여 AAD 관리자 계정으로 Azure SQL DB/DW 인스턴스에 로그인합니다.  
   - `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`을 실행합니다.  
   - AAD에 로그인하고 Azure SQL 데이터베이스 및 Azure 시냅스가 AAD의 응용 프로그램에 나열되어 있는지 확인합니다.  

5. **액세스 제어**를 선택하고 **권한 부여**를 선택한 다음 적용하려는 정책을 선택합니다. 예를 들어 **다단계 인증 필요**를 선택합니다.  
   ![액세스 권한 부여 선택](./media/sql-database-conditional-access/grant-access.png)  

## <a name="summary"></a>요약  
Azure AD Premium을 사용하여 Azure SQL DB/DW에 대한 연결을 허용하는 선택한 애플리케이션(Azure SQL Database)은 이제 선택한 조건부 액세스 정책, **필요한 다단계 인증**을 적용합니다.  
다단계 인증과 관련된 Azure SQL 데이터베이스 및 Azure 시냅스에 대한 질문은 에 문의하십시오. MFAforSQLDB@microsoft.com  

## <a name="next-steps"></a>다음 단계  

자습서는 [Azure SQL Database 보안](sql-database-security-tutorial.md)을 참조하세요.
