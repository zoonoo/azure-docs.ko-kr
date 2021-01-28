---
title: 전용 클러스터 Azure Monitor 로그
description: 데이터 모니터링에 대해 1TB를 초과 하는 데이터를 수집 하는 고객은 공유 클러스터 대신 전용을 사용할 수 있습니다.
ms.subservice: logs
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 09/16/2020
ms.openlocfilehash: 1222108694ff7274e5d8fd063635b70a76ffc59c
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954752"
---
# <a name="azure-monitor-logs-dedicated-clusters"></a>전용 클러스터 Azure Monitor 로그

Azure Monitor Logs 전용 클러스터는 Azure Monitor 로그 고객을 위한 고급 기능을 제공 하는 배포 옵션입니다. 전용 클러스터를 사용 하는 고객은 이러한 클러스터에서 호스팅될 작업 영역을 선택할 수 있습니다.

전용 클러스터를 필요로 하는 기능은 다음과 같습니다.

- **[고객 관리 키](../platform/customer-managed-keys.md)** -고객이 제공 하 고 제어 하는 키를 사용 하 여 클러스터 데이터를 암호화 합니다.
- **[Lockbox](../platform/customer-managed-keys.md#customer-lockbox-preview)** -고객은 Microsoft 지원 엔지니어에 게 데이터에 대 한 액세스 요청을 제어할 수 있습니다.
- **[이중 암호화](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption)** 는 암호화 알고리즘 또는 키 중 하나가 손상 될 수 있는 시나리오를 방지 합니다. 이 경우 추가 암호화 계층은 계속 해 서 데이터를 보호 합니다.
- **[다중 작업 영역](../log-query/cross-workspace-query.md)** -고객이 프로덕션을 위해 둘 이상의 작업 영역을 사용 하는 경우 전용 클러스터를 사용 하는 것이 적합할 수 있습니다. 작업 영역 간 쿼리는 모든 작업 영역이 동일한 클러스터에 있는 경우 더 빨리 실행 됩니다. 또한 할당 된 용량 예약 계층이 모든 클러스터 수집을 고려 하므로 전용 클러스터를 사용 하는 것이 더 비용 효율적일 수 있습니다. 그 중 일부는 작고 용량 예약 할인이 적합 하지 않은 경우에도 마찬가지입니다.

전용 클러스터는 매일 1TB 이상의 데이터 수집 용량을 사용 하 여 커밋하는 고객이 필요 합니다. 전용 클러스터로의 마이그레이션은 간단 합니다. 데이터 손실이 나 서비스가 중단 되지 않습니다. 

## <a name="management"></a>관리 

전용 클러스터는 Azure Monitor 로그 클러스터를 나타내는 Azure 리소스를 통해 관리 됩니다. 모든 작업은 PowerShell 또는 REST API를 사용 하 여이 리소스에 대해 수행 됩니다.

클러스터를 만든 후에는 클러스터를 구성 하 고 작업 영역에 연결할 수 있습니다. 작업 영역을 클러스터에 연결 하면 작업 영역에 전송 된 새 데이터가 클러스터에 저장 됩니다. 클러스터와 동일한 지역에 있는 작업 영역만 클러스터에 연결할 수 있습니다. 일부 제한 사항이 있는 클러스터에서 작업 영역을 좋아요 해제할 수 있습니다. 이러한 제한 사항에 대 한 자세한 내용은이 문서에 포함 되어 있습니다. 

전용 클러스터에 대 한 데이터 수집은 Microsoft에서 관리 하는 키 또는 [고객이 관리](../platform/customer-managed-keys.md)하는 키를 사용 하 여 서비스 수준에서 한 번, 두 개의 서로 다른 암호화 알고리즘과 두 개의 다른 키를 사용 하 여 인프라 수준에서 두 번 암호화 됩니다. [이중 암호화](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) 는 암호화 알고리즘 또는 키 중 하나가 손상 될 수 있는 시나리오를 방지 합니다. 이 경우 추가 암호화 계층은 계속 해 서 데이터를 보호 합니다. 전용 클러스터를 사용 하 여 [Lockbox](../platform/customer-managed-keys.md#customer-lockbox-preview) 제어로 데이터를 보호할 수도 있습니다.

클러스터 수준의 모든 작업에는 `Microsoft.OperationalInsights/clusters/write` 클러스터에 대 한 작업 권한이 필요 합니다. 작업을 포함 하는 소유자 또는 참가자를 통하거나 `*/write` 동작을 포함 하는 Log Analytics 참여자 역할을 통해이 사용 권한을 부여할 수 있습니다 `Microsoft.OperationalInsights/*` . Log Analytics 권한에 대 한 자세한 내용은 [Azure Monitor에서 로그 데이터 및 작업 영역에 대 한 액세스 관리](../platform/manage-access.md)를 참조 하세요. 


## <a name="cluster-pricing-model"></a>클러스터 가격 책정 모델

Log Analytics 전용 클러스터는 최소 1000 g b/일의 용량 예약 가격 책정 모델을 사용 합니다. 예약 수준을 초과하는 모든 사용량에 대한 요금은 종량제 요율로 청구됩니다.  용량 예약 가격 정보는 [Azure Monitor 가격 책정 페이지]( https://azure.microsoft.com/pricing/details/monitor/)에서 확인할 수 있습니다.  

클러스터 용량 예약 수준은에서 매개 변수를 사용 하 여 Azure Resource Manager 프로그래밍 방식으로 구성 됩니다 `Capacity` `Sku` . `Capacity`는 GB 단위로 지정되며 일일 100GB의 증분 단위로 일일 1000GB 이상의 값을 가질 수 있습니다.

클러스터에서 사용 하기 위한 두 가지 모드의 요금 청구 방법이 있습니다. `billingType`클러스터를 구성할 때 매개 변수를 통해 지정할 수 있습니다. 

1. **Cluster**:이 경우 (기본값) 수집 데이터에 대 한 청구는 클러스터 수준에서 수행 됩니다. 클러스터에 연결 된 각 작업 영역의 수집 데이터 수량은 집계 되어 클러스터의 일별 청구 금액을 계산 합니다. 

2. **작업 영역**: 클러스터의 용량 예약 비용은 클러스터의 작업 영역에 대 한 특성을 사용 합니다 (각 작업 영역에 대 한 노드 별 [Azure Security Center](../../security-center/index.yml) 할당에 대 한 회계).

작업 영역이 레거시 노드당 가격 책정 계층을 사용 하는 경우 클러스터에 연결 되 면 클러스터의 용량 예약에 대 한 데이터 수집를 기준으로 요금이 청구 되 고 노드당 더 이상 청구 되지 않습니다. 노드당 데이터 할당 Azure Security Center 계속 적용 됩니다.

자세한 내용은 [여기]( https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#log-analytics-dedicated-clusters)에서 사용할 수 있는 Log Analytics 전용 클러스터에 대 한 청구입니다.

## <a name="asynchronous-operations-and-status-check"></a>비동기 작업 및 상태 검사

일부 구성 단계는 신속 하 게 완료할 수 없기 때문에 비동기적으로 실행 됩니다. 응답에 포함 된 상태는 오류 코드를 포함 하 여 ' InProgress ', ' 업데이트 중 ', ' 삭제 중 ', ' 성공 ' 또는 ' 실패 ' 중 하나일 수 있습니다. REST를 사용 하는 경우 응답은 초기에 Azure-AsyncOperation 속성을 사용 하 여 HTTP 상태 코드 202 (수락 됨) 및 헤더를 반환 합니다.

```JSON
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-08-01"
```

GET 요청을 Azure-AsyncOperation 헤더 값으로 보내 비동기 작업의 상태를 확인할 수 있습니다.

```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-08-01
Authorization: Bearer <token>
```

## <a name="creating-a-cluster"></a>클러스터 만들기

먼저 클러스터 리소스를 만들어 전용 클러스터 만들기를 시작 합니다.

다음 속성을 지정 해야 합니다.

- **ClusterName**: 관리 목적으로 사용 됩니다. 사용자는이 이름에 노출 되지 않습니다.
- **ResourceGroupName**: Azure 리소스의 경우 클러스터는 리소스 그룹에 속합니다. 일반적으로 클러스터는 조직의 많은 팀에서 공유 되기 때문에 중앙 IT 리소스 그룹을 사용 하는 것이 좋습니다. 디자인 고려 사항에 대 한 자세한 내용은 [Azure Monitor 로그 배포 디자인](../platform/design-logs-deployment.md) 을 검토 하세요.
- **위치**: 클러스터는 특정 Azure 지역에 있습니다. 이 영역에 있는 작업 영역만이 클러스터에 연결할 수 있습니다.
- **SkuCapacity**: *클러스터* 리소스를 만들 때 sku ( *용량 예약* 수준)를 지정 해야 합니다. *용량 예약* 수준 범위는 하루 1000 gb ~ 3000 gb 일 수 있습니다. 필요한 경우 나중에 100 단계에서 업데이트할 수 있습니다. 하루에 3000 GB 보다 높은 용량 예약 수준을 요구 하는 경우 microsoft에 문의 하세요 LAIngestionRate@microsoft.com . 클러스터 비용에 대 한 자세한 내용은 [Log Analytics 클러스터에 대 한 비용 관리](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters) 를 참조 하세요.

*클러스터* 리소스를 만든 후 *Sku*, * keyVaultProperties, *billingType* 등의 추가 속성을 편집할 수 있습니다. 아래 세부 정보를 참조 하세요.

지역별 구독 당 최대 2 개의 활성 클러스터가 있을 수 있습니다. 클러스터를 삭제 한 경우에도 14 일 동안 예약 됩니다. 지역별 구독 당 최대 4 개의 예약 된 클러스터 (활성 또는 최근 삭제 됨)를 사용할 수 있습니다.

> [!WARNING]
> 클러스터 만들기는 리소스 할당 및 프로 비전을 트리거합니다. 이 작업을 완료 하는 데 최대 한 시간이 걸릴 수 있습니다. 이를 비동기적으로 실행 하는 것이 좋습니다.

클러스터를 만드는 사용자 계정에는 표준 Azure 리소스 만들기 권한이 있어야 `Microsoft.Resources/deployments/*` 하 고, 해당 `Microsoft.OperationalInsights/clusters/write` 역할에이 특정 작업을 할당 하 여 클러스터 쓰기 권한이 있어야 `Microsoft.OperationalInsights/*` 합니다 `*/write` .

### <a name="create"></a>만들기 

**PowerShell**

```powershell
New-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name} -Location {region-name} -SkuCapacity {daily-ingestion-gigabyte} -AsJob

# Check when the job is done
Get-Job -Command "New-AzOperationalInsightsCluster*" | Format-List -Property *
```

**REST (영문)**

*호출* 
```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
Authorization: Bearer <token>
Content-type: application/json

{
  "identity": {
    "type": "systemAssigned"
    },
  "sku": {
    "name": "capacityReservation",
    "Capacity": 1000
    },
  "properties": {
    "billingType": "cluster",
    },
  "location": "<region-name>",
}
```

*응답*

202 (수락 됨) 및 헤더 여야 합니다.

### <a name="check-cluster-provisioning-status"></a>클러스터 프로 비전 상태 확인

Log Analytics 클러스터를 프로 비전 하는 작업은 완료 하는 데 시간이 걸립니다. 여러 가지 방법으로 프로 비전 상태를 확인할 수 있습니다.

- 리소스 그룹 이름을 사용 하 여 Get-AzOperationalInsightsCluster PowerShell 명령을 실행 하 고 ProvisioningState 속성을 확인 합니다. 값은 프로 비전 중에 *ProvisioningAccount* 완료 되 면 *성공* 합니다.
  ```powershell
  New-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} 
  ```

- 응답에서 Azure-AsyncOperation URL 값을 복사하고 비동기 작업 상태 검사를 수행합니다.

- GET 요청을 *클러스터* 리소스에 보내고 *provisioningState* 값을 확인합니다. 값은 프로 비전 중에 *ProvisioningAccount* 완료 되 면 *성공* 합니다.

   ```rst
   GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
   Authorization: Bearer <token>
   ```

   **응답**

   ```json
   {
     "identity": {
       "type": "SystemAssigned",
       "tenantId": "tenant-id",
       "principalId": "principal-id"
       },
     "sku": {
       "name": "capacityReservation",
       "capacity": 1000,
       "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
       },
     "properties": {
       "provisioningState": "ProvisioningAccount",
       "billingType": "cluster",
       "clusterId": "cluster-id"
       },
     "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
     "name": "cluster-name",
     "type": "Microsoft.OperationalInsights/clusters",
     "location": "region-name"
   }
   ```

*Principalid* GUID는 *클러스터* 리소스에 대 한 관리 id 서비스에 의해 생성 됩니다.

## <a name="link-a-workspace-to-cluster"></a>작업 영역을 클러스터에 연결

작업 영역이 전용 클러스터에 연결 된 경우 기존 데이터가 기존 클러스터에 유지 되는 동안 작업 영역에 수집 되는 새 데이터가 새 클러스터로 라우팅됩니다. 전용 클러스터가 CMK (고객 관리 키)를 사용 하 여 암호화 된 경우에는 키로 새 데이터만 암호화 됩니다. 시스템은 사용자와이 차이를 추상화 하 고, 시스템에서 백 엔드에 대 한 클러스터 간 쿼리를 수행 하는 동안 일반적인 방법으로 작업 영역을 쿼리 합니다.

클러스터는 최대 100 개의 작업 영역에 연결할 수 있습니다. 연결 된 작업 영역은 클러스터와 동일한 지역에 있습니다. 시스템 백 엔드를 보호 하 고 데이터 조각화를 방지 하려면 작업 영역을 한 달에 두 번 이상 클러스터에 연결할 수 없습니다.

연결 작업을 수행 하려면 작업 영역 및 *클러스터* 리소스 모두에 대 한 ' 쓰기 ' 권한이 있어야 합니다.

- 작업 영역에서: *OperationalInsights/작업 영역/쓰기*
- *클러스터* 리소스: *OperationalInsights/클러스터/쓰기*

청구 측면 외에도 연결 된 작업 영역에는 데이터 보존 길이와 같은 고유한 설정이 유지 됩니다.
작업 영역과 클러스터는 서로 다른 구독에 있을 수 있습니다. Azure Lighthouse를 사용 하 여 둘 다를 단일 테 넌 트에 매핑하면 작업 영역 및 클러스터가 다른 테 넌 트에 있을 수 있습니다.

클러스터 작업으로 작업 영역 연결은 Log Analytics 클러스터 프로 비전이 완료 된 후에만 수행할 수 있습니다.

> [!WARNING]
> 작업 영역을 클러스터에 연결 하려면 여러 백 엔드 구성 요소를 동기화 하 고 캐시 하이드레이션를 보장 해야 합니다. 이 작업을 완료 하는 데 최대 2 시간이 걸릴 수 있습니다. 이를 비동기적으로 실행 하는 것이 좋습니다.


**PowerShell**

다음 PowerShell 명령을 사용 하 여 클러스터에 연결 합니다.

```powershell
# Find cluster resource ID
$clusterResourceId = (Get-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name}).id

# Link the workspace to the cluster
Set-AzOperationalInsightsLinkedService -ResourceGroupName {resource-group-name} -WorkspaceName {workspace-name} -LinkedServiceName cluster -WriteAccessResourceId $clusterResourceId -AsJob

# Check when the job is done
Get-Job -Command "Set-AzOperationalInsightsLinkedService" | Format-List -Property *
```


**REST (영문)**

다음 REST 호출을 사용 하 여 클러스터에 연결 합니다.

*보내기*

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-08-01 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "WriteAccessResourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    }
}
```

*응답*

202 (수락 됨) 및 헤더.

### <a name="check-workspace-link-status"></a>작업 영역 링크 상태 확인
  
고객 관리 키를 사용 하는 경우 수집 데이터는 연결 작업 후에 관리 되는 키로 암호화 된 상태로 저장 됩니다 .이 작업은 완료 하는 데 최대 90 분까지 걸릴 수 있습니다. 

작업 영역 연결 상태는 다음 두 가지 방법으로 확인할 수 있습니다.

- 응답에서 Azure-AsyncOperation URL 값을 복사하고 비동기 작업 상태 검사를 수행합니다.

- 작업 영역에서 가져오기 작업을 수행 하 고, *기능의* 응답에 *clusterresourceid* 속성이 있는지 확인 합니다.

**CLI**

```azurecli
az monitor log-analytics cluster show --resource-group "resource-group-name" --name "cluster-name"
```

**PowerShell**

```powershell
Get-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
```

**REST (영문)**

*호출*

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>?api-version=2020-08-01
Authorization: Bearer <token>
```

*응답*

```json
{
  "properties": {
    "source": "Azure",
    "customerId": "workspace-name",
    "provisioningState": "Succeeded",
    "sku": {
      "name": "pricing-tier-name",
      "lastSkuUpdate": "Tue, 28 Jan 2020 12:26:30 GMT"
    },
    "retentionInDays": 31,
    "features": {
      "legacy": 0,
      "searchVersion": 1,
      "enableLogAccessUsingOnlyResourcePermissions": true,
      "clusterResourceId": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name"
    },
    "workspaceCapping": {
      "dailyQuotaGb": -1.0,
      "quotaNextResetTime": "Tue, 28 Jan 2020 14:00:00 GMT",
      "dataIngestionStatus": "RespectQuota"
    }
  },
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
  "name": "workspace-name",
  "type": "Microsoft.OperationalInsights/workspaces",
  "location": "region-name"
}
```

## <a name="change-cluster-properties"></a>클러스터 속성 변경

*클러스터* 리소스를 만들고 완전히 프로 비전 한 후에는 PowerShell 또는 REST API를 사용 하 여 클러스터 수준에서 추가 속성을 편집할 수 있습니다. 클러스터를 만드는 동안 사용할 수 있는 속성 외에도 클러스터를 프로 비전 한 후에는 추가 속성을 설정할 수 있습니다.

- **keyVaultProperties** -Azure Key Vault에서 키를 업데이트 합니다. [키 식별자 세부 정보를 사용 하 여 클러스터 업데이트를](../platform/customer-managed-keys.md#update-cluster-with-key-identifier-details)참조 하세요. *KeyVaultUri*, *KeyName*, *keyversion* 매개 변수를 포함 합니다. 
- **billingType** - *billingType* 속성은 *클러스터* 리소스 및 해당 데이터에 대 한 청구 특성을 결정 합니다.
  - **클러스터** (기본값)-클러스터의 용량 예약 비용은 *클러스터* 리소스의 특성을 갖습니다.
  - **작업 영역** -클러스터의 용량 예약 비용은 클러스터의 작업 영역에 대 한 특성을 기준으로 하며, 해당 요일의 총 수집 데이터가 용량 예약에 포함 되는 경우 *클러스터* 리소스의 사용량에 대 한 비용이 청구 됩니다. 클러스터 가격 책정 모델에 대해 자세히 알아보려면 [전용 클러스터 Log Analytics](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters) 를 참조 하세요. 

> [!NOTE]
> *BillingType* 속성은 PowerShell에서 지원 되지 않습니다.

### <a name="get-all-clusters-in-resource-group"></a>리소스 그룹의 모든 클러스터 가져오기
  
**CLI**

```azurecli
az monitor log-analytics cluster list --resource-group "resource-group-name"
```

**PowerShell**

```powershell
Get-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name"
```

**REST (영문)**

*호출*

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

*응답*
  
  ```json
  {
    "value": [
      {
        "identity": {
          "type": "SystemAssigned",
          "tenantId": "tenant-id",
          "principalId": "principal-Id"
        },
        "sku": {
          "name": "capacityReservation",
          "capacity": 1000,
          "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
          },
        "properties": {
           "keyVaultProperties": {
              "keyVaultUri": "https://key-vault-name.vault.azure.net",
              "keyName": "key-name",
              "keyVersion": "current-version"
              },
          "provisioningState": "Succeeded",
          "billingType": "cluster",
          "clusterId": "cluster-id"
        },
        "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
        "name": "cluster-name",
        "type": "Microsoft.OperationalInsights/clusters",
        "location": "region-name"
      }
    ]
  }
  ```

### <a name="get-all-clusters-in-subscription"></a>구독의 모든 클러스터 가져오기

**CLI**

```azurecli
az monitor log-analytics cluster list
```

**PowerShell**

```powershell
Get-AzOperationalInsightsCluster
```

**REST (영문)**

*호출*

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
Authorization: Bearer <token>
```
    
*응답*
    
' 리소스 그룹의 클러스터 '의 경우와 동일 하지만 구독 범위에 있습니다.



### <a name="update-capacity-reservation-in-cluster"></a>클러스터에서 용량 예약 업데이트

연결 된 작업 영역에 대 한 데이터 볼륨이 시간이 지남에 따라 변경 되 고 용량 예약 수준을 적절 하 게 업데이트 하려는 경우. 용량은 GB 단위로 지정 되며 100 g b/일 단위로 1000 g b/일 이상의 값을 가질 수 있습니다. 전체 REST 요청 본문을 제공할 필요는 없지만 sku는 포함 해야 합니다.

**CLI**

```azurecli
az monitor log-analytics cluster update --name "cluster-name" --resource-group "resource-group-name" --sku-capacity 1000
```

**PowerShell**

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -SkuCapacity 1000
```

**REST (영문)**

*호출*

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "sku": {
      "name": "capacityReservation",
      "Capacity": 2000
    }
  }
  ```

### <a name="update-billingtype-in-cluster"></a>클러스터에서 billingType 업데이트

*BillingType* 속성은 클러스터 및 해당 데이터에 대 한 청구 특성을 결정 합니다.
- *cluster*(기본값) - 클러스터 리소스를 호스팅하는 구독을 기반으로 하는 청구입니다.
- *workspaces* - 작업 영역을 비례적으로 호스팅하는 구독을 기반으로 하는 청구입니다.

**REST (영문)**

*호출*

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "properties": {
      "billingType": "cluster",
      }  
  }
  ```

### <a name="unlink-a-workspace-from-cluster"></a>클러스터에서 작업 영역 연결 끊기

클러스터에서 작업 영역의 연결을 끊을 수 있습니다. 클러스터에서 작업 영역의 연결을 해제 한 후에는이 작업 영역에 연결 된 새 데이터가 전용 클러스터로 전송 되지 않습니다. 또한 작업 영역 요금은 더 이상 클러스터를 통해 수행 되지 않습니다. 연결 되지 않은 작업 영역의 이전 데이터가 클러스터에 남아 있을 수 있습니다. CMK (고객 관리 키)를 사용 하 여이 데이터를 암호화 하는 경우 Key Vault 비밀이 유지 됩니다. 시스템은 Log Analytics 사용자의 이러한 변경을 추상화 합니다. 사용자는 평소와 같이 작업 영역을 쿼리할 수 있습니다. 시스템은 사용자에 게 표시 하지 않고 필요에 따라 백 엔드에 대 한 클러스터 간 쿼리를 수행 합니다.  

> [!WARNING] 
> 한 달 내에 특정 작업 영역에 대 한 연결 작업은 두 개로 제한 됩니다. 적절 한 연결 끊기 작업을 고려 하 고 계획 하는 데 시간이 걸립니다.

**CLI**

```azurecli
az monitor log-analytics workspace linked-service delete --resource-group "resource-group-name" --workspace-name "MyWorkspace" --name cluster
```

**PowerShell**

다음 PowerShell 명령을 사용 하 여 클러스터에서 작업 영역의 연결을 해제 합니다.

```powershell
# Unlink a workspace from cluster
Remove-AzOperationalInsightsLinkedService -ResourceGroupName {resource-group-name} -WorkspaceName {workspace-name} -LinkedServiceName cluster
```

### <a name="delete-cluster"></a>클러스터 삭제

전용 클러스터 리소스를 삭제할 수 있습니다. 클러스터에서 모든 작업 영역을 삭제 하기 전에 연결을 해제 해야 합니다. 이 작업을 수행하려면 *클러스터* 리소스에 대한 '쓰기' 권한이 필요합니다. 

클러스터 리소스가 삭제 되 면 물리적 클러스터는 제거 및 삭제 프로세스를 시작 합니다. 클러스터를 삭제 하면 클러스터에 저장 된 모든 데이터가 삭제 됩니다. 이전에 클러스터에 연결 된 작업 영역에서 데이터를 만들 수 있습니다.

지난 14일 동안 삭제된 *클러스터* 리소스는 일시 삭제 상태에 있으므로 해당 데이터를 사용하여 복구할 수 있습니다. 클러스터 리소스 삭제 *를 사용 하 여 모든* 작업 영역을 *클러스터* 리소스에서 분리 했으므로 복구 후 작업 영역을 다시 연결 해야 합니다. 사용자가 Microsoft 채널에 연결 하거나 복구 요청을 지원 하기 위해 복구 작업을 수행할 수 없습니다.

삭제 후 14 일 이내에 클러스터 리소스 이름은 예약 되어 있으며 다른 리소스에서 사용할 수 없습니다.

> [!WARNING] 
> 구독 당 클러스터 수는 세 개로 제한 됩니다. 활성 및 일시 삭제 된 클러스터는 모두이의 일부로 계산 됩니다. 고객은 클러스터를 만들고 삭제 하는 되풀이 프로시저를 만들지 않아야 합니다. Log Analytics 백 엔드 시스템에 상당한 영향을 미칩니다.

**PowerShell**

다음 PowerShell 명령을 사용 하 여 클러스터를 삭제 합니다.

  ```powershell
  Remove-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name"
  ```

**REST (영문)**

클러스터를 삭제 하려면 다음 REST 호출을 사용 합니다.

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  **응답**

  200 정상

## <a name="limits-and-constraints"></a>제한 및 제약 조건

- 하위 지역 및 구독에 대 한 최대 활성 클러스터 수는 2입니다.

- 지역 및 구독 당 예약 된 클러스터의 최대 수 (활성 또는 최근 삭제)는 4입니다. 

- 클러스터에 연결 된 작업 영역의 최대값은 1000입니다.

- 작업 영역을 클러스터에 연결 하 고 연결을 끊을 수 있습니다. 특정 작업 영역에 대 한 작업 영역 링크 작업 수는 30 일 동안 2 개로 제한 됩니다.

- 다른 리소스 그룹 또는 구독으로의 클러스터 이동은 현재 지원 되지 않습니다.

- 현재 중국에서는 Lockbox를 사용할 수 없습니다. 

- [이중 암호화](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) 는 지원 되는 지역에서 10 월 2020 일에 만든 클러스터에 대해 자동으로 구성 됩니다. 클러스터에 GET 요청을 보내고 `isDoubleEncryptionEnabled` `true` 이중 암호화를 사용 하는 클러스터에 대 한 값을 관찰 하 여 클러스터가 이중 암호화에 대해 구성 되었는지 확인할 수 있습니다. 
  - 클러스터를 만들 때 "<영역 이름> 클러스터에 대 한 이중 암호화를 지원 하지 않습니다." 라는 오류 메시지가 표시 되 면 `"properties": {"isDoubleEncryptionEnabled": false}` REST 요청 본문에를 추가 하 여 이중 암호화 없이 클러스터를 만들 수 있습니다.
  - 클러스터를 만든 후에는 이중 암호화 설정을 변경할 수 없습니다.

## <a name="troubleshooting"></a>문제 해결

- 클러스터를 만들 때 충돌 오류가 발생 하는 경우 – 지난 14 일간 클러스터를 삭제 하 고 일시 삭제 상태에 있을 수 있습니다. 클러스터 이름은 일시 삭제 기간 동안 예약 된 상태로 유지 되며 해당 이름을 사용 하 여 새 클러스터를 만들 수 없습니다. 이 이름은 클러스터가 영구적으로 삭제 될 때 일시 삭제 기간이 지나면 릴리스됩니다.

- 클러스터를 프로 비전 하거나 상태를 업데이트 하는 동안 클러스터를 업데이트 하는 경우 업데이트는 실패 합니다.

- 일부 작업은 길고 완료 하는 데 시간이 걸릴 수 있습니다 (클러스터 만들기, 클러스터 키 업데이트 및 클러스터 삭제). 다음 두 가지 방법으로 작업 상태를 확인할 수 있습니다.
  - REST를 사용 하는 경우 응답에서 Azure-AsyncOperation URL 값을 복사 하 고 [비동기 작업 상태 검사](#asynchronous-operations-and-status-check)를 따릅니다.
  - 클러스터 또는 작업 영역에 GET 요청을 보내고 응답을 관찰 합니다. 예를 들어 연결 되지 않은 작업 영역에는 *기능* 아래에 *clusterresourceid* 가 없습니다.

- 다른 클러스터에 연결 된 경우에는 클러스터에 대 한 작업 영역 링크가 실패 합니다.

- 오류 메시지
  
  클러스터 만들기:
  -  400--클러스터 이름이 잘못 되었습니다. 클러스터 이름에는 문자 a-z, a-z, 0-9 및 3-63의 길이를 사용할 수 있습니다.
  -  400--요청 본문이 null 이거나 형식이 잘못 되었습니다.
  -  400--SKU 이름이 잘못 되었습니다. SKU 이름을 capacityReservation로 설정 합니다.
  -  400--용량이 제공 되었지만 SKU가 capacityReservation 되지 않습니다. SKU 이름을 capacityReservation로 설정 합니다.
  -  400--SKU의 용량이 누락 되었습니다. 100 (GB) 단계에서 용량 값을 1000 이상으로 설정 합니다.
  -  400--SKU의 용량이 범위에 없습니다. 최소 1000이 고 작업 영역의 ' 사용량 및 예상 비용 ' 아래에서 사용할 수 있는 최대 허용 용량까지 허용 되어야 합니다.
  -  400--용량이 30 일 동안 잠겨 있습니다. 업데이트 후 30 일 후에는 용량을 줄일 수 있습니다.
  -  400--SKU가 설정 되지 않았습니다. 100 (GB)의 단계에서 SKU 이름을 capacityReservation and Capacity value를 1000 이상으로 설정 합니다.
  -  400--id가 null 이거나 비어 있습니다. SystemAssigned 된 유형으로 Id를 설정 합니다.
  -  400--KeyVaultProperties은 만들 때 설정 됩니다. 클러스터를 만든 후 KeyVaultProperties를 업데이트 합니다.
  -  400-지금은 작업을 실행할 수 없습니다. 비동기 작업이 succeeded 이외의 상태에 있습니다. 업데이트 작업을 수행 하기 전에 클러스터에서 해당 작업을 완료 해야 합니다.

  클러스터 업데이트
  -  400--클러스터가 삭제 중 상태입니다. 비동기 작업이 진행 중입니다. 업데이트 작업을 수행 하기 전에 클러스터에서 해당 작업을 완료 해야 합니다.
  -  400--KeyVaultProperties가 비어 있지 않지만 형식이 잘못 되었습니다. [키 식별자 업데이트](../platform/customer-managed-keys.md#update-cluster-with-key-identifier-details)를 참조 하세요.
  -  400--Key Vault의 키 유효성을 검사 하지 못했습니다. 권한이 부족 하거나 키가 없는 경우에 발생할 수 있습니다. Key Vault에서 [키 및 액세스 정책을 설정](../platform/customer-managed-keys.md#grant-key-vault-permissions) 했는지 확인 합니다.
  -  400--키를 복구할 수 없습니다. 일시 삭제 및 제거 보호로 Key Vault 설정 해야 합니다. [Key Vault 설명서](../../key-vault/general/soft-delete-overview.md) 를 참조 하세요.
  -  400-지금은 작업을 실행할 수 없습니다. 비동기 작업이 완료 될 때까지 기다린 후 다시 시도 하십시오.
  -  400--클러스터가 삭제 중 상태입니다. 비동기 작업이 완료 될 때까지 기다린 후 다시 시도 하십시오.

  클러스터 가져오기:
    -  404--클러스터가 없습니다. 클러스터가 삭제 되었을 수 있습니다. 해당 이름을 사용 하 여 클러스터를 만들려고 하 고 충돌 하는 경우 클러스터는 14 일 동안 일시 삭제 됩니다. 지원 서비스에 문의 하 여 복구 하거나 다른 이름을 사용 하 여 새 클러스터를 만들 수 있습니다. 

  클러스터 삭제
    -  409--프로 비전 상태에 있는 동안에는 클러스터를 삭제할 수 없습니다. 비동기 작업이 완료 될 때까지 기다린 후 다시 시도 하십시오.

  작업 영역 링크:
  -  404--작업 영역을 찾을 수 없습니다. 지정한 작업 영역이 존재 하지 않거나 삭제 되었습니다.
  -  409--프로세스의 작업 영역 연결 또는 연결 해제 작업입니다.
  -  400--클러스터를 찾을 수 없습니다. 지정한 클러스터가 없거나 삭제 되었습니다. 해당 이름을 사용 하 여 클러스터를 만들려고 하 고 충돌 하는 경우 클러스터는 14 일 동안 일시 삭제 됩니다. 지원 서비스에 문의 하 여 복구할 수 있습니다.

  작업 영역 연결 해제:
  -  404--작업 영역을 찾을 수 없습니다. 지정한 작업 영역이 존재 하지 않거나 삭제 되었습니다.
  -  409--프로세스의 작업 영역 연결 또는 연결 해제 작업입니다.

## <a name="next-steps"></a>다음 단계

- [Log Analytics 전용 클러스터 청구](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters) 에 대해 알아보기
- [Log Analytics 작업 영역](../platform/design-logs-deployment.md) 에 대 한 적절 한 디자인 알아보기
