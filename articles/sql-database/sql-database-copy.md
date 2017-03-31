---
title: "Azure SQL Database 복사 | Microsoft Docs"
description: "Azure SQL 데이터베이스 사본 만들기"
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: 
ms.assetid: 5aaf6bcd-3839-49b5-8c77-cbdf786e359b
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 10/24/2016
ms.author: sashan
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: e64abfd5581c02b609707f7fa712962c024b293b
ms.lasthandoff: 03/23/2017


---
# <a name="copy-an-azure-sql-database"></a>Azure SQL 데이터베이스 복사

동일한 서버 또는 다른 서버에 데이터베이스 복사본을 만들 수 있습니다. 데이터베이스 복사는 복사 요청 당시의 원본 데이터베이스의 스냅숏입니다. 데이터베이스 복사의 서버 계층과 성능 수준(가격 책정 계층)은 기본적으로 원본 데이터베이스와 동일합니다. API를 사용하는 경우 동일한 서비스 계층(버전) 내의 다양한 성능 수준을 선택할 수 있습니다. 복사 완료 후 사본은 완전히 작동하는 독립 데이터베이스입니다. 이 시점에서 모든 버전으로 업그레이드하거나 다운그레이드할 수 있습니다. 로그인, 사용자 및 사용 권한은 독립적으로 관리됩니다.  

데이터베이스를 동일한 논리적 서버에 복사할 때 두 데이터베이스에서 동일한 로그인을 사용할 수 있습니다. 데이터베이스를 복사하는 데 사용하느 보안 주체는 새 데이터베이스의 데이터베이스 소유자(DBO)가 됩니다. 모든 데이터베이스 사용자, 권한 및 보안 식별자(SID)가 데이터베이스 사본에 복사됩니다.  

다른 논리 서버로 데이터베이스를 복사할 때 새 서버의 보안 주체가 새 데이터베이스의 데이터베이스 소유자가 됩니다. 데이터 액세스에 [포함된 데이터베이스 사용자](sql-database-manage-logins.md)를 사용하는 경우 복사가 완료된 후 동일한 자격 증명으로 액세스할 수 있도록 주 데이터베이스와 보조 데이터베이스에서 항상 동일한 사용자 자격 증명을 사용해야 합니다. [Azure Active Directory](../active-directory/active-directory-whatis.md)를 사용하는 경우 복사본에서 자격 증명을 관리할 필요가 없습니다. 그러나 데이터베이스를 새 서버로 복사할 때 새 서버에 로그인이 존재하지 않으므로 로그인 기반 액세스가 작동하지 않을 수 있습니다. 다른 논리 서버로 데이터베이스를 복사할 때 로그인을 관리하는 방법에 대한 자세한 내용은 [재해 복구 후에 Azure SQL Database 보안을 관리하는 방법](sql-database-geo-replication-security-config.md) 을 참조하세요. 

[Azure Portal](sql-database-copy-portal.md), [PowerShell](scripts/sql-database-copy-database-to-new-server-powershell.md) 또는 [T-SQL](sql-database-copy-transact-sql.md)을 사용하여 SQL Database를 복사할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

* 다른 논리 서버로 데이터베이스를 복사할 때 사용자 및 로그인을 관리하는 방법에 대한 자세한 내용은 [재해 복구 후에 Azure SQL 데이터베이스 보안을 관리하는 방법](sql-database-geo-replication-security-config.md) 을 참조하세요.
* 로그인에 대한 자세한 내용은 [로그인 관리](sql-database-manage-logins.md)를 참조하세요.
* 데이터베이스를 내보내려면 [데이터베이스를 BACPAC로 내보내기](sql-database-export.md)를 참조하세요.

