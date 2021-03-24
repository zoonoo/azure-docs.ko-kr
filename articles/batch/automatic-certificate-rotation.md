---
title: Batch 풀에서 자동 인증서 회전 사용
description: 관리 id와 자동으로 갱신 되는 인증서를 사용 하 여 Batch 풀을 만들 수 있습니다.
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: references_regions
ms.openlocfilehash: e8bea49b2980deb8f20258ab7ea5619ece8cd2bf
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104962572"
---
# <a name="enable-automatic-certificate-rotation-in-a-batch-pool"></a>Batch 풀에서 자동 인증서 회전 사용

 자동으로 갱신 되는 인증서를 사용 하 여 Batch 풀을 만들 수 있습니다. 이렇게 하려면 [Azure Key Vault](../key-vault/general/overview.md)에서 인증서에 액세스할 수 있는 [사용자 할당 관리 id](managed-identity-pools.md) 를 사용 하 여 풀을 만들어야 합니다.

> [!IMPORTANT]
> 사용자 할당 관리 id를 사용 하는 Azure Batch 풀에 대 한 지원은 현재 미국 서 부 2, 미국 동부, 미국 동부, US Gov 애리조나 및 US Gov 버지니아 지역에 대해 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="create-a-user-assigned-identity"></a>사용자 할당 ID 만들기

먼저 Batch 계정과 동일한 테 넌 트에서 [사용자 할당 관리 id를 만듭니다](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) . 이 관리 되는 id는 동일한 리소스 그룹에 있거나 동일한 구독에 있을 필요는 없습니다.

사용자 할당 관리 id의 **클라이언트 ID** 를 확인 해야 합니다. 나중에 이 값이 필요합니다.

:::image type="content" source="media/automatic-certificate-rotation/client-id.png" alt-text="Azure Portal에서 사용자 할당 관리 id의 클라이언트 ID를 보여 주는 스크린샷":::

## <a name="create-your-certificate"></a>인증서 만들기

다음으로 인증서를 만들어 Azure Key Vault에 추가 해야 합니다. 키 자격 증명 모음을 아직 만들지 않은 경우 먼저이 작업을 수행 해야 합니다. 지침은 [빠른 시작: Azure Portal를 사용 하 여 Azure Key Vault에서 인증서 설정 및 검색](../key-vault/certificates/quick-create-portal.md)을 참조 하세요.

인증서를 만들 때 **수명 작업 유형을** 자동 갱신으로 설정 하 고 인증서를 갱신 해야 하는 기간 (일)을 지정 해야 합니다.

:::image type="content" source="media/automatic-certificate-rotation/certificate.png" alt-text="Azure Portal 인증서 만들기 화면의 스크린샷":::

인증서를 만든 후에는 **비밀 식별자** 를 기록해 둡니다. 나중에 이 값이 필요합니다.

:::image type="content" source="media/automatic-certificate-rotation/secret-identifier.png" alt-text="인증서의 비밀 식별자를 보여 주는 스크린샷":::

## <a name="add-an-access-policy-in-azure-key-vault"></a>Azure Key Vault에 액세스 정책 추가

키 자격 증명 모음에서 사용자 할당 관리 id가 암호 및 인증서에 액세스할 수 있도록 하는 Key Vault 액세스 정책을 할당 합니다. 자세한 지침은 [Azure Portal를 사용 하 여 Key Vault 액세스 정책 할당](../key-vault/general/assign-access-policy-portal.md)을 참조 하세요.

## <a name="create-a-batch-pool-with-a-user-assigned-managed-identity"></a>사용자 할당 관리 id를 사용 하 여 Batch 풀 만들기

[Batch .net 관리 라이브러리](/dotnet/api/overview/azure/batch#management-library)를 사용 하 여 관리 id를 사용 하 여 batch 풀을 만듭니다. 자세한 내용은 [Batch 풀에서 관리 되는 Id 구성](managed-identity-pools.md)을 참조 하세요.

다음 예에서는 Batch 관리 REST API을 사용 하 여 풀을 만듭니다. 에 대 한 인증서의 **암호 식별자** `observedCertificates` 와 관리 ID의 **클라이언트 ID** 를 사용 하 여 `msiClientId` 아래 예제 데이터를 바꾸어야 합니다.

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

인증서가 성공적으로 배포 되었는지 확인 하려면 계산 노드에 로그인 합니다. 다음과 비슷한 결과가 나타나야 합니다.

```
root@74773db5fe1b42ab9a4b6cf679d929da000000:/var/lib/waagent/Microsoft.Azure.KeyVault.KeyVaultForLinux-1.0.1363.13/status# cat 1.status
[{"status":{"code":0,"formattedMessage":{"lang":"en","message":"Successfully started Key Vault extension service. 2021-03-03T23:12:23Z"},"operation":"Service start.","status":"success"},"timestampUTC":"2021-03-03T23:12:23Z","version":"1.0"}]root@74773db5fe1b42ab9a4b6cf679d929da000000:/var/lib/waagent/Microsoft.Azure.KeyVault.KeyVaultForLinux-1.0.1363.13/status#
```

## <a name="next-steps"></a>다음 단계

- [Azure 리소스에 대 한 관리 id](../active-directory/managed-identities-azure-resources/overview.md)에 대해 자세히 알아보세요.
- [사용자 관리 id에서 고객 관리 키](batch-customer-managed-key.md)를 사용 하는 방법에 대해 알아봅니다.
