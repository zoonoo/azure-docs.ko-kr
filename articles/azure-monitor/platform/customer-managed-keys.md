---
title: Azure 모니터 고객 관리 키 구성
description: Azure Key Vault 키를 사용하여 로그 분석 작업 영역의 데이터를 암호화하도록 CMK(고객 관리 키)를 구성하는 정보 및 단계입니다.
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 03/26/2020
ms.openlocfilehash: 563a50d4589a83f710caa8ff2d2d95065909fc5f
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384180"
---
# <a name="azure-monitor-customer-managed-key-configuration"></a>Azure 모니터 고객 관리 키 구성 

이 문서에서는 로그 분석 작업 영역 및 응용 프로그램 인사이트 구성 요소에 대한 CMK(고객 관리 키)를 구성하는 배경 정보 및 단계를 제공합니다. 구성되면 작업 영역 또는 구성 요소로 전송되는 모든 데이터는 Azure Key Vault 키로 암호화됩니다.

구성 하기 전에 아래 [제한 및 제약 조건을](#limitations-and-constraints) 검토 하는 것이 좋습니다.

## <a name="disclaimers"></a>고지 사항

- Azure 모니터 CMK는 초기 액세스 기능이며 등록된 구독에 대해 활성화됩니다.

- 이 문서에서 설명하는 CMK 배포는 프로덕션 품질로 제공되며 초기 액세스 기능임에도 불구하고 지원됩니다.

- CMK 기능은 Azure 데이터 탐색기(ADX) 클러스터인 전용 데이터 저장소 클러스터에서 제공되며 하루에 1TB 이상을 보내는 고객에게 적합합니다. 

- CMK 가격 책정 모델은 현재 사용할 수 없으며 이 문서에서 다루지 않습니다. 전용 ADX 클러스터의 가격 책정 모델은 2020년 2분기(CY) 2분기에 예상되며 기존 CMK 배포에 적용됩니다.

- 이 문서에서는 로그 분석 작업 영역에 대한 CMK 구성에 대해 설명합니다. 차이점이 부록에 나열되어 있는 동안 응용 프로그램 인사이트 구성 요소에 대한 CMK도 이 문서를 사용하여 지원됩니다.

> [!NOTE]
> 로그 분석 및 애플리케이션 인사이트는 동일한 데이터 저장소 플랫폼 및 쿼리 엔진을 사용하고 있습니다.
> 응용 프로그램 인사이트를 로그 애널리틱스에 통합하여 Azure Monitor에서 단일 통합 로그 저장소를 만들어 이러한 두 저장소를 함께 가져오고 있습니다. 이 변경 사항은 2020년 2분기에 계획되어 있습니다. 그때까지 응용 프로그램 인사이트 데이터에 CMK를 배포할 필요가 없는 경우 통합으로 마이그레이션한 후 이러한 배포가 중단되고 로그로 마이그레이션한 후 CMK를 다시 구성해야 하므로 통합이 완료될 때까지 기다리는 것이 좋습니다. 분석 작업 영역. 일일 최소 1TB는 클러스터 수준에서 적용되며 2분기 동안 통합이 완료될 때까지 애플리케이션 인사이트 및 로그 분석에는 별도의 클러스터가 필요합니다.

## <a name="customer-managed-key-cmk-overview"></a>CMK(고객 관리 키) 개요

[미사용 암호화는](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest) 조직에서 일반적인 개인 정보 보호 및 보안 요구 사항입니다. 암호화 또는 암호화 키를 면밀히 관리하는 다양한 옵션이 있는 동안 Azure에서 미사용 암호화를 완전히 관리할 수 있도록 할 수 있습니다.

Azure Monitor 데이터 저장소는 Azure 저장소에 저장되는 동안 Azure 관리 키를 사용하여 미사용 으로 암호화된 모든 데이터를 보장합니다. 또한 Azure Monitor는 시스템 할당된 [관리되는 ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) 인증을 사용하여 액세스되는 [Azure Key Vault에](https://docs.microsoft.com/azure/key-vault/key-vault-overview)저장된 사용자 고유의 키를 사용하여 데이터 암호화 옵션을 제공합니다. 이 키는 [소프트웨어 또는 하드웨어 HSM 보호](https://docs.microsoft.com/azure/key-vault/key-vault-overview)될 수 있습니다.
Azure 모니터 암호화 사용은 [Azure 저장소 암호화가](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption) 작동하는 방식과 동일합니다.

Azure Monitor Storage가 랩 핑 및 래핑 해제 작업을 위해 키 볼트에 액세스하는 빈도는 6~60초입니다.Azure 모니터 저장소는 항상 1시간 이내에 키 사용 권한의 변경 내용을 존중합니다.

또한 효율적인 쿼리 엔진 작동을 위해 지난 14일 동안 수집된 데이터는 핫 캐시(SSD 지원)에 보관됩니다. 이 데이터는 CMK 구성에 관계없이 Microsoft 키로 암호화된 상태로 유지되지만 2020년 초에 CmK로 SSD를 암호화하기 위해 노력하고 있습니다.

## <a name="how-cmk-works-in-azure-monitor"></a>AZURE 모니터에서 CMK 작동 방식

Azure Monitor는 시스템 할당된 관리되는 ID를 활용하여 Azure 키 자격 증명 모음에 대한 액세스 권한을 부여합니다.시스템 할당된 관리되는 ID는 단일 Azure 리소스에만 연결할 수 있습니다. Azure Monitor 데이터 저장소(ADX 클러스터)의 ID는 클러스터 수준에서 지원되며 CMK 기능이 전용 ADX 클러스터에서 제공되도록 합니다. 여러 작업 공간에서 CMK를 지원하기 위해 새 로그 분석*리소스(클러스터)는*키 볼트와 로그 분석 작업 영역 간의 중간 ID 연결로 수행됩니다. 이 개념은 시스템에서 할당된 ID 제약 조건을 준수하며 ADX 클러스터와 로그 분석 *클러스터* 리소스 간에 ID가 유지되고 연결된 모든 작업 영역의 데이터는 Key Vault 키로 보호됩니다. 언더레이 ADX 클러스터 저장소는\' *클러스터* 리소스와 연결된 관리되는 ID를 사용하여 Azure Active Directory를 통해 Azure 키 자격 증명 모음을 인증하고 액세스합니다.

![CMK 개요](media/customer-managed-keys/cmk-overview.png)
1.  고객의 키 볼트.
2.  키 볼트에 대한 권한으로 ID를 관리하는 고객의 로그 분석 *클러스터* 리소스 - ID는 데이터 저장소(ADX 클러스터) 수준에서 지원됩니다.
3.  Azure 모니터 전용 ADX 클러스터입니다.
4.  CMK 암호화를 위해 클러스터 리소스에 연결된 고객의 작업 영역입니다.

## <a name="encryption-keys-management"></a>암호화 키 관리

저장소 데이터 암호화와 관련된 키에는 세 가지 유형이 있습니다.

- **KEK** - 키 볼트(CMK)의 키 암호화 키
- **AEK** - 계정 암호화 키
- **DEK** - 데이터 암호화 키

다음 규칙이 적용됩니다.

- ADX 저장소 계정은 AEK라고 하는 모든 저장소 계정에 대해 고유한 암호화 키를 생성합니다.

- AEK는 디스크에 기록된 각 데이터 블록을 암호화하는 데 사용되는 키인 DEK를 파생시키는 데 사용됩니다.

- 키 볼트에서 키를 구성하고 *클러스터* 리소스에서 참조하면 Azure Storage는 Azure Key Vault에 요청을 보내 AEK를 래핑하고 래핑 해제하여 데이터 암호화 및 암호 해독 작업을 수행합니다.

- KEK는 키 볼트를 떠나지 않으며 HSM 키의 경우 하드웨어를 남기지 않습니다.

- Azure Storage는 *클러스터* 리소스와 연결된 관리되는 ID를 사용하여 Azure Active Directory를 통해 Azure 키 자격 증명 모음을 인증하고 액세스할 수 있습니다.

## <a name="cmk-provisioning-procedure"></a>CMK 프로비저닝 절차

애플리케이션 인사이트 CMK 구성의 경우 3단계와 6단계의 부록 내용을 따릅니다.

1. 구독 허용 목록 -- 이 초기 액세스 기능에 필요합니다.
2. Azure 키 볼트 만들기 및 키 저장
3. *클러스터* 리소스 만들기
4. Azure 모니터 데이터 저장소(ADX 클러스터) 프로비저닝
5. 키 볼트에 대한 권한 부여
6. 로그 분석 작업 영역 연결

프로시저는 현재 UI에서 지원되지 않으며 프로비저닝 프로세스는 REST API를 통해 수행됩니다.

> [!IMPORTANT]
> 모든 API 요청은 요청 헤더에 Bearer 권한 부여 토큰을 포함해야 합니다.

예를 들어:

```rst
GET
https://management.azure.com/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>?api-version=2015-11-01-preview
Authorization: Bearer eyJ0eXAiO....
```

*여기서 eyJ0eXAiO는* 전체 권한 부여 토큰을 나타냅니다. 

다음 방법 중 하나를 사용하여 토큰을 획득할 수 있습니다.

1. [앱 등록](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens) 방법을 사용합니다.

2. Azure 포털에서 다음을 수행합니다.
    1. "개발자 도구(F12)에서 Azure 포털로 이동
    1. "일괄 처리?api 버전" 인스턴스 중 하나에서 "헤더 요청" 아래에서 권한 부여 문자열을 찾습니다. 그것은 다음과 같습니다 : "권한 부여 : 베어러 \<토큰\>". 
    1. 아래 예제에 따라 API 호출에 복사하여 추가합니다.

3. Azure REST 설명서 사이트로 이동합니다. 모든 API에서 "사용해 보십시오"를 누르고 Bearer 토큰을 복사합니다.

### <a name="subscription-whitelisting"></a>구독 허용 목록

CMK 기능은 초기 액세스 기능입니다. *클러스터* 리소스를 만들려는 구독은 Azure 제품 그룹에서 미리 화이트리스트에 등록해야 합니다. Microsoft에 연락처를 사용하여 구독 아이디를 제공합니다.

> [!IMPORTANT]
> CMK 기능은 지역별 기능입니다. Azure 키 자격 증명 모음, *클러스터* 리소스 및 연결된 로그 분석 작업 영역은 동일한 지역에 있어야 하지만 다른 구독에 있을 수 있습니다.

### <a name="storing-encryption-key-kek"></a>암호화 키 저장(KEK)

이미 생성해야 하는 Azure Key Vault를 만들거나 사용하거나 데이터 암호화에 사용할 키를 가져옵니다. Azure 키 볼트는 Azure 모니터의 데이터에 대한 키 및 액세스를 보호하기 위해 복구 가능한 것으로 구성되어야 합니다. 키 자격 증명 모음의 속성에서 이 구성을 확인할 수 있으며 *소프트 삭제* 및 *지우기 보호가* 모두 활성화되어야 합니다.

이러한 설정은 CLI 및 PowerShell을 통해 사용할 수 있습니다.
- [일시 삭제](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
- 소프트 삭제 후에도 비밀 / 금고의 힘 삭제에 대한 [보호 가드를 제거합니다.](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection)

### <a name="create-cluster-resource"></a>*클러스터* 리소스 만들기

이 리소스는 Key Vault와 작업 영역 간의 중간 ID 연결로 사용됩니다. 구독이 목록에 등록되었다는 확인을 받은 후 작업 영역이 있는 리전에서 로그 분석 *클러스터* 리소스를 만듭니다. 애플리케이션 인사이트 및 로그 분석에는 별도의 *클러스터* 리소스 유형이 필요합니다. *클러스터* 리소스의 형식은 "ClusterType" 속성을 "LogAnalytics" 또는 "ApplicationInsights"로 설정하여 생성 시 정의됩니다. 클러스터 리소스 유형은 이후에 변경할 수 없습니다.

애플리케이션 인사이트 CMK 구성의 경우 부록 내용을 따르십시오.

*클러스터* 리소스를 만들 때 *클러스터* 리소스에 대한 용량 예약 수준(sku)을 지정해야 합니다. 용량 예약 수준은 1000에서 2000 범위일 수 있으며 나중에 100단계로 업데이트할 수 있습니다. 용량 예약 수준이 2000보다 높으면 Microsoft 담당자에게 문의하여 활성화하십시오. 이 속성은 현재 청구에 영향을 주지 않습니다.전용 클러스터에 대 한 가격 모델이 도입 되면 기존 CMK 배포에 대 한 청구가 적용 됩니다.

**만들기**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
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
    "clusterType": "LogAnalytics",
    },
  "location": "<region-name>",
}
```
ID는 생성 시 *클러스터* 리소스에 할당됩니다.

**Response**

202 수락. 비동기 작업에 대한 표준 리소스 관리자 응답입니다.

### <a name="azure-monitor-data-store-adx-cluster-provisioning"></a>Azure 모니터 데이터 저장소(ADX 클러스터) 프로비저닝

기능의 초기 액세스 기간 동안 ADX 클러스터는 이전 단계가 완료되면 제품 팀에서 수동으로 프로비전됩니다. *클러스터* 리소스 응답을 제공 하면서 프로비저닝에 대 한 Microsoft 채널을 사용 합니다. 

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
```

> [!IMPORTANT]
> 이후 단계에서 이러한 세부 정보가 필요하므로 응답을 복사하고 저장합니다.

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
    "clusterType": "LogAnalytics", 
    "clusterId": "cluster-id"
    },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

>[!Important]
> ADX 클러스터의 프로비저닝을 완료하는 데 몇 분 정도 걸립니다. *프로비저닝State* 값은 *프로비저닝 중프로비전계정상태이며 프로비저닝이* 완료될 때 "성공"을 나타냅니다.
> "principalId" GUID는 *클러스터* 리소스에 대한 관리되는 ID 서비스에 의해 생성됩니다.

### <a name="grant-key-vault-permissions"></a>키 볼트 권한 부여

*클러스터* 리소스에 권한을 부여하는 새 액세스 정책으로 키 볼트를 업데이트합니다. 이러한 권한은 데이터 암호화를 위해 언더레이 Azure 모니터 저장소에서 사용됩니다. Azure 포털에서 키 자격 증명 모음을 열고 "액세스 정책"을 클릭한 다음 "+ 액세스 정책 추가"를 클릭하여 이러한 설정을 사용하여 정책을 만듭니다.

- 키 사용 권한: '받기', '키 줄 바꿈' 및 '키 둘러싸기 해제' 권한을 선택합니다.
- 보안 주체 선택: 이전 단계에서 응답에서 반환된 보안 주체 id 값을 입력합니다.

![키 볼트 권한 부여](media/customer-managed-keys/grant-key-vault-permissions.png)

*Get* 권한은 키 자격 증명 모음이 복구 가능한 것으로 구성되어 있는지 확인하여 키및 Azure Monitor 데이터에 대한 액세스를 보호하는 데 필요합니다.

### <a name="update-cluster-resource-with-key-identifier-details"></a>키 식별자 세부 정보로 클러스터 리소스 업데이트

이 단계는 Key Vault의 초기 및 향후 키 버전 업데이트에 따라 적용됩니다. 새 키 버전에 대해 Azure 모니터 저장소에 알립니다.

키 볼트 *키 식별자* 세부 정보로 *클러스터* 리소스를 업데이트하려면 Azure Key Vault에서 키의 현재 버전을 선택하여 키 식별자 세부 정보를 가져옵니다.

![키 볼트 권한 부여](media/customer-managed-keys/key-identifier-8bit.png)

키 식별자 세부 정보로 *클러스터* 리소스 KeyVaultProperties를 업데이트합니다.

**업데이트**

>[!Warning]
> *ID,* *sku,* *KeyVaultProperties* 및 *위치가*포함된 *클러스터* 리소스 업데이트에서 전체 본문을 제공해야 합니다. *KeyVaultProperties* 세부 정보가 누락되면 *클러스터* 리소스에서 키 식별자가 제거되고 [키 가 해지됩니다.](#cmk-kek-revocation)

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
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
     "KeyVaultProperties": {
       KeyVaultUri: "https://<key-vault-name>.vault.azure.net",
       KeyName: "<key-name>",
       KeyVersion: "<current-version>"
       },
   },
   "location":"<region-name>"
}
```
"KeyVaultProperties"에는 키 볼트 키 식별자 세부 정보가 포함되어 있습니다.

**Response**

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
    "KeyVaultProperties": {
      KeyVaultUri: "https://key-vault-name.vault.azure.net",
      KeyName: "key-name",
      KeyVersion: "current-version"
      },
    "provisioningState": "Succeeded",
    "clusterType": "LogAnalytics", 
    "clusterId": "cluster-id"
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

### <a name="workspace-association-to-cluster-resource"></a>*클러스터* 리소스에 대한 작업 영역 연결

응용 프로그램 인사이트 CMK 구성의 경우 이 단계의 부록 내용을 따르십시오.

> [!IMPORTANT]
> 이 단계는 ADX 클러스터 프로비전 후에만 수행해야 합니다. 프로비저닝 전에 작업 영역과 수집 데이터를 연결하면 수집된 데이터가 삭제되고 복구할 수 없습니다.
> ADX 클러스터가 프로비전되었는지 확인하려면 *클러스터* 리소스 Get REST API를 실행하고 *프로비저닝State* 값이 *성공했는지*확인합니다.

다음 작업을 포함하는 이 작업을 수행하려면 작업 영역과 *클러스터* 리소스 모두에 대한 '쓰기' 권한이 있어야 합니다.

- 작업 영역에서: Microsoft.오퍼리인사이트/작업 영역/쓰기
- *클러스터* 리소스: Microsoft.운영 인사이트/클러스터/쓰기

**작업 영역 연결**
```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2019-08-01-preview 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "WriteAccessResourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    }
}
```

**Response**

```json
{
  "properties": {
    "WriteAccessResourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    },
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name/linkedservices/cluster",
  "name": "workspace-name/cluster",
  "type": "microsoft.operationalInsights/workspaces/linkedServices",
}
```

작업 영역 연결이 연결되면 작업 영역에 수집된 데이터가 관리키로 암호화되어 저장됩니다.

### <a name="workspace-association-verification"></a>작업 영역 연결 확인
작업 영역 [-](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) 응답 받기를 확인하여 작업 영역이 *Custer* 리소스에 연결되어 있는지 확인할 수 있습니다. 연결된 작업 영역에는 *클러스터* 리소스 ID가 있는 'clusterResourceId' 속성이 있습니다.

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2015-11-01-preview
```

**Response**

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

## <a name="cmk-kek-revocation"></a>CMK(KEK) 해지

키 를 비활성화하거나 키 볼트에서 *클러스터* 리소스 액세스 정책을 삭제하여 데이터에 대한 액세스를 취소할 수 있습니다. Azure Monitor Storage는 일반적으로 1시간 이내에 키 사용 권한의 변경 내용을 항상 더 빨리 적용하며 저장소를 사용할 수 없게 됩니다. *클러스터* 리소스와 연결된 작업 영역에 수집된 모든 데이터가 삭제되고 쿼리가 실패합니다. 이전에 수집된 데이터는 키가 해지되고 작업 영역이 삭제되지 않는 한 Azure Monitor Storage에서 액세스할 수 없습니다. 액세스할 수 없는 데이터는 데이터 보존 정책의 적용을 받으며 보존에 도달하면 제거됩니다.

저장소는 Key Vault에서 주기적으로 폴링하여 암호화 키의 래핑을 해제하고 액세스한 후 30분 이내에 데이터 수집 및 쿼리를 다시 시작합니다.

## <a name="cmk-kek-rotation"></a>CMK(KEK) 회전

CMK를 사용하려면 Azure 키 볼트의 새 키 버전과 함께 *클러스터* 리소스를 명시적으로 업데이트해야 합니다. 새 키 버전으로 Azure Monitor를 업데이트하려면 "키 식별자 세부 정보로 *클러스터* 리소스 업데이트" 단계의 지침을 따릅니다. 키 볼트에서 키 버전을 업데이트하고 *클러스터* 리소스의 새 키 식별자 세부 정보를 업데이트하지 않으면 Azure 모니터 저장소는 이전 키를 계속 사용하게 됩니다.
모든 데이터는 이제 새 키 암호화 키(KEK) 버전으로 암호화되는 동안 모든 데이터가 AEK(계정 암호화 키)에 의해 암호화된 상태로 유지되므로 회전 전과 그 후에 수집된 데이터를 포함하여 키 회전 작업 후에 액세스할 수 있습니다.

## <a name="limitations-and-constraints"></a>제한 및 제약 조건

- CMK 기능은 ADX 클러스터 수준에서 지원되며 전용 Azure 모니터 ADX 클러스터가 필요합니다.

- 구독당 최대 *클러스터* 리소스 수는 2개로 제한됩니다.

- *작업* 영역에 대한 클러스터 리소스 연결은 제품 그룹으로부터 ADX 클러스터 프로비저닝이 수행되었다는 확인을 받은 후에만 수행해야 합니다. 이 프로비저닝 이전에 전송된 데이터는 삭제되고 복구할 수 없습니다.

- CMK 암호화는 CMK 구성 후 새로 수집된 데이터에 적용됩니다. CMK 구성 이전에 수집된 데이터는 Microsoft 키로 암호화된 상태로 유지됩니다. CMK 구성 전후에 데이터를 원활하게 쿼리할 수 있습니다.

- 작업 영역이 *클러스터* 리소스에 연결되면 데이터가 키로 암호화되고 Azure Key Vault에서 KEK 없이는 액세스할 수 없으므로 *클러스터* 리소스에서 연결 해제할 수 없습니다.

- Azure 키 자격 증명 모음을 복구 가능한 것으로 구성해야 합니다. 이러한 속성은 기본적으로 활성화되지 않으며 CLI 및 PowerShell을 사용하여 구성해야 합니다.

  - [소프트 삭제를](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) 켜야 합니다.
  - [지우기 보호는](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) 소프트 삭제 후에도 비밀 / 금고의 강제 삭제를 방지하도록 설정해야합니다.

- 애플리케이션 인사이트 및 로그 분석에는 별도의 *클러스터* 리소스가 필요합니다. *클러스터* 리소스의 형식은 "clusterType" 속성을 'LogAnalytics' 또는 'ApplicationInsights'로 설정하여 생성 시 정의됩니다. *클러스터* 리소스 유형은 변경할 수 없습니다.

- *클러스터* 리소스가 다른 리소스 그룹으로 이동하거나 구독이 현재 지원되지 않습니다.

- Azure 키 자격 증명 모음, *클러스터* 리소스 및 연결된 작업 영역은 동일한 리전과 동일한 Azure Active Directory(Azure AD) 테넌트에 있어야 하지만 다른 구독에 있을 수 있습니다.

- *다른* 클러스터 리소스에 연결된 경우 *클러스터* 리소스에 대한 작업 영역 연결이 실패합니다.

## <a name="troubleshooting-and-management"></a>문제 해결 및 관리

- 키 볼트 가용성
    - 정상적인 작업에서는 저장소 캐시 AEK 짧은 기간 동안 주기적으로 키 볼트로 돌아가래핑을 해제합니다.
    
    - 일시적인 연결 오류입니다. 저장소는 일시적인 오류(시간 시간 지정, 연결 오류, DNS 문제)를 처리하여 키가 잠시 동안 캐시에 머무를 수 있도록 허용하므로 가용성이 작아도 이를 극복할 수 있습니다. 쿼리 및 인비전에이션 기능은 중단 없이 계속됩니다.
    
    - 라이브 사이트, 약 30분 동안 사용할 수 없게 되면 저장소 계정을 사용할 수 없게 됩니다. 쿼리 기능을 사용할 수 없으며 데이터 손실을 방지하기 위해 Microsoft 키를 사용하여 수집된 데이터가 몇 시간 동안 캐시됩니다. Key Vault에 대한 액세스가 복원되면 쿼리를 사용할 수 있게 되고 임시 캐시된 데이터가 데이터 저장소로 수집되고 CMK로 암호화됩니다.

- *클러스터* 리소스를 만들고 KeyVaultProperties를 즉시 지정하면 시스템 ID가 *클러스터* 리소스에 할당될 때까지 액세스 정책을 정의할 수 없으므로 작업이 실패할 수 있습니다.

- KeyVaultProperties를 사용 하 고 키 볼트에서 'Get' 키 액세스 정책을 사용 하 고 기존 *클러스터* 리소스를 업데이트 하는 경우 작업이 실패 합니다.

- 작업 영역에 연결된 *클러스터* 리소스를 삭제하려고 하면 삭제 작업이 실패합니다.

- 리소스 그룹에 대한 모든 *클러스터* 리소스 가져오기:

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2019-08-01-preview
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
        "properties": {
           "KeyVaultProperties": {
              KeyVaultUri: "https://key-vault-name.vault.azure.net",
              KeyName: "key-name",
              KeyVersion: "current-version"
              },
          "provisioningState": "Succeeded",
          "clusterType": "LogAnalytics", 
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

- 구독에 대한 모든 *클러스터* 리소스 가져오기:

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2019-08-01-preview
  Authorization: Bearer <token>
  ```
    
  **Response**
    
  '리소스 그룹에 대한*클러스터* 리소스'와 동일하지만 구독 범위에 해당합니다.
    
- *클러스터* 리소스 삭제 - 삭제가 우발적이든 의도적이든 14일 이내에 *클러스터* 리소스, 데이터 및 관련 작업 영역을 복구할 수 있도록 소프트 삭제 작업이 수행됩니다. 소프트 삭제 기간 이후에는 *클러스터* 리소스 및 데이터를 복구할 수 없습니다. *클러스터* 리소스 이름은 소프트 삭제 기간 동안 예약된 상태로 유지되며 해당 이름으로 새 클러스터를 만들 수 없습니다.

  ```rst
  DELETE
  https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
  Authorization: Bearer <token>
  ```

  **Response**

  200 정상

- 소프트 삭제 기간 동안 *클러스터* 리소스와 데이터를 복구하여 동일한 이름과 동일한 구독, 리소스 그룹 및 리전에서 *클러스터* 리소스를 만듭니다. 클러스터 **리소스 *Cluster* 만들기** 단계를 수행하여 *클러스터* 리소스를 복구합니다.


## <a name="appendix"></a>부록

응용 프로그램 인사이트 고객 관리 키(CMK)도 지원되지만 응용 프로그램 인사이트 구성 요소에 대한 CMK 배포를 계획하는 데 도움이 되는 다음 변경 사항을 고려해야 합니다.

로그 분석 및 애플리케이션 인사이트는 동일한 데이터 저장소 플랫폼 및 쿼리 엔진을 사용하고 있습니다. 우리는 로그 분석에 응용 프로그램 인사이트를 통합하여 이 두 저장소를 함께 가져와 2 분기까지 Azure Monitor에서 단일 통합 로그 저장소를 제공합니다.
2020. 이러한 변경으로 인해 애플리케이션 인사이트 데이터가 Log Analytics 작업 영역으로 유입되고 쿼리, 인사이트 및 기타 개선 사항이 가능해지며 작업 영역에 CMK 구성도 적용되며 애플리케이션 인사이트 데이터에도 적용됩니다.

> [!NOTE]
> 통합 하기 전에 응용 프로그램 인사이트 데이터에 대 한 CMK를 배포할 필요가 없는 경우 이러한 배포는 통합에 의해 중단 되 고 로그로 마이그레이션 후 CMK를 다시 구성 해야 하므로 응용 프로그램 인사이트 CMK를 기다리는 것이 좋습니다. 분석 작업 영역. 일일 최소 1TB는 클러스터 수준에서 적용되며 2분기 동안 통합이 완료될 때까지 애플리케이션 인사이트 및 로그 분석에는 별도의 클러스터가 필요합니다.

## <a name="application-insights-cmk-configuration"></a>애플리케이션 인사이트 CMK 구성

응용 프로그램 인사이트 CMK의 구성은 다음 단계를 제외한 제약 조건 및 문제 해결을 포함하여 이 문서에 설명된 프로세스와 동일합니다.

- *클러스터* 리소스 만들기
- 구성 요소를 *클러스터* 리소스에 연결

응용 프로그램 인사이트를 위해 CMK를 구성할 때는 위에 나열된 단계 대신 다음 단계를 사용합니다.

### <a name="create-a-cluster-resource"></a>*클러스터* 리소스 만들기

이 리소스는 키 자격 증명 모음과 구성 요소 간의 중간 ID 연결로 사용됩니다. 구독이 목록에 등록되었다는 확인을 받은 후 구성 요소가 있는 리전에서 로그 분석 *클러스터* 리소스를 만듭니다. *클러스터* 리소스의 형식은 *clusterType* 속성을 *LogAnalytics*또는 *ApplicationInsights*로 설정하여 생성 시 정의됩니다. *응용 프로그램 인사이트* CMK에 대한 응용 프로그램 인사이트여야 합니다. 구성 후 *clusterType* 설정을 변경할 수 없습니다.

**만들기**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
  "location": "<region-name>",
  "properties": {
      "clusterType":"ApplicationInsights"
  },
  "identity": {
      "type": "systemAssigned"
  }
}
```

**Response**

ID는 생성 시 *클러스터* 리소스에 할당됩니다.

```json

{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"
  },
  "sku": {
    "name": "capacityReservation",
    "Capacity": 1000
    },
  "properties": {
    "provisioningState": "Succeeded",
    "clusterType": "ApplicationInsights", 
    "clusterId": "cluster-id" 
    },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```
"원칙 id"는 관리되는 ID 서비스에서 생성된 GUID입니다.

> [!IMPORTANT]
> 다음 단계에서 필요하므로 "원칙 id" 값을 복사하고 유지합니다.

### <a name="associate-a-component-to-a-cluster-resource-using-components---create-or-update-api"></a>구성 요소 - API [만들기 또는 업데이트를](https://docs.microsoft.com/rest/api/application-insights/components/createorupdate) 사용하여 구성 요소를 *클러스터* 리소스에 연결

다음 작업을 포함하는 이 작업을 수행하려면 구성 요소와 *클러스터* 리소스 모두에 '쓰기' 권한이 있어야 합니다.

- 구성 요소: Microsoft.Insights/구성 요소/쓰기
- *클러스터* 리소스: Microsoft.운영 인사이트/클러스터/쓰기

> [!IMPORTANT]
> 이 단계는 ADX 클러스터 프로비전 후에만 수행해야 합니다. 프로비저닝 전에 구성 요소와 수집 데이터를 연결하면 수집된 데이터가 삭제되고 복구할 수 없습니다.
> ADX 클러스터가 프로비전되었는지 확인하려면 *클러스터* 리소스 Get REST API를 실행하고 *프로비저닝State* 값이 *성공했는지*확인합니다.

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
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
    "KeyVaultProperties": {
      KeyVaultUri: "https://key-vault-name.vault.azure.net",
      KeyName: "key-name",
      KeyVersion: "current-version"
      },
    "provisioningState": "Succeeded",
    "clusterType": "ApplicationInsights", 
    "clusterId": "cluster-id"
    },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
  }
```

**구성 요소 연결**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Insights/components/<component-name>?api-version=2015-05-01
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "clusterDefinitionId": "cluster-id"
  },
  "location": "<region-name>",
  "kind": "<component-type>"
}
```
"clusterDefinitionId"는 이전 단계에서 응답에 제공된 "clusterId" 값입니다.
"종류" 예는 "웹"입니다.

**Response**

```json
{
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.insights/components/component-name",
  "name": "component-name",
  "type": "Microsoft.Insights/components",
  "location": "region-name",
  "tags": "",
  "kind": "",
  "properties": {
    "clusterDefinitionId": "cluster-id"
    "ApplicationId": "",
    "AppId": "",
    "Application_Type": "",
    "Flow_Type": "",
    "Request_Source": "",
    "InstrumentationKey": "",
    "CreationDate": "",
    "TenantId": "",
    "HockeyAppId": "",
    "HockeyAppToken": "",
    "provisioningState": "",
    "SamplingPercentage":,
    "RetentionInDays":,
    "ConnectionString": "",
    "DisableIpMasking":,
    "ImmediatePurgeDataOn30Days": 
  }
}
```
"clusterDefinitionId"는 이 구성 요소에 연결된 *클러스터* 리소스 ID입니다.

연결 후 구성 요소로 전송되는 데이터는 관리 키로 암호화되어 저장됩니다.
