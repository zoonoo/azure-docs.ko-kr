---
title: 서버 트러스트 그룹
titleSuffix: Azure SQL Managed Instance
description: 서버 신뢰 그룹 및 Azure SQL 관리 되는 인스턴스 간의 트러스트를 관리 하는 방법에 대해 알아봅니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: sasapopo
ms.author: sasapopo
ms.reviewer: sstein, bonova
ms.date: 10/08/2020
ms.openlocfilehash: f9d5528746a85668677ab122d98e954bd39cd163
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92790732"
---
# <a name="use-server-trust-groups-to-set-up-and-manage-trust-between-sql-managed-instances"></a>서버 신뢰 그룹을 사용 하 여 SQL 관리 되는 인스턴스 간 트러스트 설정 및 관리
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

서버 신뢰 그룹은 Azure SQL 관리 되는 인스턴스 간의 신뢰를 관리 하는 데 사용 되는 개념입니다. 그룹을 만들면 해당 구성원 간에 인증서 기반 트러스트가 설정 됩니다. 이 트러스트는 여러 인스턴스 간 시나리오에 사용할 수 있습니다. 그룹에서 서버를 제거 하거나 그룹을 삭제 하면 서버 간의 트러스트가 제거 됩니다. 서버 신뢰 그룹을 만들거나 삭제 하려면 사용자에 게 Managed Instance에 대 한 쓰기 권한이 있어야 합니다.
[서버 트러스트 그룹](/azure/templates/microsoft.sql/allversions) 은 Azure Portal에서 **SQL 트러스트 그룹** 으로 레이블이 지정 된 Azure Resource Manager 개체입니다.

> [!NOTE]
> 서버 신뢰 그룹은 Azure SQL 관리 되는 인스턴스 간의 분산 트랜잭션 공개 미리 보기에서 도입 되었으며 현재이 문서의 뒷부분에서 설명 하는 몇 가지 제한 사항이 있습니다.

## <a name="server-trust-group-setup"></a>서버 신뢰 그룹 설정

다음 섹션에서는 서버 신뢰 그룹의 설정에 대해 설명 합니다.

1. [Azure 포털](https://portal.azure.com/)로 이동합니다.

2. 새로 만든 서버 신뢰 그룹에 추가 하려는 Azure SQL Managed Instance로 이동 합니다.

3. **보안** 설정에서 **SQL 트러스트 그룹** 탭을 선택 합니다.

   :::image type="content" source="./media/server-trust-group-overview/security-sql-trust-groups.png" alt-text="서버 트러스트 그룹":::

4. 서버 트러스트 그룹 구성 페이지에서 **새 그룹** 아이콘을 선택 합니다.

   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-create-new-group.png" alt-text="서버 트러스트 그룹":::

5. **SQL 트러스트 그룹** 만들기 블레이드에서 **그룹 이름을** 설정 합니다. 그룹 구성원이 있는 모든 지역에서 고유 해야 합니다. **신뢰 범위** 는 서버 신뢰 그룹에서 사용 하도록 설정 된 인스턴스 간 시나리오의 유형을 정의 합니다. 미리 보기에서 적용 가능한 신뢰 범위는 **분산 트랜잭션** 이므로 미리 선택 되어 있으며 변경할 수 없습니다. 모든 **그룹 구성원** 은 동일한 **구독** 에 속해 있어야 하지만 다른 리소스 그룹 아래에 있을 수 있습니다. **리소스 그룹** 및 **SQL Server/인스턴스** 를 선택 하 여 그룹의 구성원이 될 Azure SQL Managed Instance를 선택 합니다.

   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-create-blade.png" alt-text="서버 트러스트 그룹":::

6. 모든 필수 필드를 채운 후 **저장** 을 클릭 합니다.

## <a name="server-trust-group-maintenance-and-deletion"></a>서버 트러스트 그룹 유지 관리 및 삭제

서버 신뢰 그룹을 편집할 수 없습니다. 그룹에서 Managed Instance를 제거 하려면 그룹을 삭제 하 고 새 그룹을 만들어야 합니다.

다음 섹션에서는 서버 트러스트 그룹 삭제 프로세스에 대해 설명 합니다. 
1. Azure Portal로 이동합니다.
2. 신뢰 그룹에 속하는 Managed Instance로 이동 합니다.
3. **보안** 설정에서 **SQL 트러스트 그룹** 탭을 선택 합니다.
4. 삭제할 신뢰 그룹을 선택 합니다.
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-select.png" alt-text="서버 트러스트 그룹":::
5. **그룹 삭제** 를 클릭 합니다.
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-delete.png" alt-text="서버 트러스트 그룹":::
6. 서버 트러스트 그룹 이름을 입력 하 여 삭제를 확인 하 고 **삭제** 를 클릭 합니다.
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-delete-confirm.png" alt-text="서버 트러스트 그룹":::

> [!NOTE]
> 서버 신뢰 그룹을 삭제 하면 두 관리 되는 인스턴스 간의 트러스트가 즉시 제거 되지 않을 수도 있습니다. 관리 되는 인스턴스의 [장애 조치 (failover)](/powershell/module/az.sql/Invoke-AzSqlInstanceFailover) 를 호출 하 여 트러스트 제거를 적용할 수 있습니다. 이에서 최신 업데이트에 대 한 [알려진 문제](../database/doc-changes-updates-release-notes.md?tabs=managed-instance#known-issues) 를 확인 합니다.

## <a name="limitations"></a>제한 사항

공개 미리 보기 중에는 서버 신뢰 그룹에 다음과 같은 제한 사항이 적용 됩니다.
 * 서버 신뢰 그룹의 이름은 해당 구성원이 있는 모든 지역에서 고유 해야 합니다.
 * 그룹은 Azure SQL 관리 되는 인스턴스만 포함할 수 있으며 동일한 Azure 구독에 있어야 합니다.
 * 미리 보기에서는 그룹에 관리 되는 인스턴스가 정확히 두 개 있을 수 있습니다. 관리 되는 인스턴스를 두 개 이상 넘어 분산 트랜잭션을 실행 하려면 관리 되는 인스턴스의 각 쌍에 대해 서버 신뢰 그룹을 만들어야 합니다.
 * 분산 트랜잭션은 서버 신뢰 그룹에 적용 되는 유일한 범위입니다.
 * 서버 신뢰 그룹은 Azure Portal 에서만 관리할 수 있습니다. PowerShell 및 CLI 지원은 나중에 제공 됩니다.
 * Azure Portal에서 서버 신뢰 그룹을 편집할 수 없습니다. 만들거나 삭제할 수만 있습니다.
 * 분산 트랜잭션의 추가 제한 사항은 시나리오와 관련이 있을 수 있습니다. 가장 주목할 만한 것은 VNET 또는 VNET 피어 링을 통해 개인 끝점에 대 한 관리 되는 인스턴스 간에 연결 해야 한다는 것입니다. [Managed Instance에 대 한 현재 분산 트랜잭션 제한 사항을](../database/elastic-transactions-overview.md#limitations)알고 있는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

* Azure SQL Managed Instance의 분산 트랜잭션에 대 한 자세한 내용은 [분산 트랜잭션](../database/elastic-transactions-overview.md)을 참조 하세요.
* 릴리스 업데이트 및 알려진 문제 상태는 [Managed Instance 릴리스 정보](../database/doc-changes-updates-release-notes.md)를 참조 하세요.
* 기능 요청이 있는 경우 [Managed Instance 포럼](https://feedback.azure.com/forums/915676-sql-managed-instance)에 추가 합니다.