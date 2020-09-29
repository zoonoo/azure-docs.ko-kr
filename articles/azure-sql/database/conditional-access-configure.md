---
title: 조건부 액세스
description: Azure SQL Database, Azure SQL Managed Instance 및 Azure Synapse Analytics에 대 한 조건부 액세스를 구성 하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: ad80f68c1ab3b3583c5a22de49b77211571f345e
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91443990"
---
# <a name="conditional-access-with-azure-sql-database-and-azure-synapse-analytics"></a>Azure SQL Database 및 Azure Synapse Analytics를 사용한 조건부 액세스

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

[Azure SQL Database](sql-database-paas-overview.md), [azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md)및 [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 는 Microsoft 조건부 액세스를 지원 합니다.

다음 단계에서는 Azure SQL Database, SQL Managed Instance 또는 Azure Synapse를 구성 하 여 조건부 액세스 (CA) 정책을 적용 하는 방법을 보여 줍니다.  

## <a name="prerequisites"></a>필수 요건

- Azure Active Directory (Azure AD) 인증을 지원 하기 위해 Azure Synapse에서 Azure SQL Database, Azure SQL Managed Instance 또는 Azure SQL 풀을 구성 해야 합니다. 특정 단계는 [SQL Database 또는 Azure Synapse를 사용 하 여 Azure Active Directory 인증 구성 및 관리](authentication-aad-configure.md)를 참조 하세요.  
- Multi-Factor Authentication 사용 하도록 설정 된 경우 최신 SQL Server Management Studio (SSMS)와 같은 지원 되는 도구를 사용 하 여 연결 해야 합니다. 자세한 내용은 [SQL Server Management Studio에 대한 Azure SQL Database 다단계 인증 구성](authentication-mfa-ssms-configure.md)을 참조하세요.  

## <a name="configure-conditional-access"></a>조건부 액세스 구성

> [!NOTE]
> 아래 예제에서는 Azure SQL Database를 사용 하지만 조건부 액세스를 구성 하려는 적절 한 제품을 선택 해야 합니다.

1. Azure Portal에 로그인 하 고 **Azure Active Directory**을 선택한 다음 **조건부 액세스**를 선택 합니다. 자세한 내용은 [Azure Active Directory 조건부 액세스 기술 참조](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference)를 참조하세요.  
   ![조건부 액세스 블레이드](./media/conditional-access-configure/conditional-access-blade.png)

2. **조건부 액세스 정책** 블레이드에서 **새 정책**을 클릭하고 이름을 입력한 다음 **규칙 구성**을 클릭합니다.  
3. **할당**아래에서 **사용자 및 그룹**을 선택 하 고 **사용자 및 그룹 선택**을 선택 하 고 조건부 액세스를 위한 사용자 또는 그룹을 선택 합니다. **선택**을 클릭한 다음 **완료**를 클릭하여 선택 사항을 적용합니다.  
   ![사용자 및 그룹 선택](./media/conditional-access-configure/select-users-and-groups.png)  

4. **클라우드 앱**을 선택하고 **앱 선택**을 클릭합니다. 조건부 액세스에 사용할 수 있는 모든 앱이 표시 됩니다. **Azure SQL Database**를 선택하고 아래쪽에서 **선택**을 클릭한 다음, **완료**를 클릭합니다.  
   ![SQL Database 선택](./media/conditional-access-configure/select-sql-database.png)  
   다음 세 번째 스크린샷에서 나열 **Azure SQL Database** 를 찾을 수 없는 경우 다음 단계를 완료 합니다.
   - Azure AD 관리자 계정으로 SSMS를 사용 하 여 Azure SQL Database에서 데이터베이스에 연결 합니다.  
   - `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`을 실행합니다.  
   - Azure AD에 로그인 하 고 Azure AD 인스턴스의 응용 프로그램에 Azure SQL Database, SQL Managed Instance 또는 Azure Synapse가 나열 되는지 확인 합니다.  

5. **액세스 제어**를 선택하고 **권한 부여**를 선택한 다음 적용하려는 정책을 선택합니다. 예를 들어 **다단계 인증 필요**를 선택합니다.  
   ![액세스 권한 부여 선택](./media/conditional-access-configure/grant-access.png)  

## <a name="summary"></a>요약

이제 Azure AD Premium를 사용 하 여 선택한 응용 프로그램 (Azure SQL Database)에서 선택한 조건부 액세스 정책, **필수 다단계 인증** 을 적용 합니다.

Multi-factor authentication에 대 한 Azure SQL Database 및 Azure Synapse에 대 한 질문은를 문의 하세요 <MFAforSQLDB@microsoft.com> .  

## <a name="next-steps"></a>다음 단계  

자습서는 [SQL Database에서 데이터베이스 보안 유지](secure-database-tutorial.md)를 참조 하세요.
