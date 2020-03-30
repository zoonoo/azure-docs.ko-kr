---
title: 미사용 Azure Service Bus 데이터 암호화를 위해 사용자 고유의 키 구성
description: 이 문서에서는 Azure Service Bus 데이터 사용 정보를 암호화하기 위해 고유한 키를 구성하는 방법에 대한 정보를 제공합니다.
services: service-bus-messaging
ms.service: service-bus
documentationcenter: ''
author: axisc
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: aschhab
ms.openlocfilehash: aeb9a9730ddc61793e49c9e042906457e0068d9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624079"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-service-bus-data-at-rest-by-using-the-azure-portal"></a>Azure 포털을 사용하여 미사용 Azure Service Bus 데이터를 암호화하기 위한 고객 관리 키 구성
Azure Service Bus Premium은 Azure 저장소 서비스 암호화(Azure SSE)를 사용하여 미사용 데이터의 암호화를 제공합니다. Service Bus Premium은 Azure Storage를 사용하여 데이터를 저장하고 기본적으로 Azure Storage에 저장된 모든 데이터는 Microsoft 관리 키를 사용하여 암호화됩니다. 

## <a name="overview"></a>개요
Azure Service Bus는 이제 Microsoft에서 관리하는 키 또는 고객 관리 키(사용자 고유키 가져오기 - BYOK)를 사용하여 미사용 데이터를 암호화하는 옵션을 지원합니다. 이 기능을 사용하면 미사용 Azure Service Bus를 암호화하는 데 사용되는 고객 관리 키에 대한 액세스를 생성, 회전, 비활성화 및 취소할 수 있습니다.

BYOK 기능을 사용하도록 설정하는 것은 네임스페이스에서 일회성 설정 프로세스입니다.

> [!NOTE]
> 서비스 측 암호화에 대한 고객 관리 키에 대한 몇 가지 주의 사항이 있습니다. 
>   * 이 기능은 [Azure 서비스 버스 프리미엄](service-bus-premium-messaging.md) 계층에서 지원됩니다. 표준 계층 서비스 버스 네임스페이스는 사용할 수 없습니다.
>   * 새 네임스페이스나 비어 있는 네임스페이스에대해서만 암호화를 사용할 수 있습니다. 네임스페이스에 데이터가 포함된 경우 암호화 작업이 실패합니다.

Azure Key Vault를 사용하여 키를 관리하고 키 사용량을 감사할 수 있습니다. 사용자 고유의 키를 만들어 키 자격 증명 모음에 저장하거나 Azure Key Vault API를 사용하여 키를 생성할 수 있습니다. Azure 키 자격 증명 모음에 대한 자세한 내용은 [Azure 키 자격 증명 모음이란 무엇입니까?](../key-vault/key-vault-overview.md)

이 문서에서는 Azure 포털을 사용하여 고객 관리 키로 키 자격 증명 모음을 구성하는 방법을 보여 주며 있습니다. Azure 포털을 사용하여 키 자격 증명 모음을 만드는 방법을 알아보려면 [빠른 시작: Azure 포털을 사용하여 Azure 키 자격 증명 모음에서 비밀 설정 및 검색을](../key-vault/quick-create-portal.md)참조하세요.

> [!IMPORTANT]
> Azure Service Bus에서 고객 관리 키를 사용하려면 키 자격 증명 모음에 두 개의 필수 속성이 구성되어 있어야 합니다. 그들은 : **소프트 삭제** 및 **제거하지 마십시오**. 이러한 속성은 Azure Portal에서 새 키 자격 증명 모음을 만들 때 기본적으로 활성화됩니다. 그러나 기존 키 자격 증명 모음에서 이러한 속성을 사용하도록 설정해야 하는 경우 PowerShell 또는 Azure CLI를 사용해야 합니다.

## <a name="enable-customer-managed-keys"></a>고객 관리 키 사용
Azure 포털에서 고객 관리 키를 활성화하려면 다음 단계를 따르십시오.

1. 서비스 버스 프리미엄 네임스페이스로 이동합니다.
2. 서비스 버스 네임스페이스의 **설정** 페이지에서 **암호화**를 선택합니다.
3. 다음 이미지와 같이 **미사용 고객 관리 키 암호화를** 선택합니다.

    ![고객 관리 키 사용](./media/configure-customer-managed-key/enable-customer-managed-key.png)


## <a name="set-up-a-key-vault-with-keys"></a>키로 키 자격 증명 모음 설정

고객 관리 키를 사용하도록 설정한 후에는 고객 관리 키를 Azure Service Bus 네임스페이스에 연결해야 합니다. 서비스 버스는 Azure 키 볼트만 지원합니다. 이전 섹션에서 **고객이 관리하는 키** 옵션으로 암호화를 사용하도록 설정하는 경우 키를 Azure Key Vault로 가져와야 합니다. 또한 키에 **소프트 삭제** 및 **키에** 대해 제거 안 함을 구성해야 합니다. 이러한 설정은 [PowerShell](../key-vault/key-vault-soft-delete-powershell.md) 또는 [CLI를](../key-vault/key-vault-soft-delete-cli.md#enabling-purge-protection)사용하여 구성할 수 있습니다.

1. 새 키 자격 증명 모음을 만들려면 Azure 키 볼트 [빠른 시작을](../key-vault/key-vault-overview.md)따릅니다. 기존 키 가져오기에 대한 자세한 내용은 [키, 암호 및 인증서 정보](../key-vault/about-keys-secrets-and-certificates.md)참조
1. 볼트를 만들 때 소프트 삭제 및 제거 보호를 모두 사용하려면 [az 키볼트 만들기](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) 명령을 사용합니다.

    ```azurecli-interactive
    az keyvault create --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. 기존 볼트(이미 소프트 삭제가 활성화되어 있음)에 퍼지 보호를 추가하려면 [az keyvault 업데이트](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) 명령을 사용합니다.

    ```azurecli-interactive
    az keyvault update --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --enable-purge-protection true
    ```
1. 다음 단계에 따라 키를 만듭니다.
    1. 새 키를 만들려면 **설정** 아래 **키** 메뉴에서 **생성/가져오기**를 선택합니다.
        
        ![생성/가져오기 단추 선택](./media/configure-customer-managed-key/select-generate-import.png)

    1. **옵션**을 **생성**으로 설정하고 키에 이름을 지정합니다.

        ![키 만들기](./media/configure-customer-managed-key/create-key.png) 

    1. 이제 드롭다운 목록에서 암호화할 서비스 버스 네임스페이스와 연결하려면 이 키를 선택할 수 있습니다. 

        ![키 자격 증명 모음에서 키 선택](./media/configure-customer-managed-key/select-key-from-key-vault.png)
        > [!NOTE]
        > 중복성의 경우 최대 3개의 키를 추가할 수 있습니다. 키 중 하나가 만료되었거나 액세스할 수 없는 경우 다른 키가 암호화에 사용됩니다.
        
    1. 키에 대한 세부 정보를 입력하고 **선택 을**클릭합니다. 이렇게 하면 고객이 관리하는 키를 사용하여 네임스페이스에서 미사용 데이터를 암호화할 수 있습니다. 


    > [!IMPORTANT]
    > 지오 재해 복구와 함께 고객 관리 키를 사용하려는 경우 아래를 검토하십시오. 
    >
    > 고객이 관리하는 키로 미사용 암호화를 활성화하려면 지정된 Azure KeyVault에서 Service Bus의 관리ID에 대한 [액세스 정책이](../key-vault/key-vault-secure-your-key-vault.md) 설정됩니다. 이렇게 하면 Azure 서비스 버스 네임스페이스에서 Azure KeyVault에 대한 제어된 액세스가 보장됩니다.
    >
    > 이로 인해:
    > 
    >   * Service Bus 네임스페이스에 [대해 지오 재해 복구가](service-bus-geo-dr.md) 이미 활성화되어 있고 고객 관리 키를 사용하도록 설정하려는 경우 
    >     * 페어링 중단
    >     * 기본 및 보조 네임스페이스에 대해 관리되는 ID에 대한 액세스 정책을 키 자격 증명 모음에 [설정합니다.](../key-vault/managed-identity.md)
    >     * 기본 네임스페이스에 암호화를 설정합니다.
    >     * 기본 및 보조 네임스페이스를 다시 페어링합니다.
    > 
    >   * 고객 관리 키가 이미 설정된 Service Bus 네임스페이스에서 Geo-DR을 사용하도록 설정하려는 경우 -
    >     * 보조 [네임스페이스에](../key-vault/managed-identity.md) 대한 관리되는 ID에 대한 액세스 정책을 키 자격 증명 모음에 설정합니다.
    >     * 기본 및 보조 네임스페이스를 페어링합니다.


## <a name="rotate-your-encryption-keys"></a>암호화 키 회전

Azure 키 볼트 회전 메커니즘을 사용하여 키 볼트에서 키를 회전할 수 있습니다. 자세한 내용은 [키 회전 및 감사 설정을](../key-vault/key-vault-key-rotation-log-monitoring.md)참조하십시오. 활성화 및 만료 날짜를 설정하여 키 회전을 자동화할 수도 있습니다. 서비스 버스 서비스는 새 키 버전을 검색하고 자동으로 사용하기 시작합니다.

## <a name="revoke-access-to-keys"></a>키에 대한 액세스 취소

암호화 키에 대한 액세스를 취소하는 것은 서비스 버스에서 데이터를 제거하지 않습니다. 그러나 Service Bus 네임스페이스에서 데이터에 액세스할 수 없습니다. 액세스 정책을 통해 또는 키를 삭제하여 암호화 키를 해지할 수 있습니다. 키 자격 증명 모음에 대한 보안 액세스에서 액세스 정책 및 키 자격 증명 모음 [보안에](../key-vault/key-vault-secure-your-key-vault.md)대해 자세히 알아보십시오.

암호화 키가 해지되면 암호화된 네임스페이스의 Service Bus 서비스가 작동하지 않게 됩니다. 키에 대한 액세스가 활성화되었거나 삭제된 키가 복원된 경우 Service Bus 서비스가 암호화된 Service Bus 네임스페이스에서 데이터에 액세스할 수 있도록 키를 선택합니다.

## <a name="use-resource-manager-template-to-enable-encryption"></a>리소스 관리자 템플릿을 사용하여 암호화 사용
이 섹션에서는 Azure 리소스 관리자 템플릿을 사용하여 다음 작업을 수행하는 방법을 보여 **주며 있습니다.** 

1. 관리되는 서비스 **ID를**사용하여 **프리미엄** Service Bus 네임스페이스를 만듭니다.
2. 키 **자격 증명 모음을** 만들고 키 자격 증명 모음에 대한 서비스 ID 액세스 권한을 부여합니다. 
3. 키 자격 증명 모음 정보(키/값)로 서비스 버스 네임스페이스를 업데이트합니다. 


### <a name="create-a-premium-service-bus-namespace-with-managed-service-identity"></a>관리되는 서비스 ID를 사용하여 프리미엄 서비스 버스 네임스페이스 만들기
이 섹션에서는 Azure 리소스 관리자 템플릿 및 PowerShell을 사용하여 관리되는 서비스 ID를 사용하여 Azure Service Bus 네임스페이스를 만드는 방법을 보여 주며, 이 섹션에서는 관리되는 서비스 ID를 사용하여 Azure Service Bus 네임스페이스를 만드는 방법을 보여 주며, 이 섹션에서는 

1. Azure 리소스 관리자 템플릿을 만들어 관리되는 서비스 ID를 사용하여 Service Bus 프리미엄 계층 네임스페이스를 만듭니다. 파일 이름: **만들기ServiceBusPremiumNamespace.json:** 

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
2. 라는 템플릿 매개 변수 파일 만들기: **CreateServiceBusPremiumNamespaceParams.json**. 

    > [!NOTE]
    > 다음 값을 바꿉니다. 
    > - `<ServiceBusNamespaceName>`- 서비스 버스 네임스페이스 이름
    > - `<Location>`- 서비스 버스 네임스페이스 위치

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
3. 다음 PowerShell 명령을 실행하여 템플릿을 배포하여 프리미엄 Service Bus 네임스페이스를 만듭니다. 그런 다음 Service Bus 네임스페이스의 ID를 검색하여 나중에 사용하십시오. 명령을 `{MyRG}` 실행하기 전에 리소스 그룹의 이름으로 바꿉니다.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateServiceBusPremiumNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateServiceBusPremiumNamespace.json -TemplateParameterFile ./CreateServiceBusPremiumNamespaceParams.json
    
    $ServiceBusNamespaceId = $outputs.Outputs["serviceBusNamespaceId"].value
    ```
 
### <a name="grant-service-bus-namespace-identity-access-to-key-vault"></a>키 자격 증명 모음에 대한 서비스 버스 네임스페이스 ID 액세스 권한 부여

1. 다음 명령을 실행하여 **퍼지 보호** 및 소프트 삭제를 사용하도록 설정한 키 자격 증명 **모음을** 만듭니다. 

    ```powershell
    New-AzureRmKeyVault -Name "{keyVaultName}" -ResourceGroupName {RGName}  -Location "{location}" -EnableSoftDelete -EnablePurgeProtection    
    ```
    
    또는
    
    다음 명령을 실행하여 **기존 키 자격 증명 모음을**업데이트합니다. 명령을 실행하기 전에 리소스 그룹 및 키 자격 증명 모음 이름에 대한 값을 지정합니다. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Service Bus 네임스페이스의 관리되는 ID가 키 자격 증명 모음의 키 값에 액세스할 수 있도록 키 자격 증명 모음 액세스 정책을 설정합니다. 이전 섹션에서 서비스 버스 네임스페이스의 ID를 사용합니다. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $ServiceBusNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-service-bus-namespace-with-customer-managed-key-from-key-vault"></a>키 자격 증명 모음에서 고객 관리 키를 사용하여 서비스 버스 네임스페이스의 데이터 암호화
지금까지 다음 단계를 수행했습니다. 

1. 관리되는 ID를 가진 프리미엄 네임스페이스를 만들었습니다.
2. 키 자격 증명 모음을 만들고 키 자격 증명 모음에 대한 관리되는 ID 액세스 권한을 부여합니다. 

이 단계에서는 키 자격 증명 모음 정보로 Service Bus 네임스페이스를 업데이트합니다. 

1. 다음 내용으로 **UpdateServiceBusNamespaceWith암호화.json이라는** JSON 파일을 만듭니다. 

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

2. 템플릿 매개 변수 파일 만들기: **UpdateServiceBusNamespaceWith암호화파라름.json**.

    > [!NOTE]
    > 다음 값을 바꿉니다. 
    > - `<ServiceBusNamespaceName>`- 서비스 버스 네임스페이스 이름
    > - `<Location>`- 서비스 버스 네임스페이스 위치
    > - `<KeyVaultName>`- 키 금고의 이름
    > - `<KeyName>`- 키 금고의 키 이름  

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
3. 다음 PowerShell 명령을 실행하여 리소스 관리자 템플릿을 배포합니다. 명령을 `{MyRG}` 실행하기 전에 리소스 그룹의 이름으로 바꿉니다. 

    ```powershell
    New-AzResourceGroupDeployment -Name UpdateServiceBusNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateServiceBusNamespaceWithEncryption.json -TemplateParameterFile ./UpdateServiceBusNamespaceWithEncryptionParams.json
    ```
    

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.
- [서비스 버스 개요](service-bus-messaging-overview.md)
- [키 볼트 개요](../key-vault/key-vault-overview.md)


