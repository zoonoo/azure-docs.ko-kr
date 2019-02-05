---
author: MightyPen
ms.service: sql-database
ms.topic: include
ms.date: 01/28/2019
ms.author: genemi
ms.openlocfilehash: edeaa932abe4d1882f957d0ed26aaddd97dabe3f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55198064"
---
<!-- sql-database-connect-query-prerequisites-create-db-includes.md -->

- [논리 서버](https://docs.microsoft.com/azure/sql-database/sql-database-single-index) 또는 [Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index)에 배치된 Azure SQL Database. 다음 기술 중 하나를 사용하여 데이터베이스를 만들 수 있습니다.

| 논리 서버 | 관리되는 인스턴스 |
| --- | --- |
| [포털](../articles/sql-database/sql-database-get-started-portal.md) | [포털](../articles/sql-database/sql-database-managed-instance-get-started.md) |
| [CLI](../articles/sql-database/sql-database-get-started-cli.md) | |
| [PowerShell](../articles/sql-database/sql-database-get-started-powershell.md) | |

- **논리 서버만** - 논리 서버에 연결할 수 있도록 구성된 서버 수준 방화벽 규칙. 자세한 내용은 [서버 수준 방화벽 규칙 만들기](../articles/sql-database/sql-database-get-started-portal-firewall.md)를 참조하세요.
- **Managed Instance만** - Managed Instance에 액세스하는 컴퓨터에서 구성된 연결. 다음 옵션을 사용할 수 있습니다.
  - 인스턴스에 액세스할 수 있는 Managed Instance와 동일한 Azure VNet에 있는 [Azure Virtual Machine](../articles/sql-database/sql-database-managed-instance-configure-vm.md).
  - 컴퓨터를 Managed Instance가 배치되는 VNet에 조인하고 Managed Instance를 네트워크의 다른 SQL Server로 사용할 수 있게 해주는 [지점 및 사이트간 연결](../articles/sql-database/sql-database-managed-instance-configure-p2s.md).
