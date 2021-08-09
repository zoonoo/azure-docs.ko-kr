---
title: 조건부 액세스
description: Azure SQL Database, Azure SQL Managed Instance, Azure Synapse Analytics에서 조건부 액세스를 구성하는 방법에 대해 알아봅니다.
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.custom: sqldbrb=1
ms.date: 04/28/2020
tag: azure-synpase
ms.openlocfilehash: c18d235977f1256a10e813fa8e02aa3590366fe1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97936416"
---
# <a name="conditional-access-with-azure-sql-database-and-azure-synapse-analytics"></a>Azure SQL Database 및 Azure Synapse Analytics를 사용한 조건부 액세스

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

[Azure SQL Database](sql-database-paas-overview.md), [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md), [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)는 Microsoft 조건부 액세스를 지원합니다.

다음 단계에서는 조건부 액세스 정책을 적용하도록 Azure SQL Database, SQL Managed Instance 또는 Azure Synapse를 구성하는 방법을 보여 줍니다.  

## <a name="prerequisites"></a>필수 구성 요소

- Azure AD(Azure Active Directory) 인증을 지원하려면 Azure Synapse에서 Azure SQL Database, Azure SQL Managed Instance 또는 전용 SQL 풀을 구성해야 합니다. 자세한 단계는 [SQL Database 또는 Azure Synapse에서 Azure Active Directory 인증 구성 및 관리](authentication-aad-configure.md)를 참조하세요.  
- Multi-Factor Authentication을 사용하는 경우 최신 SSMS(SQL Server Management Studio)와 같은 지원되는 도구에 연결해야 합니다. 자세한 내용은 [SQL Server Management Studio에 대한 Azure SQL Database 다단계 인증 구성](authentication-mfa-ssms-configure.md)을 참조하세요.  

## <a name="configure-conditional-access"></a>조건부 액세스 구성

> [!NOTE]
> 아래 예제에서는 Azure SQL Database를 사용하지만 조건부 액세스를 구성하려는 적절한 제품을 선택해야 합니다.

1. Azure Portal에 로그인하고 **Azure Active Directory** 를 선택한 후 **조건부 액세스** 를 선택합니다. 자세한 내용은 [Azure Active Directory 조건부 액세스 기술 참조](../../active-directory/conditional-access/concept-conditional-access-conditions.md)를 참조하세요.  
   ![조건부 액세스 블레이드](./media/conditional-access-configure/conditional-access-blade.png)

2. **조건부 액세스 정책** 블레이드에서 **새 정책** 을 클릭하고 이름을 입력한 다음 **규칙 구성** 을 클릭합니다.  
3. **할당** 아래에서 **사용자 및 그룹** 을 선택하고 **사용자 및 그룹 선택** 옵션을 선택한 후 조건부 액세스에 대한 사용자 또는 그룹을 선택합니다. **선택** 을 클릭한 다음 **완료** 를 클릭하여 선택 사항을 적용합니다.  
   ![사용자 및 그룹 선택](./media/conditional-access-configure/select-users-and-groups.png)  

4. **클라우드 앱** 을 선택하고 **앱 선택** 을 클릭합니다. 조건부 액세스에 사용할 수 있는 모든 앱이 표시됩니다. **Azure SQL Database** 를 선택하고 아래쪽에서 **선택** 을 클릭한 다음, **완료** 를 클릭합니다.  
   ![SQL Database 선택](./media/conditional-access-configure/select-sql-database.png)  
   다음 세 번째 스크린샷에 나열된 **Azure SQL Database** 를 찾을 수 없는 경우 다음 단계를 완료합니다.
   - Azure AD 관리자 계정으로 SSMS를 사용하여 Azure SQL Database의 데이터베이스에 연결합니다.  
   - `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`을 실행합니다.  
   - Azure AD에 로그인하여 Azure SQL Database, SQL Managed Instance 또는 Azure Synapse가 Azure AD 인스턴스의 애플리케이션에 나열되어 있는지 확인합니다.  

5. **액세스 제어** 를 선택하고 **권한 부여** 를 선택한 다음 적용하려는 정책을 선택합니다. 예를 들어 **다단계 인증 필요** 를 선택합니다.  
   ![액세스 권한 부여 선택](./media/conditional-access-configure/grant-access.png)  

## <a name="summary"></a>요약

Azure AD Premium을 사용하여 선택한 애플리케이션(Azure SQL Database)은 이제 선택한 조건부 액세스 정책, **필요한 다단계 인증** 을 적용합니다.

Azure SQL Database와 Azure Synapse의 다단계 인증에 대한 질문은 <MFAforSQLDB@microsoft.com>에 문의하세요.  

## <a name="next-steps"></a>다음 단계  

자습서는 [SQL Database의 데이터베이스 보안](secure-database-tutorial.md)을 참조하세요.