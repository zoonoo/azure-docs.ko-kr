---
title: "Azure Portal: Azure SQL Database 서버 수준 방화벽 규칙 | Microsoft Docs"
description: "Azure Portal을 사용하여 Azure SQL 서버에 액세스하는 IP 주소에 대한 서버 수준 방화벽 규칙을 구성하는 방법에 대해 알아봅니다."
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
ms.assetid: c3b206b5-af6e-41af-8306-db12ecfc1b5d
ms.service: sql-database
ms.custom: authentication and authorization
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/21/2017
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: ae230c012a17eb73c8993a32197c844c6abaa2a4
ms.openlocfilehash: 1939b69ee806d9091a21f3b21a276175d5a19a62
ms.lasthandoff: 02/17/2017


---
# <a name="create-and-manage-azure-sql-database-server-level-firewall-rules-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure SQL Database 서버 수준 방화벽 규칙 만들기 및 관리

관리자는 서버 수준 방화벽 규칙을 사용하여 지정된 IP 주소 또는 IP 주소 범위에서 SQL Database 서버에 액세스 할 수 있습니다. 또한 동일한 액세스를 요구하는 데이터베이스가 많이 있고 각 데이터베이스를 개별적으로 구성하는 데 시간을 허비하지 않으려는 경우 사용자에 대해 서버 수준 방화벽 규칙을 사용할 수도 있습니다. 보안을 강화하고 데이터베이스의 휴대성을 높이기 위해 가능한 경우에는 항상 데이터베이스 수준 방화벽 규칙을 사용하는 것이 좋습니다. SQL Database 방화벽에 대한 개요는 [SQL Database 방화벽 규칙 개요](sql-database-firewall-configure.md)를 참조하세요.

> [!Note]
> 비즈니스 연속성의 맥락에서 휴대용 데이터베이스에 대한 자세한 내용은 [재해 복구를 위한 인증 요구 사항](sql-database-geo-replication-security-config.md)을 참조하세요.
>

[!INCLUDE [Create SQL Database firewall rule](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Azure 포털을 통해 기존 서버 수준 방화벽 규칙 관리
서버 수준 방화벽 규칙을 관리하는 단계를 반복합니다.

* 현재 컴퓨터를 추가하려면 클라이언트 IP 추가를 클릭합니다.
* 추가 IP 주소를 추가 하려면 규칙 이름, 시작 IP 주소 및 끝 IP 주소를 입력 합니다.
* 기존 규칙을 수정 하려면 규칙의 필드 중 하나를 클릭 후 변경 합니다.
* 기존 규칙을 삭제 하려면 행의 끝에서 X가 나타날 때까지 규칙 위로 가져갑니다. 규칙을 제거 하려면 X를 클릭 합니다.

**저장** 을 클릭하여 변경 내용을 저장합니다.

## <a name="next-steps"></a>다음 단계

- 서버 수준 방화벽을 사용하여 서버에 프로비전 및 연결하는 방법을 보여주는 자습서는 [자습서: Azure Portal 및 SQL Server Management Studio를 사용하여 Azure SQL Database에 프로비전 및 액세스](sql-database-get-started.md)합니다.
- SQL Server 인증 및 데이터베이스 수준 방화벽에 대한 자습서는 [SQL 인증 및 권한 부여](sql-database-control-access-sql-authentication-get-started.md)를 참조하세요.
- 오픈 소스 또는 타사 응용 프로그램에서 Azure SQL 데이터베이스에 연결하는 방법에 대한 도움말은 [SQL 데이터베이스에 대한 클라이언트 빠른 시작 코드 샘플](https://msdn.microsoft.com/library/azure/ee336282.aspx)을 참조하세요.
- 데이터베이스에 연결할 수 있는 추가 사용자를 만드는 방법을 이해하려면 [SQL 데이터베이스 인증 및 권한 부여: 액세스 부여](https://msdn.microsoft.com/library/azure/ee336235.aspx)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스
* [데이터베이스 보안 설정](sql-database-security-overview.md)   
* [SQL Server 데이터베이스 엔진 및 Azure SQL 데이터베이스 보안 센터](https://msdn.microsoft.com/library/bb510589)   




