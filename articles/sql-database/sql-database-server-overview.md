---
title: "Azure SQL Database 논리 서버 개요 | Microsoft Docs"
description: "이 페이지에서는 Azure SQL 논리 서버를 사용하는 고려 사항 및 지침을 제공합니다."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: servers
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 11/28/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: e5b5751facb68ae4a62e3071fe4dfefc02434a9f
ms.openlocfilehash: 17e2830dceeaa313dd0fd7d406bf68a75b6f900e


---
# <a name="azure-sql-database-logical-servers"></a>Azure SQL Database 논리 서버

이 항목에서는 Azure SQL 논리 서버를 사용하는 고려 사항 및 지침을 제공합니다. Azure SQL Database에 대한 자세한 내용은 [SQL Database](sql-database-overview.md)를 참조하세요.

## <a name="what-is-an-azure-sql-database-logical-server"></a>Azure SQL Database 논리 서버란?
Azure SQL Database 논리 서버는 여러 데이터베이스에 대한 중앙 관리 지점의 역할을 합니다. SQL Database에서 서버는 온-프레미스 환경에서 친숙한 SQL Server 인스턴스와 구별되는 논리적 구문입니다. 특히, SQL Database 서비스는 해당 논리 서버와 관련하여 데이터베이스의 위치르 보증하지는 않으며 인스턴스 수준의 액세스 또는 기능을 노출하지 않습니다. Azure SQL 논리 서버에 대한 자세한 정보는 [논리 서버](sql-database-server-overview.md)를 참조하세요. 

Azure 데이터베이스 논리 서버는 다음과 같습니다.

- Azure 구독 내에서 만들었지만 다른 구독에 포함된 리소스를 사용하여 이동시킬 수 있습니다
- 데이터베이스, 탄력적 풀 및 데이터 웨어하우스의 상위 리소스입니다.
- 데이터베이스, 탄력적 풀 및 데이터 웨어하우스의 네임스페이스를 제공합니다.
- 강력한 수명 의미 체계를 가진 논리 컨테이너로서 서버를 삭제하고 포함된 데이터베이스, 탄력적 풀, 데이터 웨어하우스를 삭제합니다.
- Azure RBAC(역할 기반 액세스 제어)에 포함됩니다. 서버 내의 데이터베이스, 탄력적 풀은 서버에서 액세스 권한을 상속합니다.
- Azure 리소스를 관리하기 위해 데이터베이스 및 탄력적 풀의 ID 상위 요소입니다(데이터베이스 및 풀은 URL 구성표 참조).
- 지역에 리소스 배치
- 데이터베이스 액세스에 대한 연결 끝점을 제공합니다(<serverName>.database.windows.net).
- 마스터 데이터베이스에 연결하여 DMV를 통해 포함된 리소스 관련 메타데이터에 대한 액세스를 제공합니다. 
- 로그인, 방화벽, 감사, 위협 요소 탐지 등 해당 데이터베이스에 적용되는 관리 정책에 대한 범위를 제공합니다. 
- 상위 구독 내의 할당량으로 제한됩니다(구독 당 6대의 서버 [여기에서 구독 제한 참조](../azure-subscription-service-limits.md)).
- 포함한 리소스에 대한 데이터베이스 할당량 및 DTU 할당량의 범위를 제공합니다(예: V12에서 45000DTU).
- 포함된 리소스에서 사용하도록 설정된 기능에 대한 버전 관리 범위입니다(최신 버전 V12).
- 서버 수준 주체 로그인은 서버에 있는 모든 데이터베이스를 관리할 수 있습니다.
- 서버에서 하나 이상의 데이터베이스에 대한 액세스를 부여하는 SQL Server 온-프레미스 인스턴스에서 해당 항목과 비슷한 로그인을 포함하고 제한된 관리 권한이 부여될 수 있습니다. 자세한 내용은 [로그인](sql-database-manage-logins.md)을 참조하세요.

## <a name="how-do-i-connect-and-authenticate-to-an-azure-sql-database-logical-server"></a>Azure SQL Database 논리 서버에 연결하고 인증하려면 어떻게 해야 하나요?

- **인증 및 권한 부여**: Azure SQL Database는 SQL 인증 및 Azure Active Directory 인증을 지원하며 특정 제한 사항을 포함합니다. 인증에 대해서는 [Azure Active Directory 인증을 사용하여 SQL Database에 연결](sql-database-aad-authentication.md)을 참조하세요. 서버의 마스터 데이터베이스를 통해 Azure SQL Database 또는 사용자 데이터베이스에 직접 연결하고 인증할 수 있습니다. 자세한 내용은 [Azure SQL Database에서 데이터베이스 및 로그인 관리](sql-database-manage-logins.md)를 참조하세요. Windows 인증은 지원되지 않습니다. 
- **TDS**: Microsoft Azure SQL Database는 TDS(Tabular Data Stream) 프로토콜 클라이언트 버전 7.3 이상을 지원합니다.
- **TCP/IP**: TCP/IP 연결만 허용됩니다.
- **SQL Database 방화벽**: 데이터를 보호하기 위해, SQL Database 방화벽은 권한이 있는 컴퓨터를 지정할 때까지 데이터베이스 서버 또는 해당 데이터베이스에 대한 모든 액세스를 금지합니다. [방화벽](sql-database-firewall-configure.md)을 참조하세요.

## <a name="what-collations-are-supported"></a>어떤 데이터 정렬이 지원되나요?

마스터 데이터베이스를 비롯한 Microsoft Azure SQL Database에서 사용하는 기본 데이터베이스 데이터 정렬은 **SQL_LATIN1_GENERAL_CP1_CI_AS**이며 여기서 **LATIN1_GENERAL**은 영어(미국), **CP1**은 코드 페이지 1252, **CI**는 대/소문자 구분, **AS**는 악센트를 구분합니다. V12 데이터베이스를 만든 후에는 데이터 정렬을 변경하지 않는 것이 좋습니다. 데이터 정렬에 대한 자세한 내용은 [정렬(Transact-SQL)](https://msdn.microsoft.com/library/ms184391.aspx)을 참조하세요.

## <a name="what-are-the-naming-requirements-for-database-objects"></a>데이터베이스 개체에 대한 명명 요구 사항은 무엇인가요?

모든 새 개체에 대한 이름은 식별자에 대한 SQL Server 규칙을 준수해야 합니다. 자세한 내용은 [Identifiers](https://msdn.microsoft.com/library/ms175874.aspx)를 참조하세요.

## <a name="what-features-are-supported"></a>어떤 기능이 지원되나요?

지원되는 기능에 대한 자세한 내용은 [기능](sql-database-features.md)을 참조하세요. 특정 유형의 기능을 지원하지 않는 이유에 대한 자세한 배경은 [Azure SQL Database Transact-SQL의 차이점](sql-database-transact-sql-information.md)을 참조하세요.

## <a name="how-do-i-manage-a-logical-server"></a>논리 서버를 관리하려면 어떻게 해야 하나요?

몇 가지 메서드를 사용하여 Azure SQL Database 논리 서버를 관리할 수 있습니다.
- [Azure 포털](sql-database-manage-portal.md)
- [PowerShell](sql-database-manage-powershell.md)
- [REST (영문)](/rest/api/sql/)

## <a name="next-steps"></a>다음 단계

- Azure SQL Database 서비스에 대한 정보는 [SQL Database 정의](sql-database-technical-overview.md)를 참조하세요.
- 지원되는 기능에 대한 자세한 내용은 [기능](sql-database-features.md)을 참조하세요.
- SQL Database 개요는 [SQL Database 개요](sql-database-overview.md)를 참조하세요.
- Transact-SQL 지원 및 차이점에 대한 정보는 [Azure SQL Database Transact-SQL의 차이점](sql-database-transact-sql-information.md)을 참조하세요.
- **서비스 계층**에 따른 특정 리소스 할당량 및 제한 사항에 대한 정보입니다. 서비스 계층에 대한 개요는 [SQL 데이터베이스 서비스 계층](sql-database-service-tiers.md)을 참조하세요.
- 보안 개요에 대해서는 [Azure SQL Database 보안 개요](sql-database-security-overview.md)를 참조하세요.
- 드라이버 가용성 및 SQL 데이터베이스 지원에 대한 내용은 [SQL 데이터베이스 및 SQL Server의 연결 라이브러리](sql-database-libraries.md)를 참조하세요.




<!--HONumber=Dec16_HO4-->


