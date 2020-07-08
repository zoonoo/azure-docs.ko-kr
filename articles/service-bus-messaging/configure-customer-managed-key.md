---
title: 휴지 상태의 데이터를 암호화 하기 위한 고유한 키 구성 Azure Service Bus
description: 이 문서에서는 rest Azure Service Bus 데이터를 암호화 하기 위한 고유한 키를 구성 하는 방법에 대 한 정보를 제공 합니다.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: ca1597f26ec1c7ccaa578d4e7dcd68e0ef54f60c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85475988"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-service-bus-data-at-rest-by-using-the-azure-portal"></a>Azure Portal를 사용 하 여 미사용 데이터 Azure Service Bus 데이터를 암호화 하기 위해 고객이 관리 하는 키 구성
Azure Service Bus Premium은 Azure SSE (Azure Storage 서비스 암호화)를 사용 하 여 미사용 데이터의 암호화를 제공 합니다. Service Bus Premium은 Azure Storage를 사용 하 여 데이터를 저장 하며, 기본적으로 Azure Storage와 함께 저장 되는 모든 데이터는 Microsoft 관리 키를 사용 하 여 암호화 됩니다. 

## <a name="overview"></a>개요
이제 Azure Service Bus은 Microsoft에서 관리 하는 키 또는 고객이 관리 하는 키 (Bring Your Own Key BYOK)를 사용 하 여 미사용 데이터를 암호화 하는 옵션을 지원 합니다. 이 기능을 사용 하면 미사용 Azure Service Bus을 암호화 하는 데 사용 되는 고객 관리 키에 대 한 액세스를 만들고, 회전 하거나, 사용 하지 않도록 설정할 수 있습니다.

BYOK 기능을 사용 하도록 설정 하는 작업은 네임 스페이스에서 한 번만 설정 하면 됩니다.

> [!NOTE]
> 서비스 쪽 암호화에 대 한 고객 관리 키에는 몇 가지 주의 사항이 있습니다. 
>   * 이 기능은 [Azure Service Bus 프리미엄](service-bus-premium-messaging.md) 계층에서 지원 됩니다. 표준 계층 Service Bus 네임 스페이스에 대해 사용 하도록 설정할 수 없습니다.
>   * 암호화는 새 네임 스페이스 또는 빈 네임 스페이스에만 사용할 수 있습니다. 네임 스페이스에 데이터가 포함 되어 있으면 암호화 작업이 실패 합니다.

Azure Key Vault를 사용 하 여 키를 관리 하 고 키 사용을 감사할 수 있습니다. 사용자 고유의 키를 만들어 키 자격 증명 모음에 저장할 수도 있고, Azure Key Vault API를 사용하여 키를 생성할 수도 있습니다. Azure Key Vault에 대한 자세한 내용은 [Azure Key Vault란?](../key-vault/general/overview.md)

이 문서에서는 Azure Portal를 사용 하 여 고객이 관리 하는 키로 key vault를 구성 하는 방법을 보여 줍니다. Azure Portal을 사용하여 키 자격 증명 모음을 만드는 방법은 [빠른 시작: Azure Portal을 사용하여 Azure Key Vault에서 비밀을 설정하고 검색](../key-vault/secrets/quick-create-portal.md)을 참조하세요.

> [!IMPORTANT]
> Azure Service Bus에서 고객 관리 키를 사용 하려면 키 자격 증명 모음에 두 개의 필수 속성이 구성 되어 있어야 합니다. **일시 삭제** 및 **제거 안 함**입니다. 이러한 속성은 Azure Portal에서 새 키 자격 증명 모음을 만들 때 기본적으로 사용 하도록 설정 됩니다. 그러나 기존 키 자격 증명 모음에서 이러한 속성을 사용 하도록 설정 해야 하는 경우에는 PowerShell 또는 Azure CLI를 사용 해야 합니다.

## <a name="enable-customer-managed-keys"></a>고객 관리형 키 사용
Azure Portal에서 고객 관리형 키를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Service Bus Premium 네임 스페이스로 이동 합니다.
2. Service Bus 네임 스페이스의 **설정** 페이지에서 **암호화**를 선택 합니다.
3. 다음 그림에 표시 된 것 처럼 **미사용에서 고객이 관리 하는 키 암호화** 를 선택 합니다.

    ![고객 관리형 키 사용](./media/configure-customer-managed-key/enable-customer-managed-key.png)


## <a name="set-up-a-key-vault-with-keys"></a>키를 사용 하 여 키 자격 증명 모음 설정

고객이 관리 하는 키를 사용 하도록 설정한 후에는 고객 관리 키를 Azure Service Bus 네임 스페이스와 연결 해야 합니다. Service Bus은 Azure Key Vault만 지원 합니다. 이전 섹션에서 **고객이 관리 하는 키로 암호화** 옵션을 사용 하도록 설정 하는 경우 키를 Azure Key Vault으로 가져와야 합니다. 또한 키에는 **일시 삭제** 및 키에 대해 구성 된 **제거 안 함** 이 있어야 합니다. 이러한 설정은 [PowerShell](../key-vault/general/soft-delete-powershell.md) 또는 [CLI](../key-vault/general/soft-delete-cli.md#enabling-purge-protection)를 사용 하 여 구성할 수 있습니다.

1. 새 키 자격 증명 모음을 만들려면 Azure Key Vault [빠른](../key-vault/general/overview.md)시작을 수행 합니다. 기존 키를 가져오는 방법에 대 한 자세한 내용은 [키, 암호 및 인증서 정보](../key-vault/about-keys-secrets-and-certificates.md)를 참조 하세요.
1. 자격 증명 모음을 만들 때 일시 삭제 및 제거 보호를 모두 켜려면 [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) 명령을 사용 합니다.

    ```azurecli-interactive
    az keyvault create --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. 이미 일시 삭제를 사용 하도록 설정한 상태에서 기존 자격 증명 모음에 제거 방지를 추가 하려면 [az keyvault update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) 명령을 사용 합니다.

    ```azurecli-interactive
    az keyvault update --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --enable-purge-protection true
    ```
1. 다음 단계를 수행 하 여 키를 만듭니다.
    1. 새 키를 만들려면 **설정** 아래 **키** 메뉴에서 **생성/가져오기**를 선택합니다.
        
        ![생성/가져오기 단추를 선택 합니다.](./media/configure-customer-managed-key/select-generate-import.png)

    1. **옵션**을 **생성**으로 설정하고 키에 이름을 지정합니다.

        ![키 만들기](./media/configure-customer-managed-key/create-key.png) 

    1. 이제이 키를 선택 하 여 드롭다운 목록에서 암호화할 Service Bus 네임 스페이스와 연결할 수 있습니다. 

        ![키 자격 증명 모음에서 키 선택](./media/configure-customer-managed-key/select-key-from-key-vault.png)
        > [!NOTE]
        > 중복성을 위해 최대 3 개의 키를 추가할 수 있습니다. 키 중 하나가 만료 되었거나 액세스할 수 없는 경우 다른 키가 암호화에 사용 됩니다.
        
    1. 키에 대 한 세부 정보를 입력 하 고 **선택**을 클릭 합니다. 이렇게 하면 고객이 관리 하는 키를 사용 하 여 네임 스페이스에 있는 미사용 데이터를 암호화할 수 있습니다. 


    > [!IMPORTANT]
    > 지역 재해 복구와 함께 고객 관리 키를 사용 하려는 경우 다음을 검토 하세요. 
    >
    > 고객 관리 키를 사용 하 여 미사용 암호화를 사용 하도록 설정 하기 위해 지정 된 Azure KeyVault에서 Service Bus ' 관리 id에 대 한 [액세스 정책이](../key-vault/general/secure-your-key-vault.md) 설정 됩니다. 이렇게 하면 Azure Service Bus 네임 스페이스의 Azure KeyVault에 대 한 제어 된 액세스가 보장 됩니다.
    >
    > 다음 이유로 인해:
    > 
    >   * Service Bus 네임 스페이스에 대해 [지역 재해 복구](service-bus-geo-dr.md) 를 이미 사용 하도록 설정 하 고 고객 관리 키를 사용 하도록 설정 하려는 경우 
    >     * 페어링 분리
    >     * 주요 자격 증명 모음에 대 한 기본 및 보조 네임 스페이스에 대 한 관리 되는 id에 대 한 [액세스 정책을 설정](../key-vault/general/managed-identity.md) 합니다.
    >     * 기본 네임 스페이스에 대 한 암호화를 설정 합니다.
    >     * 기본 및 보조 네임 스페이스를 다시 페어링 합니다.
    > 
    >   * 고객 관리 키가 이미 설정 된 Service Bus 네임 스페이스에서 지역 DR을 사용 하도록 설정 하려는 경우 다음을 사용 합니다.
    >     * 보조 네임 스페이스에 대 한 관리 되는 id에 대 한 [액세스 정책을](../key-vault/general/managed-identity.md) 키 자격 증명 모음에 설정 합니다.
    >     * 기본 및 보조 네임 스페이스를 쌍으로 연결 합니다.


## <a name="rotate-your-encryption-keys"></a>암호화 키 회전

Azure 키 자격 증명 모음 회전 메커니즘을 사용 하 여 키 자격 증명 모음에서 키를 회전할 수 있습니다. 키 회전을 자동화 하도록 활성화 및 만료 날짜를 설정할 수도 있습니다. Service Bus 서비스는 새 키 버전을 검색 하 고 자동으로 사용을 시작 합니다.

## <a name="revoke-access-to-keys"></a>키에 대 한 액세스 취소

암호화 키에 대 한 액세스를 취소 해도 Service Bus의 데이터는 제거 되지 않습니다. 그러나 Service Bus 네임 스페이스에서 데이터에 액세스할 수 없습니다. 액세스 정책을 통해 또는 키를 삭제 하 여 암호화 키를 해지할 수 있습니다. 키 자격 증명 [모음에 대 한 보안 액세스](../key-vault/general/secure-your-key-vault.md)에서 액세스 정책 및 키 자격 증명 모음 보안에 대해 자세히 알아보세요.

암호화 키가 해지 되 면 암호화 된 네임 스페이스의 Service Bus 서비스가 작동 하지 않게 됩니다. 키에 대 한 액세스를 사용 하도록 설정 하거나 삭제 된 키를 복원 하는 경우 Service Bus 서비스는 암호화 된 Service Bus 네임 스페이스의 데이터에 액세스할 수 있도록 키를 선택 합니다.

## <a name="use-resource-manager-template-to-enable-encryption"></a>리소스 관리자 템플릿을 사용 하 여 암호화 사용
이 섹션에서는 **Azure Resource Manager 템플릿을**사용 하 여 다음 작업을 수행 하는 방법을 보여 줍니다. 

1. **관리 서비스 id**를 사용 하 여 **프리미엄** Service Bus 네임 스페이스를 만듭니다.
2. 주요 자격 **증명 모음** 을 만들고 키 자격 증명 모음에 대 한 서비스 id 액세스 권한을 부여 합니다. 
3. 키 자격 증명 모음 정보 (키/값)를 사용 하 여 Service Bus 네임 스페이스를 업데이트 합니다. 


### <a name="create-a-premium-service-bus-namespace-with-managed-service-identity"></a>관리 서비스 id를 사용 하 여 프리미엄 Service Bus 네임 스페이스 만들기
이 섹션에서는 Azure Resource Manager 템플릿 및 PowerShell을 사용 하 여 관리 서비스 id를 사용 하 여 Azure Service Bus 네임 스페이스를 만드는 방법을 보여 줍니다. 

1. 관리 서비스 id를 사용 하 여 Service Bus 프리미엄 계층 네임 스페이스를 만드는 Azure Resource Manager 템플릿을 만듭니다. 파일 이름: **CreateServiceBusPremiumNamespace.js**: 

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
2. 이름: **CreateServiceBusPremiumNamespaceParams.js에**템플릿 매개 변수 파일을 만듭니다. 

    > [!NOTE]
    > 다음 값을 바꿉니다. 
    > - `<ServiceBusNamespaceName>`-Service Bus 네임 스페이스의 이름
    > - `<Location>`-Service Bus 네임 스페이스의 위치

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
3. 다음 PowerShell 명령을 실행 하 여 템플릿을 배포 하 고 premium Service Bus 네임 스페이스를 만듭니다. 그런 다음 나중에 사용할 Service Bus 네임 스페이스의 ID를 검색 합니다. `{MyRG}`명령을 실행 하기 전에 리소스 그룹의 이름으로 대체 합니다.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateServiceBusPremiumNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateServiceBusPremiumNamespace.json -TemplateParameterFile ./CreateServiceBusPremiumNamespaceParams.json
    
    $ServiceBusNamespaceId = $outputs.Outputs["serviceBusNamespaceId"].value
    ```
 
### <a name="grant-service-bus-namespace-identity-access-to-key-vault"></a>Key vault에 Service Bus 네임 스페이스 id 액세스 권한 부여

1. 다음 명령을 실행 하 여 **보호 제거** 및 **일시 삭제** 를 사용 하는 주요 자격 증명 모음을 만듭니다. 

    ```powershell
    New-AzureRmKeyVault -Name "{keyVaultName}" -ResourceGroupName {RGName}  -Location "{location}" -EnableSoftDelete -EnablePurgeProtection    
    ```
    
    또는
    
    다음 명령을 실행 하 여 **기존 주요 자격 증명 모음**을 업데이트 합니다. 명령을 실행 하기 전에 리소스 그룹 및 주요 자격 증명 모음 이름에 대 한 값을 지정 합니다. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Service Bus 네임 스페이스의 관리 id가 key vault의 키 값에 액세스할 수 있도록 키 자격 증명 모음 액세스 정책을 설정 합니다. 이전 섹션에서 Service Bus 네임 스페이스의 ID를 사용 합니다. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $ServiceBusNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-service-bus-namespace-with-customer-managed-key-from-key-vault"></a>Key vault에서 고객 관리 키를 사용 하 여 Service Bus 네임 스페이스의 데이터 암호화
지금까지 다음 단계를 완료 했습니다. 

1. 관리 id를 사용 하 여 프리미엄 네임 스페이스를 만들었습니다.
2. 주요 자격 증명 모음을 만들고 관리 id에 키 자격 증명 모음에 대 한 액세스 권한을 부여 합니다. 

이 단계에서는 키 자격 증명 모음 정보를 사용 하 여 Service Bus 네임 스페이스를 업데이트 합니다. 

1. 다음 콘텐츠를 사용 하 여 **UpdateServiceBusNamespaceWithEncryption.js에** 이라는 JSON 파일을 만듭니다. 

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

2. 템플릿 매개 변수 파일 만들기: **UpdateServiceBusNamespaceWithEncryptionParams.js**.

    > [!NOTE]
    > 다음 값을 바꿉니다. 
    > - `<ServiceBusNamespaceName>`-Service Bus 네임 스페이스의 이름
    > - `<Location>`-Service Bus 네임 스페이스의 위치
    > - `<KeyVaultName>`-키 자격 증명 모음의 이름
    > - `<KeyName>`-키 자격 증명 모음에 있는 키의 이름  

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
3. 다음 PowerShell 명령을 실행 하 여 리소스 관리자 템플릿을 배포 합니다. `{MyRG}`명령을 실행 하기 전에을 리소스 그룹의 이름으로 바꿉니다. 

    ```powershell
    New-AzResourceGroupDeployment -Name UpdateServiceBusNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateServiceBusNamespaceWithEncryption.json -TemplateParameterFile ./UpdateServiceBusNamespaceWithEncryptionParams.json
    ```
    

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.
- [Service Bus 개요](service-bus-messaging-overview.md)
- [Key Vault 개요](../key-vault/general/overview.md)


