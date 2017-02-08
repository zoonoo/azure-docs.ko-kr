---
title: "Azure SQL Database란? | Microsoft Docs"
description: "이 문서에서는 Azure SQL Database의 개요를 제공합니다."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: single databases
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 11/28/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 06639080dec485395d6821c371a4f792c7c02845
ms.openlocfilehash: 5e3ac3a08ea3e5c72082dcc7faa209d848acf2fe


---
# <a name="azure-sql-database-overview"></a>Azure SQL Database 개요
이 항목에서는 Azure SQL Database의 개요를 제공합니다. Azure SQL 논리 서버에 대한 정보는 [논리 서버](sql-database-server-overview.md)를 참조하세요.

## <a name="what-is-azure-sql-database"></a>Azure SQL Database 정의
Azure SQL Database의 데이터베이스는 각각 로컬 서버와 연결됩니다. 데이터베이스는 다음과 같을 수 있습니다.

- [고유한 리소스 집합](sql-database-what-is-a-dtu.md#what-are-database-transaction-units-dtus)(DTU)이 있는 단일 데이터베이스
- [리소스 집합을 공유](sql-database-what-is-a-dtu.md#what-are-elastic-database-transaction-units-edtus)(eDTU)하는 [탄력적 풀](sql-database-elastic-pool.md)의 일부
- 일부 [분할 데이터베이스의 확장된 집합](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling)은 단일 또는 풀링된 데이터베이스
- [다중 테넌트 SaaS 디자인 패턴](sql-database-design-patterns-multi-tenancy-saas-applications.md)에 속한 데이터베이스 집합의 일부 및 해당 데이터베이스는 단일 또는 풀링된 데이터베이스 또는 둘 다일 수 있습니다. 

## <a name="how-do-i-connect-and-authenticate-to-an-azure-sql-database"></a>Azure SQL Database에 연결하고 인증하려면 어떻게 해야 하나요?

- **인증 및 권한 부여**: Azure SQL Database는 SQL 인증 및 Azure Active Directory 인증을 지원하며 특정 제한 사항을 포함합니다. 인증에 대해서는 [Azure Active Directory 인증을 사용하여 SQL Database에 연결](sql-database-aad-authentication.md)을 참조하세요. 서버의 마스터 데이터베이스를 통해 Azure SQL Database 또는 사용자 데이터베이스에 직접 연결하고 인증할 수 있습니다. 자세한 내용은 [Azure SQL Database에서 데이터베이스 및 로그인 관리](sql-database-manage-logins.md)를 참조하세요. Windows 인증은 지원되지 않습니다. 
- **TDS**: Microsoft Azure SQL Database는 TDS(Tabular Data Stream) 프로토콜 클라이언트 버전 7.3 이상을 지원합니다.
- **TCP/IP**: TCP/IP 연결만 허용됩니다.
- **SQL Database 방화벽**: 데이터를 보호하기 위해, SQL Database 방화벽은 권한이 있는 컴퓨터를 지정할 때까지 데이터베이스 서버 또는 해당 데이터베이스에 대한 모든 액세스를 금지합니다. [방화벽](sql-database-firewall-configure.md)을 참조하세요.

## <a name="what-collations-are-supported"></a>어떤 데이터 정렬이 지원되나요?
Microsoft Azure SQL Database에서 사용하는 기본 데이터베이스 데이터 정렬은 **SQL_LATIN1_GENERAL_CP1_CI_AS**이며 여기서 **LATIN1_GENERAL**은 영어(미국), **CP1**은 코드 페이지 1252, **CI**는 대/소문자 구분, **AS**는 악센트를 구분합니다. V12 데이터베이스의 데이터 정렬을 변경하는 것은 불가능합니다. 데이터 정렬을 설정하는 방법에 대한 자세한 내용은 [COLLATE(Transact-SQL)](https://msdn.microsoft.com/library/ms184391.aspx)를 참조하세요.

## <a name="what-are-the-naming-requirements-for-database-objects"></a>데이터베이스 개체에 대한 명명 요구 사항은 무엇인가요?

모든 새 개체에 대한 이름은 식별자에 대한 SQL Server 규칙을 준수해야 합니다. 자세한 내용은 [Identifiers](https://msdn.microsoft.com/library/ms175874.aspx)를 참조하세요.

## <a name="what-features-are-supported-by-azure-sql-databases"></a>Azure SQL Database에서 어떤 기능이 지원되나요?

지원되는 기능에 대한 자세한 내용은 [기능](sql-database-features.md)을 참조하세요. 특정 유형의 기능을 지원하지 않는 이유에 대한 자세한 배경은 [Azure SQL Database Transact-SQL의 차이점](sql-database-transact-sql-information.md)을 참조하세요.

## <a name="how-do-i-manage-an-azure-sql-database"></a>Azure SQL Database를 관리하려면 어떻게 해야 하나요?

몇 가지 메서드를 사용하여 Azure SQL Database 논리 서버를 관리할 수 있습니다.
- [Azure 포털](sql-database-manage-portal.md)
- [PowerShell](sql-database-manage-powershell.md)
- [Transact-SQL](sql-database-manage-azure-ssms.md)
- [REST (영문)](/rest/api/sql/)

## <a name="next-steps"></a>다음 단계

- Azure SQL Database 서비스에 대한 정보는 [SQL Database 정의](sql-database-technical-overview.md)를 참조하세요.
- 지원되는 기능에 대한 자세한 내용은 [기능](sql-database-features.md)을 참조하세요.
- Azure SQL 논리 서버의 전체적인 개요는 [SQL Database 논리 서버 개요](sql-database-server-overview.md)를 참조하세요.
- Transact-SQL 지원 및 차이점에 대한 정보는 [Azure SQL Database Transact-SQL의 차이점](sql-database-transact-sql-information.md)을 참조하세요.
- **서비스 계층**에 따른 특정 리소스 할당량 및 제한 사항에 대한 정보입니다. 서비스 계층에 대한 개요는 [SQL 데이터베이스 서비스 계층](sql-database-service-tiers.md)을 참조하세요.
- 보안 개요에 대해서는 [Azure SQL Database 보안 개요](sql-database-security-overview.md)를 참조하세요.
- 드라이버 가용성 및 SQL 데이터베이스 지원에 대한 내용은 [SQL 데이터베이스 및 SQL Server의 연결 라이브러리](sql-database-libraries.md)를 참조하세요.




<!--HONumber=Feb17_HO1-->


