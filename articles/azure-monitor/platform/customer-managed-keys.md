---
title: Azure Monitor 고객 관리형 키
description: CMK(고객 관리형 키)를 구성하여 Azure Key Vault 키를 사용하여 Log Analytics 작업 영역의 데이터를 암호화하는 방법에 대한 정보 및 단계입니다.
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 07/05/2020
ms.openlocfilehash: eec056cbe246f129fb78e15faa0027846c271181
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87382953"
---
# <a name="azure-monitor-customer-managed-key"></a>Azure Monitor 고객 관리형 키 

이 문서에는 Log Analytics 작업 영역에 대한 CMK(고객 관리형 키)를 구성하는 방법에 대한 배경 정보와 단계가 나와 있습니다. 구성된 후에는 작업 영역으로 보낸 모든 데이터가 Azure Key Vault 키로 암호화됩니다.

구성하기 전에 아래 [제한 사항 및 제약 조건](#limitationsandconstraints)을 검토하는 것이 좋습니다.

## <a name="customer-managed-key-cmk-overview"></a>CMK(고객 관리형 키) 개요

[저장 데이터 암호화](../../security/fundamentals/encryption-atrest.md)는 조직의 일반적인 개인 정보 및 보안 요구 사항입니다. Azure에서 저장 데이터 암호화를 완전하게 관리할 수 있으며, 암호화 또는 암호화 키를 긴밀하게 관리하기 위한 다양한 옵션이 있습니다.

Azure Monitor를 사용 하면 모든 데이터 및 저장 된 쿼리가 Microsoft 관리 키 (MMK)를 사용 하 여 미사용 상태로 암호화 됩니다. 또한 Azure Monitor은 [Azure Key Vault](../../key-vault/general/overview.md) 에 저장 되 고 시스템 할당 [관리 id](../../active-directory/managed-identities-azure-resources/overview.md) 인증을 사용 하 여 저장소에서 액세스 하는 고유한 키를 사용 하 여 암호화 옵션을 제공 합니다. 이 키 (CMK)는 [소프트웨어 또는 하드웨어 HSM으로 보호](../../key-vault/general/overview.md)될 수 있습니다.

Azure Monitor의 암호화 사용은  [Azure Storage 암호화](../../storage/common/storage-service-encryption.md#about-azure-storage-encryption)의  작동 방식과 동일합니다.

CMK를 사용하면 언제든지 데이터에 대한 액세스를 제어하고 철회할 수 있습니다. Azure Monitor 스토리지는 키 권한의 변경 내용을 항상 1시간 이내에 적용합니다. 또한 쿼리 엔진이 효율적으로 작동할 수 있도록 지난 14일 동안 수집된 데이터도 핫 캐시(SSD 지원)로 유지됩니다. 이 데이터는 CMK 구성에 관계없이 Microsoft 키로 암호화된 상태로 유지되지만 SSD 데이터에 대한 제어는  [키 해지](#cmk-kek-revocation)를 준수합니다. 2020년 하반기에는 CMK를 사용하여 SSD 데이터를 암호화하기 위해 노력하고 있습니다.

CMK 기능은 전용 Log Analytics 클러스터에서 제공됩니다. 사용자의 지역에 필요한 용량이 있는지 확인 하려면 구독이 미리 허용 되어야 합니다. CMK 구성을 시작 하기 전에 Microsoft 연락처를 사용 하 여 구독을 허용 하세요.

 [Log Analytics 클러스터 가격 책정 모델](./manage-cost-storage.md#log-analytics-dedicated-clusters)에서는 1,000GB/일 수준에서 시작하는 용량 예약을 사용합니다.

## <a name="how-cmk-works-in-azure-monitor"></a>Azure Monitor에서 CMK가 작동하는 방식

Azure Monitor는 시스템이 할당한 관리 ID를 활용하여 Azure Key Vault에 대한 액세스 권한을 부여합니다. 시스템이 할당한 관리 ID는 단일 Azure 리소스에만 연결될 수 있지만, Log Analytics 클러스터의 ID는 클러스터 수준에서 지원됩니다. 이는 CMK 기능이 전용 Log Analytics 클러스터에서 제공됨을 나타냅니다. 여러 작업 영역에서 CMK를 지원하기 위해 새 Log Analytics *클러스터* 리소스에서 Key Vault와 Log Analytics 작업 영역 간의 중간 ID 연결 역할을 수행합니다. Log Analytics 클러스터 스토리지는 *클러스터* 리소스와 연결된 관리 ID를 사용하여 Azure Active Directory를 통해 Azure Key Vault에 인증합니다. 

CMK가 구성되면 *클러스터* 리소스와 연결된 작업 영역으로 수집된 모든 데이터가 Key Vault의 키를 사용하여 암호화됩니다. 작업 영역 연결은 언제든지 *클러스터* 리소스에서 해제할 수 있습니다. 새 데이터는 Log Analytics 스토리지에 수집되고 Microsoft 키를 사용하여 암호화되지만, 새 데이터와 기존 데이터를 원활하게 쿼리할 수 있습니다.


![CMK 개요](media/customer-managed-keys/cmk-overview.png)

1. Key Vault
2. Key Vault에 대한 권한이 있는 관리 ID를 사용하는 Log Analytics *클러스터* 리소스 - 이 ID는 기본 전용 Log Analytics 클러스터 스토리지로 전파됩니다.
3. 전용 Log Analytics 클러스터
4. CMK 암호화를 위해 *클러스터* 리소스와 연결된 작업 영역

## <a name="encryption-keys-operation"></a>암호화 키 작업

스토리지 데이터 암호화에는 세 가지 유형의 키가 있습니다.

- **KEK** - CMK(키 암호화 키)
- **AEK** - 계정 암호화 키
- **DEK** - 데이터 암호화 키

다음 규칙이 적용됩니다.

- Log Analytics 클러스터 스토리지 계정은 모든 스토리지 계정에 대해 AEK라고 하는 고유한 암호화 키를 생성합니다.

- AEK는 디스크에 기록되는 각 데이터 블록을 암호화하는 데 사용되는 키인 DEK를 파생하는 데 사용됩니다.

- Key Vault에서 키를 구성하고 *클러스터* 리소스에서 참조하는 경우 Azure Storage에서 요청을 Azure Key Vault에 보내 AEK를 래핑하고 이를 해제하여 데이터 암호화 및 암호 해독 작업을 수행합니다.

- KEK는 Key Vault를 유지하며, HSM 키의 경우 하드웨어를 그대로 유지합니다.

- Azure Storage는 *클러스터* 리소스와 연결된 관리 ID를 사용하여 Azure Active Directory를 통해 Azure Key Vault를 인증하고 액세스합니다.

## <a name="cmk-provisioning-procedure"></a>CMK 프로비저닝 절차

1. 구독 허용--CMK 기능은 전용 Log Analytics 클러스터에서 제공 됩니다. 사용자의 지역에 필요한 용량이 있는지 확인 하려면 구독이 미리 허용 되어야 합니다. Microsoft 연락처를 사용 하 여 구독을 허용 하세요.
2. Azure Key Vault 만들기 및 키 저장
3. *클러스터* 리소스 만들기
4. Key Vault에 권한 부여
5. Log Analytics 작업 영역 연결

이 프로시저는 Azure Portal에서 지원 되지 않으며 PowerShell 또는 REST 요청을 통해 프로 비전이 수행 됩니다.

> [!IMPORTANT]
> 모든 REST 요청은 요청 헤더에 전달자 권한 부여 토큰을 포함 해야 합니다.

다음은 그 예입니다.

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
Authorization: Bearer eyJ0eXAiO....
```

여기서 *eyJ0eXAiO....* 는 모든 권한 부여 토큰을 나타냅니다. 

토큰은 다음 방법 중 하나를 사용하여 가져올 수 있습니다.

1. [앱 등록](/graph/auth/auth-concepts#access-tokens) 메서드를 사용합니다.
2. Azure 포털에서 다음을 수행합니다.
    1. "개발자 도구"에 있는 동안 Azure Portal로 이동합니다(F12 키).
    1. "batch?api-version" 인스턴스 중 하나의 "요청 헤더" 아래에서 권한 부여 문자열을 찾습니다. "authorization: Bearer eyJ0eXAiO...."와 같습니다. 
    1. 아래 예제에 따라 이를 복사하여 API 호출에 추가합니다.
3. Azure REST 설명서 사이트로 이동합니다. API에서 "사용해 보세요"를 누르고 전달자 토큰을 복사합니다.

### <a name="asynchronous-operations-and-status-check"></a>비동기 작업 및 상태 검사

이 구성 절차의 작업 중 일부는 빨리 완료할 수 없으므로 비동기적으로 실행됩니다. 구성에서 REST 요청을 사용 하는 경우 응답은 처음에 허용 되는 경우 *Azure-AsyncOperation* 속성을 사용 하 여 HTTP 상태 코드 200 (OK) 및 헤더를 반환 합니다.
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-03-01-preview"
```

그런 다음 GET 요청을 *Azure AsyncOperation* 헤더 값에 보내서 비동기 작업의 상태를 확인할 수 있습니다.
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

응답에는 작업 및 해당 *상태*에 대한 정보가 포함됩니다. 다음 중 하나일 수 있습니다.

작업 진행 중
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "InProgress", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
}
```

키 식별자 업데이트 작업 진행 중
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Updating", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
}
```

*클러스터* 리소스 삭제 진행 중 - 작업 영역과 연결된 작업 영역이 있는 *클러스터* 리소스를 삭제하면 시간이 걸릴 수 있는 비동기 작업에서 각 작업 영역에 대한 연결 해제 작업이 수행됩니다.
연결된 작업 영역이 없는 *클러스터*를 삭제하는 경우에는 관련이 없습니다. 이 경우 *클러스터* 리소스가 즉시 삭제됩니다.
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Deleting", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
}
```

작업 완료
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Succeeded", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
}
```

작업 실패
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Failed", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
    "error" : { 
        "code": "error-code",  
        "message": "error-message" 
    }
}
```

### <a name="allowing-subscription-for-cmk-deployment"></a>CMK 배포에 대 한 구독 허용

CMK 기능은 전용 Log Analytics 클러스터에서 제공됩니다.사용자의 지역에 필요한 용량이 있는지 확인 하려면 구독이 미리 허용 되어야 합니다. 연락처를 Microsoft에 사용하여 구독 ID를 제공합니다.

> [!IMPORTANT]
> CMK 기능은 지역별 기능입니다. Azure Key Vault, *클러스터* 리소스 및 연결된 Log Analytics 작업 영역은 동일한 지역에 있어야 하지만 서로 다른 구독에 있을 수 있습니다.

### <a name="storing-encryption-key-kek"></a>암호화 키(KEK) 저장

이미 생성해야 하는 Azure Key Vault를 만들거나 사용하거나 데이터 암호화에 사용할 키를 가져옵니다. Azure Monitor에서 키와 데이터에 대한 액세스를 보호하기 위해 Azure Key Vault를 복구 가능으로 구성해야 합니다. 이 구성은 Key Vault의 속성에서 확인할 수 있습니다. *일시 삭제* 및 *제거 보호*를 모두 사용하도록 설정되어 있어야 합니다.

![일시 삭제 및 제거 보호 설정](media/customer-managed-keys/soft-purge-protection.png)

이러한 설정은 CLI 및 PowerShell을 통해 업데이트할 수 있습니다.

- [일시 삭제](../../key-vault/general/soft-delete-overview.md)
- [제거 보호](../../key-vault/general/soft-delete-overview.md#purge-protection)는 일시 삭제 후에도 비밀/자격 증명 모음을 강제로 삭제하지 않도록 방지합니다.

### <a name="create-cluster-resource"></a>*클러스터* 리소스 만들기

이 리소스는 Key Vault와 Log Analytics 작업 영역 간의 중간 ID 연결로 사용됩니다. 구독이 허용 되었다는 확인 메시지가 표시 되 면 작업 영역이 있는 지역에서 Log Analytics *클러스터* 리소스를 만듭니다.

*클러스터* 리소스를 만드는 경우 *용량 예약* 수준(sku)을 지정해야 합니다. *용량 예약* 수준 범위는 매일 1000 ~ 3000 GB 이며 100 단계에서 업데이트할 수 있습니다. 하루에 3000 GB 보다 높은 용량 예약 수준을 요구 하는 경우 microsoft에 문의 하세요 LAIngestionRate@microsoft.com . [자세히 알아보기](./manage-cost-storage.md#log-analytics-dedicated-clusters)

*billingType* 속성은 *클러스터* 리소스와 해당 데이터에 대한 청구 특성을 결정합니다.
- *클러스터 (기본값* )-클러스터의 용량 예약 비용은 *클러스터* 리소스의 특성을 갖습니다.
- *작업 영역* --클러스터의 용량 예약 비용은 클러스터의 작업 영역에 대 한 특성을 기준으로 하며, 해당 요일의 총 수집 데이터가 용량 예약 상태에 있는 경우 *클러스터 리소스의* 사용량에 대 한 비용이 청구 됩니다. 클러스터 가격 책정 모델에 대해 자세히 알아보려면 [전용 클러스터 Log Analytics](manage-cost-storage.md#log-analytics-dedicated-clusters) 를 참조 하세요. 

> [!NOTE]
> * *클러스터* 리소스를 만든 후에 PATCH REST 요청을 사용하여 해당 리소스를 *sku*, *keyVaultProperties* 또는 *billingType*으로 업데이트할 수 있습니다.
> * 현재 REST 요청을 사용 하 여 *billingType* 를 업데이트할 수 있습니다 .이는 PowerShell에서 지원 되지 않습니다.

이 작업은 비동기 작업 이므로 완료 하는 데는 몇를 사용할 수 있습니다.

> [!IMPORTANT]
> 다음 단계에서 세부 정보가 필요하므로 응답을 복사하여 저장합니다.
> 

```powershell
New-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -Location "region-name" -SkuCapacity daily-ingestion-gigabyte 
```

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

ID는 만들 때 *클러스터* 리소스에 할당됩니다.

**Response**

200 OK 및 헤더입니다.

Log Analytics 클러스터 프로비저닝을 완료하는 데 시간이 걸리지만 다음 두 가지 방법으로 프로비저닝 상태를 확인할 수 있습니다.

1. 응답에서 Azure-AsyncOperation URL 값을 복사하고 [비동기 작업 상태 검사](#asynchronous-operations-and-status-check)를 수행합니다.
2. GET 요청을 *클러스터* 리소스에 보내고 *provisioningState* 값을 확인합니다. 프로비저닝 중이면 *ProvisioningAccount*이고, 완료되면 *Succeeded*입니다.

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

"principalId" GUID가 *클러스터* 리소스에 대한 관리 ID 서비스에서 생성됩니다.

### <a name="grant-key-vault-permissions"></a>Key Vault 권한 부여

새 액세스 정책을 사용 하 여 Key Vault를 업데이트 하 여 *클러스터* 리소스에 대 한 사용 권한을 부여 합니다. 이러한 권한은 기본 Azure Monitor 스토리지에서 데이터를 암호화하는 데 사용됩니다. Azure Portal에서 Key Vault를 열고, "액세스 정책"을 클릭한 다음, "+ 액세스 정책 추가"를 클릭하여 다음 설정을 통해 정책을 만듭니다.

- 키 권한: '가져오기', '키 래핑' 및 '키 래핑 해제' 권한을 선택합니다.
- 보안 주체 선택: 이전 단계의 응답에서 반환 된 *클러스터* 리소스 이름 또는 사용자 id 값을 입력 합니다.

![Key Vault 권한 부여](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

Azure Monitor 데이터에 대한 액세스와 키를 보호하기 위해 Key Vault가 복구 가능으로 구성되었는지 확인하려면 *가져오기* 권한이 필요합니다.

### <a name="update-cluster-resource-with-key-identifier-details"></a>키 식별자 세부 정보를 사용하여 클러스터 리소스 업데이트

이 단계는 Key Vault에서 초기 및 향후 키 버전을 업데이트하는 중에 수행됩니다. 데이터 암호화에 사용할 키 버전에 대해 Azure Monitor 스토리지에 알립니다. 업데이트되면 새 키를 사용하여 AEK(스토리지 키)에 래핑 및 래핑 해제합니다.

*클러스터* 리소스를 Key Vault *키 식별자* 세부 정보로 업데이트하려면 Azure Key Vault에서 키의 현재 버전을 선택하여 키 식별자 세부 정보를 가져옵니다.

![Key Vault 권한 부여](media/customer-managed-keys/key-identifier-8bit.png)

*클러스터* 리소스의 KeyVaultProperties를 키 식별자 세부 정보로 업데이트합니다.

키 식별자 정보를 업데이트할 때이 작업은 비동기적 이므로 완료 하는 데 다소 시간이 걸릴 수 있습니다. 용량 값을 업데이트할 때 동기화 됩니다.

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -KeyVaultUri "key-uri" -KeyName "key-name" -KeyVersion "key-version"
```

> [!NOTE]
> PATCH를 사용 하 여 *클러스터* 리소스 *sku*, *keyVaultProperties* 또는 *billingType* 를 업데이트할 수 있습니다.

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
   "identity": { 
     "type": "systemAssigned" 
     },
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

**Response**

200 OK 및 헤더입니다.
키 식별자의 전파를 완료하는 데 몇 분 정도 걸립니다. 업데이트 상태는 다음 두 가지 방법으로 확인할 수 있습니다.
1. 응답에서 Azure-AsyncOperation URL 값을 복사하고 [비동기 작업 상태 검사](#asynchronous-operations-and-status-check)를 수행합니다.
2. GET 요청을 *클러스터* 리소스에 보내고 *KeyVaultProperties* 값을 확인합니다. 최근에 업데이트한 키 식별자 세부 정보가 응답으로 반환됩니다.

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

### <a name="workspace-association-to-cluster-resource"></a>*클러스터* 리소스에 대한 작업 영역 연결

이 작업을 수행하려면 작업 영역 및 *클러스터* 리소스에 대한 '쓰기' 권한이 있어야 합니다. 여기에는 다음 작업이 포함됩니다.

- 작업 영역의 경우: Microsoft.OperationalInsights/workspaces/write
- *클러스터* 리소스의 경우: Microsoft.OperationalInsights/clusters/write

> [!IMPORTANT]
> 이 단계는 Log Analytics 클러스터 프로비저닝이 완료된 후에만 수행해야 합니다. 프로비저닝하기 전에 작업 영역을 연결하고 데이터를 수집하면 수집된 데이터가 삭제되어 복구할 수 없습니다.

이 작업은 비동기 작업 이므로 완료 하는 데는 몇를 사용할 수 있습니다.

```powershell
$clusterResourceId = (Get-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name").id
Set-AzOperationalInsightsLinkedService -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -LinkedServiceName cluster -WriteAccessResourceId $clusterResourceId
```

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

**응답**

200 OK 및 헤더입니다.

수집된 데이터는 연결 작업 후 관리형 키를 사용하여 암호화된 상태로 저장되며, 이 작업을 완료하는 데 최대 90분까지 걸릴 수 있습니다. 작업 영역 연결 상태는 다음 두 가지 방법으로 확인할 수 있습니다.

1. 응답에서 Azure-AsyncOperation URL 값을 복사하고 [비동기 작업 상태 검사](#asynchronous-operations-and-status-check)를 수행합니다.
2. [작업 영역 – 가져오기](/rest/api/loganalytics/workspaces/get) 요청을 보내고 응답을 관찰합니다. 연결된 작업 영역의 "features" 아래에 clusterResourceId가 있습니다.

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

**응답**

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

## <a name="cmk-kek-revocation"></a>CMK(KEK) 해지

키를 사용하지 않도록 설정하거나 Key Vault에서 *클러스터* 리소스 액세스 정책을 삭제하여 데이터에 대한 액세스를 철회할 수 있습니다. Log Analytics 클러스터 스토리지는 키 권한의 변경 내용을 항상 1시간 이내에 적용합니다. 이로 인해 스토리지가 사용할 수 없게 됩니다.  *클러스터* 리소스와 연결된 작업 영역에 수집된 모든 새 데이터가 삭제되어 복구할 수 없으며, 데이터에 액세스할 수 없고, 이러한 작업 영역에 대한 쿼리가 실패합니다. *클러스터* 리소스와 작업 영역을 삭제하지 않는 한 이전에 수집된 데이터는 스토리지에서 유지됩니다. 액세스할 수 없는 데이터는 데이터 보존 정책에 따라 제어되며 보존 기간에 도달하면 삭제됩니다. 

또한 쿼리 엔진이 효율적으로 작동할 수 있도록 지난 14일 동안 수집된 데이터도 핫 캐시(SSD 지원)로 유지됩니다. 이는 키 해지 작업에서 삭제되며 액세스할 수 없게 됩니다.

스토리지는 Key Vault를 주기적으로 폴링하여 암호화 키를 래핑 해제하려고 시도하며, 액세스한 후에는 30분 이내에 데이터 수집 및 쿼리가 다시 시작됩니다.

## <a name="cmk-kek-rotation"></a>CMK(KEK) 회전

CMK를 회전하려면 Azure Key Vault의 새 키 버전을 사용하여 *클러스터* 리소스를 명시적으로 업데이트해야 합니다. "키 식별자 세부 정보를 사용하여 *클러스터* 리소스 업데이트" 단계의 지침을 따릅니다. *클러스터* 리소스에서 새 키 식별자 세부 정보를 업데이트하지 않으면 Log Analytics 클러스터 스토리지에서 이전 키를 암호화에 계속 사용합니다. *클러스터* 리소스에서 새 키를 업데이트하기 전에 이전 키를 사용하지 않도록 설정하거나 삭제하면 [키 해지](#cmk-kek-revocation) 상태가 됩니다.

AEK는 이제 Key Vault의 새 KEK(키 암호화 키) 버전을 사용하여 암호화되지만, 데이터는 항상 AEK(계정 암호화 키)를 사용하여 암호화되므로 키 회전 작업 후에도 모든 데이터에 계속 액세스할 수 있습니다.

## <a name="cmk-for-queries"></a>쿼리에 CMK

Log Analytics에 사용 되는 쿼리 언어는 표현 되며 쿼리에 추가 하는 설명 또는 쿼리 구문에 중요 한 정보를 포함할 수 있습니다. 일부 조직에서는 이러한 정보를 CMK 정책의 일부로 보호 된 상태로 유지 하 고 암호화 된 쿼리를 키로 저장 해야 합니다. Azure Monitor를 사용 하면 작업 영역에 연결 될 때 사용자 고유의 저장소 계정에서 키로 암호화 된 *저장 된 검색* 및 *로그 경고* 쿼리를 저장할 수 있습니다. 

> [!NOTE]
> Log Analytics 쿼리는 사용 된 시나리오에 따라 다양 한 저장소에 저장할 수 있습니다. CMK 구성: Azure Monitor, Azure 대시보드, Azure 논리 앱, Azure Notebooks 및 자동화 Runbook의 통합 문서와 상관 없이 쿼리는 Microsoft key (MMK)를 사용 하 여 암호화 된 상태로 유지 됩니다.

사용자 고유의 저장소 (BYOS)를 가져와서 작업 영역에 연결 하면 서비스에서 *저장 된 검색* 및 *로그 경고* 쿼리를 저장소 계정에 업로드 합니다. 즉, Log Analytics 클러스터의 데이터를 암호화 하는 데 사용 하는 것과 동일한 키를 사용 하거나 다른 키를 사용 하 여 저장소 계정 및 [미사용 암호화 정책을](../../storage/common/encryption-customer-managed-keys.md) 제어할 수 있습니다. 그러나 해당 저장소 계정과 관련 된 비용을 담당 하 게 됩니다. 

**쿼리에 CMK를 설정 하기 전 고려 사항**
* 작업 영역 및 저장소 계정에 대 한 ' 쓰기 ' 권한이 있어야 합니다.
* Log Analytics 작업 영역이 있는 동일한 지역에 저장소 계정을 만들어야 합니다.
* 저장소의 *저장 검색* 은 서비스 아티팩트로 간주 되 고 형식은 변경 될 수 있습니다.
* 기존 *저장 검색* 은 작업 영역에서 제거 됩니다. 복사 및는 구성 전에 필요한 *검색을 저장* 합니다. [PowerShell](/powershell/module/az.operationalinsights/get-azoperationalinsightssavedsearch) 을 사용 하 여 *저장 된 검색* 을 볼 수 있습니다.
* 쿼리 기록은 지원 되지 않으므로 실행 한 쿼리를 볼 수 없습니다.
* 쿼리 저장을 위해 단일 저장소 계정을 작업 영역에 연결할 수 있지만 *저장 된 검색* 및 *로그 경고* 쿼리를 가져오면 사용할 수 있습니다.
* 대시보드에 고정은 지원 되지 않습니다.

**저장 된 검색 쿼리를 위한 BYOS 구성**

*쿼리에* 대 한 저장소 계정을 작업 영역에 연결 합니다. *저장 된 검색* 쿼리는 저장소 계정에 저장 됩니다. 

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Query -StorageAccountIds $storageAccount.Id
```

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Query?api-version=2020-03-01-preview
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "dataSourceType": "Query", 
    "storageAccountIds": 
    [
      "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"
    ]
  }
}
```

구성 후에는 저장 된 새 *검색* 쿼리가 저장소에 저장 됩니다.

**로그 경고 쿼리를 위한 BYOS 구성**

*경고* 에 대 한 저장소 계정을 작업 영역에 연결 합니다.- *로그-경고* 쿼리는 저장소 계정에 저장 됩니다. 

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Alerts -StorageAccountIds $storageAccount.Id
```

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Alerts?api-version=2020-03-01-preview
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "dataSourceType": "Alerts", 
    "storageAccountIds": 
    [
      "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"
    ]
  }
}
```

구성 후에는 새 경고 쿼리가 저장소에 저장 됩니다.

## <a name="cmk-management"></a>CMK 관리

- **리소스 그룹에 대한 모든 *클러스터* 리소스 가져오기**
  
  ```powershell
  Get-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name"
  ```

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **Response**
  
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

- **구독에 대한 모든 *클러스터* 리소스 가져오기**
  
  ```powershell
  Get-AzOperationalInsightsCluster
  ```

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```
    
  **응답**
    
  리소스 그룹의 '*클러스터* 리소스'와 동일한 응답이지만 구독 범위에 있습니다.

- ***클러스터* 리소스의 *용량 예약* 업데이트**

  연결된 작업 영역의 데이터 볼륨이 시간이 지남에 따라 변경되고 용량 예약 수준을 적절하게 업데이트하려는 경우입니다. [*클러스터* 리소스 업데이트](#update-cluster-resource-with-key-identifier-details)에 따라 새 용량 값을 제공합니다. 1000 ~ 3000 g b의 범위와 100의 단계에 있을 수 있습니다. 하루 3000 g b 보다 높은 수준의 경우 Microsoft 담당자에 게 연락 하 여 사용 하도록 설정 합니다. 전체 REST 요청 본문을 제공할 필요는 없지만 sku는 다음과 같습니다.

  ```powershell
  Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -SkuCapacity "daily-ingestion-gigabyte"
  ```

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "sku": {
      "name": "capacityReservation",
      "Capacity": 1000
    }
  }
  ```

- ***클러스터* 리소스의 *billingType* 업데이트**

  *billingType* 속성은 *클러스터* 리소스와 해당 데이터에 대한 청구 특성을 결정합니다.
  - *cluster*(기본값) - 클러스터 리소스를 호스팅하는 구독을 기반으로 하는 청구입니다.
  - *workspaces* - 작업 영역을 비례적으로 호스팅하는 구독을 기반으로 하는 청구입니다.
  
  [*클러스터* 리소스 업데이트](#update-cluster-resource-with-key-identifier-details)에 따라 새 billingType 값을 제공합니다. 전체 REST 요청 본문을 제공할 필요는 없으며 *billingType*을 포함해야 합니다.

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "properties": {
      "billingType": "cluster",
      }  
  }
  ``` 

- **작업 영역 연결 해제**

  이 작업을 수행하려면 작업 영역 및 *클러스터* 리소스에 대한 '쓰기' 권한이 필요합니다. 작업 영역 연결은 언제든지 *클러스터* 리소스에서 해제할 수 있습니다. 연결 해제 작업 후에 새로 수집된 데이터는 Log Analytics 스토리지에 저장되고 Microsoft 키를 사용하여 암호화됩니다. *클러스터* 리소스가 프로비저닝되고 유효한 Key Vault 키로 구성되어 있으면 연결 해제 전후에 작업 영역에 수집된 데이터를 원활하게 쿼리할 수 있습니다.

  이 작업은 비동기 작업 이므로 완료 하는 데는 몇를 사용할 수 있습니다.

  ```powershell
  Remove-AzOperationalInsightsLinkedService -ResourceGroupName "resource-group-name" -Name "workspace-name" -LinkedServiceName cluster
  ```

  ```rest
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **Response**

  200 OK 및 헤더입니다.

  연결 해제 작업 후에 수집된 데이터는 Log Analytics 스토리지에 저장됩니다. 이를 완료하는 데 90분 정도 걸릴 수 있습니다. 작업 영역 연결 해제 상태는 다음 두 가지 방법으로 확인할 수 있습니다.

  1. 응답에서 Azure-AsyncOperation URL 값을 복사하고 [비동기 작업 상태 검사](#asynchronous-operations-and-status-check)를 수행합니다.
  2. [작업 영역 – 가져오기](/rest/api/loganalytics/workspaces/get) 요청을 보내고 응답을 관찰합니다. 연결 해제된 작업 영역의 *features* 아래에 *clusterResourceId*가 없습니다.

- **작업 영역 연결 상태 확인**
  
  작업 영역에서 가져오기 작업을 수행 하 고, *기능의*응답에 *clusterresourceid* 속성이 있는지 확인 합니다. 연결 된 작업 영역에는 *Clusterresourceid* 속성이 있습니다.

  ```powershell
  Get-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
  ```

- ***클러스터* 리소스 삭제**

  이 작업을 수행하려면 *클러스터* 리소스에 대한 '쓰기' 권한이 필요합니다. 실수로 또는 의도적으로 삭제되었는지 여부에 관계없이 14일 이내에 데이터를 포함하여 *클러스터* 리소스를 복구할 수 있도록 일시 삭제 작업이 수행됩니다. *클러스터* 리소스 이름은 일시 삭제 기간 동안 예약된 상태로 유지되며, 해당 이름을 사용하여 새 클러스터를 만들 수 없습니다. 일시 삭제 기간이 지나면 *클러스터* 리소스 이름이 해제되고, *클러스터* 리소스와 데이터가 영구적으로 삭제되어 복구할 수 없습니다. 삭제 작업 시 연결된 모든 작업 영역이 *클러스터* 리소스에서 연결 해제됩니다. 새로 수집된 데이터는 Log Analytics 스토리지에 저장되고 Microsoft 키를 사용하여 암호화됩니다. 
  
  작업 영역 연결 해제 작업은 비동기적이며, 이 작업을 완료하는 데 최대 90분까지 걸릴 수 있습니다.

  ```powershell
  Remove-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name"
  ```

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **응답**

  200 정상

- ***클러스터* 리소스 및 데이터 복구** 
  
  지난 14일 동안 삭제된 *클러스터* 리소스는 일시 삭제 상태에 있으므로 해당 데이터를 사용하여 복구할 수 있습니다. 모든 작업 영역이 *클러스터* 리소스 삭제를 통해 *클러스터* 리소스에서 연결 해제되었으므로 CMK 암호화를 복구한 후에 작업 영역에 다시 연결해야 합니다. 복구 작업은 현재 제품 그룹에서 수동으로 수행됩니다. Microsoft 채널을 복구 요청에 사용합니다.

## <a name="limitationsandconstraints"></a>제한 사항 및 제약 조건

- CMK는 전용 Log Analytics 클러스터에서 지원 되며 매일 1TB를 전송 하는 고객에 게 적합 합니다.

- 하위 지역 및 구독 당 최대 *클러스터* 리소스 수는 2입니다.

- 작업 영역에 작업 영역을 연결 *Cluster* 하 고, 작업 영역에 cmk가 필요 하지 않으면 연결을 끊을 수 있습니다.  30일 기간 동안 특정 작업 영역의 작업 영역 연결 수는 2개로 제한됩니다.

- *클러스터* 리소스에 대 한 작업 영역 연결은 Log Analytics 클러스터 프로 비전이 완료 되었음을 확인 한 후에만 수행 해야 합니다.  완료된 후에는 작업 영역으로 보낸 데이터를 삭제하고 복구할 수 있습니다.

- Cmk 암호화는 CMK 구성 후에 새로 수집 데이터에 적용 됩니다.  CMK가 구성되기 전에 수집된 데이터는Microsoft 키를 사용하여 암호화된 상태로 유지됩니다.  CMK가 구성되기 전후에 수집된 데이터는 원활하게 쿼리할 수 있습니다.

- Azure Key Vault를 복구할 수 있는 것으로 구성 해야 합니다. 이러한 속성은 기본적으로 사용 하도록 설정 되어 있지 않으며 CLI 또는 PowerShell을 사용 하 여 구성 해야 합니다.<br>
  - [일시 삭제](../../key-vault/general/soft-delete-overview.md)
  - 일시 삭제 후에도 비밀/자격 증명 모음을 강제로 삭제 하는 것을 방지 하려면 [보호 제거](../../key-vault/general/soft-delete-overview.md#purge-protection) 를 켜야 합니다.

- 다른 리소스 그룹 또는 구독에 대 한 *클러스터* 리소스 이동은 현재 지원 되지 않습니다.

- Azure Key Vault, *클러스터* 리소스 및 연결 된 작업 영역은 동일한 지역 및 동일한 Azure Active Directory (Azure AD) 테 넌 트에 있어야 하지만 다른 구독에 있을 수 있습니다.

- 다른 *클러스터* 리소스와 연결 된 경우 *클러스터* 리소스에 대 한 작업 영역 연결이 실패 합니다.

## <a name="troubleshooting"></a>문제 해결

- Key Vault 가용성과 관련된 동작
  - 정상 작업에서 - 스토리지에서 AEK를 짧은 시간 동안 캐시하고, Key Vault로 돌아가서 래핑을 주기적으로 해제합니다.
    
  - 일시적인 연결 오류 - 스토리지에서 키를 짧은 시간 동안 캐시에서 유지할 수 있도록 하여 일시적인 오류(시간 제한, 연결 실패, DNS 문제)를 처리하고, 이로 인한 사소한 가용성 문제도 해결합니다. 쿼리 및 수집 기능은 중단 없이 계속됩니다.
    
  - 라이브 사이트 - 약 30분 동안 사용할 수 없으면 스토리지 계정을 사용할 수 없게 됩니다. 쿼리 기능을 사용할 수 없으며, 수집된 데이터는 데이터 손실을 방지하기 위해 Microsoft 키를 사용하여 몇 시간 동안 캐시됩니다. Key Vault에 대한 액세스가 복원되면 쿼리를 사용할 수 있게 되고, 임시로 캐시된 데이터가 데이터 저장소에 수집되어 CMK를 사용하여 암호화됩니다.

  - Key Vault 액세스 속도 - 래핑 및 래핑 해제 작업을 위해 Azure Monitor 스토리지에서 Key Vault에 액세스하는 빈도는 6-60초입니다.

- *클러스터* 리소스를 만들고 KeyVaultProperties를 즉시 지정하면 시스템 ID가 *클러스터* 리소스에 할당될 때까지 액세스 정책을 정의할 수 없으므로 작업이 실패할 수 있습니다.

- 기존 *클러스터* 리소스를 KeyVaultProperties로 업데이트하고 키 '가져오기' 액세스 정책이 Key Vault에 없으면 작업이 실패합니다.

- *클러스터* 리소스를 만들 때 충돌 오류가 발생하는 경우 – 일시 삭제 기간 내에 지난 14일 동안 *클러스터* 리소스를 삭제했을 수 있습니다. *클러스터* 리소스 이름은 일시 삭제 기간 동안 예약된 상태로 유지되며, 해당 이름을 사용하여 새 클러스터를 만들 수 없습니다. *클러스터* 리소스가 영구적으로 삭제되면 일시 삭제 기간 후에 이름이 해제됩니다.

- 작업이 진행되는 동안 *클러스터* 리소스를 업데이트하면 작업이 실패합니다.

- *클러스터* 리소스가 배포되지 않으면 Azure Key Vault, *클러스터* 리소스 및 연결된 Log Analytics 작업 영역이 동일한 지역에 있는지 확인합니다. 서로 다른 구독에 있을 수 있습니다.

- Key Vault에서 키 버전을 업데이트하고 *클러스터* 리소스의 새 키 식별자 세부 정보를 업데이트하지 않으면 Log Analytics 클러스터에서 이전 키를 계속 사용하므로 데이터에 액세스할 수 없게 됩니다. *클러스터* 리소스의 새 키 식별자 세부 정보를 업데이트하여 데이터 수집 및 데이터 쿼리 기능을 다시 시작합니다.

- 일부 *작업은 길고* 완료 하는 데 시간이 걸릴 수 있습니다 (클러스터 만들기, *클러스터* 키 업데이트 및 *클러스터* 삭제). 다음 두 가지 방법으로 작업 상태를 확인할 수 있습니다.
  1. REST를 사용 하는 경우 응답에서 AsyncOperation URL 값을 복사 하 고 [비동기 작업 상태 검사](#asynchronous-operations-and-status-check)를 수행 합니다.
  2. *클러스터* 또는 작업 영역에 GET 요청을 보내고 응답을 관찰 합니다. 예를 들어 연결이 끊긴 작업 영역에는 *기능*아래에 *clusterresourceid* 가 없습니다.

- 고객 관리형 키와 관련된 지원 및 도움을 받으려면 Microsoft에 문의하세요.

- 오류 메시지
  
  *클러스터* 리소스 만들기:
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

  *클러스터* 리소스 업데이트
  -  400--클러스터가 삭제 중 상태입니다. 비동기 작업이 진행 중입니다. 업데이트 작업을 수행 하기 전에 클러스터에서 해당 작업을 완료 해야 합니다.
  -  400--KeyVaultProperties가 비어 있지 않지만 형식이 잘못 되었습니다. [키 식별자 업데이트](#update-cluster-resource-with-key-identifier-details)를 참조 하세요.
  -  400--Key Vault의 키 유효성을 검사 하지 못했습니다. 권한이 부족 하거나 키가 없는 경우에 발생할 수 있습니다. Key Vault에서 [키 및 액세스 정책을 설정](#grant-key-vault-permissions) 했는지 확인 합니다.
  -  400--키를 복구할 수 없습니다. 일시 삭제 및 제거 보호로 Key Vault 설정 해야 합니다. [Key Vault 설명서](../../key-vault/general/soft-delete-overview.md) 를 참조 하세요.
  -  400-지금은 작업을 실행할 수 없습니다. 비동기 작업이 완료 될 때까지 기다린 후 다시 시도 하십시오.
  -  400--클러스터가 삭제 중 상태입니다. 비동기 작업이 완료 될 때까지 기다린 후 다시 시도 하십시오.

    *클러스터* 리소스 가져오기:
    -  404--클러스터가 없습니다. 클러스터가 삭제 되었을 수 있습니다. 해당 이름을 사용 하 여 클러스터를 만들려고 하 고 충돌 하는 경우 클러스터는 14 일 동안 일시 삭제 됩니다. 지원 서비스에 문의 하 여 복구 하거나 다른 이름을 사용 하 여 새 클러스터를 만들 수 있습니다. 

  *클러스터* 리소스 삭제
    -  409--프로 비전 상태에 있는 동안에는 클러스터를 삭제할 수 없습니다. 비동기 작업이 완료 될 때까지 기다린 후 다시 시도 하십시오.

  작업 영역 연결:
  -  404--작업 영역을 찾을 수 없습니다. 지정한 작업 영역이 존재 하지 않거나 삭제 되었습니다.
  -  409--작업 영역 연결 또는 연결 해제 작업을 진행 중입니다.
  -  400--클러스터를 찾을 수 없습니다. 지정한 클러스터가 없거나 삭제 되었습니다. 해당 이름을 사용 하 여 클러스터를 만들려고 하 고 충돌 하는 경우 클러스터는 14 일 동안 일시 삭제 됩니다. 지원 서비스에 문의 하 여 복구할 수 있습니다.

  작업 영역 연관 해제:
  -  404--작업 영역을 찾을 수 없습니다. 지정한 작업 영역이 존재 하지 않거나 삭제 되었습니다.
  -  409--작업 영역 연결 또는 연결 해제 작업을 진행 중입니다.
