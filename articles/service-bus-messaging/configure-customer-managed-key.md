---
title: Azure Service Bus 미사용 데이터를 암호화하기 위한 고유 키 구성
description: 이 문서에서는 Azure Service Bus 미사용 데이터를 암호화하기 위해 고유 키를 구성하는 방법에 대한 정보를 제공합니다.
ms.topic: conceptual
ms.date: 02/10/2021
ms.openlocfilehash: de716b9f14191ba057c83a060104e64937c4192a
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816011"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-service-bus-data-at-rest-by-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure Service Bus 미사용 데이터를 암호화하기 위한 고객 관리형 키 구성
Azure Service Bus 프리미엄은 Azure SSE(스토리지 서비스 암호화)를 사용하여 미사용 데이터의 암호화를 제공합니다. Service Bus 프리미엄은 Azure Storage를 사용하여 데이터를 저장합니다. Azure Storage에 저장되는 모든 데이터는 Microsoft 관리형 키를 사용하여 암호화됩니다. 사용자 고유의 키(BYOK(Bring Your Own Key) 또는 고객 관리형 키라고도 함)를 사용하는 경우 데이터는 Microsoft 관리형 키를 사용하여 계속 암호화되지만, 그 외의 Microsoft 관리형 키는 고객 관리형 키를 사용하여 암호화됩니다. 이 기능을 사용하면 Microsoft 관리형 키를 암호화하는 데 사용되는 고객 관리형 키의 액세스를 만들고, 회전시키고, 사용하지 않도록 설정하며, 철회할 수 있습니다. BYOK를 사용하도록 설정하는 기능은 네임스페이스에서 한 번만 설정하면 됩니다.

서비스 쪽 암호화에 대한 고객 관리형 키에는 몇 가지 주의 사항이 있습니다. 
- 이 기능은 [Azure Service Bus 프리미엄](service-bus-premium-messaging.md) 계층에서만 지원됩니다. 표준 계층 Service Bus 네임스페이스에는 사용하도록 설정할 수 없습니다.
- 암호화는 새 또는 빈 네임스페이스에서만 사용할 수 있습니다. 네임스페이스에 큐 또는 토픽이 포함된 경우 암호화 작업이 실패합니다.

Azure Key Vault를 사용하여 키를 관리하고 키 사용을 감사할 수 있습니다. 사용자 고유의 키를 만들어 키 자격 증명 모음에 저장할 수도 있고, Azure Key Vault API를 사용하여 키를 생성할 수도 있습니다. Azure Key Vault에 대한 자세한 내용은 [Azure Key Vault란?](../key-vault/general/overview.md)

이 문서에서는 Azure Portal을 사용하여 고객 관리형 키로 키 자격 증명 모음을 구성하는 방법을 보여줍니다. Azure Portal을 사용하여 키 자격 증명 모음을 만드는 방법은 [빠른 시작: Azure Portal을 사용하여 Azure Key Vault 만들기](../key-vault/general/quick-create-portal.md)를 참조하세요.

> [!IMPORTANT]
> Azure Service Bus에서 고객 관리형 키를 사용하려면 키 자격 증명 모음에 두 가지 필수 속성이 구성되어 있어야 합니다. 바로 **일시 삭제** 와 **제거 안 함** 입니다. 이러한 속성은 Azure Portal에서 새 키 자격 증명 모음을 만들 때 기본적으로 사용하도록 설정됩니다. 그러나 기존 키 자격 증명 모음에서 이러한 속성을 사용하도록 설정해야 하는 경우에는 PowerShell 또는 Azure CLI를 사용해야 합니다.

## <a name="enable-customer-managed-keys"></a>고객 관리형 키 사용
Azure Portal에서 고객 관리형 키를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Service Bus 프리미엄 네임스페이스로 이동합니다.
2. Service Bus 네임스페이스의 **설정** 페이지에서 **암호화** 를 선택합니다.
3. 다음 이미지에 표시된 것처럼 **미사용 고객 관리형 키 암호화** 를 선택합니다.

    ![고객 관리형 키 사용](./media/configure-customer-managed-key/enable-customer-managed-key.png)


## <a name="set-up-a-key-vault-with-keys"></a>키를 사용하여 키 자격 증명 모음 설정

고객 관리형 키를 사용하도록 설정한 후에는 고객 관리형 키를 Azure Service Bus 네임스페이스와 연결해야 합니다. Service Bus는 Azure Key Vault만 지원합니다. 이전 섹션에서 **고객 관리형 키로 암호화** 옵션을 사용하도록 설정하는 경우 키를 Azure Key Vault로 가져와야 합니다. 또한 키에는 키를 위해 구성된 **일시 삭제** 및 **제거 안 함** 속성이 있어야 합니다. 해당 설정은 [PowerShell](../key-vault/general/key-vault-recovery.md) 또는 [CLI](../key-vault/general/key-vault-recovery.md)를 이용해 구성할 수 있습니다.

1. 새로운 키 자격 증명 모음을 만들려면 Azure Key Vault [빠른 시작](../key-vault/general/overview.md)을 따릅니다. 기존 키를 가져오는 방법에 대한 자세한 내용은 [키, 비밀, 인증서 정보](../key-vault/general/about-keys-secrets-certificates.md)를 참조하세요.
1. 자격 증명 모음을 만들 때 일시 삭제 및 제거 보호를 모두 설정하려면 [az keyvault create](/cli/azure/keyvault#az_keyvault_create) 명령을 사용합니다.

    ```azurecli-interactive
    az keyvault create --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. 이미 일시 삭제를 사용하는 기존 자격 증명 모음에 제거 보호를 추가하려면 [az keyvault update](/cli/azure/keyvault#az_keyvault_update) 명령을 사용합니다.

    ```azurecli-interactive
    az keyvault update --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --enable-purge-protection true
    ```
1. 다음 단계에 따라 키를 만듭니다.
    1. 새 키를 만들려면 **설정** 아래 **키** 메뉴에서 **생성/가져오기** 를 선택합니다.
        
        ![생성/가져오기 단추 선택](./media/configure-customer-managed-key/select-generate-import.png)

    1. **옵션** 을 **생성** 으로 설정하고 키에 이름을 지정합니다.

        ![키 만들기](./media/configure-customer-managed-key/create-key.png) 

    1. 이제 이 키를 선택하여 드롭다운 목록에서 암호화할 Service Bus 네임스페이스와 연결할 수 있습니다. 

        ![키 자격 증명 모음에서 키 선택](./media/configure-customer-managed-key/select-key-from-key-vault.png)
        > [!NOTE]
        > 중복 구성을 위해 최대 3개의 키를 추가할 수 있습니다. 키 중 하나가 만료되었거나 액세스할 수 없는 경우 다른 키가 암호화에 사용됩니다.
        
    1. 키 세부 정보를 입력하고 **선택** 을 클릭합니다. 그러면 Microsoft 관리형 키와 사용자의 키(고객 관리형 키)를 암호화할 수 있습니다. 


    > [!IMPORTANT]
    > 지역 재해 복구에 고객 관리형 키를 사용하려는 경우 이 섹션을 검토하세요. 
    >
    > 고객 관리형 키를 통한 Microsoft 관리형 키의 암호화를 사용하도록 설정하려면 지정한 Azure KeyVault에서 Service Bus의 관리 ID에 대해 [액세스 정책](../key-vault/general/security-features.md)을 설정해야 합니다. 이렇게 하면 Azure Service Bus 네임스페이스에서 Azure KeyVault에 대한 제어된 액세스가 보장됩니다.
    >
    > 이런 이유로
    > 
    >   * Service Bus 네임스페이스에 대해 [지역 재해 복구](service-bus-geo-dr.md)를 이미 사용하도록 설정했고 고객 관리형 키를 사용하도록 설정하려는 경우 
    >     * 페어링 중단
    >     * 기본 및 보조 네임스페이스 모두에 대해 관리 ID의 [액세스 정책](../key-vault/general/assign-access-policy-portal.md)을 키 자격 증명 모음으로 설정합니다.
    >     * 기본 네임스페이스에 대해 암호화를 설정합니다.
    >     * 기본 및 보조 네임스페이스를 쌍으로 연결합니다.
    > 
    >   * 고객 관리형 키가 이미 설정된 Service Bus 네임스페이스에서 Geo-DR(지역 재해 복구)를 사용하려는 경우
    >     * 보조 네임스페이스의 관리 ID에 대해 [액세스 정책](../key-vault/general/assign-access-policy-portal.md)을 키 자격 증명 모음으로 설정합니다.
    >     * 기본 및 보조 네임스페이스를 쌍으로 연결합니다.


## <a name="rotate-your-encryption-keys"></a>암호화 키 회전

Azure Key Vaults 회전 메커니즘을 사용하여 키 자격 증명 모음에서 키를 회전할 수 있습니다. 키 회전을 자동화하도록 활성화하고 만료일을 설정할 수도 있습니다. Service Bus 서비스는 새로운 키 버전을 검색하고 자동으로 이를 사용하기 시작 합니다.

## <a name="revoke-access-to-keys"></a>키 액세스 철회

암호화 키에 대한 액세스를 철회해도 Service Bus의 데이터는 제거되지 않습니다. 그러나 Service Bus 네임스페이스에서 데이터에 액세스할 수 없습니다. 액세스 정책을 통해, 또는 키를 삭제하여 암호화 키를 철회할 수 있습니다. [키 자격 증명 모음에 대한 보안 액세스](../key-vault/general/security-features.md)에서 액세스 정책 및 키 자격 증명 모음 보안에 대해 자세히 알아보세요.

암호화 키가 해지되면 암호화된 네임스페이스의 Service Bus 서비스가 작동하지 않게 됩니다. 키에 대한 액세스를 사용하도록 설정하거나 삭제 키가 복원된 경우, 암호화된 Service Bus 네임스페이스의 데이터에 액세스할 수 있도록 Service Bus 서비스에서 키를 선택합니다.

## <a name="caching-of-keys"></a>키 캐싱
Service Bus 인스턴스는 나열된 암호화 키를 5분마다 폴링합니다. 캐시하여 5분 후인 다음 폴링 전까지 사용합니다. 하나 이상의 키를 사용할 수 있다면 큐와 토픽에 액세스할 수 있습니다. 폴링 시점에 나열된 모든 키에 액세스할 수 없는 경우 모든 키와 토픽을 사용할 수 없게 됩니다. 

다음은 자세한 내용입니다. 

- 5분마다 Service Bus 서비스는 네임스페이스의 레코드에 나열된 모든 고객 관리형 키를 폴링합니다.
    - 키를 회전한 경우 레코드가 새 키로 업데이트됩니다.
    - 키가 철회되면 레코드에서 제거됩니다.
    - 모든 키가 철회될 경우 네임스페이스의 암호화 상태가 **철회됨** 으로 설정됩니다. Service Bus 네임스페이스에서 데이터에 액세스할 수 없습니다. 
    

## <a name="use-resource-manager-template-to-enable-encryption"></a>Resource Manager 템플릿을 통해 암호화 사용
이 섹션에서는 **Azure Resource Manager 템플릿** 을 사용하여 다음 작업을 수행하는 방법을 보여줍니다. 

1. **관리 서비스 ID** 를 사용하여 **프리미엄** Service Bus 네임스페이스를 만듭니다.
2. **키 자격 증명 모음** 을 만들고 키 자격 증명 모음에 대한 서비스 ID 액세스 권한을 부여합니다. 
3. 키 자격 증명 모음 정보(키/값)를 사용하여 Service Bus 네임스페이스를 업데이트합니다. 


### <a name="create-a-premium-service-bus-namespace-with-managed-service-identity"></a>관리 서비스 ID로 프리미엄 Service Bus 네임스페이스 만들기
이 섹션에서는 Azure Resource Manager 템플릿 및 PowerShell을 사용하여 관리 서비스 ID로 Azure Service Bus 네임스페이스를 만드는 방법을 보여줍니다. 

1. Azure Resource Manager 템플릿을 만들어서 관리 서비스 ID를 사용하여 Service Bus 네임스페이스를 만듭니다. 파일 이름을 **CreateServiceBusPremiumNamespace.json** 으로 지정합니다. 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.ServiceBus/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Premium",
                "tier":"Premium",
                "capacity":1
             },
             "properties":{
    
             }
          }
       ],
       "outputs":{
          "ServiceBusNamespaceId":{
             "type":"string",
             "value":"[resourceId('Microsoft.ServiceBus/namespaces',parameters('namespaceName'))]"
          }
       }
    }
    ```
2. 이름이 **CreateServiceBusPremiumNamespaceParams.json** 인 템플릿 매개 변수 파일을 만듭니다. 

    > [!NOTE]
    > 다음 값을 바꿉니다. 
    > - `<ServiceBusNamespaceName>` - Service Bus 네임스페이스의 이름
    > - `<Location>` - Service Bus 네임스페이스의 위치

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<ServiceBusNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          }
       }
    }
    ```
3. 다음 PowerShell 명령을 실행하여 템플릿을 배포해 Service Bus 네임스페이스를 만듭니다. 그런 다음, 나중에 사용할 Service Bus 네임스페이스의 ID를 검색합니다. 명령을 실행하기 전에 `{MyRG}`는 해당 리소스 그룹의 이름으로 바꿉니다.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateServiceBusPremiumNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateServiceBusPremiumNamespace.json -TemplateParameterFile ./CreateServiceBusPremiumNamespaceParams.json
    
    $ServiceBusNamespaceId = $outputs.Outputs["serviceBusNamespaceId"].value
    ```
 
### <a name="grant-service-bus-namespace-identity-access-to-key-vault"></a>키 자격 증명 모음에 Service Bus 네임스페이스 ID 액세스 권한 부여

1. 다음 명령을 실행하여 **보호 제거** 및 **일시 삭제** 를 사용하는 키 자격 증명 모음을 만듭니다. 

    ```powershell
    New-AzureRmKeyVault -Name "{keyVaultName}" -ResourceGroupName {RGName}  -Location "{location}" -EnableSoftDelete -EnablePurgeProtection    
    ```
    
    또는
    
    다음 명령을 실행하여 **기존 키 자격 증명 모음** 을 업데이트합니다. 명령을 실행하기 전에 리소스 그룹 및 키 자격 증명 모음 이름의 값을 지정합니다. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Service Bus 네임스페이스의 관리 ID가 키 자격 증명 모음의 키 값에 액세스할 수 있도록 키 자격 증명 모음 액세스 정책을 설정합니다. 이전 섹션의 Service Bus 네임스페이스 ID를 사용합니다. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $ServiceBusNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-service-bus-namespace-with-customer-managed-key-from-key-vault"></a>키 자격 증명 모음의 고객 관리형 키를 사용하여 Service Bus 네임스페이스의 데이터 암호화
지금까지 다음 단계를 완료했습니다. 

1. 관리 ID를 사용하여 프리미엄 네임스페이스를 만들었습니다.
2. 키 자격 증명 모음을 만들고 관리 ID에 키 자격 증명 모음에 대한 액세스 권한을 부여했습니다. 

이 단계에서는 키 자격 증명 모음 정보를 사용하여 Service Bus 네임스페이스를 업데이트합니다. 

1. 다음 콘텐츠로 이름이 **UpdateServiceBusNamespaceWithEncryption.json** 인 JSON 파일을 만듭니다. 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace to be created in cluster."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          },
          "keyVaultUri":{
             "type":"string",
             "metadata":{
                "description":"URI of the KeyVault."
             }
          },
          "keyName":{
             "type":"string",
             "metadata":{
                "description":"KeyName."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.ServiceBus/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Premium",
                "tier":"Premium",
                "capacity":1
             },
             "properties":{
                "encryption":{
                   "keySource":"Microsoft.KeyVault",
                   "keyVaultProperties":[
                      {
                         "keyName":"[parameters('keyName')]",
                         "keyVaultUri":"[parameters('keyVaultUri')]"
                      }
                   ]
                }
             }
          }
       ]
    }
    ``` 

2. 템플릿 매개 변수 파일 **UpdateServiceBusNamespaceWithEncryptionParams.json** 을 만듭니다.

    > [!NOTE]
    > 다음 값을 바꿉니다. 
    > - `<ServiceBusNamespaceName>` - Service Bus 네임스페이스의 이름
    > - `<Location>` - Service Bus 네임스페이스의 위치
    > - `<KeyVaultName>` - 키 자격 증명 모음의 이름
    > - `<KeyName>` - 키 자격 증명 모음에 있는 키의 이름  

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<ServiceBusNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          },
          "keyName":{
             "value":"<KeyName>"
          },
          "keyVaultUri":{
             "value":"https://<KeyVaultName>.vault.azure.net"
          }
       }
    }
    ```             
3. 다음 PowerShell 명령을 실행하여 Resource Manager 템플릿을 배포합니다. 명령을 실행하기 전에 `{MyRG}`는 해당 리소스 그룹의 이름으로 바꿉니다. 

    ```powershell
    New-AzResourceGroupDeployment -Name UpdateServiceBusNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateServiceBusNamespaceWithEncryption.json -TemplateParameterFile ./UpdateServiceBusNamespaceWithEncryptionParams.json
    ```
    

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.
- [Service Bus 개요](service-bus-messaging-overview.md)
- [Key Vault 개요](../key-vault/general/overview.md)