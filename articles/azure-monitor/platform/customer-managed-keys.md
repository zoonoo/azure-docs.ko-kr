---
title: Azure Monitor 고객 관리형 키
description: CMK(고객 관리형 키)를 구성하여 Azure Key Vault 키를 사용하여 Log Analytics 작업 영역의 데이터를 암호화하는 방법에 대한 정보 및 단계입니다.
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 05/20/2020
ms.openlocfilehash: 037edb8af6e04a2ff65977a92a66482c9f4f880f
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83845101"
---
# <a name="azure-monitor-customer-managed-key"></a>Azure Monitor 고객 관리형 키 

이 문서에는 Log Analytics 작업 영역에 대한 CMK(고객 관리형 키)를 구성하는 방법에 대한 배경 정보와 단계가 나와 있습니다. 구성된 후에는 작업 영역으로 보낸 모든 데이터가 Azure Key Vault 키로 암호화됩니다.

구성하기 전에 아래 [제한 사항 및 제약 조건](#limitationsandconstraints)을 검토하는 것이 좋습니다.

## <a name="customer-managed-key-cmk-overview"></a>CMK(고객 관리형 키) 개요

[저장 데이터 암호화](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)는 조직의 일반적인 개인 정보 및 보안 요구 사항입니다. Azure에서 저장 데이터 암호화를 완전하게 관리할 수 있으며, 암호화 또는 암호화 키를 긴밀하게 관리하기 위한 다양한 옵션이 있습니다.

Azure Monitor는 모든 저장 데이터가 Azure 관리형 키를 사용하여 암호화되도록 합니다. 또한 Azure Monitor는  [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)에 저장되어 있고 시스템이 할당한  [관리 ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)  인증을 사용하여 스토리지를 통해 액세스되는 사용자 고유의 키를 사용하는 데이터 암호화 옵션을 제공합니다. 이 키는 [소프트웨어 또는 하드웨어 HSM 보호](https://docs.microsoft.com/azure/key-vault/key-vault-overview) 키 중 하나일 수 있습니다.

Azure Monitor의 암호화 사용은  [Azure Storage 암호화](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption)의  작동 방식과 동일합니다.

CMK를 사용하면 언제든지 데이터에 대한 액세스를 제어하고 철회할 수 있습니다. Azure Monitor 스토리지는 키 권한의 변경 내용을 항상 1시간 이내에 적용합니다. 또한 쿼리 엔진이 효율적으로 작동할 수 있도록 지난 14일 동안 수집된 데이터도 핫 캐시(SSD 지원)로 유지됩니다. 이 데이터는 CMK 구성에 관계없이 Microsoft 키로 암호화된 상태로 유지되지만 SSD 데이터에 대한 제어는  [키 해지](#cmk-kek-revocation)를 준수합니다. 2020년 하반기에는 CMK를 사용하여 SSD 데이터를 암호화하기 위해 노력하고 있습니다.

CMK 기능은 전용 Log Analytics 클러스터에서 제공됩니다. 사용자의 지역에 필요한 용량이 있는지 확인하려면 구독이 허용 목록에 이미 있어야 합니다. CMK 구성을 시작하기 전에 Microsoft 연락처를 사용하여 구독을 허용 목록에 추가하세요.

 [Log Analytics 클러스터 가격 책정 모델](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#log-analytics-dedicated-clusters)에서는 1,000GB/일 수준에서 시작하는 용량 예약을 사용합니다.

## <a name="how-cmk-works-in-azure-monitor"></a>Azure Monitor에서 CMK가 작동하는 방식

Azure Monitor는 시스템이 할당한 관리 ID를 활용하여 Azure Key Vault에 대한 액세스 권한을 부여합니다. 시스템이 할당한 관리 ID는 단일 Azure 리소스에만 연결될 수 있지만, Log Analytics 클러스터의 ID는 클러스터 수준에서 지원됩니다. 이는 CMK 기능이 전용 Log Analytics 클러스터에서 제공됨을 나타냅니다. 여러 작업 영역에서 CMK를 지원하기 위해 새 Log Analytics *클러스터* 리소스에서 Key Vault와 Log Analytics 작업 영역 간의 중간 ID 연결 역할을 수행합니다. Log Analytics 클러스터 스토리지는 *클러스터* 리소스와 연결된 관리 ID를 사용하여 Azure Active Directory를 통해 Azure Key Vault에 인증합니다. 

CMK가 구성되면 *클러스터* 리소스와 연결된 작업 영역으로 수집된 모든 데이터가 Key Vault의 키를 사용하여 암호화됩니다. 작업 영역 연결은 언제든지 *클러스터* 리소스에서 해제할 수 있습니다. 새 데이터는 Log Analytics 스토리지에 수집되고 Microsoft 키를 사용하여 암호화되지만, 새 데이터와 기존 데이터를 원활하게 쿼리할 수 있습니다.


![CMK 개요](media/customer-managed-keys/cmk-overview-8bit.png)

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

1. 허용 목록에 구독 추가 - CMK 기능은 전용 Log Analytics 클러스터에서 제공됩니다. 사용자의 지역에 필요한 용량이 있는지 확인하려면 구독이 허용 목록에 이미 있어야 합니다. Microsoft 연락처를 사용하여 구독을 허용 목록에 추가하세요.
2. Azure Key Vault 만들기 및 키 저장
3. *클러스터* 리소스 만들기
4. Key Vault에 권한 부여
5. Log Analytics 작업 영역 연결

이 절차는 현재 UI에서 지원되지 않으므로 프로비저닝 프로세스가 REST API를 통해 수행됩니다.

> [!IMPORTANT]
> 모든 API 요청에는 전달자 권한 부여 토큰이 요청 헤더에 포함되어야 합니다.

다음은 그 예입니다.

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
Authorization: Bearer eyJ0eXAiO....
```

여기서 *eyJ0eXAiO....* 는 모든 권한 부여 토큰을 나타냅니다. 

토큰은 다음 방법 중 하나를 사용하여 가져올 수 있습니다.

1. [앱 등록](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens) 메서드를 사용합니다.
2. Azure 포털에서 다음을 수행합니다.
    1. "개발자 도구"에 있는 동안 Azure Portal로 이동합니다(F12 키).
    1. "batch?api-version" 인스턴스 중 하나의 "요청 헤더" 아래에서 권한 부여 문자열을 찾습니다. "authorization: Bearer eyJ0eXAiO...."와 같습니다. 
    1. 아래 예제에 따라 이를 복사하여 API 호출에 추가합니다.
3. Azure REST 설명서 사이트로 이동합니다. API에서 "사용해 보세요"를 누르고 전달자 토큰을 복사합니다.

### <a name="asynchronous-operations-and-status-check"></a>비동기 작업 및 상태 검사

이 구성 절차의 작업 중 일부는 빨리 완료할 수 없으므로 비동기적으로 실행됩니다. 비동기 작업에 대한 응답은 처음에 *Azure-AsyncOperation* 속성이 포함된 200(OK) HTTP 상태 코드 및 헤더를 반환합니다.
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-03-01-preview"
```

GET 요청을 *Azure-AsyncOperation* 헤더 값으로 보내 비동기 작업의 상태를 확인할 수 있습니다.
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

### <a name="subscription-whitelisting"></a>허용 목록에 구독 추가

CMK 기능은 전용 Log Analytics 클러스터에서 제공됩니다. 사용자의 지역에 필요한 용량이 있는지 확인하려면 구독이 허용 목록에 이미 있어야 합니다. 연락처를 Microsoft에 사용하여 구독 ID를 제공합니다.

> [!IMPORTANT]
> CMK 기능은 지역별 기능입니다. Azure Key Vault, *클러스터* 리소스 및 연결된 Log Analytics 작업 영역은 동일한 지역에 있어야 하지만 서로 다른 구독에 있을 수 있습니다.

### <a name="storing-encryption-key-kek"></a>암호화 키(KEK) 저장

이미 생성해야 하는 Azure Key Vault를 만들거나 사용하거나 데이터 암호화에 사용할 키를 가져옵니다. Azure Monitor에서 키와 데이터에 대한 액세스를 보호하기 위해 Azure Key Vault를 복구 가능으로 구성해야 합니다. 이 구성은 Key Vault의 속성에서 확인할 수 있습니다. *일시 삭제* 및 *제거 보호*를 모두 사용하도록 설정되어 있어야 합니다.

![일시 삭제 및 제거 보호 설정](media/customer-managed-keys/soft-purge-protection.png)

다음 설정은 CLI 및 PowerShell을 통해 사용할 수 있습니다.
- [일시 삭제](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
- [제거 보호](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection)는 일시 삭제 후에도 비밀/자격 증명 모음을 강제로 삭제하지 않도록 방지합니다.

### <a name="create-cluster-resource"></a>*클러스터* 리소스 만들기

이 리소스는 Key Vault와 Log Analytics 작업 영역 간의 중간 ID 연결로 사용됩니다. 구독이 허용 목록에 추가되어 있음이 확인되면 작업 영역이 있는 지역에서 Log Analytics *클러스터* 리소스를 만듭니다.

*클러스터* 리소스를 만드는 경우 *용량 예약* 수준(sku)을 지정해야 합니다. *용량 예약* 수준은 하루 1,000-2,000GB일 수 있으며, 나중에 100GB 단위로 업데이트할 수 있습니다. 하루 2,000GB보다 높은 용량 예약 수준이 필요한 경우 LAIngestionRate@microsoft.com에 문의하세요. [자세히 알아보기](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#log-analytics-clusters)

*billingType* 속성은 *클러스터* 리소스와 해당 데이터에 대한 청구 특성을 결정합니다.
- *cluster*(기본값) - *클러스터* 리소스를 호스팅하는 구독을 기반으로 하는 청구입니다.
- *workspaces* - 작업 영역을 비례적으로 호스팅하는 구독을 기반으로 하는 청구입니다.

> [!NOTE]
> *클러스터* 리소스를 만든 후에 PATCH REST 요청을 사용하여 해당 리소스를 *sku*, *keyVaultProperties* 또는 *billingType*으로 업데이트할 수 있습니다.

**만들기**

이 Resource Manager 요청은 비동기 작업입니다.

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

**응답**

200 OK 및 헤더입니다.

Log Analytics 클러스터 프로비저닝을 완료하는 데 시간이 걸리지만 다음 두 가지 방법으로 프로비저닝 상태를 확인할 수 있습니다.

1. 응답에서 Azure-AsyncOperation URL 값을 복사하고 [비동기 작업 상태 검사](#asynchronous-operations-and-status-check)를 수행합니다.
2. GET 요청을 *클러스터* 리소스에 보내고 *provisioningState* 값을 확인합니다. 프로비저닝 중이면 *ProvisioningAccount*이고, 완료되면 *Succeeded*입니다.

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

> [!IMPORTANT]
> 다음 단계에서 세부 정보가 필요하므로 응답을 복사하여 저장합니다.

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
    "clusterType": "LogAnalytics",
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

권한을 *클러스터* 리소스에 부여하는 새 액세스 정책을 사용하여 Key Vault를 업데이트합니다. 이러한 권한은 기본 Azure Monitor 스토리지에서 데이터를 암호화하는 데 사용됩니다. Azure Portal에서 Key Vault를 열고, "액세스 정책"을 클릭한 다음, "+ 액세스 정책 추가"를 클릭하여 다음 설정을 통해 정책을 만듭니다.

- 키 권한: '가져오기', '키 래핑' 및 '키 래핑 해제' 권한을 선택합니다.
- 보안 주체 선택: 이전 단계의 응답에서 반환된 principal-id 값을 입력합니다.

![Key Vault 권한 부여](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

Azure Monitor 데이터에 대한 액세스와 키를 보호하기 위해 Key Vault가 복구 가능으로 구성되었는지 확인하려면 *가져오기* 권한이 필요합니다.

### <a name="update-cluster-resource-with-key-identifier-details"></a>키 식별자 세부 정보를 사용하여 클러스터 리소스 업데이트

이 단계는 Key Vault에서 초기 및 향후 키 버전을 업데이트하는 중에 수행됩니다. 데이터 암호화에 사용할 키 버전에 대해 Azure Monitor 스토리지에 알립니다. 업데이트되면 새 키를 사용하여 AEK(스토리지 키)에 래핑 및 래핑 해제합니다.

*클러스터* 리소스를 Key Vault *키 식별자* 세부 정보로 업데이트하려면 Azure Key Vault에서 키의 현재 버전을 선택하여 키 식별자 세부 정보를 가져옵니다.

![Key Vault 권한 부여](media/customer-managed-keys/key-identifier-8bit.png)

*클러스터* 리소스의 KeyVaultProperties를 키 식별자 세부 정보로 업데이트합니다.

**Update**

키 식별자 세부 정보를 업데이트할 때 이 Resource Manager 요청은 비동기 작업이지만 [용량] 값을 업데이트할 때는 비동기 작업입니다.

> [!NOTE]
> 부분 본문을 *클러스터* 리소스에 제공하여 *sku*, *keyVaultProperties* 또는 *billingType*을 업데이트할 수 있습니다.

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

**응답**

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
    "clusterType": "LogAnalytics", 
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

**작업 영역 연결**

이 Resource Manager 요청은 비동기 작업입니다.

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
2. [작업 영역 – 가져오기](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) 요청을 보내고 응답을 관찰합니다. 연결된 작업 영역의 "features" 아래에 clusterResourceId가 있습니다.

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

## <a name="cmk-manage"></a>CMK 관리

- **리소스 그룹에 대한 모든 *클러스터* 리소스 가져오기**

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **응답**
  
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
          "clusterType": "LogAnalytics", 
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

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```
    
  **응답**
    
  리소스 그룹의 '*클러스터* 리소스'와 동일한 응답이지만 구독 범위에 있습니다.

- ***클러스터* 리소스의 *용량 예약* 업데이트**

  연결된 작업 영역의 데이터 볼륨이 시간이 지남에 따라 변경되고 용량 예약 수준을 적절하게 업데이트하려는 경우입니다. [*클러스터* 리소스 업데이트](#update-cluster-resource-with-key-identifier-details)에 따라 새 용량 값을 제공합니다. 하루 1,000-2,000GB의 범위 및 100GB 단위 업데이트를 적용할 수 있습니다. 하루 2,000GB보다 높은 수준의 경우 Microsoft 연락처에 문의하여 이 수준을 사용하도록 설정합니다. 전체 REST 요청 본문을 제공할 필요는 없으며 sku를 포함해야 합니다.

  **body**
  ```json
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

  **body**
  ```json
  {
    "properties": {
      "billingType": "cluster",
      }  
  }
  ``` 

- **작업 영역 연결 해제**

  이 작업을 수행하려면 작업 영역 및 *클러스터* 리소스에 대한 '쓰기' 권한이 필요합니다. 작업 영역 연결은 언제든지 *클러스터* 리소스에서 해제할 수 있습니다. 연결 해제 작업 후에 새로 수집된 데이터는 Log Analytics 스토리지에 저장되고 Microsoft 키를 사용하여 암호화됩니다. *클러스터* 리소스가 프로비저닝되고 유효한 Key Vault 키로 구성되어 있으면 연결 해제 전후에 작업 영역에 수집된 데이터를 원활하게 쿼리할 수 있습니다.

  이 Resource Manager 요청은 비동기 작업입니다.

  ```rest
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **응답**

  200 OK 및 헤더입니다.

  연결 해제 작업 후에 수집된 데이터는 Log Analytics 스토리지에 저장됩니다. 이를 완료하는 데 90분 정도 걸릴 수 있습니다. 작업 영역 연결 해제 상태는 다음 두 가지 방법으로 확인할 수 있습니다.

  1. 응답에서 Azure-AsyncOperation URL 값을 복사하고 [비동기 작업 상태 검사](#asynchronous-operations-and-status-check)를 수행합니다.
  2. [작업 영역 – 가져오기](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) 요청을 보내고 응답을 관찰합니다. 연결 해제된 작업 영역의 *features* 아래에 *clusterResourceId*가 없습니다.

- ***클러스터* 리소스 삭제**

  이 작업을 수행하려면 *클러스터* 리소스에 대한 '쓰기' 권한이 필요합니다. 실수로 또는 의도적으로 삭제되었는지 여부에 관계없이 14일 이내에 데이터를 포함하여 *클러스터* 리소스를 복구할 수 있도록 일시 삭제 작업이 수행됩니다. *클러스터* 리소스 이름은 일시 삭제 기간 동안 예약된 상태로 유지되며, 해당 이름을 사용하여 새 클러스터를 만들 수 없습니다. 일시 삭제 기간이 지나면 *클러스터* 리소스 이름이 해제되고, *클러스터* 리소스와 데이터가 영구적으로 삭제되어 복구할 수 없습니다. 삭제 작업 시 연결된 모든 작업 영역이 *클러스터* 리소스에서 연결 해제됩니다. 새로 수집된 데이터는 Log Analytics 스토리지에 저장되고 Microsoft 키를 사용하여 암호화됩니다. 작업 영역 연결 해제 작업은 비동기적이며, 이 작업을 완료하는 데 최대 90분까지 걸릴 수 있습니다.

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **응답**

  200 정상

- ***클러스터* 리소스 및 데이터 복구** 
  
  지난 14일 동안 삭제된 *클러스터* 리소스는 일시 삭제 상태에 있으므로 해당 데이터를 사용하여 복구할 수 있습니다. 모든 작업 영역이 *클러스터* 리소스 삭제를 통해 *클러스터* 리소스에서 연결 해제되었으므로 CMK 암호화를 복구한 후에 작업 영역에 다시 연결해야 합니다. 복구 작업은 현재 제품 그룹에서 수동으로 수행됩니다. Microsoft 채널을 복구 요청에 사용합니다.

## <a name="limitationsandconstraints"></a>제한 사항 및 제약 조건

\- CMK는 전용 Log Analytics 클러스터에서 지원되며, 하루 1TB 이상을 보내는 고객에게 적합합니다.

\- 지역 및 구독당 최대 *클러스터* 리소스 수는 2개입니다.

\- 작업 영역에 CMK가 필요하지 않은 경우 작업 영역을 *클러스터* 리소스에 연결한 다음, 연결을 해제할 수 있습니다. 30일 기간 동안 특정 작업 영역의 작업 영역 연결 수는 2개로 제한됩니다.

\- Log Analytics 클러스터 프로비저닝이 완료되었음을 확인한 후에만  *클러스터*  리소스에 대한 작업 영역을 연결해야 합니다. 완료된 후에는 작업 영역으로 보낸 데이터를 삭제하고 복구할 수 있습니다.

\- CMK가 구성되면     CMK 암호화가 새로 수집된 데이터에 적용됩니다. CMK가 구성되기 전에 수집된 데이터는    Microsoft 키를 사용하여 암호화된 상태로 유지됩니다. CMK가 구성되기 전후에 수집된 데이터는     원활하게 쿼리할 수 있습니다.

\- Azure Key Vault는 복구 가능으로 구성해야 합니다. 이러한 속성은 기본적으로 사용하도록 설정되지 않으므로 CLI 또는 PowerShell을 사용하여 구성해야 합니다.   - [일시 삭제](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
    설정해야 합니다.   - [제거 보호](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) 일시 삭제 후에도 비밀/자격 증명 모음이 강제로 삭제되지 않도록 방지하려면 설정해야 합니다.

- *클러스터* 리소스가 다른 리소스 그룹으로 이동하거나     구독이 현재 지원되지 않습니다.

\- Azure Key Vault, *클러스터* 리소스 및 연결된 작업 영역은 동일한 지역 및 동일한 Azure AD(Azure Active Directory) 테넌트에 있어야 하지만 서로 다른 구독에 있을 수 있습니다.

\- 다른 *클러스터* 리소스에 연결되면      *클러스터* 리소스에 대한 작업 영역 연결이 실패합니다.

## <a name="troubleshooting"></a>문제 해결

- Key Vault 가용성과 관련된 동작
  - 정상 작업에서 - 스토리지에서 AEK를 짧은 시간 동안 캐시하고, Key Vault로 돌아가서 래핑을 주기적으로 해제합니다.
    
  - 일시적인 연결 오류 - 스토리지에서 키를 짧은 시간 동안 캐시에서 유지할 수 있도록 하여 일시적인 오류(시간 제한, 연결 실패, DNS 문제)를 처리하고, 이로 인한 사소한 가용성 문제도 해결합니다. 쿼리 및 수집 기능은 중단 없이 계속됩니다.
    
  - 라이브 사이트 - 약 30분 동안 사용할 수 없으면 스토리지 계정을 사용할 수 없게 됩니다. 쿼리 기능을 사용할 수 없으며, 수집된 데이터는 데이터 손실을 방지하기 위해 Microsoft 키를 사용하여 몇 시간 동안 캐시됩니다. Key Vault에 대한 액세스가 복원되면 쿼리를 사용할 수 있게 되고, 임시로 캐시된 데이터가 데이터 저장소에 수집되어 CMK를 사용하여 암호화됩니다.

  - Key Vault 액세스 속도 - 래핑 및 래핑 해제 작업을 위해 Azure Monitor 스토리지에서 Key Vault에 액세스하는 빈도는 6-60초입니다.

- *클러스터* 리소스를 만들고 KeyVaultProperties를 즉시 지정하면 시스템 ID가 *클러스터* 리소스에 할당될 때까지 액세스 정책을 정의할 수 없으므로 작업이 실패할 수 있습니다.

- 기존 *클러스터* 리소스를 KeyVaultProperties로 업데이트하고 키 '가져오기' 액세스 정책이 Key Vault에 없으면 작업이 실패합니다.

- 작업 영역에 연결된 *클러스터* 리소스를 삭제하려고 하면 삭제 작업이 실패합니다.

- *클러스터* 리소스를 만들 때 충돌 오류가 발생하는 경우 – 일시 삭제 기간 내에 지난 14일 동안 *클러스터* 리소스를 삭제했을 수 있습니다. *클러스터* 리소스 이름은 일시 삭제 기간 동안 예약된 상태로 유지되며, 해당 이름을 사용하여 새 클러스터를 만들 수 없습니다. *클러스터* 리소스가 영구적으로 삭제되면 일시 삭제 기간 후에 이름이 해제됩니다.

- 작업이 진행되는 동안 *클러스터* 리소스를 업데이트하면 작업이 실패합니다.

- *클러스터* 리소스가 배포되지 않으면 Azure Key Vault, *클러스터* 리소스 및 연결된 Log Analytics 작업 영역이 동일한 지역에 있는지 확인합니다. 서로 다른 구독에 있을 수 있습니다.

- Key Vault에서 키 버전을 업데이트하고 *클러스터* 리소스의 새 키 식별자 세부 정보를 업데이트하지 않으면 Log Analytics 클러스터에서 이전 키를 계속 사용하므로 데이터에 액세스할 수 없게 됩니다. *클러스터* 리소스의 새 키 식별자 세부 정보를 업데이트하여 데이터 수집 및 데이터 쿼리 기능을 다시 시작합니다.

- 고객 관리형 키와 관련된 지원 및 도움을 받으려면 Microsoft에 문의하세요.
