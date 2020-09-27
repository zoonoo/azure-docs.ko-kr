---
title: 전용 클러스터 Azure Monitor 로그
description: 데이터 모니터링에 대해 1TB를 초과 하는 데이터를 수집 하는 고객은 공유 클러스터 대신 전용을 사용할 수 있습니다.
ms.subservice: logs
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 09/16/2020
ms.openlocfilehash: e5ab3800e2d20bec34f321e0992240be8624404c
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2020
ms.locfileid: "91400879"
---
# <a name="azure-monitor-logs-dedicated-clusters"></a>전용 클러스터 Azure Monitor 로그

Azure Monitor Logs 전용 클러스터는 대용량 고객에 게 더 나은 서비스를 제공 하는 배포 옵션입니다. 하루에 최대 4 TB 이상의 데이터를 수집 하는 고객은 전용 클러스터를 사용 합니다. 전용 클러스터를 사용 하는 고객은 이러한 클러스터에서 호스팅될 작업 영역을 선택할 수 있습니다.

대용량 클러스터를 지 원하는 것 외에 전용 클러스터를 사용 하는 경우 다음과 같은 이점이 있습니다.

- **요금 제한** -고객은 전용 클러스터에 대해서만 수집 속도가 더 높아질 수 있습니다.
- **기능** -특정 엔터프라이즈 기능은 전용 클러스터-특히 cmk (고객 관리 키) 및 LockBox 지원 에서만 사용할 수 있습니다. 
- **일관성** -고객은 자신의 전용 리소스를 가지 므로 동일한 공유 인프라에서 실행 되는 다른 고객의 영향을 받지 않습니다.
- **비용 효율성** -할당 된 용량 예약 계층이 모든 클러스터 수집을 고려 하므로 전용 클러스터를 사용 하는 것이 더 비용 효율적일 수 있습니다. 이러한 항목 중 일부는 작고 용량 예약 할인이 적합 하지 않은 경우에도 마찬가지입니다.
- 모든 작업 영역이 동일한 클러스터에 있으면 **작업 영역 간** 쿼리가 더 빠르게 실행 됩니다.

전용 클러스터는 매일 1TB 이상의 데이터 수집 용량을 사용 하 여 커밋하는 고객이 필요 합니다. 전용 클러스터로의 마이그레이션은 간단 합니다. 데이터 손실이 나 서비스가 중단 되지 않습니다. 

> [!IMPORTANT]
> 전용 클러스터는 승인 되며 프로덕션 배포에 대해 완벽 하 게 지원 됩니다. 그러나 임시 용량 제약 조건으로 인해 기능을 사용 하려면 사전 등록이 필요 합니다. 연락처를 Microsoft에 사용하여 구독 ID를 제공합니다.

## <a name="management"></a>관리 

전용 클러스터는 Azure Monitor 로그 클러스터를 나타내는 Azure 리소스를 통해 관리 됩니다. 모든 작업은 PowerShell 또는 REST API를 사용 하 여이 리소스에 대해 수행 됩니다.

클러스터를 만든 후에는 클러스터를 구성 하 고 작업 영역에 연결할 수 있습니다. 작업 영역을 클러스터에 연결 하면 작업 영역에 전송 된 새 데이터가 클러스터에 저장 됩니다. 클러스터와 동일한 지역에 있는 작업 영역만 클러스터에 연결할 수 있습니다. 일부 제한 사항이 있는 클러스터에서 작업 영역을 좋아요 해제할 수 있습니다. 이러한 제한 사항에 대 한 자세한 내용은이 문서에 포함 되어 있습니다. 

클러스터 수준의 모든 작업에는 `Microsoft.OperationalInsights/clusters/write` 클러스터에 대 한 작업 권한이 필요 합니다. 작업을 포함 하는 소유자 또는 참가자를 통하거나 `*/write` 동작을 포함 하는 Log Analytics 참여자 역할을 통해이 사용 권한을 부여할 수 있습니다 `Microsoft.OperationalInsights/*` . Log Analytics 권한에 대 한 자세한 내용은 [Azure Monitor에서 로그 데이터 및 작업 영역에 대 한 액세스 관리](../platform/manage-access.md)를 참조 하세요. 

## <a name="billing"></a>결제

전용 클러스터는 용량 예약 계층을 사용 하거나 사용 하지 않고 GB 당 요금제를 사용 하는 작업 영역에 대해서만 지원 됩니다. 전용 클러스터에는 해당 클러스터에 대해 1TB 이상 수집 하도록 커밋하는 고객에 대 한 추가 요금이 부과 되지 않습니다. "수집에 커밋"은 클러스터 수준에서 최소 1TB/일의 용량 예약 계층을 할당 했음을 의미 합니다. 용량 예약이 클러스터 수준에서 연결 되어 있는 동안에는 데이터에 대 한 실제 요금 청구에 대해 다음과 같은 두 가지 옵션을 사용할 수 있습니다.

- *클러스터* (기본값)-클러스터의 용량 예약 비용은 *클러스터* 리소스의 특성을 갖습니다.
- *작업 영역* -클러스터의 용량 예약 비용은 클러스터의 작업 영역에 대 한 특성을 사용 합니다. 하루에 대 한 총 수집 데이터가 용량 예약 아래에 있는 경우 *클러스터* 리소스의 사용량에 대 한 요금이 청구 됩니다. 클러스터 가격 책정 모델에 대해 자세히 알아보려면 [전용 클러스터 Log Analytics](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters) 를 참조 하세요.

전용 클러스터 요금 청구에 대 한 자세한 내용은 [Log Analytics 전용 클러스터 청구](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters)를 참조 하세요.

## <a name="creating-a-cluster"></a>클러스터 만들기

먼저 클러스터 리소스를 만들어 전용 클러스터 만들기를 시작 합니다.

다음 속성을 지정 해야 합니다.

- **ClusterName**: 관리 목적으로 사용 됩니다. 사용자는이 이름에 노출 되지 않습니다.
- **ResourceGroupName**: Azure 리소스의 경우 클러스터는 리소스 그룹에 속합니다. 일반적으로 클러스터는 조직의 많은 팀에서 공유 되기 때문에 중앙 IT 리소스 그룹을 사용 하는 것이 좋습니다. 디자인 고려 사항에 대 한 자세한 내용은 [Azure Monitor 로그 배포 디자인](../platform/design-logs-deployment.md) 을 검토 하세요.
- **위치**: 클러스터는 특정 Azure 지역에 있습니다. 이 영역에 있는 작업 영역만이 클러스터에 연결할 수 있습니다.
- **SkuCapacity**: *클러스터* 리소스를 만들 때 sku ( *용량 예약* 수준)를 지정 해야 합니다. *용량 예약* 수준 범위는 하루 1000 gb ~ 3000 gb 일 수 있습니다. 필요한 경우 나중에 100 단계에서 업데이트할 수 있습니다. 하루에 3000 GB 보다 높은 용량 예약 수준을 요구 하는 경우 microsoft에 문의 하세요 LAIngestionRate@microsoft.com . 클러스터 비용에 대 한 자세한 내용은 [Log Analytics 클러스터에 대 한 비용 관리](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters) 를 참조 하세요.

*클러스터* 리소스를 만든 후 *Sku*, * keyVaultProperties, *billingType*등의 추가 속성을 편집할 수 있습니다. 아래 세부 정보를 참조 하세요.

> [!WARNING]
> 클러스터 만들기는 리소스 할당 및 프로 비전을 트리거합니다. 이 작업을 완료 하는 데 최대 한 시간이 걸릴 수 있습니다. 이를 비동기적으로 실행 하는 것이 좋습니다.

클러스터를 만드는 사용자 계정에는 표준 Azure 리소스 만들기 권한 `Microsoft.Resources/deployments/*` 및 클러스터 쓰기 권한이 있어야 `(Microsoft.OperationalInsights/clusters/write)` 합니다.

### <a name="create"></a>만들기 

**PowerShell**

```powershell
invoke-command -scriptblock { New-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name} -Location {region-name} -SkuCapacity {daily-ingestion-gigabyte} } -asjob

# Check when the job is done
Get-Job

```

**REST (영문)**

*전화할* 
```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
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

*Response*

200 OK 및 헤더로 지정 해야 합니다.

### <a name="check-provisioning-status"></a>프로비저닝 상태 확인

Log Analytics 클러스터를 프로 비전 하는 작업은 완료 하는 데 시간이 걸립니다. 다음 두 가지 방법으로 프로 비전 상태를 확인할 수 있습니다.

1. 응답에서 Azure-AsyncOperation URL 값을 복사하고 비동기 작업 상태 검사를 수행합니다.

   또는

1. GET 요청을 *클러스터* 리소스에 보내고 *provisioningState* 값을 확인합니다. 값은 프로 비전 중에 *ProvisioningAccount* 완료 되 면 *성공* 합니다.

   ```rst
   GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
   Authorization: Bearer <token>
   ```

   **Response**

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

## <a name="change-cluster-properties"></a>클러스터 속성 변경

*클러스터* 리소스를 만들고 완전히 프로 비전 한 후에는 PowerShell 또는 REST API를 사용 하 여 클러스터 수준에서 추가 속성을 편집할 수 있습니다. 클러스터를 만드는 동안 사용할 수 있는 속성 외에도 클러스터를 프로 비전 한 후에는 추가 속성을 설정할 수 있습니다.

- **keyVaultProperties**: [고객이 관리 하는 Azure Monitor 키](../platform/customer-managed-keys.md#cmk-provisioning-procedure)를 프로 비전 하는 데 사용 되는 Azure Key Vault를 구성 하는 데 사용 됩니다. *KeyVaultUri*, *KeyName*, *keyversion*매개 변수를 포함 합니다. 
- **billingType** - *billingType* 속성은 *클러스터* 리소스 및 해당 데이터에 대 한 청구 특성을 결정 합니다.
- **클러스터** (기본값)-클러스터의 용량 예약 비용은 *클러스터* 리소스의 특성을 갖습니다.
- **작업 영역** -클러스터의 용량 예약 비용은 클러스터의 작업 영역에 대 한 특성을 기준으로 하며, 해당 요일의 총 수집 데이터가 용량 예약에 포함 되는 경우 *클러스터* 리소스의 사용량에 대 한 비용이 청구 됩니다. 클러스터 가격 책정 모델에 대해 자세히 알아보려면 [전용 클러스터 Log Analytics](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters) 를 참조 하세요. 

> [!NOTE]
> *BillingType* 속성은 PowerShell에서 지원 되지 않습니다.
> 클러스터 속성 업데이트는 비동기적으로 실행 될 수 있으며 완료 하는 데 다소 시간이 걸릴 수 있습니다.

**PowerShell**

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name} -KeyVaultUri {key-uri} -KeyName {key-name} -KeyVersion {key-version}
```

**REST (영문)**

> [!NOTE]
> PATCH를 사용 하 여 *클러스터* 리소스 *sku*, *keyVaultProperties* 또는 *billingType* 를 업데이트할 수 있습니다.

예를 들면 다음과 같습니다. 

*전화할*

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
   "sku": {
     "name": "capacityReservation",
     "capacity": 1000
     },
   "properties": {
    "billingType": "cluster",
     "KeyVaultProperties": {
       "KeyVaultUri": "https://<key-vault-name>.vault.azure.net",
       "KeyName": "<key-name>",
       "KeyVersion": "<current-version>"
       }
   },
   "location":"<region-name>"
}
```

"KeyVaultProperties"에는 Key Vault 키 식별자 세부 정보가 포함됩니다.

*응답*

200 OK 및 헤더

### <a name="check-cluster-update-status"></a>클러스터 업데이트 상태 확인

키 식별자의 전파를 완료 하는 데 몇 분이 걸립니다. 업데이트 상태는 다음 두 가지 방법으로 확인할 수 있습니다.

- 응답에서 Azure-AsyncOperation URL 값을 복사하고 비동기 작업 상태 검사를 수행합니다. 

   또는

- GET 요청을 *클러스터* 리소스에 보내고 *KeyVaultProperties* 값을 확인합니다. 최근에 업데이트한 키 식별자 세부 정보가 응답으로 반환됩니다.

   키 식별자 업데이트가 완료되면 *클러스터* 리소스에 대한 GET 요청의 응답은 다음과 같습니다.

   ```json
   {
     "identity": {
       "type": "SystemAssigned",
       "tenantId": "tenant-id",
       "principalId": "principle-id"
       },
     "sku": {
       "name": "capacityReservation",
       "capacity": 1000,
       "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
       },
     "properties": {
       "keyVaultProperties": {
         "keyVaultUri": "https://key-vault-name.vault.azure.net",
         "kyName": "key-name",
         "keyVersion": "current-version"
         },
        "provisioningState": "Succeeded",
       "billingType": "cluster",
       "clusterId": "cluster-id"
     },
     "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
     "name": "cluster-name",
     "type": "Microsoft.OperationalInsights/clusters",
     "location": "region-name"
  }
  ```

## <a name="link-a-workspace-to-the-cluster"></a>작업 영역을 클러스터에 연결

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


### <a name="link-operations"></a>링크 작업

**PowerShell**

다음 PowerShell 명령을 사용 하 여 클러스터에 연결 합니다.

```powershell
# Find cluster resource ID
$clusterResourceId = (Get-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name}).id

# Link the workspace to the cluster
invoke-command -scriptblock { Set-AzOperationalInsightsLinkedService -ResourceGroupName {resource-group-name} -WorkspaceName {workspace-name} -LinkedServiceName cluster -WriteAccessResourceId $clusterResourceId } -asjob

# Check when the job is done
Get-Job
```


**REST (영문)**

다음 REST 호출을 사용 하 여 클러스터에 연결 합니다.

*보내기*

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-03-01-preview 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "WriteAccessResourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    }
}
```

*응답*

200 OK 및 헤더입니다.

### <a name="using-customer-managed-keys-with-linking"></a>연결에서 고객 관리 키 사용

고객 관리 키를 사용 하는 경우 수집 데이터는 연결 작업 후에 관리 되는 키로 암호화 된 상태로 저장 됩니다 .이 작업은 완료 하는 데 최대 90 분까지 걸릴 수 있습니다. 

작업 영역 연결 상태는 다음 두 가지 방법으로 확인할 수 있습니다.

- 응답에서 Azure-AsyncOperation URL 값을 복사하고 비동기 작업 상태 검사를 수행합니다.

- [작업 영역 보내기 –](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) 요청을 가져오고 응답을 관찰 합니다. 연결 된 작업 영역의 "기능" 아래에 clusterResourceId가 있습니다.

보내기 요청은 다음과 같습니다.

*보내기*

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

*Response*

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

## <a name="unlink-a-workspace-from-a-dedicated-cluster"></a>전용 클러스터에서 작업 영역 연결 해제

클러스터에서 작업 영역의 연결을 끊을 수 있습니다. 클러스터에서 작업 영역의 연결을 해제 한 후에는이 작업 영역에 연결 된 새 데이터가 전용 클러스터로 전송 되지 않습니다. 또한 작업 영역 요금은 더 이상 클러스터를 통해 수행 되지 않습니다. 연결 되지 않은 작업 영역의 이전 데이터가 클러스터에 남아 있을 수 있습니다. CMK (고객 관리 키)를 사용 하 여이 데이터를 암호화 하는 경우 Key Vault 비밀이 유지 됩니다. 시스템은 Log Analytics 사용자의 이러한 변경을 추상화 합니다. 사용자는 평소와 같이 작업 영역을 쿼리할 수 있습니다. 시스템은 사용자에 게 표시 하지 않고 필요에 따라 백 엔드에 대 한 클러스터 간 쿼리를 수행 합니다.  

> [!WARNING] 
> 한 달 내에 작업 영역 당 연결 작업은 두 개로 제한 됩니다. 적절 한 연결 끊기 작업을 고려 하 고 계획 하는 데 시간이 걸립니다. 

## <a name="delete-a-dedicated-cluster"></a>전용 클러스터 삭제

전용 클러스터 리소스를 삭제할 수 있습니다. 클러스터에서 모든 작업 영역을 삭제 하기 전에 연결을 해제 해야 합니다. 클러스터 리소스가 삭제 되 면 물리적 클러스터는 제거 및 삭제 프로세스를 시작 합니다. 클러스터를 삭제 하면 클러스터에 저장 된 모든 데이터가 삭제 됩니다. 이전에 클러스터에 연결 된 작업 영역에서 데이터를 만들 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Log Analytics 전용 클러스터 청구](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters) 에 대해 알아보기
- [Log Analytics 작업 영역](../platform/design-logs-deployment.md) 에 대 한 적절 한 디자인 알아보기
