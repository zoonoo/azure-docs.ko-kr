---
title: 미사용 Azure 이벤트 허브 데이터 암호화를 위해 사용자 고유의 키 구성
description: 이 문서에서는 Azure Event Hubs 데이터 나머지를 암호화하기 위해 고유한 키를 구성하는 방법에 대한 정보를 제공합니다.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: spelluru
ms.openlocfilehash: 43e626355feaf1e51fc840f82506c559a1859b84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77621997"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-event-hubs-data-at-rest-by-using-the-azure-portal"></a>Azure 포털을 사용하여 미사용 Azure Event Hubs 데이터를 암호화하기 위한 고객 관리 키 구성
Azure 이벤트 허브는 Azure 저장소 서비스 암호화(Azure SSE)를 사용하여 미사용 데이터의 암호화를 제공합니다. 이벤트 허브는 데이터를 저장하기 위해 Azure Storage를 사용하며 기본적으로 Azure Storage에 저장된 모든 데이터는 Microsoft 관리 키를 사용하여 암호화됩니다. 

## <a name="overview"></a>개요
Azure 이벤트 허브는 이제 Microsoft에서 관리하는 키 또는 고객 관리 키(사용자 고유키 가져오기 – BYOK)를 사용하여 미사용 데이터를 암호화하는 옵션을 지원합니다. 이 기능을 사용하면 미사용 Azure Event Hubs 데이터를 암호화하는 데 사용되는 고객 관리 키에 대한 액세스를 생성, 회전, 비활성화 및 취소할 수 있습니다.

BYOK 기능을 사용하도록 설정하는 것은 네임스페이스에서 일회성 설정 프로세스입니다.

> [!NOTE]
> BYOK 기능은 이벤트 [허브 전용 단일 테넌트](event-hubs-dedicated-overview.md) 클러스터에서 지원됩니다. 표준 이벤트 허브 네임스페이스는 사용할 수 없습니다.

Azure Key Vault를 사용하여 키를 관리하고 키 사용량을 감사할 수 있습니다. 사용자 고유의 키를 만들어 키 자격 증명 모음에 저장하거나 Azure Key Vault API를 사용하여 키를 생성할 수 있습니다. Azure 키 자격 증명 모음에 대한 자세한 내용은 [Azure 키 자격 증명 모음이란 무엇입니까?](../key-vault/key-vault-overview.md)

이 문서에서는 Azure 포털을 사용하여 고객 관리 키로 키 자격 증명 모음을 구성하는 방법을 보여 주며 있습니다. Azure 포털을 사용하여 키 자격 증명 모음을 만드는 방법을 알아보려면 [빠른 시작: Azure 포털을 사용하여 Azure 키 자격 증명 모음에서 비밀 설정 및 검색을](../key-vault/quick-create-portal.md)참조하세요.

> [!IMPORTANT]
> Azure Event Hubs를 사용하여 고객 관리 키를 사용하려면 키 자격 증명 모음에 두 개의 필수 속성이 구성되어 있어야 합니다. 그들은 : **소프트 삭제** 및 **제거하지 마십시오**. 이러한 속성은 Azure Portal에서 새 키 자격 증명 모음을 만들 때 기본적으로 활성화됩니다. 그러나 기존 키 자격 증명 모음에서 이러한 속성을 사용하도록 설정해야 하는 경우 PowerShell 또는 Azure CLI를 사용해야 합니다.

## <a name="enable-customer-managed-keys"></a>고객 관리 키 사용
Azure 포털에서 고객 관리 키를 활성화하려면 다음 단계를 따르십시오.

1. 이벤트 허브 전용 클러스터로 이동합니다.
1. BYOK를 사용할 네임스페이스를 선택합니다.
1. 이벤트 허브 네임스페이스의 **설정** 페이지에서 **암호화**를 선택합니다. 
1. 다음 이미지와 같이 **미사용 고객 관리 키 암호화를** 선택합니다. 

    ![고객 관리 키 사용](./media/configure-customer-managed-key/enable-customer-managed-key.png)

## <a name="set-up-a-key-vault-with-keys"></a>키로 키 자격 증명 모음 설정
고객 관리 키를 사용하도록 설정한 후에는 고객 관리 키를 Azure Event Hubs 네임스페이스에 연결해야 합니다. 이벤트 허브는 Azure 키 자격 증명 모음만 지원합니다. 이전 섹션에서 **고객이 관리하는 키** 옵션으로 암호화를 사용하도록 설정하는 경우 키를 Azure Key Vault로 가져와야 합니다. 또한 키에 **소프트 삭제** 및 **키에** 대해 제거 안 함을 구성해야 합니다. 이러한 설정은 [PowerShell](../key-vault/key-vault-soft-delete-powershell.md) 또는 [CLI를](../key-vault/key-vault-soft-delete-cli.md#enabling-purge-protection)사용하여 구성할 수 있습니다.

1. 새 키 자격 증명 모음을 만들려면 Azure 키 볼트 [빠른 시작을](../key-vault/key-vault-overview.md)따릅니다. 기존 키 가져오기에 대한 자세한 내용은 [키, 암호 및 인증서 정보](../key-vault/about-keys-secrets-and-certificates.md)참조
1. 볼트를 만들 때 소프트 삭제 및 제거 보호를 모두 사용하려면 [az 키볼트 만들기](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) 명령을 사용합니다.

    ```azurecli-interactive
    az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. 기존 볼트(이미 소프트 삭제가 활성화되어 있음)에 퍼지 보호를 추가하려면 [az keyvault 업데이트](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) 명령을 사용합니다.

    ```azurecli-interactive
    az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
    ```
1. 다음 단계에 따라 키를 만듭니다.
    1. 새 키를 만들려면 **설정** 아래 **키** 메뉴에서 **생성/가져오기**를 선택합니다.
        
        ![생성/가져오기 단추 선택](./media/configure-customer-managed-key/select-generate-import.png)
    1. **옵션**을 **생성**으로 설정하고 키에 이름을 지정합니다.

        ![키 만들기](./media/configure-customer-managed-key/create-key.png) 
    1. 이제 드롭다운 목록에서 암호화할 수 있도록 이 키를 선택하여 이벤트 허브 네임스페이스와 연결할 수 있습니다. 

        ![키 자격 증명 모음에서 키 선택](./media/configure-customer-managed-key/select-key-from-key-vault.png)
    1. 키에 대한 세부 정보를 입력하고 **선택 을**클릭합니다. 이렇게 하면 고객이 관리하는 키를 사용하여 네임스페이스에서 미사용 데이터를 암호화할 수 있습니다. 


## <a name="rotate-your-encryption-keys"></a>암호화 키 회전
Azure 키 볼트 회전 메커니즘을 사용하여 키 볼트에서 키를 회전할 수 있습니다. 자세한 내용은 [키 회전 및 감사 설정을](../key-vault/key-vault-key-rotation-log-monitoring.md)참조하십시오. 활성화 및 만료 날짜를 설정하여 키 회전을 자동화할 수도 있습니다. 이벤트 허브 서비스는 새 키 버전을 검색하고 자동으로 사용하기 시작합니다.

## <a name="revoke-access-to-keys"></a>키에 대한 액세스 취소
암호화 키에 대한 액세스를 취소한다고 해서 이벤트 허브에서 데이터가 제거되지는 않습니다. 그러나 이벤트 허브 네임스페이스에서 데이터에 액세스할 수 없습니다. 액세스 정책을 통해 또는 키를 삭제하여 암호화 키를 해지할 수 있습니다. 키 자격 증명 모음에 대한 보안 액세스에서 액세스 정책 및 키 자격 증명 모음 [보안에](../key-vault/key-vault-secure-your-key-vault.md)대해 자세히 알아보십시오.

암호화 키가 해지되면 암호화된 네임스페이스의 이벤트 허브 서비스가 작동하지 않게 됩니다. 키에 대한 액세스가 활성화되어 있거나 삭제 키가 복원된 경우 이벤트 허브 서비스는 암호화된 이벤트 허브 네임스페이스에서 데이터에 액세스할 수 있도록 키를 선택합니다.

## <a name="set-up-diagnostic-logs"></a>진단 로그 설정 
BYOK 사용 네임스페이스에 대한 진단 로그를 설정하면 네임스페이스가 고객 관리 키로 암호화될 때 작업에 필요한 정보를 제공합니다. 이러한 로그를 활성화하고 나중에 이벤트 허브로 스트리밍하거나 로그 분석을 통해 분석하거나 스토리지로 스트리밍하여 사용자 지정 분석을 수행할 수 있습니다. 진단 로그에 대한 자세한 내용은 [Azure 진단 로그 개요를](../azure-monitor/platform/platform-logs-overview.md)참조하십시오.

## <a name="enable-user-logs"></a>사용자 로그 사용
다음 단계에 따라 고객 관리 키에 대한 로그를 사용하도록 설정합니다.

1. Azure 포털에서 BYOK가 활성화된 네임스페이스로 이동합니다.
1. **모니터링**에서 **진단 설정을** 선택합니다.

    ![진단 설정 선택](./media/configure-customer-managed-key/select-diagnostic-settings.png)
1. **+진단 설정 추가를**선택합니다. 

    ![진단 설정 추가 선택](./media/configure-customer-managed-key/select-add-diagnostic-setting.png)
1. **이름을** 제공하고 로그를 스트리밍할 위치를 선택합니다.
1. **고객 관리키유저 로그를** 선택하고 **을 저장합니다.** 이 작업을 수행하면 네임스페이스에서 BYOK에 대한 로그를 사용할 수 있습니다.

    ![고객 관리 키 사용자 로그 옵션 선택](./media/configure-customer-managed-key/select-customer-managed-key-user-logs.png)

## <a name="log-schema"></a>로그 스키마 
모든 로그는 JSON(JavaScript Object Notation) 형식으로 저장됩니다. 각 항목에는 다음 표에 설명된 형식을 사용하는 문자열 필드가 있습니다. 

| 이름 | 설명 |
| ---- | ----------- | 
| TaskName | 실패한 작업에 대한 설명입니다. |
| ActivityId | 추적에 사용되는 내부 ID입니다. |
| category | 작업의 분류를 정의합니다. 예를 들어 키 자격 증명 모음의 키가 비활성화된 경우 정보 범주가 되거나 키를 래핑해제할 수 없는 경우 오류가 발생했습니다. |
| resourceId | Azure Resource Manager 리소스 ID |
| 키 볼트 | 키 볼트의 전체 이름입니다. |
| key | 이벤트 허브 네임스페이스를 암호화하는 데 사용되는 키 이름입니다. |
| 버전 | 사용 중인 키의 버전입니다. |
| operation(작업) | 키 자격 증명 모음의 키에서 수행되는 작업입니다. 예를 들어 키를 비활성화/활성화하거나 래핑 또는 래핑 해제 |
| 코드 | 작업과 연결된 코드입니다. 예: 오류 코드, 404는 키가 발견되지 않았음을 의미합니다. |
| message | 작업과 연결된 모든 오류 메시지 |

다음은 고객 관리 키에 대한 로그의 예입니다.

```json
{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111-1111-1111-1111-111111111111",
   "category": "error"
   "resourceId": "/SUBSCRIPTIONS/11111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "1111111111111111111111111111111",
   "operation": "wrapKey",
   "code": "404",
   "message": "Key not found: ehbyok0/111111111111111111111111111111",
}



{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111111111-1111-1111-1111111111111",
   "category": "info"
   "resourceId": "/SUBSCRIPTIONS/111111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "111111111111111111111111111111",
   "operation": "disable" | "restore",
   "code": "",
   "message": "",
}
```

## <a name="use-resource-manager-template-to-enable-encryption"></a>리소스 관리자 템플릿을 사용하여 암호화 사용
이 섹션에서는 Azure 리소스 관리자 템플릿을 사용하여 다음 작업을 수행하는 방법을 보여 **주며 있습니다.** 

1. 관리되는 서비스 ID를 사용하여 **이벤트 허브 네임스페이스를** 만듭니다.
2. 키 **자격 증명 모음을** 만들고 키 자격 증명 모음에 대한 서비스 ID 액세스 권한을 부여합니다. 
3. 키 자격 증명 모음 정보(키/값)로 이벤트 허브 네임스페이스를 업데이트합니다. 


### <a name="create-an-event-hubs-cluster-and-namespace-with-managed-service-identity"></a>관리되는 서비스 ID로 이벤트 허브 클러스터 및 네임스페이스 만들기
이 섹션에서는 Azure 리소스 관리자 템플릿 및 PowerShell을 사용하여 관리되는 서비스 ID를 사용하여 Azure Event Hubs 네임스페이스를 만드는 방법을 보여 주며, 이 섹션에서는 관리되는 서비스 ID를 사용하여 Azure Event Hubs 네임스페이스를 만드는 방법을 보여 주며, 이 섹션에서는 

1. Azure 리소스 관리자 템플릿을 만들어 관리되는 서비스 ID를 사용하여 이벤트 허브 네임스페이스를 만듭니다. 파일 이름 지정: **CreateEventHub클러스터AndNamespace.json:** 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
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
          }
       },
       "resources":[
          {
             "type":"Microsoft.EventHub/clusters",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('clusterName')]",
             "location":"[parameters('location')]",
             "sku":{
                "name":"Dedicated",
                "capacity":1
             }
          },
          {
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             },
             "dependsOn":[
                "[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             ]
          }
       ],
       "outputs":{
          "EventHubNamespaceId":{
             "type":"string",
             "value":"[resourceId('Microsoft.EventHub/namespaces',parameters('namespaceName'))]"
          }
       }
    }
    ```
2. 라는 템플릿 매개 변수 파일 만들기: **CreateEventHubClusterAndNamespaceParams.json**. 

    > [!NOTE]
    > 다음 값을 바꿉니다. 
    > - `<EventHubsClusterName>`- 이벤트 허브 클러스터의 이름    
    > - `<EventHubsNamespaceName>`- 이벤트 허브 네임스페이스 이름
    > - `<Location>`- 이벤트 허브 네임스페이스 위치

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          }
       }
    }
    
    ```
3. 다음 PowerShell 명령을 실행하여 템플릿을 배포하여 이벤트 허브 네임스페이스를 만듭니다. 그런 다음 Event Hubs 네임스페이스의 ID를 검색하여 나중에 사용하십시오. 명령을 `{MyRG}` 실행하기 전에 리소스 그룹의 이름으로 바꿉니다.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateEventHubClusterAndNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateEventHubClusterAndNamespace.json -TemplateParameterFile ./CreateEventHubClusterAndNamespaceParams.json

    $EventHubNamespaceId = $outputs.Outputs["eventHubNamespaceId"].value
    ```
 
### <a name="grant-event-hubs-namespace-identity-access-to-key-vault"></a>키 자격 증명 모음에 대한 권한 부여 이벤트 허브 네임스페이스 ID 액세스

1. 다음 명령을 실행하여 **퍼지 보호** 및 소프트 삭제를 사용하도록 설정한 키 자격 증명 **모음을** 만듭니다. 

    ```powershell
    New-AzureRmKeyVault -Name {keyVaultName} -ResourceGroupName {RGName}  -Location {location} -EnableSoftDelete -EnablePurgeProtection    
    ```     
    
    또는    
    
    다음 명령을 실행하여 **기존 키 자격 증명 모음을**업데이트합니다. 명령을 실행하기 전에 리소스 그룹 및 키 자격 증명 모음 이름에 대한 값을 지정합니다. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. 이벤트 허브 네임스페이스의 관리되는 ID가 키 자격 증명 모음의 키 값에 액세스할 수 있도록 키 자격 증명 모음 액세스 정책을 설정합니다. 이전 섹션의 이벤트 허브 네임스페이스의 ID를 사용합니다. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $EventHubNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-event-hubs-namespace-with-customer-managed-key-from-key-vault"></a>키 자격 증명 모음에서 고객 관리 키로 이벤트 허브 네임스페이스의 데이터 암호화
지금까지 다음 단계를 수행했습니다. 

1. 관리되는 ID를 가진 프리미엄 네임스페이스를 만들었습니다.
2. 키 자격 증명 모음을 만들고 키 자격 증명 모음에 대한 관리되는 ID 액세스 권한을 부여합니다. 

이 단계에서는 키 자격 증명 모음 정보로 이벤트 허브 네임스페이스를 업데이트합니다. 

1. 다음 내용을 사용하여 **CreateEventHubClusterAndNamespace.json이라는** JSON 파일을 만듭니다. 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
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
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]",
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

2. 템플릿 매개 변수 파일 만들기: **업데이트EventHubClusterAndNamespaceParams.json**. 

    > [!NOTE]
    > 다음 값을 바꿉니다. 
    > - `<EventHubsClusterName>`- 이벤트 허브 클러스터의 이름입니다.        
    > - `<EventHubsNamespaceName>`- 이벤트 허브 네임스페이스 이름
    > - `<Location>`- 이벤트 허브 네임스페이스 위치
    > - `<KeyVaultName>`- 키 금고의 이름
    > - `<KeyName>`- 키 금고의 키 이름

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
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
    New-AzResourceGroupDeployment -Name UpdateEventHubNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateEventHubClusterAndNamespace.json -TemplateParameterFile ./UpdateEventHubClusterAndNamespaceParams.json 
    ```

## <a name="troubleshoot"></a>문제 해결
가장 좋은 방법은 항상 이전 섹션과 같은 로그를 사용하도록 설정합니다. BYOK 암호화가 활성화된 경우 활동을 추적하는 데 도움이 됩니다. 또한 문제를 해결하는 데 도움이됩니다.

다음은 BYOK 암호화가 활성화된 시기를 찾는 일반적인 오류 코드입니다.

| 작업 | 오류 코드 | 결과 데이터 상태 |
| ------ | ---------- | ----------------------- | 
| 키 자격 증명 모음에서 랩/래핑 해제 권한 제거 | 403 |    액세스할 수 없음 |
| 랩/래핑 해제 권한을 부여한 AAD 보안 주체에서 AAD 역할 멤버 자격 제거 | 403 |  액세스할 수 없음 |
| 키 자격 증명 모음에서 암호화 키 삭제 | 404 | 액세스할 수 없음 |
| 키 자격 증명 모음 삭제 | 404 | 액세스할 수 없음(필요한 설정인 소프트 삭제가 활성화되어 있다고 가정합니다.) |
| 암호화 키의 만료 기간이 이미 만료되도록 변경 | 403 |   액세스할 수 없음  |
| 키 암호화 키가 활성화되지 않도록 NBF(이전이 아님)를 변경 | 403 | 액세스할 수 없음  |
| 키 볼트 방화벽에 대한 **MSFT 서비스 허용** 옵션을 선택하거나 암호화 키가 있는 키 자격 증명 모음에 대한 네트워크 액세스를 차단합니다. | 403 | 액세스할 수 없음 |
| 키 자격 증명 모음을 다른 테넌트로 이동 | 404 | 액세스할 수 없음 |  
| 간헐적인 네트워크 문제 또는 DNS/AAD/MSI 중단 |  | 캐시된 데이터 암호화 키를 사용하여 액세스 가능 |

> [!IMPORTANT]
> BYOK 암호화를 사용하는 네임스페이스에서 Geo-DR을 사용하려면 페어링을 위한 보조 네임스페이스가 전용 클러스터에 있어야 하며 시스템에서 관리되는 ID가 활성화되어 있어야 합니다. 자세한 내용은 [Azure 리소스에 대한 관리되는 ID](../active-directory/managed-identities-azure-resources/overview.md)를 참조하십시오.

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.
- [Event Hubs 개요](event-hubs-about.md)
- [키 볼트 개요](../key-vault/key-vault-overview.md)




