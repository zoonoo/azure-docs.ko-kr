---
author: stevestein
ms.service: sql-database
ms.subservice: operations
ms.topic: include
ms.date: 08/23/2019
ms.author: sstein
ms.openlocfilehash: 3cc8e836d39a38f950e15d31aeb3e3acdca45acc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84031754"
---
## <a name="create-and-manage-azure-sql-resources-with-the-azure-portal"></a>Azure Portal을 사용하여 Azure SQL 리소스 만들기 및 관리

Azure Portal은 SQL 가상 머신을 포함하여 [모든 Azure SQL 리소스](https://go.microsoft.com/fwlink/?linkid=2100641)를 관리할 수 있는 단일 페이지를 제공합니다.

**AZURE sql** 페이지에 액세스 하려면 Azure Portal 메뉴에서 **azure sql** 을 선택 하거나, 모든 페이지에서 **azure sql** 을 검색 하 고 선택 합니다.

> [!NOTE]
> **AZURE sql** 은 Azure SQL Database의 단일 및 풀링된 데이터베이스와 해당 데이터베이스를 호스팅하는 논리 SQL SERVER, Sql 관리 되는 인스턴스 및 sql 가상 컴퓨터를 비롯 하 여 Azure Portal의 모든 SQL 리소스에 쉽고 빠르게 액세스할 수 있는 방법을 제공 합니다.  [AZURE sql](../azure-sql-iaas-vs-paas-what-is-overview.md) 은 서비스 또는 리소스가 아니라 sql 관련 서비스 제품군입니다. 

기존 리소스를 관리하려면 목록에서 원하는 항목을 선택합니다. 새 Azure SQL 리소스를 만들려면 **+ 추가**를 선택합니다. 

![Azure SQL 포털 페이지](./media/sql-database-create-manage-portal/add-azure-sql-resources.png)

**+ 추가**를 선택한 후에는 타일에서 **세부 정보 표시**를 선택하여 다양한 옵션에 대한 추가 정보를 확인합니다.

![데이터베이스 타일 세부 정보](./media/sql-database-create-manage-portal/single-sql-database-deployment-options.png)

자세한 내용은 다음을 참조하세요.

- [단일 데이터베이스 만들기](../database/single-database-create-quickstart.md)
- [탄력적 풀 만들기](../database/elastic-pool-overview.md#creating-a-new-sql-database-elastic-pool-using-the-azure-portal)
- [관리형 인스턴스 만들기](../managed-instance/instance-create-quickstart.md)
- [SQL 가상 머신 만들기](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)