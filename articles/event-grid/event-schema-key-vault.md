---
title: Event Grid 원본으로 Azure Key Vault
description: Azure Key Vault 이벤트에 제공 되는 속성 및 스키마에 대해 설명 Azure Event Grid
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: ea8821b15000b74a10f28730ccf82b538e7819e5
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100363409"
---
# <a name="azure-key-vault-as-event-grid-source"></a>Event Grid 원본으로 Azure Key Vault

이 문서에서는 [Azure Key Vault](../key-vault/index.yml)이벤트에 대 한 속성 및 스키마를 제공 합니다. 이벤트 스키마에 대한 소개는 [Azure Event Grid 이벤트 스키마](event-schema.md)를 참조하세요.


## <a name="available-event-types"></a>사용할 수 있는 이벤트 유형

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
| VaultAccessPolicyChanged | 자격 증명 모음 액세스 정책이 변경 됨 | Key Vault에 대 한 액세스 정책이 변경 될 때 트리거됩니다. Azure 역할 기반 액세스 제어에서 Key Vault 권한 모델이 변경 되는 시나리오를 포함 합니다.   |

## <a name="event-examples"></a>이벤트 예제

# <a name="event-grid-event-schema"></a>[Event Grid 이벤트 스키마](#tab/event-grid-event-schema)

다음 예제에서는 **SecretNewVersionCreated** 에 대 한 스키마를 보여 줍니다.

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

# <a name="cloud-event-schema"></a>[클라우드 이벤트 스키마](#tab/cloud-event-schema)

다음 예제에서는 **SecretNewVersionCreated** 에 대 한 스키마를 보여 줍니다.

```JSON
[
   {
      "id":"00eccf70-95a7-4e7c-8299-2eb17ee9ad64",
      "source":"/subscriptions/{subscription-id}/resourceGroups/sample-rg/providers/Microsoft.KeyVault/vaults/sample-kv",
      "subject":"newsecret",
      "type":"Microsoft.KeyVault.SecretNewVersionCreated",
      "time":"2019-07-25T01:08:33.1036736Z",
      "data":{
         "Id":"https://sample-kv.vault.azure.net/secrets/newsecret/ee059b2bb5bc48398a53b168c6cdcb10",
         "vaultName":"sample-kv",
         "objectType":"Secret",
         "objectName ":"newsecret",
         "version":" ee059b2bb5bc48398a53b168c6cdcb10",
         "nbf":"1559081980",
         "exp":"1559082102"
      },
      "specversion":"1.0"
   }
]
```

---

### <a name="event-properties"></a>이벤트 속성

# <a name="event-grid-event-schema"></a>[Event Grid 이벤트 스키마](#tab/event-grid-event-schema)
이벤트에는 다음과 같은 최상위 데이터가 있습니다.

| 속성 | 형식 | Description |
| -------- | ---- | ----------- |
| `topic` | 문자열 | 이벤트 원본에 대한 전체 리소스 경로입니다. 이 필드는 쓸 수 없습니다. Event Grid는 이 값을 제공합니다. |
| `subject` | 문자열 | 게시자가 정의한 이벤트 주체의 경로입니다. |
| `eventType` | 문자열 | 이 이벤트 원본에 대해 등록된 이벤트 유형 중 하나입니다. |
| `eventTime` | 문자열 | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| `id` | 문자열 | 이벤트에 대한 고유 식별자입니다. |
| `data` | object | 앱 구성 이벤트 데이터입니다. |
| `dataVersion` | 문자열 | 데이터 개체의 스키마 버전입니다. 게시자가 스키마 버전을 정의합니다. |
| `metadataVersion` | 문자열 | 이벤트 메타데이터의 스키마 버전입니다. Event Grid는 최상위 속성의 스키마를 정의합니다. Event Grid는 이 값을 제공합니다. |


# <a name="cloud-event-schema"></a>[클라우드 이벤트 스키마](#tab/cloud-event-schema)

이벤트에는 다음과 같은 최상위 데이터가 있습니다.

| 속성 | 형식 | Description |
| -------- | ---- | ----------- |
| `source` | 문자열 | 이벤트 원본에 대한 전체 리소스 경로입니다. 이 필드는 쓸 수 없습니다. Event Grid는 이 값을 제공합니다. |
| `subject` | 문자열 | 게시자가 정의한 이벤트 주체의 경로입니다. |
| `type` | 문자열 | 이 이벤트 원본에 대해 등록된 이벤트 유형 중 하나입니다. |
| `time` | 문자열 | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| `id` | 문자열 | 이벤트에 대한 고유 식별자입니다. |
| `data` | object | 앱 구성 이벤트 데이터입니다. |
| `specversion` | 문자열 | CloudEvents 스키마 사양 버전입니다. |

---
 

데이터 개체의 속성은 다음과 같습니다.

| 속성 | 형식 | Description |
| ---------- | ----------- |---|
| `id` | 문자열 | 이 이벤트를 트리거한 개체의 ID입니다. |
| `vaultName` | 문자열 | 이 이벤트를 트리거한 개체의 키 자격 증명 모음 이름입니다. |
| `objectType` | 문자열 | 이 이벤트를 트리거한 개체의 형식입니다. |
| `objectName` | 문자열 | 이 이벤트를 트리거한 개체의 이름입니다. |
| `version` | 문자열 | 이 이벤트를 트리거한 개체의 버전입니다. |
| `nbf` | 숫자 | 이 이벤트를 트리거한 개체의 1970-01-01T00:00:00Z 이후 경과 되지 않은 날짜 (초)입니다. |
| `exp` | 숫자 | 1970-01-01T00:00:00Z 이후이 이벤트를 트리거한 개체의 만료 날짜 (초)입니다. |

## <a name="tutorials-and-how-tos"></a>자습서 및 방법
|제목  |설명  |
|---------|---------|
| [Azure Event Grid를 사용 하 여 Key Vault 이벤트 모니터링](../key-vault/general/event-grid-overview.md) | Event Grid와 Key Vault 통합에 대 한 개요입니다. |
| [자습서: Event Grid를 사용 하 여 Key Vault 이벤트 만들기 및 모니터링](../key-vault/general/event-grid-logicapps.md) | Key Vault에 대 한 Event Grid 알림을 설정 하는 방법을 알아봅니다. |


## <a name="next-steps"></a>다음 단계

* Azure Event Grid에 대 한 소개는 [Event Grid 무엇입니까?](overview.md)를 참조 하세요.
* Azure Event Grid 구독을 만드는 방법에 대 한 자세한 내용은 [Event Grid 구독 스키마](subscription-creation-schema.md)를 참조 하세요.
* Key Vault에 대 한 자세한 내용은 [Azure Key Vault 항목](../key-vault/general/overview.md) 을 참조 하세요.

