---
title: 키 볼트 참조 사용
description: Azure 키 자격 증명 모음 참조를 사용하도록 Azure 앱 서비스 및 Azure 함수를 설정하는 방법에 대해 알아봅니다. 키 볼트 암호를 응용 프로그램 코드에 사용할 수 있도록 합니다.
author: mattchenderson
ms.topic: article
ms.date: 10/09/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 7fdb7c980a278e2dcd4b64a4b70de50721d0b72a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280340"
---
# <a name="use-key-vault-references-for-app-service-and-azure-functions"></a>앱 서비스 및 Azure 기능에 키 볼트 참조 사용

이 항목에서는 코드 변경 없이도 App Service 또는 Azure Functions 애플리케이션에서 Azure Key Vault의 비밀을 사용하는 방법을 보여 줍니다. [Azure Key Vault](../key-vault/key-vault-overview.md)는 액세스 정책 및 감사 기록에 대한 전체 제어와 함께 중앙 집중식 비밀 관리를 제공하는 서비스입니다.

## <a name="granting-your-app-access-to-key-vault"></a>Key Vault에 앱 액세스 권한 부여

Key Vault에서 비밀을 읽으려면 자격 증명 모음을 만들고 해당 자격 증명에 앱 권한을 부여해야 합니다.

1. [Key Vault 빠른 시작](../key-vault/quick-create-cli.md)에 따라 키 자격 증명 모음을 만듭니다.

1. 애플리케이션에 대한 [시스템 할당 관리형 ID](overview-managed-identity.md)를 만듭니다.

   > [!NOTE] 
   > Key Vault 참조는 현재 시스템 할당 관리형 ID만 지원합니다. 사용자 할당 ID를 사용할 수 없습니다.

1. 이전에 만든 애플리케이션 ID에 대한 [Key Vault에서 액세스 정책](../key-vault/key-vault-secure-your-key-vault.md#key-vault-access-policies)을 만듭니다. 이 정책에 대한 “가져오기” 비밀 권한을 사용하도록 설정합니다. "권한 있는 애플리케이션" 또는 `applicationId` 설정은 관리 ID와 호환되지 않으므로 구성하지 마세요.

    > [!NOTE]
    > 키 Vault 참조는 현재 [네트워크 제한이](../key-vault/key-vault-overview-vnet-service-endpoints.md)있는 키 자격 증명 모음에 저장된 비밀을 확인할 수 없습니다.

## <a name="reference-syntax"></a>참조 구문

Key Vault 참조는 `@Microsoft.KeyVault({referenceString})` 형식이며, 여기서 `{referenceString}`은 다음 옵션 중 하나로 대체됩니다.

> [!div class="mx-tdBreakAll"]
> | 참조 문자열                                                            | 설명                                                                                                                                                                                 |
> |-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | SecretUri=_secretUri_                                                       | **SecretUri**는 버전을 포함하여 Key Vault에 있는 비밀의 전체 데이터 평면 URI여야 합니다(예: https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931).  |
> | VaultName=_vaultName_;SecretName=_secretName_;SecretVersion=_secretVersion_ | **VaultName**은 Key Vault 리소스의 이름이어야 합니다. **SecretName**은 대상 비밀의 이름이어야 합니다. **SecretVersion**은 사용할 비밀의 버전이어야 합니다. |

예를 들어 버전에 대한 전체 참조는 다음과 같습니다.

```
@Microsoft.KeyVault(SecretUri=https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931)
```
또는 다음과 같습니다.

```
@Microsoft.KeyVault(VaultName=myvault;SecretName=mysecret;SecretVersion=ec96f02080254f109c51a1f14cdb1931)
```


## <a name="source-application-settings-from-key-vault"></a>Key Vault의 원본 애플리케이션 설정

키 볼트 참조는 응용 [프로그램 설정의](configure-common.md#configure-app-settings)값으로 사용할 수 있으므로 사이트 구성 대신 키 볼트에 비밀을 유지할 수 있습니다. 응용 프로그램 설정은 미사용 으로 안전하게 암호화되지만 비밀 관리 기능이 필요한 경우 키 볼트로 이동해야 합니다.

애플리케이션 설정에 Key Vault 참조를 사용하려면 참조를 설정 값으로 설정합니다. 앱은 키를 사용하여 비밀을 정상적으로 참조할 수 있습니다. 코드 변경은 필요하지 않습니다.

> [!TIP]
> Key Vault 참조를 사용하는 대부분의 애플리케이션 설정은 각 환경에 대해 별도의 자격 증명 모음이 있어야 슬롯 설정으로 표시되어야 합니다.

### <a name="azure-resource-manager-deployment"></a>Azure Resource Manager 배포

Azure Resource Manager 템플릿을 통해 리소스 배포를 자동화할 때 이 기능이 작동하려면 종속성을 특정 순서로 시퀀스해야 할 수 있습니다. 중요한 것은, 사이트 정의에서 `siteConfig` 속성을 사용하는 대신 애플리케이션 설정을 자체 리소스로 정의해야 합니다. 이는 사이트를 먼저 정의해야 사이트를 사용하여 시스템 할당 ID를 만들고 액세스 정책에서 사용할 수 있기 때문입니다.

함수 앱에 대한 예제 의사 템플릿은 다음과 같습니다.

```json
{
    //...
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            //...
        },
        {
            "type": "Microsoft.Insights/components",
            "name": "[variables('appInsightsName')]",
            //...
        },
        {
            "type": "Microsoft.Web/sites",
            "name": "[variables('functionAppName')]",
            "identity": {
                "type": "SystemAssigned"
            },
            //...
            "resources": [
                {
                    "type": "config",
                    "name": "appsettings",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('storageConnectionStringName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('appInsightsKeyName'))]"
                    ],
                    "properties": {
                        "AzureWebJobsStorage": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "APPINSIGHTS_INSTRUMENTATIONKEY": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('appInsightsKeyResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_ENABLE_SYNC_UPDATE_SITE": "true"
                        //...
                    }
                },
                {
                    "type": "sourcecontrols",
                    "name": "web",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.Web/sites/config', variables('functionAppName'), 'appsettings')]"
                    ],
                }
            ]
        },
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "[variables('keyVaultName')]",
            //...
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]"
            ],
            "properties": {
                //...
                "accessPolicies": [
                    {
                        "tenantId": "[reference(concat('Microsoft.Web/sites/',  variables('functionAppName'), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').tenantId]",
                        "objectId": "[reference(concat('Microsoft.Web/sites/',  variables('functionAppName'), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').principalId]",
                        "permissions": {
                            "secrets": [ "get" ]
                        }
                    }
                ]
            },
            "resources": [
                {
                    "type": "secrets",
                    "name": "[variables('storageConnectionStringName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
                    ],
                    "properties": {
                        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountResourceId'),'2015-05-01-preview').key1)]"
                    }
                },
                {
                    "type": "secrets",
                    "name": "[variables('appInsightsKeyName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
                    ],
                    "properties": {
                        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
                    }
                }
            ]
        }
    ]
}
```

> [!NOTE] 
> 이 예제에서 원본 제어 배포는 애플리케이션 설정에 따라 다릅니다. 일반적으로 앱 설정 업데이트는 비동기적으로 동작하므로 안전하지 않은 동작입니다. 그러나 `WEBSITE_ENABLE_SYNC_UPDATE_SITE` 애플리케이션 설정을 포함했으므로 업데이트가 동기적입니다. 이는 애플리케이션 설정이 완전히 업데이트된 후에만 원본 제어 배포가 시작됨을 의미합니다.

## <a name="troubleshooting-key-vault-references"></a>키 볼트 참조 문제 해결

참조가 제대로 확인되지 않으면 참조 값이 대신 사용됩니다. 즉, 응용 프로그램 설정의 경우 값에 구문이 `@Microsoft.KeyVault(...)` 있는 환경 변수가 만들어집니다. 이로 인해 특정 구조의 비밀을 예상했기 때문에 응용 프로그램이 오류를 throw할 수 있습니다.

가장 일반적으로 이는 키 자격 증명 [모음 액세스 정책의](#granting-your-app-access-to-key-vault)잘못 구성 때문입니다. 그러나 더 이상 존재하지 않는 비밀이나 참조 자체의 구문 오류 때문일 수도 있습니다.

구문이 올바른 경우 포털의 현재 확인 상태를 확인하여 다른 오류 원인을 볼 수 있습니다. 응용 프로그램 설정으로 이동하여 해당 참조에 대해 "편집"을 선택합니다. 설정 구성 아래에 오류를 포함한 상태 정보가 표시됩니다. 이러한 구문이 없으면 참조 구문이 유효하지 않음을 의미합니다.

기본 제공 감지기 중 하나를 사용하여 추가 정보를 얻을 수도 있습니다.

### <a name="using-the-detector-for-app-service"></a>앱 서비스에 대한 검출기 사용

1. 포털에서 앱으로 이동합니다.
2. **문제 진단 및 해결**을 선택합니다.
3. **가용성 및 성능을** 선택하고 웹 앱을 **선택합니다.**
4. **키 볼트 응용 프로그램 설정 진단을** 찾아 자세한 **정보를**클릭합니다.


### <a name="using-the-detector-for-azure-functions"></a>Azure 기능에 대한 감지기 사용

1. 포털에서 앱으로 이동합니다.
2. 플랫폼 **기능으로 이동합니다.**
3. **문제 진단 및 해결**을 선택합니다.
4. **가용성 및 성능을** 선택하고 함수 앱을 아래로 **선택하거나 오류를 보고합니다.**
5. 키 **볼트 응용 프로그램 설정 진단을 클릭합니다.**
