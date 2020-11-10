---
title: Azure Monitor 고객 관리형 키
description: Azure Key Vault 키를 사용 하 여 Log Analytics 작업 영역에서 데이터를 암호화 하도록 Customer-Managed 키를 구성 하는 방법에 대 한 정보 및 단계입니다.
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 11/09/2020
ms.openlocfilehash: 7f62aade114613261a22a818ab47e096eb16084b
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94427975"
---
# <a name="azure-monitor-customer-managed-key"></a>Azure Monitor 고객 관리형 키 

이 문서에서는 Log Analytics 작업 영역에 대 한 고객 관리 키를 구성 하는 단계 및 배경 정보를 제공 합니다. 구성된 후에는 작업 영역으로 보낸 모든 데이터가 Azure Key Vault 키로 암호화됩니다.

구성하기 전에 아래 [제한 사항 및 제약 조건](#limitationsandconstraints)을 검토하는 것이 좋습니다.

## <a name="customer-managed-key-overview"></a>고객 관리 키 개요

[미사용 암호화](../../security/fundamentals/encryption-atrest.md) 는 조직의 일반적인 개인 정보 및 보안 요구 사항입니다. 암호화 또는 암호화 키를 긴밀 하 게 관리 하는 다양 한 옵션을 사용 하 여 Azure에서 미사용 암호화를 완전히 관리할 수 있습니다.

Azure Monitor를 사용 하면 모든 데이터 및 저장 된 쿼리가 Microsoft 관리 키 (MMK)를 사용 하 여 미사용 상태로 암호화 됩니다. Azure Monitor은 [Azure Key Vault](../../key-vault/general/overview.md) 에 저장 되 고 데이터 암호화를 위해 저장소에서 사용 하는 고유한 키를 사용 하 여 암호화 옵션을 제공 합니다. 키는 [소프트웨어 또는 하드웨어 HSM으로 보호](../../key-vault/general/overview.md)될 수 있습니다. 암호화 사용 Azure Monitor는 암호화가 작동 하는 [Azure Storage](../../storage/common/storage-service-encryption.md#about-azure-storage-encryption) 방식과 동일 합니다.

고객 관리 키 기능은 전용 Log Analytics 클러스터에서 제공 됩니다. 이를 통해 [Lockbox](#customer-lockbox-preview) 제어를 사용 하 여 데이터를 보호할 수 있으며 언제 든 지 데이터에 대 한 액세스를 취소할 수 있습니다. 또한 쿼리 엔진이 효율적으로 작동할 수 있도록 지난 14일 동안 수집된 데이터도 핫 캐시(SSD 지원)로 유지됩니다. 이 데이터는 고객 관리 키 구성에 관계 없이 Microsoft 키를 사용 하 여 암호화 된 상태로 유지 되지만 SSD 데이터에 대 한 제어는 [키 해지](#key-revocation)를 따릅니다. 2021의 처음 절반에서 Customer-Managed 키로 SSD 데이터를 암호화 하기 위해 노력 하 고 있습니다.

지역에서 전용 클러스터를 프로 비전 하는 데 필요한 용량이 있는지 확인 하려면 구독이 미리 허용 되어야 합니다. Customer-Managed 키 구성을 시작 하기 전에 Microsoft 연락처 또는 오픈 지원 요청을 사용 하 여 구독을 허용 하세요.

[Log Analytics 클러스터 가격 책정 모델](./manage-cost-storage.md#log-analytics-dedicated-clusters) 은 1000 g b/일 수준부터 용량 예약을 사용 합니다.

## <a name="how-customer-managed-key-works-in-azure-monitor"></a>Azure Monitor에서 Customer-Managed 키가 작동 하는 방식

Azure Monitor는 시스템이 할당한 관리 ID를 활용하여 Azure Key Vault에 대한 액세스 권한을 부여합니다. 시스템 할당 관리 id는 클러스터 수준에서 Log Analytics 클러스터의 id가 지원 되는 동안 단일 Azure 리소스와만 연결할 수 있습니다 .이는 기능이 전용 Log Analytics 클러스터에서 제공 됨을 나타냅니다. 여러 작업 영역에서 Customer-Managed 키를 지원 하기 위해 새 Log Analytics *클러스터* 리소스는 Key Vault와 Log Analytics 작업 영역 간에 중간 id 연결로 수행 됩니다. Log Analytics 클러스터 스토리지는 *클러스터* 리소스와 연결된 관리 ID를 사용하여 Azure Active Directory를 통해 Azure Key Vault에 인증합니다. 

구성 후 전용 클러스터에 연결 된 작업 영역에 대 한 모든 데이터 수집 Key Vault의 키를 사용 하 여 암호화 됩니다. 언제 든 지 클러스터에서 작업 영역의 연결을 해제할 수 있습니다. 새 데이터는 수집 저장소 및 Microsoft 키를 사용 Log Analytics 하 여 암호화 되는를 가져오며, 새 데이터와 이전 데이터를 원활 하 게 쿼리할 수 있습니다.


![Customer-Managed 키 개요](media/customer-managed-keys/cmk-overview.png)

1. Key Vault
2. Key Vault에 대한 권한이 있는 관리 ID를 사용하는 Log Analytics *클러스터* 리소스 - 이 ID는 기본 전용 Log Analytics 클러스터 스토리지로 전파됩니다.
3. 전용 Log Analytics 클러스터
4. *클러스터* 리소스에 연결 된 작업 영역 

## <a name="encryption-keys-operation"></a>암호화 키 작업

스토리지 데이터 암호화에는 세 가지 유형의 키가 있습니다.

- **KEK** -키 암호화 키 (사용자 Customer-Managed 키)
- **AEK** - 계정 암호화 키
- **DEK** - 데이터 암호화 키

다음 규칙이 적용됩니다.

- Log Analytics 클러스터 저장소 계정은 AEK로 알려진 모든 저장소 계정에 대해 고유한 암호화 키를 생성 합니다.
- AEK는 디스크에 기록 되는 데이터의 각 블록을 암호화 하는 데 사용 되는 키인 DEKs를 파생 하는 데 사용 됩니다.
- Key Vault에서 키를 구성 하 고 클러스터에서 참조 하는 경우 Azure Storage는 데이터 암호화 및 암호 해독 작업을 수행 하기 위해 AEK를 래핑하고 래핑 해제 하는 요청을 Azure Key Vault 보냅니다.
- KEK는 Key Vault을 유지 하지 않으며 HSM 키의 경우 하드웨어를 그대로 유지 합니다.
- Azure Storage는 *클러스터* 리소스와 연결 된 관리 되는 id를 사용 하 여 인증 하 고 Azure Active Directory를 통해 Azure Key Vault에 액세스 합니다.

## <a name="customer-managed-key-provisioning-procedure"></a>Customer-Managed 키 프로 비전 절차

1. 구독 허용-이 기능은 전용 Log Analytics 클러스터에서 제공 됩니다. 사용자의 지역에 필요한 용량이 있는지 확인 하려면 구독이 미리 허용 되어야 합니다. Microsoft 연락처를 사용 하 여 구독을 허용 하세요.
2. Azure Key Vault 만들기 및 키 저장
3. 클러스터를 만드는 중
4. Key Vault에 권한 부여
5. Log Analytics 작업 영역 연결

Customer-Managed 키 구성은 Azure Portal에서 지원 되지 않으며 프로 비전은 [PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/), [CLI](https://docs.microsoft.com/cli/azure/monitor/log-analytics) 또는 [REST](https://docs.microsoft.com/rest/api/loganalytics/) 요청을 통해 수행 됩니다.

### <a name="asynchronous-operations-and-status-check"></a>비동기 작업 및 상태 검사

일부 구성 단계는 신속 하 게 완료할 수 없기 때문에 비동기적으로 실행 됩니다. 구성에서 REST 요청을 사용 하는 경우 응답은 처음에 허용 되는 경우 *Azure-AsyncOperation* 속성을 사용 하 여 HTTP 상태 코드 200 (OK) 및 헤더를 반환 합니다.
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-08-01"
```

그런 다음 GET 요청을 *Azure AsyncOperation* 헤더 값에 보내서 비동기 작업의 상태를 확인할 수 있습니다.
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-08-01
Authorization: Bearer <token>
```

응답에는 작업 및 해당 *상태* 에 대한 정보가 포함됩니다. 다음 중 하나일 수 있습니다.

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

클러스터 삭제가 진행 중입니다. 연결 된 작업 영역이 있는 클러스터를 삭제 하면 각 작업 영역에 대해 연결 끊기 작업이 비동기적으로 수행 되 고 작업에 시간이 오래 걸릴 수 있습니다.
연결 된 작업 영역이 없는 클러스터를 삭제 하는 경우에는 관련이 없습니다 .이 경우 클러스터가 즉시 삭제 됩니다.
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

### <a name="allowing-subscription"></a>구독 허용

> [!IMPORTANT]
> Customer-Managed 키 기능은 지역입니다. Azure Key Vault, 클러스터 및 연결 된 Log Analytics 작업 영역은 동일한 지역에 있어야 하지만 다른 구독에 있을 수 있습니다.
> 지역에서 전용 클러스터를 프로 비전 하는 데 필요한 용량이 있는지 확인 하려면 구독이 미리 허용 되어야 합니다. Customer-Managed 키 구성을 시작 하기 전에 Microsoft 연락처 또는 오픈 지원 요청을 사용 하 여 구독을 허용 하세요. 

### <a name="storing-encryption-key-kek"></a>암호화 키(KEK) 저장

이미 생성해야 하는 Azure Key Vault를 만들거나 사용하거나 데이터 암호화에 사용할 키를 가져옵니다. Azure Monitor에서 키와 데이터에 대한 액세스를 보호하기 위해 Azure Key Vault를 복구 가능으로 구성해야 합니다. 이 구성은 Key Vault의 속성에서 확인할 수 있습니다. *일시 삭제* 및 *제거 보호* 를 모두 사용하도록 설정되어 있어야 합니다.

![일시 삭제 및 제거 보호 설정](media/customer-managed-keys/soft-purge-protection.png)

이러한 설정은 CLI 및 PowerShell을 통해 Key Vault에서 업데이트할 수 있습니다.

- [일시 삭제](../../key-vault/general/soft-delete-overview.md)
- [제거 보호](../../key-vault/general/soft-delete-overview.md#purge-protection)는 일시 삭제 후에도 비밀/자격 증명 모음을 강제로 삭제하지 않도록 방지합니다.

### <a name="create-cluster"></a>클러스터 만들기

[전용 클러스터 문서](https://docs.microsoft.com/azure/azure-monitor/log-query/logs-dedicated-clusters#creating-a-cluster)에 설명 된 절차를 따릅니다. 

> [!IMPORTANT]
> 다음 단계에서 세부 정보가 필요하므로 응답을 복사하여 저장합니다.

### <a name="grant-key-vault-permissions"></a>Key Vault 권한 부여

클러스터에 대 한 사용 권한을 부여 하는 Key Vault에 대 한 액세스 정책을 만듭니다. 이러한 권한은 기본 Azure Monitor 스토리지에서 데이터를 암호화하는 데 사용됩니다. Azure Portal에서 Key Vault를 열고, "액세스 정책"을 클릭한 다음, "+ 액세스 정책 추가"를 클릭하여 다음 설정을 통해 정책을 만듭니다.

- 키 권한: '가져오기', '키 래핑' 및 '키 래핑 해제' 권한을 선택합니다.
- 보안 주체 선택: 이전 단계의 응답에서 반환 된 클러스터 이름 또는 사용자 id 값을 입력 합니다.

![Key Vault 권한 부여](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

Azure Monitor 데이터에 대한 액세스와 키를 보호하기 위해 Key Vault가 복구 가능으로 구성되었는지 확인하려면 *가져오기* 권한이 필요합니다.

### <a name="update-cluster-with-key-identifier-details"></a>키 식별자 정보를 사용 하 여 클러스터 업데이트

클러스터의 모든 작업에는 OperationalInsights/cluster/write action 권한이 필요 합니다. *OperationalInsights/action을 포함 하는 Log Analytics 참여자 역할을 통해/쓰기 작업이* 포함 된 소유자 또는 참가자를 통해이 사용 권한을 부여할 수 있습니다.

이 단계에서는 데이터 암호화에 사용할 키와 버전으로 Azure Monitor 저장소를 업데이트 합니다. 업데이트 된 경우 새 키를 사용 하 여 저장소 키 (AEK)를 래핑하고 래핑 해제 합니다.

키 식별자 정보를 가져오려면 Azure Key Vault에서 키의 현재 버전을 선택 합니다.

![Key Vault 권한 부여](media/customer-managed-keys/key-identifier-8bit.png)

키 식별자 세부 정보를 사용 하 여 클러스터에서 KeyVaultProperties를 업데이트 합니다.

작업은 비동기적 이며 완료 하는 데 다소 시간이 걸릴 수 있습니다.

```azurecli
az monitor log-analytics cluster update --name "cluster-name" --resource-group "resource-group-name" --key-name "key-name" --key-vault-uri "key-uri" --key-version "key-version"
```

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -KeyVaultUri "key-uri" -KeyName "key-name" -KeyVersion "key-version"
```

**Response**

키 식별자의 전파를 완료하는 데 몇 분 정도 걸립니다. 업데이트 상태는 다음 두 가지 방법으로 확인할 수 있습니다.
1. 응답에서 Azure-AsyncOperation URL 값을 복사하고 [비동기 작업 상태 검사](#asynchronous-operations-and-status-check)를 수행합니다.
2. 클러스터에 GET 요청을 보내고 *KeyVaultProperties* 속성을 확인 합니다. 최근에 업데이트한 키 식별자 세부 정보가 응답으로 반환됩니다.

키 식별자 업데이트가 완료 되 면 GET 요청에 대 한 응답이 다음과 같이 표시 됩니다. 200 OK and header
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

### <a name="link-workspace-to-cluster"></a>클러스터에 작업 영역 연결

이 작업을 수행 하려면 작업 영역 및 클러스터 모두에 ' 쓰기 ' 권한이 있어야 합니다. 여기에는 다음 작업이 포함 됩니다.

- 작업 영역의 경우: Microsoft.OperationalInsights/workspaces/write
- 클러스터에서: OperationalInsights/클러스터/쓰기

> [!IMPORTANT]
> 이 단계는 Log Analytics 클러스터 프로비저닝이 완료된 후에만 수행해야 합니다. 프로 비전 전에 작업 영역을 연결 하 고 데이터를 수집 하는 경우 수집 데이터는 삭제 되 고 복구할 수 없습니다.

이 작업은 비동기 작업 이므로 완료 하는 데는 몇를 사용할 수 있습니다.

[전용 클러스터 문서](https://docs.microsoft.com/azure/azure-monitor/log-query/logs-dedicated-clusters#link-a-workspace-to-the-cluster)에 설명 된 절차를 따릅니다.

## <a name="key-revocation"></a>키 해지

키를 사용 하지 않도록 설정 하거나 Key Vault에서 클러스터의 액세스 정책을 삭제 하 여 데이터에 대 한 액세스 권한을 해지할 수 있습니다. Log Analytics 클러스터 스토리지는 키 권한의 변경 내용을 항상 1시간 이내에 적용합니다. 이로 인해 스토리지가 사용할 수 없게 됩니다. 클러스터와 연결 된 작업 영역에 대 한 모든 새 데이터 수집는 삭제 되 고 복구할 수 없으며, 데이터에 액세스할 수 없고 이러한 작업 영역에 대 한 쿼리가 실패 합니다. 이전에 수집 데이터는 클러스터와 작업 영역을 삭제 하지 않는 한 저장소에 남아 있습니다. 액세스할 수 없는 데이터는 데이터 보존 정책에 따라 제어되며 보존 기간에 도달하면 삭제됩니다. 

또한 쿼리 엔진이 효율적으로 작동할 수 있도록 지난 14일 동안 수집된 데이터도 핫 캐시(SSD 지원)로 유지됩니다. 이는 키 해지 작업에서 삭제되며 액세스할 수 없게 됩니다.

스토리지는 Key Vault를 주기적으로 폴링하여 암호화 키를 래핑 해제하려고 시도하며, 액세스한 후에는 30분 이내에 데이터 수집 및 쿼리가 다시 시작됩니다.

## <a name="key-rotation"></a>키 회전

Customer-Managed 키를 회전 하려면 Azure Key Vault에서 새 키 버전을 사용 하는 클러스터에 대 한 명시적 업데이트가 필요 합니다. "키 식별자 세부 정보를 사용 하 여 클러스터 업데이트" 단계의 지침을 따르세요. 클러스터에서 새 키 식별자 세부 정보를 업데이트 하지 않으면 Log Analytics 클러스터 저장소는 암호화를 위해 이전 키를 계속 사용 합니다. 클러스터의 새 키를 업데이트 하기 전에 이전 키를 사용 하지 않도록 설정 하거나 삭제 하면 [키 해지](#key-revocation) 상태가 됩니다.

AEK는 이제 Key Vault의 새 KEK(키 암호화 키) 버전을 사용하여 암호화되지만, 데이터는 항상 AEK(계정 암호화 키)를 사용하여 암호화되므로 키 회전 작업 후에도 모든 데이터에 계속 액세스할 수 있습니다.

## <a name="customer-managed-key-for-queries"></a>쿼리에 대 한 Customer-Managed 키

Log Analytics에 사용 되는 쿼리 언어는 표현 되며 쿼리에 추가 하는 설명 또는 쿼리 구문에 중요 한 정보를 포함할 수 있습니다. 일부 조직에서는 Customer-Managed 키 정책에서 이러한 정보를 보호 된 상태로 유지 해야 하며, 암호화 된 쿼리를 키로 저장 해야 합니다. Azure Monitor를 사용 하면 작업 영역에 연결 될 때 사용자 고유의 저장소 계정에서 키로 암호화 된 *저장 된 검색* 및 *로그 경고* 쿼리를 저장할 수 있습니다. 

> [!NOTE]
> Log Analytics 쿼리는 사용 된 시나리오에 따라 다양 한 저장소에 저장할 수 있습니다. Customer-Managed 키 구성: Azure Monitor, Azure 대시보드, Azure 논리 앱, Azure Notebooks 및 자동화 Runbook의 통합 문서에 관계 없이 쿼리는 Microsoft key (MMK)를 사용 하 여 암호화 된 상태로 유지 됩니다.

사용자 고유의 저장소 (BYOS)를 가져와서 작업 영역에 연결 하는 경우 서비스는 *저장 된 검색* 및 *로그 경고* 쿼리를 저장소 계정에 업로드 합니다. 즉, Log Analytics 클러스터의 데이터를 암호화 하는 데 사용 하는 것과 동일한 키를 사용 하거나 다른 키를 사용 하 여 저장소 계정 및 [미사용 암호화 정책을](../../storage/common/customer-managed-keys-overview.md) 제어할 수 있습니다. 그러나 해당 저장소 계정과 관련 된 비용을 담당 하 게 됩니다. 

**쿼리에 대해 Customer-Managed 키를 설정 하기 전의 고려 사항**
* 작업 영역 및 저장소 계정에 대 한 ' 쓰기 ' 권한이 있어야 합니다.
* Log Analytics 작업 영역이 있는 동일한 지역에 저장소 계정을 만들어야 합니다.
* 저장소의 *저장 검색* 은 서비스 아티팩트로 간주 되 고 형식은 변경 될 수 있습니다.
* 기존 *저장 검색* 은 작업 영역에서 제거 됩니다. 복사 및는 구성 전에 필요한 *검색을 저장* 합니다. [PowerShell](/powershell/module/az.operationalinsights/get-azoperationalinsightssavedsearch) 을 사용 하 여 *저장 된 검색* 을 볼 수 있습니다.
* 쿼리 기록은 지원 되지 않으므로 실행 한 쿼리를 볼 수 없습니다.
* 쿼리 저장을 위해 단일 저장소 계정을 작업 영역에 연결할 수 있지만 *저장 된 검색* 및 *로그 경고* 쿼리를 가져오면 사용할 수 있습니다.
* 대시보드에 고정은 지원 되지 않습니다.

**저장 된 검색 쿼리를 위한 BYOS 구성**

*쿼리에* 사용할 저장소 계정을 작업 영역에 연결 합니다. *저장 된 검색* 쿼리는 저장소 계정에 저장 됩니다. 

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Query -StorageAccountIds $storageAccount.Id
```

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Query?api-version=2020-08-01
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
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Alerts?api-version=2020-08-01
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

## <a name="customer-lockbox-preview"></a>고객 Lockbox (미리 보기)
Lockbox는 지원 요청 중에 데이터에 액세스 하는 Microsoft 엔지니어 요청을 승인 하거나 거부할 수 있는 컨트롤을 제공 합니다.

Azure Monitor에서 Log Analytics 전용 클러스터에 연결 된 작업 영역의 데이터에 대해이 컨트롤을 사용할 수 있습니다. Lockbox 컨트롤은 Log Analytics 전용 클러스터에 저장 된 데이터에 적용 되며,이는 Lockbox로 보호 되는 구독에서 클러스터의 저장소 계정에 격리 된 상태를 유지 합니다.  

[Microsoft Azure 고객 Lockbox](../../security/fundamentals/customer-lockbox-overview.md) 에 대 한 자세한 정보

## <a name="customer-managed-key-operations"></a>Customer-Managed 키 작업

- **리소스 그룹의 모든 클러스터 가져오기**
  
  ```azurecli
  az monitor log-analytics cluster list --resource-group "resource-group-name"
  ```

  ```powershell
  Get-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name"
  ```

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
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

- **구독의 모든 클러스터 가져오기**

  ```azurecli
  az monitor log-analytics cluster list
  ```

  ```powershell
  Get-AzOperationalInsightsCluster
  ```

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
  Authorization: Bearer <token>
  ```
    
  **Response**
    
  ' 리소스 그룹의 클러스터 '의 경우와 동일한 응답이 구독 범위에 있습니다.

- **클러스터에서 *용량 예약* 업데이트**

  연결 된 작업 영역에 대 한 데이터 볼륨이 시간이 지남에 따라 변경 되 고 용량 예약 수준을 적절 하 게 업데이트 하려는 경우. [업데이트 클러스터](#update-cluster-with-key-identifier-details) 를 따르고 새 용량 값을 제공 합니다. 1000 ~ 3000 g b의 범위와 100의 단계에 있을 수 있습니다. 하루 3000 g b 보다 높은 수준의 경우 Microsoft 담당자에 게 연락 하 여 사용 하도록 설정 합니다. 전체 REST 요청 본문을 제공할 필요는 없지만 sku는 다음과 같습니다.

  ```azurecli
  az monitor log-analytics cluster update --name "cluster-name" --resource-group "resource-group-name" --sku-capacity daily-ingestion-gigabyte
  ```

  ```powershell
  Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -SkuCapacity daily-ingestion-gigabyte
  ```

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "sku": {
      "name": "capacityReservation",
      "Capacity": daily-ingestion-gigabyte
    }
  }
  ```

- **클러스터에서 *billingType* 업데이트**

  *BillingType* 속성은 클러스터 및 해당 데이터에 대 한 청구 특성을 결정 합니다.
  - *cluster* (기본값) - 클러스터 리소스를 호스팅하는 구독을 기반으로 하는 청구입니다.
  - *workspaces* - 작업 영역을 비례적으로 호스팅하는 구독을 기반으로 하는 청구입니다.
  
  [업데이트 클러스터](#update-cluster-with-key-identifier-details) 에 따라 새로운 billingType 값을 제공 합니다. 전체 REST 요청 본문을 제공할 필요는 없으며 *billingType* 을 포함해야 합니다.

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

- **작업 영역 연결 해제**

  이 작업을 수행 하려면 작업 영역 및 클러스터에 대 한 ' 쓰기 ' 권한이 있어야 합니다. 언제 든 지 클러스터에서 작업 영역의 연결을 끊을 수 있습니다. 연결 해제 작업 후 새 수집 데이터는 Log Analytics 저장소에 저장 되 고 Microsoft 키를 사용 하 여 암호화 됩니다. 클러스터가 프로 비전 되 고 유효한 Key Vault 키로 구성 되는 한 연결 해제 전후에 작업 영역에 수집 된 데이터를 쿼리할 수 있습니다.

  이 작업은 비동기 작업 이므로 완료 하는 데는 몇를 사용할 수 있습니다.

  ```azurecli
  az monitor log-analytics workspace linked-service delete --resource-group "resource-group-name" --name "cluster-name" --workspace-name "workspace-name"
  ```

  ```powershell
  Remove-AzOperationalInsightsLinkedService -ResourceGroupName "resource-group-name" -Name "workspace-name" -LinkedServiceName cluster
  ```

  ```rest
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  - **작업 영역 링크 상태 확인**
  
  작업 영역에서 가져오기 작업을 수행 하 고, *기능의* 응답에 *clusterresourceid* 속성이 있는지 확인 합니다. 연결 된 작업 영역에는 *Clusterresourceid* 속성이 포함 됩니다.

  ```azurecli
  az monitor log-analytics cluster show --resource-group "resource-group-name" --name "cluster-name"
  ```

  ```powershell
  Get-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
  ```

- **클러스터 삭제**

  이 작업을 수행 하려면 클러스터에 대 한 ' 쓰기 ' 권한이 있어야 합니다. 일시 삭제 작업은 삭제가 실수로 또는 의도적인 것인지에 상관 없이 14 일 이내에 데이터를 포함 하 여 클러스터를 복구할 수 있도록 하기 위해 수행 됩니다. 클러스터 이름은 일시 삭제 기간 동안 예약 된 상태로 유지 되며 해당 이름을 사용 하 여 새 클러스터를 만들 수 없습니다. 일시 삭제 기간이 지나면 클러스터 이름이 해제 되 고 클러스터 및 해당 데이터가 영구적으로 삭제 되며 복구할 수 없습니다. 모든 연결 된 작업 영역은 삭제 작업 시 클러스터에서 연결이 해제 됩니다. 새로 수집된 데이터는 Log Analytics 스토리지에 저장되고 Microsoft 키를 사용하여 암호화됩니다. 
  
  연결 해제 작업은 비동기적 이며 완료 하는 데 최대 90 분이 걸릴 수 있습니다.

  ```azurecli
  az monitor log-analytics cluster delete --resource-group "resource-group-name" --name "cluster-name"
  ```
 
  ```powershell
  Remove-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name"
  ```

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  ```
  
- **클러스터 및 데이터 복구** 
  
  지난 14 일 동안 삭제 된 클러스터는 일시 삭제 상태 이며 해당 데이터를 사용 하 여 복구할 수 있습니다. 모든 작업 영역에서 클러스터 삭제와의 연결이 해제 되었으므로 클러스터 복구 후 작업 영역을 다시 연결 해야 합니다. 현재 제품 그룹에서 복구 작업을 수동으로 수행 하 고 있습니다. Microsoft 채널을 사용 하거나 삭제 된 클러스터의 복구에 대 한 지원 요청을 여세요.

## <a name="limitations-and-constraints"></a>제한 사항 및 제약 조건

- Customer-Managed 키는 전용 Log Analytics 클러스터에서 지원 되며 매일 1TB를 전송 하는 고객에 게 적합 합니다.

- 하위 지역 및 구독 당 최대 클러스터 수는 2입니다.

- 클러스터에 연결 된 작업 영역의 최대값은 1000입니다.

- 작업 영역을 클러스터에 연결 하 고 연결을 끊을 수 있습니다. 특정 작업 영역에 대 한 작업 영역 링크 작업 수는 30 일 동안 2 개로 제한 됩니다.

- 클러스터에 대 한 작업 영역 링크는 Log Analytics 클러스터 프로 비전이 완료 되었음을 확인 한 후에만 수행 해야 합니다.  완료된 후에는 작업 영역으로 보낸 데이터를 삭제하고 복구할 수 있습니다.

- Customer-Managed 키 암호화는 구성 시간 이후 새로 수집 데이터에 적용 됩니다. 구성 이전에 수집 된 데이터는 Microsoft 키로 암호화 된 상태로 유지 됩니다. Customer-Managed 키 구성 전후에 데이터 수집을 원활 하 게 쿼리할 수 있습니다.

- Azure Key Vault를 복구할 수 있는 것으로 구성 해야 합니다. 이러한 속성은 기본적으로 사용 하도록 설정 되어 있지 않으며 CLI 또는 PowerShell을 사용 하 여 구성 해야 합니다.<br>
  - [일시 삭제](../../key-vault/general/soft-delete-overview.md)
  - 일시 삭제 후에도 비밀/자격 증명 모음을 강제로 삭제 하는 것을 방지 하려면 [보호 제거](../../key-vault/general/soft-delete-overview.md#purge-protection) 를 켜야 합니다.

- 다른 리소스 그룹 또는 구독으로의 클러스터 이동은 현재 지원 되지 않습니다.

- Azure Key Vault, 클러스터 및 연결 된 작업 영역은 동일한 지역 및 동일한 Azure Active Directory (Azure AD) 테 넌 트에 있어야 하지만 다른 구독에 있을 수 있습니다.

- 다른 클러스터에 연결 된 경우에는 클러스터에 대 한 작업 영역 링크가 실패 합니다.

## <a name="troubleshooting"></a>문제 해결

- Key Vault 가용성과 관련된 동작
  - 정상 작업에서 - 스토리지에서 AEK를 짧은 시간 동안 캐시하고, Key Vault로 돌아가서 래핑을 주기적으로 해제합니다.
    
  - 일시적인 연결 오류 - 스토리지에서 키를 짧은 시간 동안 캐시에서 유지할 수 있도록 하여 일시적인 오류(시간 제한, 연결 실패, DNS 문제)를 처리하고, 이로 인한 사소한 가용성 문제도 해결합니다. 쿼리 및 수집 기능은 중단 없이 계속됩니다.
    
  - 라이브 사이트 - 약 30분 동안 사용할 수 없으면 스토리지 계정을 사용할 수 없게 됩니다. 쿼리 기능을 사용할 수 없으며, 수집된 데이터는 데이터 손실을 방지하기 위해 Microsoft 키를 사용하여 몇 시간 동안 캐시됩니다. Key Vault에 대 한 액세스가 복원 되 면 쿼리를 사용할 수 있게 되며 임시 캐시 된 데이터는 데이터 저장소에 수집 되 고 Customer-Managed 키로 암호화 됩니다.

  - Key Vault 액세스 속도 - 래핑 및 래핑 해제 작업을 위해 Azure Monitor 스토리지에서 Key Vault에 액세스하는 빈도는 6-60초입니다.

- 클러스터를 만들고 KeyVaultProperties를 즉시 지정 하는 경우 시스템 id가 클러스터에 할당 될 때까지 액세스 정책을 정의할 수 없으므로 작업이 실패할 수 있습니다.

- KeyVaultProperties를 사용 하 여 기존 클러스터를 업데이트 하 고 ' Get ' 키 액세스 정책이 Key Vault에 없는 경우 작업이 실패 합니다.

- 클러스터를 만들 때 충돌 오류가 발생 하는 경우-지난 14 일 동안 클러스터를 삭제 했 고 일시 삭제 기간에 있는 것이 원인일 수 있습니다. 클러스터 이름은 일시 삭제 기간 동안 예약 된 상태로 유지 되며 해당 이름을 사용 하 여 새 클러스터를 만들 수 없습니다. 이 이름은 클러스터가 영구적으로 삭제 될 때 일시 삭제 기간이 지나면 릴리스됩니다.

- 작업이 진행 되는 동안 클러스터를 업데이트 하면 작업이 실패 합니다.

- 클러스터를 배포 하지 못한 경우 Azure Key Vault, 클러스터 및 연결 된 Log Analytics 작업 영역이 같은 지역에 있는지 확인 합니다. 서로 다른 구독에 있을 수 있습니다.

- Key Vault에서 키 버전을 업데이트 하 고 클러스터의 새 키 식별자 정보를 업데이트 하지 않으면 Log Analytics 클러스터는 이전 키를 계속 사용 하 고 데이터에 액세스할 수 없게 됩니다. 데이터 수집 및 데이터 쿼리 기능을 다시 시작 하기 위해 클러스터의 새 키 식별자 세부 정보를 업데이트 합니다.

- 일부 작업은 길고 완료 하는 데 시간이 걸릴 수 있습니다 (클러스터 만들기, 클러스터 키 업데이트 및 클러스터 삭제). 다음 두 가지 방법으로 작업 상태를 확인할 수 있습니다.
  1. REST를 사용 하는 경우 응답에서 Azure-AsyncOperation URL 값을 복사 하 고 [비동기 작업 상태 검사](#asynchronous-operations-and-status-check)를 따릅니다.
  2. 클러스터 또는 작업 영역에 GET 요청을 보내고 응답을 관찰 합니다. 예를 들어 연결 되지 않은 작업 영역에는 *기능* 아래에 *clusterresourceid* 가 없습니다.

- 고객 관리형 키와 관련된 지원 및 도움을 받으려면 Microsoft에 문의하세요.

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
  -  400--KeyVaultProperties가 비어 있지 않지만 형식이 잘못 되었습니다. [키 식별자 업데이트](#update-cluster-with-key-identifier-details)를 참조 하세요.
  -  400--Key Vault의 키 유효성을 검사 하지 못했습니다. 권한이 부족 하거나 키가 없는 경우에 발생할 수 있습니다. Key Vault에서 [키 및 액세스 정책을 설정](#grant-key-vault-permissions) 했는지 확인 합니다.
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
