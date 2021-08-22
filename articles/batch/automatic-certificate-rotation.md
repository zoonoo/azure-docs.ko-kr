---
title: Batch 풀에서 자동 인증서 순환 사용
description: 관리 ID와 자동으로 갱신되는 인증서를 사용하여 Batch 풀을 만들 수 있습니다.
ms.topic: conceptual
ms.date: 07/16/2021
ms.openlocfilehash: 9a8f4d785c9a411df6c867724f057b1dc9b29722
ms.sourcegitcommit: 47ac63339ca645096bd3a1ac96b5192852fc7fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114362421"
---
# <a name="enable-automatic-certificate-rotation-in-a-batch-pool"></a>Batch 풀에서 자동 인증서 순환 사용

 자동으로 갱신되는 인증서를 사용하여 Batch 풀을 만들 수 있습니다. 그러려면 [Azure Key Vault](../key-vault/general/overview.md)에서 인증서에 액세스할 수 있는 [사용자가 할당한 관리 ID](managed-identity-pools.md)를 사용하여 풀을 만들어야 합니다.

## <a name="create-a-user-assigned-identity"></a>사용자 할당 ID 만들기

먼저 배치 계정과 동일한 테넌트에서 [사용자가 할당한 관리 ID 만들기](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)를 수행합니다. 이 관리 ID는 동일한 리소스 그룹 또는 동일한 구독에 있을 필요는 없습니다.

사용자가 할당한 관리 ID의 **클라이언트 ID** 를 확인해야 합니다. 나중에 이 값이 필요합니다.

:::image type="content" source="media/automatic-certificate-rotation/client-id.png" alt-text="Azure Portal에서 사용자가 할당한 관리 ID의 클라이언트 ID를 보여 주는 스크린샷.":::

## <a name="create-your-certificate"></a>인증서 만들기

다음으로 인증서를 만들어 Azure Key Vault에 추가해야 합니다. 키 자격 증명 모음을 아직 만들지 않은 경우 먼저 키 자격 증명 모음을 만들어야 합니다. 지침은 [빠른 시작: Azure Portal을 사용하여 Azure Key Vault에서 인증서 설정 및 검색](../key-vault/certificates/quick-create-portal.md)을 참조하세요.

인증서를 만들 때 **수명 작업 유형** 을 자동 갱신으로 설정하고 인증서를 갱신해야 하는 기간(일)을 지정해야 합니다.

:::image type="content" source="media/automatic-certificate-rotation/certificate.png" alt-text="Azure Portal 인증서 만들기 화면의 스크린샷.":::

인증서를 만든 후에는 **비밀 식별자** 를 기록해 둡니다. 나중에 이 값이 필요합니다.

:::image type="content" source="media/automatic-certificate-rotation/secret-identifier.png" alt-text="인증서의 비밀 식별자를 보여 주는 스크린샷.":::

## <a name="add-an-access-policy-in-azure-key-vault"></a>Azure Key Vault에서 액세스 정책 추가

키 자격 증명 모음에서 사용자가 할당한 관리 ID가 비밀 및 인증서에 액세스할 수 있게 하는 Key Vault 액세스 정책을 할당합니다. 자세한 지침은 [Azure Portal을 사용하여 Key Vault 액세스 정책 할당](../key-vault/general/assign-access-policy-portal.md)을 참조하세요.

## <a name="create-a-batch-pool-with-a-user-assigned-managed-identity"></a>사용자가 할당한 관리 ID를 사용하여 Batch 풀 만들기

[Batch .NET 관리 라이브러리](/dotnet/api/overview/azure/batch#management-library)를 사용하여 관리 ID로 Batch 풀을 만듭니다. 자세한 내용은 [Batch 풀의 관리 ID 구성](managed-identity-pools.md)을 참조하세요.

다음 예제에서는 Batch 관리 REST API를 사용하여 풀을 만듭니다. `observedCertificates`에는 인증서의 **비밀 식별자** 를 사용하고 `msiClientId`에는 관리 ID의 **클라이언트 ID** 를 사용하여 아래 예제 데이터를 바꾸어야 합니다.

REST API URI

```http
PUT https://management.azure.com/subscriptions/<subscriptionid>/resourceGroups/<resourcegroupName>/providers/Microsoft.Batch/batchAccounts/<batchaccountname>/pools/<poolname>?api-version=2021-01-01
```

요청 본문

```json
{
    "name": "test2",
    "type": "Microsoft.Batch/batchAccounts/pools",
    "properties": {
        "vmSize": "STANDARD_DS2_V2",
        "taskSchedulingPolicy": {
            "nodeFillType": "Pack"
        },
        "deploymentConfiguration": {
            "virtualMachineConfiguration": {
                "imageReference": {
                    "publisher": "canonical",
                    "offer": "ubuntuserver",
                    "sku": "18.04-lts",
                    "version": "latest"
                },
                "nodeAgentSkuId": "batch.node.ubuntu 18.04",
                "extensions": [
                    {
                        "name": "KVExtensions",
                        "type": "KeyVaultForLinux",
                        "publisher": "Microsoft.Azure.KeyVault",
                        "typeHandlerVersion": "1.0",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "secretsManagementSettings": {
                                "pollingIntervalInS": "300",
                                "certificateStoreLocation": "/var/lib/waagent/Microsoft.Azure.KeyVault",
                                "requireInitialSync": true,
                                "observedCertificates": [
                                    "https://testkvwestus2s.vault.azure.net/secrets/authcertforumatesting/8f5f3f491afd48cb99286ba2aacd39af"
                                ]
                            },
                            "authenticationSettings": {
                                "msiEndpoint": "http://169.254.169.254/metadata/identity",
                                "msiClientId": "b9f6dd56-d2d6-4967-99d7-8062d56fd84c"
                            }  }, "protectedSettings":{}
                    }                ]            }
        },
        "scaleSettings": {
            "fixedScale": {
                "targetDedicatedNodes": 1,
                "resizeTimeout": "PT15M"
            }
        },
    },
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "/subscriptions/042998e4-36dc-4b7d-8ce3-a7a2c4877d33/resourceGroups/ACR/providers/Microsoft.ManagedIdentity/userAssignedIdentities/testumaforpools": {}
        }
    }
}

```

## <a name="validate-the-certificate"></a>인증서 유효성 검사

인증서가 성공적으로 배포되었는지 확인하려면 컴퓨팅 노드에 로그인합니다. 다음과 비슷한 결과가 나타나야 합니다.

```
root@74773db5fe1b42ab9a4b6cf679d929da000000:/var/lib/waagent/Microsoft.Azure.KeyVault.KeyVaultForLinux-1.0.1363.13/status# cat 1.status
[{"status":{"code":0,"formattedMessage":{"lang":"en","message":"Successfully started Key Vault extension service. 2021-03-03T23:12:23Z"},"operation":"Service start.","status":"success"},"timestampUTC":"2021-03-03T23:12:23Z","version":"1.0"}]root@74773db5fe1b42ab9a4b6cf679d929da000000:/var/lib/waagent/Microsoft.Azure.KeyVault.KeyVaultForLinux-1.0.1363.13/status#
```

## <a name="next-steps"></a>다음 단계

- [Azure 리소스의 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)에 대해 자세히 알아봅니다.
- [사용자 관리 ID에서 고객 관리형 키](batch-customer-managed-key.md)를 사용하는 방법에 대해 알아봅니다.
