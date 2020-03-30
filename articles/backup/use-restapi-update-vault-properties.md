---
title: REST API를 사용 하 고 복구 서비스 볼트 구성 업데이트
description: 이 문서에서는 REST API를 사용하여 볼트의 구성을 업데이트하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 12/06/2019
ms.assetid: 9aafa5a0-1e57-4644-bf79-97124db27aa2
ms.openlocfilehash: 6cecbb18e0cd6f548e1688ef978f10dcee7d9fbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252364"
---
# <a name="update-azure-recovery-services-vault-configurations-using-rest-api"></a>REST API를 사용하여 Azure 복구 서비스 볼트 구성 업데이트

이 문서에서는 REST API를 사용하여 Azure 복구 서비스 자격 증명 모음에서 백업 관련 구성을 업데이트하는 방법에 대해 설명합니다.

## <a name="soft-delete-state"></a>소프트 삭제 상태

보호된 항목의 백업을 삭제하는 것은 모니터링해야 하는 중요한 작업입니다. 실수로 삭제되지 않도록 Azure Recovery Services 자격 증명 모음에는 소프트 삭제 기능이 있습니다. 이 기능을 사용하면 고객은 삭제 후 기간 내에 필요한 경우 삭제된 백업을 복원할 수 있습니다.

그러나 이 기능이 필요하지 않은 시나리오가 있습니다. Azure 복구 서비스 자격 증명 모음 내에 일시 삭제된 백업 항목이 있는 경우 삭제할 수 없습니다. 볼트를 즉시 삭제해야 하는 경우 문제가 발생할 수 있습니다. 예를 들어 배포 작업은 종종 동일한 워크플로에서 생성된 리소스를 정리합니다. 배포는 볼트를 만들고, 항목에 대한 백업을 구성하고, 테스트 복원을 수행한 다음 백업 항목 및 볼트를 삭제할 수 있습니다. 볼트 삭제에 실패하면 전체 배포가 실패할 수 있습니다. 소프트 삭제를 사용하지 않도록 설정하는 것이 즉각적인 삭제를 보장하는 유일한 방법입니다.

따라서 고객은 시나리오에 따라 특정 자격 증명 모음에 대해 소프트 삭제를 사용하지 않도록 설정할지 여부를 신중하게 선택해야 합니다. 자세한 내용은 소프트 [삭제 문서를](backup-azure-security-feature-cloud.md#soft-delete)참조하십시오.

### <a name="fetch-soft-delete-state-using-rest-api"></a>REST API를 사용하여 소프트 삭제 상태 가져오기

기본적으로 새로 만든 복구 서비스 자격 증명 모음에 대해 소프트 삭제 상태가 활성화됩니다. 볼트의 소프트 삭제 상태를 가져오/업데이트하려면 백업 볼트의 구성 관련 [REST API 문서를](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs) 사용합니다.

볼트에 대한 현재 소프트 삭제 상태를 가져오려면 다음 *GET* 작업을 사용합니다.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-05-13
```

GET URI에는 `{subscriptionId}` `{vaultName}`매개 `{vaultresourceGroupName}` 변수가 있습니다. 이 예제에서는 `{vaultName}` "testVault"이고 `{vaultresourceGroupName}` "testVaultRG"입니다. 모든 필수 매개 변수가 URI에서 지정되므로 별도 요청 본문이 필요 없습니다.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-05-13
```

#### <a name="responses"></a>응답

'GET' 작업에 대한 성공적인 응답은 다음과 같습니다.

|이름  |Type  |Description  |
|---------|---------|---------|
|200 정상     |   [백업리소스볼트콘피그](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | 확인        |

##### <a name="example-response"></a>예제 응답

'GET' 요청이 제출되면 200(성공) 응답이 반환됩니다.

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testvaultRG/providers/Microsoft.RecoveryServices/vaults/testvault/backupconfig/vaultconfig",
  "name": "vaultconfig",
  "type": "Microsoft.RecoveryServices/vaults/backupconfig",
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Enabled"
  }
}
```

### <a name="update-soft-delete-state-using-rest-api"></a>REST API를 사용하여 소프트 삭제 상태 업데이트

REST API를 사용하여 복구 서비스 볼트의 소프트 삭제 상태를 업데이트하려면 다음 *PATCH* 작업을 사용합니다.

```http
PATCH https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-05-13
```

PATCH URI에는 `{subscriptionId}` `{vaultName}`매개 `{vaultresourceGroupName}` 변수가 있습니다. 이 예제에서는 `{vaultName}` "testVault"이고 `{vaultresourceGroupName}` "testVaultRG"입니다. URI를 위의 값으로 바꾸면 URI는 다음과 같이 표시됩니다.

```http
PATCH https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-05-13
```

#### <a name="create-the-request-body"></a>요청 본문 만들기

THe 는 공통 정의를 따르는 것이 요청 본문을 만드는 데 사용됩니다.

자세한 내용은 REST [API 설명서를](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/update#request-body) 참조하십시오.

|이름  |필수  |Type  |Description  |
|---------|---------|---------|---------|
|eTag     |         |   String      |  선택적 eTag       |
|위치     |  true       |String         |   리소스 위치      |
|properties     |         | [VaultProperties](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  자격 증명 모음의 속성       |
|tags     |         | Object        |     리소스 태그    |

#### <a name="example-request-body"></a>요청 본문 예제

다음 예제는 소프트 삭제 상태를 '사용 안 함'으로 업데이트하는 데 사용됩니다.

```json
{
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Disabled"
  }
}
```

#### <a name="responses"></a>응답

'PATCH' 작업에 대한 성공적인 응답은 다음과 같습니다.

|이름  |Type  |Description  |
|---------|---------|---------|
|200 정상     |   [백업리소스볼트콘피그](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | 확인        |

##### <a name="example-response"></a>예제 응답

'PATCH' 요청이 제출되면 200(성공) 응답이 반환됩니다.

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testvaultRG/providers/Microsoft.RecoveryServices/vaults/testvault/backupconfig/vaultconfig",
  "name": "vaultconfig",
  "type": "Microsoft.RecoveryServices/vaults/backupconfig",
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Disabled"
  }
}
```

## <a name="next-steps"></a>다음 단계

[이 자격 증명 모음의 Azure VM을 백업하기 위한 백업 정책을 만듭니다](backup-azure-arm-userestapi-createorupdatepolicy.md).

Azure REST API에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure Recovery Services 공급자 REST API](/rest/api/recoveryservices/)
- [Azure REST API 시작하기](/rest/api/azure/)
