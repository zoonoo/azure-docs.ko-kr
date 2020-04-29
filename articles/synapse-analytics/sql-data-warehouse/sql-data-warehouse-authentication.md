---
title: 인증
description: Azure Active Directory (Azure AD) 또는 SQL Server 인증을 사용 하 여 Azure Synapse Analytics에 인증 하는 방법에 대해 알아봅니다.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/02/2019
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-synapse
ms.openlocfilehash: ed8dd902ac490b4e6a0f172029bf3ffa1d44acee
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81251846"
---
# <a name="authenticate-to-azure-synapse-analytics"></a>Azure Synapse Analytics에 인증

AAD (Azure Active Directory) 또는 SQL Server 인증을 사용 하 여 Azure Synapse에서 SQL Analytics에 인증 하는 방법에 대해 알아봅니다.

SQL 풀에 연결 하려면 인증을 위해 보안 자격 증명을 전달 해야 합니다. 연결을 설정할 때 특정 연결 설정이 쿼리 세션을 설정하는 일부로 구성됩니다.  

보안에 대 한 자세한 내용과 데이터 웨어하우스에 대 한 연결을 설정 하는 방법에 대 한 자세한 내용은 [데이터베이스 보안 설명서](sql-data-warehouse-overview-manage-security.md)를 참조 하세요.

## <a name="sql-authentication"></a>SQL 인증

SQL 풀에 연결 하려면 다음 정보를 제공 해야 합니다.

* 정규화된 서버 이름
* SQL 인증 지정
* 사용자 이름
* 암호
* 기본 데이터베이스(옵션)

기본적으로 연결은 사용자 데이터베이스가 아니라 *master* 데이터베이스에 연결 됩니다. 사용자의 사용자 데이터베이스에 연결하려면 다음과 같은 두 가지 작업 중 하나를 수행하도록 선택할 수 있습니다.

* SSDT, SSMS 또는 애플리케이션 연결 문자열에서 SQL Server 개체 탐색기에 서버를 등록할 때 기본 데이터베이스를 지정합니다. 예를 들어 ODBC 연결에 대해 InitialCatalog 매개 변수를 포함합니다.
* SSDT에서 세션을 만들기 전에 사용자 데이터베이스를 강조 표시합니다.

> [!NOTE]
> 연결을 위한 데이터베이스 변경의 경우 Transact-SQL 문 **USE MyDatabase;** 는 지원되지 않습니다. SSDT를 사용 하 여 SQL 풀에 연결 하는 지침은 [Visual Studio로 쿼리](sql-data-warehouse-query-visual-studio.md) 문서를 참조 하세요.

## <a name="azure-active-directory-aad-authentication"></a>AAD(Azure Active Directory) 인증

[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 인증은 Azure Active Directory (Azure AD)에서 id를 사용 하 여 SQL 풀에 연결 하는 메커니즘입니다. Azure Active Directory 인증을 사용하면 데이터베이스 사용자 및 다른 Microsoft 서비스의 ID를 하나의 중앙 위치에서 관리할 수 있습니다. 중앙 ID 관리는 Azure Synapse 사용자를 관리 하 고 권한 관리를 간소화 하는 단일 장소를 제공 합니다.

### <a name="benefits"></a>이점

Azure Active Directory의 이점은 다음과 같습니다.

* SQL Server 인증에 대한 대안을 제공합니다.
* 데이터베이스 서버 전체에서 사용자 ID의 확산을 중지합니다.
* 한 위치에서의 암호 회전이 가능합니다.
* 외부 (Azure AD) 그룹을 사용 하 여 데이터베이스 사용 권한을 관리 합니다.
* Windows 통합 인증 또는 Azure Active Directory에서 지원하는 기타 인증을 사용하여 암호 저장을 제거할 수 있습니다.
* 포함된 데이터베이스 사용자를 통해 데이터베이스 수준에서 ID를 인증합니다.
* 는 SQL 풀에 연결 하는 응용 프로그램에 대 한 토큰 기반 인증을 지원 합니다.
* [SQL Server Management Studio](../../sql-database/sql-database-ssms-mfa-authentication.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 및 [SQL Server Data Tools](/sql/ssdt/azure-active-directory?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)를 비롯 한 다양 한 도구에 대 한 Active Directory 범용 인증을 통해 multi-factor authentication을 지원 합니다.

> [!NOTE]
> Azure Active Directory는 비교적 새로운 기능으로, 몇 가지 제한 사항이 있습니다. Azure Active Directory이 사용자 환경에 적합 한지 확인 하려면 [AZURE AD 기능 및 제한](../../sql-database/sql-database-aad-authentication.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#azure-ad-features-and-limitations)사항, 특히 추가 고려 사항을 참조 하세요.

### <a name="configuration-steps"></a>구성 단계

다음 단계에 따라 Azure Active Directory 인증을 구성합니다.

1. Azure Active Directory 만들기 및 채우기
2. 옵션: 현재 Azure 구독과 연결된 Active Directory를 연결하거나 변경합니다.
3. Azure Synapse에 대 한 Azure Active Directory 관리자 만들기
4. 클라이언트 컴퓨터 구성
5. Azure AD ID에 매핑된 데이터베이스에서 포함된 데이터베이스 사용자 만들기
6. Azure AD id를 사용 하 여 SQL 풀에 연결

현재 Azure Active Directory 사용자는 SSDT 개체 탐색기에 표시되지 않습니다. 해결 방법으로 [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)에서 사용자를 봅니다.

### <a name="find-the-details"></a>세부 정보 찾기

* Azure Active Directory 인증을 구성 하 고 사용 하는 단계는 Azure Synapse의 Azure SQL Database 및 SQL Analytics에서 거의 동일 합니다. [Azure Active Directory 인증을 사용 하 여 SQL Database 또는 SQL 풀에 연결](../../sql-database/sql-database-aad-authentication.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)항목의 자세한 단계를 따르세요.
* 사용자 지정 데이터베이스 역할을 만들고 역할에 사용자를 추가 합니다. 그런 다음 역할에 세부적인 권한을 부여합니다. 자세한 내용은 [데이터베이스 엔진 권한 시작](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)을 참조하세요.

## <a name="next-steps"></a>다음 단계

Visual Studio 및 기타 응용 프로그램을 사용 하 여 쿼리를 시작 하려면 [Visual studio를 사용 하 여 쿼리](sql-data-warehouse-query-visual-studio.md)를 참조 하세요.
