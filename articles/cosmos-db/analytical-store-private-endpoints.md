---
title: Azure Cosmos DB 분석 저장소에 대 한 개인 끝점을 구성 합니다.
description: Azure Cosmos DB 분석 저장소에 대 한 관리 되는 개인 끝점을 설정 하 여 네트워크 액세스를 제한 하는 방법을 알아봅니다.
author: AnithaAdusumilli
ms.service: cosmos-db
ms.topic: how-to
ms.date: 03/02/2021
ms.author: anithaa
ms.openlocfilehash: 9b07af72983931e0e1cab9e7d5093fd845b363bc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101692201"
---
# <a name="configure-private-endpoints-for-azure-cosmos-db-analytical-store"></a>Azure Cosmos DB 분석 저장소에 대한 프라이빗 엔드포인트 구성
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

이 문서에서는 Azure Cosmos DB 분석 저장소에 대 한 관리 되는 개인 끝점을 설정 하는 방법을 설명 합니다. 트랜잭션 저장소를 사용 하는 경우 [트랜잭션 저장소에 대 한 개인 끝점](how-to-configure-private-endpoints.md) 문서를 참조 하세요. 관리 되는 개인 끝점을 사용 하 여 Azure Cosmos DB 분석 저장소에 대 한 네트워크 액세스를 Azure Synapse 관리 가상 네트워크로 제한할 수 있습니다. 관리 되는 개인 끝점은 분석 저장소에 대 한 개인 링크를 설정 합니다.

## <a name="enable-private-endpoint-for-the-analytical-store"></a>분석 저장소에 대 한 개인 끝점 사용

### <a name="set-up-an-azure-synapse-analytics-workspace-with-a-managed-virtual-network"></a>관리 되는 가상 네트워크를 사용 하 여 Azure Synapse Analytics 작업 영역 설정

[데이터 exfiltration을 사용 하 여 Azure Synapse Analytics에서 작업 영역을 만듭니다.](../synapse-analytics/security/how-to-create-a-workspace-with-data-exfiltration-protection.md) [데이터 exfiltration 보호](../synapse-analytics/security/workspace-data-exfiltration-protection.md)를 사용 하면 악의적인 사용자가 Azure 리소스에서 조직의 범위 밖의 위치로 데이터를 복사 하거나 전송할 수 없도록 할 수 있습니다.

Azure Synapse Analytics 작업 영역에 대해 데이터-exfiltration 보호가 설정 된 경우 다음 액세스 제한이 적용 됩니다.

* Azure Synapse Analytics에 Azure Spark를 사용 하는 경우 Azure Cosmos DB 분석 저장소에 대해 승인 된 관리 되는 전용 끝점에 대 한 액세스만 허용 됩니다.

* Synapse 서버를 사용 하지 않는 SQL 풀을 사용 하는 경우 Azure Synapse 링크를 사용 하 여 모든 Azure Cosmos DB 계정을 쿼리할 수 있습니다. 그러나 [select (CETAS)로 외부 테이블을 만드는](../synapse-analytics/sql/develop-tables-cetas.md) 쓰기 요청은 작업 영역 가상 네트워크에서 승인 된 관리 전용 끝점에만 허용 됩니다.

> [!NOTE]
> 작업 영역을 만든 후에는 관리 되는 가상 네트워크 및 데이터-exfiltration 구성을 변경할 수 없습니다.

### <a name="add-a-managed-private-endpoint-for-azure-cosmos-db-analytical-store"></a>Azure Cosmos DB 분석 저장소에 대 한 관리 되는 개인 끝점 추가

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. Azure Portal에서 Synapse Analytics 작업 영역으로 이동 하 고 **개요** 창을 엽니다.

1. **시작** 창으로 이동 하 여 Synapse studio를 시작 하 고 **open Synapse studio** 에서 **열기** 를 선택 합니다.

1. Synapse Studio에서 **관리** 탭을 엽니다.

1. **관리 되는 개인 끝점** 으로 이동 하 고 **새로 만들기** 를 선택 합니다.

   :::image type="content" source="./media/analytical-store-private-endpoints/create-new-private-endpoint.png" alt-text="분석 저장소에 대 한 새 개인 끝점을 만듭니다." border="true":::

1. **Azure Cosmos DB (SQL API)** 계정 유형을 선택 하 > **계속** 합니다.

   :::image type="content" source="./media/analytical-store-private-endpoints/select-private-endpoint.png" alt-text="Azure Cosmos DB SQL API를 선택 하 여 개인 끝점을 만듭니다." border="true":::

1. 다음 세부 정보를 사용 하 여 **관리 되는 새 개인 끝점** 폼을 채웁니다.

   * **이름** -관리 되는 개인 끝점의 이름입니다. 이 이름은 만든 후에는 업데이트할 수 없습니다.
   * **설명** -개인 끝점을 식별 하는 친숙 한 설명을 제공 합니다.
   * **Azure 구독** -azure 구독의 사용 가능한 계정 목록에서 Azure Cosmos DB 계정을 선택 합니다.
   * **계정 이름 Azure Cosmos DB** -SQL 또는 MongoDB 유형의 기존 Azure Cosmos DB 계정을 선택 합니다.
   * **대상 하위 리소스** - **분석: Azure Cosmos DB** 분석 저장소에 대 한 개인 끝점을 추가 하려면 다음 옵션 중 하나를 선택 합니다.
     **Sql** (또는 **MONGODB**): OLTP 또는 트랜잭션 계정 끝점을 추가 하려는 경우

   > [!NOTE]
   > Azure Synapse Analytics 작업 영역에서 동일한 Azure Cosmos DB 계정에 트랜잭션 저장소와 분석 저장소 개인 끝점을 모두 추가할 수 있습니다. 분석 쿼리를 실행 하려는 경우에만 분석 전용 끝점을 매핑해야 할 수 있습니다.

   :::image type="content" source="./media/analytical-store-private-endpoints/choose-analytical-private-endpoint.png" alt-text="대상 하위 리소스에 대 한 분석을 선택 합니다." border="true":::

1. 을 만든 후 개인 끝점 이름으로 이동 하 고 **Azure Portal에서 승인 관리** 를 선택 합니다.

1. Azure Cosmos DB 계정으로 이동 하 여 개인 끝점을 선택 하 고 **승인** 을 선택 합니다.

1. Synapse Analytics 작업 영역으로 다시 이동 하 고 **관리 되는 개인 끝점** 창에서 **새로 고침** 을 클릭 합니다. 개인 끝점이 **승인** 된 상태 인지 확인 합니다.

   :::image type="content" source="./media/analytical-store-private-endpoints/approved-private-endpoint.png" alt-text="개인 끝점이 승인 되었는지 확인 합니다." border="true":::

## <a name="use-apache-spark-for-azure-synapse-analytics"></a>Azure Synapse Analytics에 Apache Spark 사용

데이터 exfiltration 보호가 설정 된 Azure Synapse 작업 영역을 만든 경우 Synapse Spark에서 Azure Cosmos DB 계정으로의 아웃 바운드 액세스는 기본적으로 차단 됩니다. 또한 Azure Cosmos DB에 기존 개인 끝점이 이미 있는 경우 Synapse Spark는 해당 끝점에 대 한 액세스를 차단 합니다.

Azure Cosmos DB 데이터에 대 한 액세스를 허용 하려면:

* Azure Synapse 링크를 사용 하 여 Azure Cosmos DB 데이터를 쿼리하려면 Azure Cosmos DB 계정에 대 한 관리 되는 **분석** 개인 끝점을 추가 합니다.

* 트랜잭션 저장소에 대해 일괄 쓰기/읽기 및/또는 스트리밍 쓰기/읽기를 사용 하는 경우 Azure Cosmos DB 계정에 대 한 관리 되는 *SQL* 또는 *MongoDB* 개인 끝점을 추가 합니다. 또한 다음 코드 조각과 같이 *Connectionmode* 를 *Gateway* 로 설정 해야 합니다.

  ```python
  # Write a Spark DataFrame into an Azure Cosmos DB container
  # To select a preferred lis of regions in a multi-region account, add .option("spark.cosmos.preferredRegions", "<Region1>, <Region2>")
  
  YOURDATAFRAME.write\
    .format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "<your-Cosmos-DB-linked-service-name>")\
    .option("spark.cosmos.container","<your-Cosmos-DB-container-name>")\
    .option("spark.cosmos.write.upsertEnabled", "true")\
    .option("spark.cosmos.connection.mode", "Gateway")\
    .mode('append')\
    .save()
  
  ```

## <a name="using-synapse-serverless-sql-pools"></a>Synapse 서버를 사용 하지 않는 SQL 풀 사용

Synapse 서버를 사용 하지 않는 SQL 풀은 관리 되는 가상 네트워크에 배포 되지 않은 다중 테 넌 트 기능을 사용 합니다. Azure Cosmos DB 계정에 기존 개인 끝점이 있으면 서버를 사용 하지 않는 SQL 풀은 Azure Cosmos DB 계정의 네트워크 격리 검사로 인해 계정에 액세스 하지 못하도록 차단 됩니다 Synapse.

Synapse 작업 영역에서이 계정에 대 한 네트워크 격리를 구성 하려면:

1. Synapse 작업 영역에서 `NetworkAclBypassResourceId` 계정에 대 한 설정을 지정 하 여 Azure Cosmos DB 계정에 액세스할 수 있도록 허용 합니다.

   **PowerShell 사용**

   ```powershell-interactive
   Update-AzCosmosDBAccount -Name MyCosmosDBDatabaseAccount -ResourceGroupName MyResourceGroup -NetworkAclBypass AzureServices -NetworkAclBypassResourceId "/subscriptions/subId/resourceGroups/rgName/providers/Microsoft.Synapse/workspaces/wsName"
   ```

   **Azure CLI 사용**

   ```azurecli-interactive
   az cosmosdb update --name MyCosmosDBDatabaseAccount --resource-group MyResourceGroup --network-acl-bypass AzureServices --network-acl-bypass-resource-ids "/subscriptions/subId/resourceGroups/rgName/providers/Microsoft.Synapse/workspaces/wsName"
   ```

   > 두고 Azure Cosmos DB 계정 및 Azure Synapse Analytics 작업 영역은 동일한 AD (Azure Active Directory) 테 넌 트 아래에 있어야 합니다.

2. 이제 Azure Synapse 링크를 통해 T-sql 쿼리를 사용 하 여 서버를 사용 하지 않는 SQL 풀에서 계정에 액세스할 수 있습니다. 그러나 분석 저장소에서 데이터에 대 한 네트워크 격리를 보장 하려면이 계정에 대 한 **분석** 관리 개인 끝점을 추가 해야 합니다. 그렇지 않으면 분석 저장소의 데이터는 공용 액세스에서 차단 되지 않습니다.

> [!IMPORTANT]
> Azure Synapse 링크를 사용 하 고 분석 저장소에서 데이터에 대 한 네트워크 격리가 필요한 경우 **분석** 관리 개인 끝점을 사용 하 여 Azure Cosmos DB 계정을 Synapse 작업 영역에 매핑해야 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Synapse Spark를 사용 하 여 분석 저장소 쿼리](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json) 시작
* [Azure Synapse 서버 리스 SQL 풀을 사용 하 여 분석 저장소 쿼리](../synapse-analytics/sql/query-cosmos-db-analytical-store.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json) 시작
