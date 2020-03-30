---
title: Azure 키 볼트에 대한 Azure 이벤트 그리드 이벤트 스키마
description: Azure 이벤트 그리드를 통해 Azure 키 볼트 이벤트에 대해 제공된 속성 및 스키마에 대해 설명합니다.
services: event-grid
author: msmbaldwin
ms.service: event-grid
ms.topic: reference
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: 17404388b2b6c3fee1c6ab666f7233a66817f642
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74082861"
---
# <a name="azure-event-grid-event-schema-for-azure-key-vault-preview"></a>Azure Key Vault에 대한 Azure Event Grid 이벤트 스키마(미리 보기)

이 문서에서는 현재 미리 보기 중인 [Azure Key Vault의](../key-vault/index.yml)이벤트에 대한 속성 및 스키마를 제공합니다. 이벤트 스키마에 대한 소개는 [Azure Event Grid 이벤트 스키마](event-schema.md)를 참조하세요.

## <a name="available-event-types"></a>사용할 수 있는 이벤트 유형

Azure Key Vault 계정은 다음과 같은 이벤트 유형을 생성합니다.

| 이벤트 전체 이름 | 이벤트 표시 이름 | 설명 |
| ---------- | ----------- |---|
| 마이크로소프트.키볼트.인증서새로운버전생성 | 인증서 새 버전 생성 | 새 인증서 또는 새 인증서 버전이 생성될 때 트리거됩니다. |
| 마이크로소프트.키볼트.인증서가까운 엑스피리 | 만료 근처 인증서 | 현재 버전의 인증서가 만료될 때 트리거됩니다. 기본값은 만료날짜 30일 전입니다. |
| 마이크로소프트.키볼트.인증서 만료됨 | 인증서 만료됨 | 인증서가 만료될 때 트리거됩니다. |
| 마이크로소프트.키볼트.키뉴버전 생성 | 새 버전 키 생성 | 새 키 또는 새 키 버전이 생성될 때 트리거됩니다. |
| 마이크로소프트.키볼트.키니어익스피리 | 만료 근처 의 키 | 키의 현재 버전이 곧 만료될 때 트리거됩니다. 기본값은 만료날짜 30일 전입니다. |
| 마이크로소프트.키볼트.키 만료됨 | 키 만료됨 | 키가 만료되면 트리거됩니다. |
| 마이크로소프트.키볼트.시크릿뉴버전생성 | 비밀 새 버전 생성 | 새 비밀 또는 새 비밀 버전이 생성될 때 트리거됩니다. |
| 마이크로소프트.키볼트.시크릿니어익스피리 | 만료 에 가까운 비밀 | 현재 버전의 보안 이 만료될 때 트리거됩니다. 기본값은 만료날짜 30일 전입니다. |
| 마이크로소프트.키볼트.비밀 만료됨 | 비밀이 만료됨 | 비밀이 만료되면 트리거됩니다. |

## <a name="event-examples"></a>이벤트 예제

다음 예제는 **Microsoft.KeyVault.SecretNewVersion만든**스키마를 보여 준다.

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

## <a name="event-properties"></a>이벤트 속성

이벤트에는 다음과 같은 최상위 데이터가 있습니다.

| 속성 | Type | Description |
| ---------- | ----------- |---|
| id | 문자열 | 이 이벤트를 트리거한 개체의 ID |
| vaultName | 문자열 | 이 이벤트를 트리거한 개체의 키 자격 증명 모음 이름 |
| objectType | 문자열 | 이 이벤트를 트리거한 개체의 형식 |
| Objectname | 문자열 | 이 이벤트를 트리거한 개체의 이름 |
| 버전 | 문자열 | 이 이벤트를 트리거한 개체의 버전 |
| nbf | number | 이 이벤트를 트리거한 개체의 1970-01-01T00:00:00Z 이후 초이 |
| exp | number | 이 이벤트를 트리거한 개체의 1970-01-01T00:00:00Z 이후의 만료 날짜 |


## <a name="next-steps"></a>다음 단계

* Azure 이벤트 그리드에 대한 소개는 [이벤트 그리드란 무엇입니까?](overview.md)
* Azure Event Grid 구독을 만드는 방법에 대한 자세한 내용은 [Event Grid 구독 스키마](subscription-creation-schema.md)를 참조하십시오.
* 이벤트 그리드와의 키 볼트 통합에 대한 자세한 내용은 [Azure 이벤트 그리드를 사용하여 키 볼트 모니터링(미리 보기)을](../key-vault/event-grid-overview.md)참조하십시오.
* 이벤트 그리드와의 키 볼트 통합에 대한 자습서는 [Azure 이벤트 그리드(미리 보기)를 사용하여 키 볼트 알림 수신 및 응답을](../key-vault/event-grid-tutorial.md)참조합니다.
* 키 자격 증명 모음 및 Azure 자동화에 대한 추가 지침을 얻으려면 다음을 참조하십시오.
    - [Azure Key Vault란?](../key-vault/key-vault-overview.md)
    - [Azure Event Grid를 사용하여 Key Vault 모니터링(미리 보기)](../key-vault/event-grid-overview.md)
    - [Azure Event Grid를 사용하여 키 자격 증명 모음 알림 수신 및 응답(미리보기)](../key-vault/event-grid-tutorial.md)
    - [Azure Automation 개요](../automation/index.yml)
