---
title: Event Grid 원본으로 Azure Key Vault
description: Azure Key Vault 이벤트에 제공 되는 속성 및 스키마에 대해 설명 Azure Event Grid
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: b3d961228429c1f84e45c1df9147fa1687ab5074
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91324079"
---
# <a name="azure-key-vault-as-event-grid-source"></a>Event Grid 원본으로 Azure Key Vault

이 문서에서는 [Azure Key Vault](../key-vault/index.yml)이벤트에 대 한 속성 및 스키마를 제공 합니다. 이벤트 스키마에 대한 소개는 [Azure Event Grid 이벤트 스키마](event-schema.md)를 참조하세요.

## <a name="event-grid-event-schema"></a>Event Grid 이벤트 스키마

### <a name="available-event-types"></a>사용할 수 있는 이벤트 유형

Azure Key Vault 계정은 다음과 같은 이벤트 유형을 생성 합니다.

| 이벤트 전체 이름 | 이벤트 표시 이름 | 설명 |
| ---------- | ----------- |---|
| CertificateNewVersionCreated | 인증서 새 버전이 만들어짐 | 새 인증서 또는 새 인증서 버전이 만들어질 때 트리거됩니다. |
| CertificateNearExpiry | 인증서 만료 임박 | 현재 버전의 인증서가 만료 될 때 트리거됩니다. 이 이벤트는 만료 날짜 30 일 전에 트리거됩니다. |
| Microsoft. KeyVault. 인증서 만료 됨 | 인증서 만료됨 | 인증서가 만료 되 면 트리거됩니다. |
| Microsoft. KeyNewVersionCreated | 새로 만든 주요 버전 | 새 키 또는 새 키 버전이 만들어질 때 트리거됩니다. |
| KeyNearExpiry | 키 만료가 임박 합니다. | 키의 현재 버전이 곧 만료 될 때 트리거됩니다. 이 이벤트는 만료 날짜 30 일 전에 트리거됩니다. |
| Microsoft. KeyVault. Keyvault 됨 | 키가 만료 됨 | 키가 만료 되 면 트리거됩니다. |
| SecretNewVersionCreated | 새 보안 버전이 만들어짐 | 새 비밀 또는 새 비밀 버전이 만들어질 때 트리거됩니다. |
| SecretNearExpiry | 비밀 만료 임박 | 암호의 현재 버전이 곧 만료 될 때 트리거됩니다. 이 이벤트는 만료 날짜 30 일 전에 트리거됩니다. |
| SecretExpired | 비밀 만료 됨 | 암호가 만료 되 면 트리거됩니다. |
| VaultAccessPolicyChanged | 자격 증명 모음 액세스 정책이 변경 됨 | Key Vault에 대 한 액세스 정책이 변경 될 때 트리거됩니다. Azure RBAC에서 Key Vault 권한 모델이 변경 되는 시나리오를 포함 합니다.  |

### <a name="event-examples"></a>이벤트 예제

다음 예제에서는 **SecretNewVersionCreated**에 대 한 스키마를 보여 줍니다.

```JSON
[
   {
      "id":"00eccf70-95a7-4e7c-8299-2eb17ee9ad64",
      "topic":"/subscriptions/{subscription-id}/resourceGroups/sample-rg/providers/Microsoft.KeyVault/vaults/sample-kv",
      "subject":"newsecret",
      "eventType":"Microsoft.KeyVault.SecretNewVersionCreated",
      "eventTime":"2019-07-25T01:08:33.1036736Z",
      "data":{
         "Id":"https://sample-kv.vault.azure.net/secrets/newsecret/ee059b2bb5bc48398a53b168c6cdcb10",
         "vaultName":"sample-kv",
         "objectType":"Secret",
         "objectName ":"newsecret",
         "version":" ee059b2bb5bc48398a53b168c6cdcb10",
         "nbf":"1559081980",
         "exp":"1559082102"
      },
      "dataVersion":"1",
      "metadataVersion":"1"
   }
]
```

### <a name="event-properties"></a>이벤트 속성

이벤트에는 다음과 같은 최상위 데이터가 있습니다.

| 속성 | 유형 | Description |
| ---------- | ----------- |---|
| id | 문자열 | 이 이벤트를 트리거한 개체의 ID입니다. |
| vaultName | 문자열 | 이 이벤트를 트리거한 개체의 키 자격 증명 모음 이름입니다. |
| objectType | 문자열 | 이 이벤트를 트리거한 개체의 형식입니다. |
| objectName | 문자열 | 이 이벤트를 트리거한 개체의 이름입니다. |
| 버전 | 문자열 | 이 이벤트를 트리거한 개체의 버전입니다. |
| nbf | number | 이 이벤트를 트리거한 개체의 1970-01-01T00:00:00Z 이후 경과 되지 않은 날짜 (초)입니다. |
| exp | number | 1970-01-01T00:00:00Z 이후이 이벤트를 트리거한 개체의 만료 날짜 (초)입니다. |

## <a name="tutorials-and-how-tos"></a>자습서 및 방법
|제목  |설명  |
|---------|---------|
| [Azure Event Grid를 사용 하 여 Key Vault 이벤트 모니터링](../key-vault/general/event-grid-overview.md) | Event Grid와 Key Vault 통합에 대 한 개요입니다. |
| [자습서: Event Grid를 사용 하 여 Key Vault 이벤트 만들기 및 모니터링](../key-vault/general/event-grid-logicapps.md) | Key Vault에 대 한 Event Grid 알림을 설정 하는 방법을 알아봅니다. |


## <a name="next-steps"></a>다음 단계

* Azure Event Grid에 대 한 소개는 [Event Grid 무엇입니까?](overview.md)를 참조 하세요.
* Azure Event Grid 구독을 만드는 방법에 대 한 자세한 내용은 [Event Grid 구독 스키마](subscription-creation-schema.md)를 참조 하세요.
* Key VAult에 대 한 자세한 내용은 [Azure Key Vault?](../key-vault/general/overview.md) 을 참조 하세요.

