---
title: 고객 관리 키 Azure Monitor
description: CMK (고객이 관리 하는 키)를 구성 하 여 Azure Key Vault 키를 사용 하 여 Log Analytics 작업 영역의 데이터를 암호화 하는 방법에 대 한 정보 및 단계입니다.
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 05/07/2020
ms.openlocfilehash: c78d8d603b6686d382ec7edcccc24d5dacc4745a
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982227"
---
# <a name="azure-monitor-customer-managed-key"></a>고객 관리 키 Azure Monitor 

이 문서에서는 Log Analytics 작업 영역에 대 한 CMK (고객 관리 키)를 구성 하는 단계 및 배경 정보를 제공 합니다. 구성 된 후에는 작업 영역으로 전송 되는 모든 데이터가 Azure Key Vault 키로 암호화 됩니다.

구성 전에 [제한 사항 및 제약 조건을](#limitations-and-constraints) 검토 하는 것이 좋습니다.

## <a name="disclaimers"></a>고지 사항

- Azure Monitor CMK는 초기 액세스 기능이 며 등록 된 구독에 사용할 수 있습니다.

- 이 문서에서 설명 하는 CMK 배포는 초기 액세스 기능 이더라도 프로덕션 품질로 제공 되며 지원 됩니다.

- CMK 기능은 매일 1TB를 전송 하는 고객에 게 적합 한 물리적 클러스터 및 데이터 저장소 인 전용 Log Analytics 클러스터에서 제공 됩니다.

- CMK 가격 책정 모델은 현재 사용할 수 없으며,이 문서에서 다루지 않습니다. 전용 Log Analytics 클러스터에 대 한 가격 책정 모델은 1 번째 사분기 (CY) 2020에 예상 되며 기존 CMK 배포에 적용 됩니다.

## <a name="customer-managed-key-cmk-overview"></a>CMK (고객 관리 키) 개요

[미사용 암호화](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest) 는 조직의 일반적인 개인 정보 및 보안 요구 사항입니다. 암호화 또는 암호화 키를 긴밀 하 게 관리 하는 다양 한 옵션을 사용 하 여 Azure에서 미사용 암호화를 완전히 관리할 수 있습니다.

Azure Monitor 저장소는 Azure Storage에 저장 되어 있는 동안 Azure 관리 키를 사용 하 여 미사용 데이터를 암호화 하는 모든 데이터를 확인 합니다. 또한 Azure Monitor는 시스템 할당 [관리 id](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) 인증을 사용 하 여 액세스 하는 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)에 저장 된 사용자 고유의 키를 사용 하는 데이터 암호화 옵션을 제공 합니다. 이 키는 [소프트웨어 또는 하드웨어 HSM으로 보호](https://docs.microsoft.com/azure/key-vault/key-vault-overview)될 수 있습니다.
암호화 사용 Azure Monitor는 암호화가 작동 하는 [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption) 방식과 동일 합니다.

줄 바꿈 및 래핑 해제 작업에 Key Vault Azure Monitor 저장소 액세스 빈도는 6 ~ 60 초 사이입니다.Azure Monitor 저장소는 항상 1 시간 이내에 키 사용 권한을 변경 합니다.

또한 지난 14 일간 수집 데이터는 효율적인 쿼리 엔진 작업을 위해 핫 캐시 (SSD 지원)로 유지 됩니다. CMK 구성에 관계 없이이 데이터는 Microsoft 키를 사용 하 여 암호화 된 상태로 유지 되지만 SSD 데이터에 대 한 제어는 [키 해지](#cmk-kek-revocation) 를 준수 하며 액세스할 수 없습니다. 2020의 두 번째 절반에 CMK로 암호화 된 SSD 데이터를 제공 하기 위해 노력 하 고 있습니다.

## <a name="how-cmk-works-in-azure-monitor"></a>Azure Monitor에서 CMK가 작동 하는 방식

Azure Monitor는 시스템 할당 관리 id를 활용 하 여 Azure Key Vault에 대 한 액세스 권한을 부여 합니다.시스템 할당 관리 id는 단일 Azure 리소스와만 연결할 수 있습니다. 전용 Log Analytics 클러스터의 id는 클러스터 수준에서 지원 되며 CMK 기능이 전용 Log Analytics 클러스터에서 제공 됩니다. 여러 작업 영역에서 CMK를 지원 하기 위해 새 Log Analytics *클러스터* 리소스는 Key Vault와 Log Analytics 작업 영역 간에 중간 id 연결로 수행 됩니다. 이 개념은 전용 Log Analytics 클러스터와 Log Analytics *클러스터* 리소스 간의 id를 유지 하지만, 연결 된 작업 영역의 데이터는 Key Vault 키로 보호 됩니다. 전용 Log Analytics 클러스터 저장소는 *클러스터* 리소스와 연결 된\'관리 되는 id를 사용 하 여 인증 하 고 Azure Active Directory를 통해 Azure Key Vault에 액세스 합니다.

![CMK 개요](media/customer-managed-keys/cmk-overview-8bit.png)
1.    고객의 Key Vault입니다.
2.    Key Vault에 대 한 사용 권한이 있는 관리 id가 있는 고객의 Log Analytics *클러스터* 리소스-id는 전용 Log Analytics 클러스터 수준에서 지원 됩니다.
3.    전용 Log Analytics 클러스터.
4.    CMK 암호화에 대 한 *클러스터* 리소스와 연결 된 고객의 작업 영역입니다.

## <a name="encryption-keys-operation"></a>암호화 키 작업

저장소 데이터 암호화에는 세 가지 유형의 키가 있습니다.

- CMK ( **KEK** Key Encryption key)
- **Aek** -계정 암호화 키
- **Dek** -데이터 암호화 키

다음 규칙이 적용됩니다.

- 전용 Log Analytics 클러스터 저장소 계정은 AEK로 알려진 모든 저장소 계정에 대해 고유한 암호화 키를 생성 합니다.

- AEK는 디스크에 기록 되는 데이터의 각 블록을 암호화 하는 데 사용 되는 키인 DEKs를 파생 하는 데 사용 됩니다.

- Key Vault에서 키를 구성 하 고 *클러스터* 리소스에서 키를 참조 하는 경우 Azure Storage는 데이터 암호화 및 암호 해독 작업을 수행 하기 위해 aek를 래핑하고 래핑 해제 하는 요청을 Azure Key Vault 보냅니다.

- KEK는 Key Vault을 유지 하지 않으며 HSM 키의 경우 하드웨어를 그대로 유지 합니다.

- Azure Storage는 *클러스터* 리소스와 연결 된 관리 되는 id를 사용 하 여 인증 하 고 Azure Active Directory를 통해 Azure Key Vault에 액세스 합니다.

## <a name="cmk-provisioning-procedure"></a>CMK 프로 비전 절차

1. Subscription 허용 목록-이 초기 액세스 기능에 필요 합니다.
2. Azure Key Vault 만들기 및 키 저장
3. *클러스터* 리소스 만들기
5. Key Vault에 대 한 사용 권한 부여
6. Log Analytics 작업 영역 연결

이 프로시저는 현재 UI에서 지원 되지 않으며 프로 비전 프로세스는 REST API를 통해 수행 됩니다.

> [!IMPORTANT]
> 모든 API 요청은 요청 헤더에 전달자 권한 부여 토큰을 포함 해야 합니다.

다음은 그 예입니다. 

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
Authorization: Bearer eyJ0eXAiO....
```

여기서 *eyJ0eXAiO* 는 전체 권한 부여 토큰을 나타냅니다. 

다음 방법 중 하나를 사용 하 여 토큰을 가져올 수 있습니다.

1. [앱 등록](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens) 메서드를 사용 합니다.
2. Azure 포털에서 다음을 수행합니다.
    1. F12 ("개발자 도구")의 Azure Portal으로 이동
    1. "Batch? api-version" 인스턴스 중 하나에서 "요청 헤더" 아래에 있는 권한 부여 문자열을 찾습니다. "권한 부여: 전달자 eyJ0eXAiO ..."와 같습니다. 
    1. 다음 예제에 따라이를 복사 하 여 API 호출에 추가 합니다.
3. Azure REST 설명서 사이트로 이동 합니다. 모든 API에서 "체험"을 누르고 전달자 토큰을 복사 합니다.

### <a name="asynchronous-operations-and-status-check"></a>비동기 작업 및 상태 검사

이 구성 절차의 일부 작업은 신속 하 게 완료할 수 없기 때문에 비동기적으로 실행 됩니다. 비동기 작업에 대 한 응답은 처음에 허용 될 때 HTTP 상태 코드 200 (OK) 및 *AsyncOperation* 속성을 사용 하 여 헤더를 반환 합니다.
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-03-01-preview"
```

GET 요청을 *Azure AsyncOperation* 헤더 값에 보내서 비동기 작업의 상태를 확인할 수 있습니다.
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

응답에는 작업 및 작업 *상태*에 대 한 정보가 포함 됩니다. 다음 중 하나일 수 있습니다.

작업이 진행 중입니다.
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "InProgress", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
}
```

작업이 완료 되었습니다.
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

### <a name="subscription-whitelisting"></a>구독 허용 목록

CMK 기능은 초기 액세스 기능입니다. *클러스터* 리소스를 만들려는 구독은 Azure 제품 그룹에서 미리 허용 목록 되어야 합니다. Microsoft에 연락처를 사용 하 여 구독 Id를 제공 합니다.

> [!IMPORTANT]
> CMK 기능은 지역입니다. Azure Key Vault, *클러스터* 리소스 및 연결 된 Log Analytics 작업 영역이 같은 지역에 있어야 하지만 다른 구독에 있을 수 있습니다.

### <a name="storing-encryption-key-kek"></a>암호화 키 저장 (KEK)

이미 생성 된 Azure Key Vault을 만들거나 사용 하 여 데이터 암호화에 사용할 키를 가져와야 합니다. Azure Monitor의 데이터에 대 한 액세스 및 키를 보호 하려면 Azure Key Vault를 복구 가능으로 구성 해야 합니다. Key Vault의 속성에서이 구성을 확인할 수 있습니다. *일시 삭제* 및 *제거 보호* 를 모두 사용 하도록 설정 해야 합니다.

![일시 삭제 및 보호 설정 제거](media/customer-managed-keys/soft-purge-protection.png)

이러한 설정은 CLI 및 PowerShell을 통해 사용할 수 있습니다.
- [일시 삭제](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
- 일시 삭제 후에도 비밀/자격 증명 모음을 강제로 삭제 하지 않도록 [보호 제거](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection)

### <a name="create-cluster-resource"></a>*클러스터* 리소스 만들기

이 리소스는 Key Vault와 Log Analytics 작업 영역 간의 중간 id 연결로 사용 됩니다. 구독이 허용 목록 된 것을 확인 한 후에는 작업 영역이 있는 지역에서 Log Analytics *클러스터* 리소스를 만듭니다.

*클러스터* 리소스를 만들 때 sku ( *용량 예약* 수준)를 지정 해야 합니다. *용량 예약* 수준 범위는 매일 1000 ~ 2000 GB 이며, 나중에 100의 단계에서 업데이트할 수 있습니다. 하루에 2000 GB 보다 높은 용량 예약 수준을 요구 하는 경우 microsoft에 문의 LAIngestionRate@microsoft.com하세요. [자세히 알아보기](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#log-analytics-clusters)
    
*BillingType* 속성은 *클러스터* 리소스 및 해당 데이터에 대 한 청구 특성을 결정 합니다.
- *클러스터* (기본값)--청구는 *클러스터* 리소스를 호스트 하는 구독에 대 한 특성을 갖습니다.
- *작업 영역* --청구는 작업 영역을 상대적으로 호스트 하는 구독에 대 한 특성을 가집니다. 

> [!NOTE]
> *클러스터* 리소스를 만든 후에는 PATCH REST 요청을 사용 하 여 *sku*, *keyVaultProperties* 또는 *billingType* 를 사용 하 여 업데이트할 수 있습니다.

**만들기**

이 리소스 관리자 요청은 비동기 작업입니다.

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
Id는 생성 시 *클러스터* 리소스에 할당 됩니다.

**응답**

200 OK 및 헤더

작업을 완료 하는 데는 전용 Log Analytics 클러스터를 프로 비전 하는 동안 두 가지 방법으로 프로 비전 상태를 확인할 수 있습니다.

1. 응답에서 AsyncOperation URL 값을 복사 하 고 [비동기 작업 상태 검사](#asynchronous-operations-and-status-check)를 수행 합니다.
2. *클러스터* 리소스에 GET 요청을 보내고 *provisioningState* 값을 확인 합니다. 프로 비전 중에 *ProvisioningAccount* 완료 되 면 *성공* 합니다.


```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

> [!IMPORTANT]
> 다음 단계에서 세부 정보가 필요 하므로 응답을 복사 하 고 저장 합니다.

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

"PrincipalId" GUID는 *클러스터* 리소스에 대 한 관리 id 서비스에 의해 생성 됩니다.

### <a name="grant-key-vault-permissions"></a>Key Vault 권한 부여

*클러스터* 리소스에 대 한 사용 권한을 부여 하는 새 액세스 정책을 사용 하 여 Key Vault를 업데이트 합니다. 이러한 권한은 언더레이 Azure Monitor 저장소에서 데이터 암호화에 사용 됩니다. Azure Portal에서 Key Vault을 열고 "액세스 정책"을 클릭 한 다음 "+ 액세스 정책 추가"를 클릭 하 여 다음 설정으로 정책을 만듭니다.

- 키 사용 권한: ' 가져오기 ', ' 줄 바꿈 키 ' 및 ' 래핑 해제 키 ' 권한을 선택 합니다.
- 보안 주체 선택: 이전 단계의 응답에서 반환 된 사용자 id 값을 입력 합니다.

![Key Vault 권한 부여](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

사용자의 키와 Azure Monitor 데이터에 대 한 액세스를 보호 하기 위해 Key Vault가 복구 가능한 것으로 구성 되었는지 확인 하려면 *Get* 권한이 필요 합니다.

### <a name="update-cluster-resource-with-key-identifier-details"></a>키 식별자 세부 정보를 사용 하 여 클러스터 리소스 업데이트

이 단계는 Key Vault에서 초기 및 향후 주요 버전 업데이트를 수행 하는 동안 수행 됩니다. 데이터 암호화에 사용할 키 버전에 대 한 Azure Monitor 저장소에 알립니다. 업데이트 된 경우 새 키를 사용 하 여 AEK (저장소 키)로 래핑하고 래핑 해제 합니다.

Key Vault *키 식별자* 정보를 사용 하 여 *클러스터* 리소스를 업데이트 하려면 Azure Key Vault에서 키의 현재 버전을 선택 하 여 키 식별자 정보를 가져옵니다.

![Key Vault 권한 부여](media/customer-managed-keys/key-identifier-8bit.png)

키 식별자 세부 정보를 사용 하 여 *클러스터* 리소스 KeyVaultProperties를 업데이트 합니다.

**업데이트**

이 리소스 관리자 요청은 키 식별자 정보를 업데이트할 때 비동기 작업이 며 용량 값을 업데이트할 때 동기식입니다.

> [!Note]
> *클러스터* 리소스의 부분 본문을 제공 하 여 *sku*, *keyVaultProperties* 또는 *billingType*를 업데이트할 수 있습니다.

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
       KeyVaultUri: "https://<key-vault-name>.vault.azure.net",
       KeyName: "<key-name>",
       KeyVersion: "<current-version>"
       }
   },
   "location":"<region-name>"
}
```
"KeyVaultProperties"에는 Key Vault 키 식별자 정보가 포함 되어 있습니다.

**응답**

200 OK 및 헤더
키 식별자의 전파를 완료 하는 데 몇 분이 걸립니다. 다음 두 가지 방법으로 업데이트 상태를 확인할 수 있습니다.
1. 응답에서 AsyncOperation URL 값을 복사 하 고 [비동기 작업 상태 검사](#asynchronous-operations-and-status-check)를 수행 합니다.
2. *클러스터* 리소스에 GET 요청을 보내고 *KeyVaultProperties* 속성을 확인 합니다. 최근 업데이트 된 키 식별자 정보는 응답에서 반환 되어야 합니다.

키 식별자 업데이트가 완료 되 면 *클러스터* 리소스에 대 한 GET 요청에 대 한 응답이 다음과 같이 표시 됩니다.

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
      keyVaultUri: "https://key-vault-name.vault.azure.net",
      kyName: "key-name",
      keyVersion: "current-version"
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

### <a name="workspace-association-to-cluster-resource"></a>*클러스터* 리소스에 작업 영역 연결

이 작업을 수행 하려면 작업 영역 및 *클러스터* 리소스 모두에 ' 쓰기 ' 권한이 있어야 합니다. 여기에는 다음 작업이 포함 됩니다.

- 작업 영역에서: OperationalInsights/작업 영역/쓰기
- *클러스터* 리소스: OperationalInsights/클러스터/쓰기

> [!IMPORTANT]
> 이 단계는 전용 Log Analytics 클러스터 프로 비전이 완료 된 후에만 수행 해야 합니다. 프로 비전 전에 작업 영역을 연결 하 고 데이터를 수집 하는 경우 수집 데이터는 삭제 되 고 복구할 수 없습니다.

**작업 영역 연결**

이 리소스 관리자 요청은 비동기 작업입니다.

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

200 OK 및 헤더

수집 데이터는 연결 작업 후에 관리 키를 사용 하 여 암호화 된 상태로 저장 됩니다 .이 작업은 완료 하는 데 최대 90 분까지 걸릴 수 있습니다. 다음 두 가지 방법으로 작업 영역 연결 상태를 확인할 수 있습니다.

1. 응답에서 AsyncOperation URL 값을 복사 하 고 [비동기 작업 상태 검사](#asynchronous-operations-and-status-check)를 수행 합니다.
2. [작업 영역 보내기 –](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) 요청을 가져오고 응답을 관찰 합니다. 연결 된 작업 영역에는 "기능" 아래에 clusterresourceid가 있습니다.

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
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
    "retentionInDays": days,
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

## <a name="cmk-kek-revocation"></a>CMK (KEK) 해지

사용자의 키를 사용 하지 않도록 설정 하거나 Key Vault에서 *클러스터* 리소스 액세스 정책을 삭제 하 여 데이터에 대 한 액세스를 해지할 수 있습니다. Azure Monitor 저장소는 항상 1 시간 이내에 키 사용 권한의 변경 사항을 적용 하 고, 일반적으로 더 일찍 저장소를 사용할 수 없게 됩니다. *클러스터* 리소스와 연결 된 작업 영역에 대 한 모든 데이터 수집 삭제 되 고 쿼리가 실패 합니다. 이전에 수집 데이터는 *클러스터* 리소스이 고 작업 영역이 삭제 되지 않는 한 Azure Monitor 저장소에서 액세스할 수 없는 상태로 유지 됩니다. 액세스할 수 없는 데이터는 데이터 보존 정책에 의해 제어 되며 보존에 도달 하면 제거 됩니다. 

또한 지난 14 일간 수집 데이터는 효율적인 쿼리 엔진 작업을 위해 핫 캐시 (SSD 지원)로 유지 됩니다. CMK 구성에 관계 없이이 데이터는 Microsoft 키를 사용 하 여 암호화 된 상태로 유지 되지만 키 해지 작업에서 삭제 되 고 액세스할 수 없게 됩니다.

저장소는 암호화 키 래핑 해제를 시도 하 고 30 분 내에 액세스 한 후 데이터 수집 및 쿼리를 다시 시작할 수 있도록 주기적으로 Key Vault를 폴링합니다.

## <a name="cmk-kek-rotation"></a>CMK (KEK) 회전

CMK를 회전 하려면 Azure Key Vault의 새 키 버전을 사용 하 여 *클러스터* 리소스를 명시적으로 업데이트 해야 합니다. 새 키 버전을 사용 하 여 Azure Monitor를 업데이트 하려면 "키 식별자 세부 정보를 사용 하 여 *클러스터* 리소스 업데이트" 단계의 지침을 따르세요. Key Vault에서 키 버전을 업데이트 하 고 *클러스터* 리소스의 새 키 식별자 세부 정보를 업데이트 하지 않는 경우 Azure Monitor Storage는 이전 키를 계속 사용 합니다.
모든 데이터는 현재 KEK (계정 암호화 키) 버전에 의해 암호화 되는 동안 모든 데이터는 AEK (계정 암호화 키)에 의해 암호화 된 상태로 유지 되 고, 그 후에는 수집 데이터를 포함 하 여 키 회전 작업 후에 액세스할 수 있습니다.

## <a name="limitations-and-constraints"></a>제한 사항 및 제약 조건

- CMK는 매일 1TB를 전송 하는 고객에 게 적합 한 전용 Log Analytics 클러스터에서 지원 됩니다.

- 하위 지역 및 구독 당 최대 *클러스터* 리소스 수는 2입니다.

- 작업 영역을 *클러스터* 리소스에 연결한 다음, 해당 데이터에 대 한 cmk가 더 이상 필요 하지 않거나 다른 이유로 작업 영역을 연결 해제할 수 있습니다. 30 일 동안 작업 영역에서 수행할 수 있는 작업 영역 연결 수는 2로 제한 됩니다.

- *클러스터* 리소스에 대 한 작업 영역 연결은 전용 Log Analytics 클러스터 프로 비전이 완료 되었음을 확인 한 후에만 수행 해야 합니다. 완료 되기 전에 작업 영역으로 전송 된 데이터는 삭제 되 고 복구할 수 없습니다.

- Cmk 암호화는 CMK 구성 후에 새로 수집 데이터에 적용 됩니다. CMK 구성 이전에 수집 된 데이터는 Microsoft 키로 암호화 된 상태로 유지 됩니다. CMK 구성 전후에 데이터 수집을 원활 하 게 쿼리할 수 있습니다.

- Azure Key Vault를 복구할 수 있는 것으로 구성 해야 합니다. 이러한 속성은 기본적으로 사용 하도록 설정 되어 있지 않으며 CLI 또는 PowerShell을 사용 하 여 구성 해야 합니다.

  - [일시 삭제](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) 를 켜야 합니다.
  - 일시 삭제 후에도 비밀/자격 증명 모음을 강제로 삭제 하는 것을 방지 하려면 [보호 제거](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) 를 켜야 합니다.

- 다른 리소스 그룹 또는 구독에 대 한 *클러스터* 리소스 이동은 현재 지원 되지 않습니다.

- Azure Key Vault, *클러스터* 리소스 및 연결 된 작업 영역은 동일한 지역 및 동일한 Azure Active Directory (Azure AD) 테 넌 트에 있어야 하지만 다른 구독에 있을 수 있습니다.

- 다른 *클러스터* 리소스와 연결 된 경우 *클러스터* 리소스에 대 한 작업 영역 연결이 실패 합니다.


## <a name="management"></a>관리

- **리소스 그룹에 대 한 모든 *클러스터* 리소스 가져오기**

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
              keyVaultUri: "https://key-vault-name.vault.azure.net",
              keyName: "key-name",
              keyVersion: "current-version"
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

- **구독에 대 한 모든 *클러스터* 리소스 가져오기**

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```
    
  **응답**
    
  ' 리소스 그룹에 대 한*클러스터* 리소스 '와 동일한 응답 이지만 구독 범위에 있습니다.

- ***클러스터* 리소스의 *용량 예약* 업데이트**

  연결 된 작업 영역에 대 한 데이터 볼륨이 시간이 지남에 따라 변경 되 고 용량 예약 수준을 적절 하 게 업데이트 하려는 경우. [ *클러스터* 리소스 업데이트](#update-cluster-resource-with-key-identifier-details) 를 따르고 새 용량 값을 제공 합니다. 1000 ~ 2000 g b의 범위와 100의 단계에 있을 수 있습니다. 하루 2000 g b 보다 높은 수준의 경우 Microsoft 담당자에 게 연락 하 여 사용 하도록 설정 합니다. 전체 REST 요청 본문을 제공할 필요 없이 sku를 포함 해야 합니다.

  ```json
  {
    "sku": {
      "name": "capacityReservation",
      "Capacity": 1000
    }
  }
  ``` 

- ***클러스터* 리소스에서 *billingType* 업데이트**

  *BillingType* 속성은 *클러스터* 리소스 및 해당 데이터에 대 한 청구 특성을 결정 합니다.
  - *클러스터* (기본값)--청구는 클러스터 리소스를 호스트 하는 구독에 대 한 특성을 갖습니다.
  - *작업 영역* --청구는 작업 영역을 상대적으로 호스트 하는 구독에 대 한 특성을 가집니다.
  
  [ *클러스터* 리소스 업데이트](#update-cluster-resource-with-key-identifier-details) 를 따르고 새 billingType 값을 제공 합니다. 전체 REST 요청 본문을 제공할 필요 없이 *billingType*를 포함 해야 합니다.

  ```json
  {
    "properties": {
      "billingType": "cluster",
      }  
  }
  ``` 

- **작업 영역 연결 해제**

  이 작업을 수행 하려면 작업 영역 및 *클러스터* 리소스에 대 한 ' 쓰기 ' 권한이 있어야 합니다. 언제 든 지 *클러스터* 리소스에서 작업 영역을 연결 해제할 수 있습니다. 연결 취소 작업 후 새 수집 데이터는 Log Analytics 저장소에 저장 되 고 Microsoft 키로 암호화 됩니다. *클러스터* 리소스가 프로 비전 되 고 유효한 Key Vault 키로 구성 되는 한, 연결 취소 전후에 작업 영역에 수집 된 데이터를 쿼리할 수 있습니다.

  이 리소스 관리자 요청은 비동기 작업입니다.

  ```rest
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-03-01-preview
  ```

  **응답**

  200 OK 및 헤더

  연결 해제 작업 후 수집 데이터는 Log Analytics 저장소에 저장 됩니다 .이 작업은 완료 하는 데 90 분 정도 걸릴 수 있습니다. 다음 두 가지 방법으로 작업 영역 연결 해제 상태를 확인할 수 있습니다.

  1. 응답에서 AsyncOperation URL 값을 복사 하 고 [비동기 작업 상태 검사](#asynchronous-operations-and-status-check)를 수행 합니다.
  2. [작업 영역 보내기 –](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) 요청을 가져오고 응답을 관찰 합니다. 연결 된 작업 영역에는 *기능*아래에 *clusterresourceid* 가 포함 되지 않습니다.


- ***클러스터* 리소스를 삭제 합니다.**

  이 작업을 수행 하려면 *클러스터* 리소스에 대 한 ' 쓰기 ' 권한이 있어야 합니다. 일시 삭제 작업은 삭제가 실수나 의도적인 지 여부에 관계 없이 14 일 내에 데이터를 포함 하 여 *클러스터* 리소스를 복구할 수 있도록 하기 위해 수행 됩니다. *클러스터* 리소스 이름은 일시 삭제 기간 동안 예약 된 상태로 유지 되며 해당 이름을 사용 하 여 새 클러스터를 만들 수 없습니다. 일시 삭제 기간이 지나면 *클러스터* 리소스 이름이 해제 되 고 *클러스터* 리소스와 데이터가 영구적으로 삭제 되며 복구할 수 없습니다. 연결 된 작업 영역은 삭제 작업 시 *클러스터* 리소스에서 연결 해제 됩니다. 새 수집 데이터는 Log Analytics 저장소에 저장 되 고 Microsoft 키를 사용 하 여 암호화 됩니다. 작업 영역 연결 해제 작업은 비동기적 이며 완료 하는 데 최대 90 분이 걸릴 수 있습니다.

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **응답**

  200 정상

- ***클러스터* 리소스 및 데이터 복구** 
  
  지난 14 일 동안 삭제 된 *클러스터* 리소스는 일시 삭제 상태 이며 복구할 수 있습니다. 이는 현재 제품 그룹에서 수동으로 수행 합니다. 복구 요청에 Microsoft 채널을 사용 합니다.


## <a name="troubleshooting"></a>문제 해결
- Key Vault 가용성에 대 한 동작
  - 정상 작업에서-저장소는 짧은 기간 동안 AEK를 캐시 하 고 Key Vault로 돌아가서 정기적으로 래핑 해제 합니다.
    
  - 일시적인 연결 오류-저장소는 키가 짧은 시간 동안 캐시에 유지 되도록 허용 하 여 일시적인 오류 (시간 제한, 연결 오류, DNS 문제)를 처리 하 고,이를 통해 작은 문제 가용성을 극복 합니다. 쿼리 및 수집 기능은 중단 없이 계속 됩니다.
    
  - 라이브 사이트-약 30 분을 사용할 수 없으면 저장소 계정을 사용할 수 없게 됩니다. 데이터 손실을 방지 하기 위해 쿼리 기능을 사용할 수 없으며 Microsoft 키를 사용 하 여 몇 시간 동안 수집 데이터를 캐시 합니다. Key Vault에 대 한 액세스가 복원 되 면 쿼리를 사용할 수 있게 되며 임시 캐시 된 데이터는 데이터 저장소에 수집 CMK를 사용 하 여 암호화 됩니다.

- *클러스터* 리소스를 만들고 KeyVaultProperties를 즉시 지정 하는 경우 시스템 Id가 *클러스터* 리소스에 할당 될 때까지 액세스 정책을 정의할 수 없으므로 작업이 실패할 수 있습니다.

- KeyVaultProperties를 사용 하 여 기존 *클러스터* 리소스를 업데이트 하 고 ' Get ' 키 액세스 정책이 Key Vault에 없는 경우 작업이 실패 합니다.

- 작업 영역에 연결 된 *클러스터* 리소스를 삭제 하려고 하면 삭제 작업은 실패 합니다.

- *클러스터* 리소스를 만들 때 충돌 오류가 발생 하는 경우 – 지난 14 일 동안 *클러스터* 리소스를 삭제 하 고 일시 삭제 기간에 있을 수 있습니다. *클러스터* 리소스 이름은 일시 삭제 기간 동안 예약 된 상태로 유지 되며 해당 이름을 사용 하 여 새 클러스터를 만들 수 없습니다. 이 이름은 *클러스터* 리소스가 영구적으로 삭제 될 때 일시 삭제 기간이 지나면 릴리스됩니다.

- 작업이 진행 되는 동안 *클러스터* 리소스를 업데이트 하는 경우 작업이 실패 합니다.
