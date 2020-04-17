---
title: 데이터 암호화 - Azure CLI - MySQL용 Azure 데이터베이스
description: Azure CLI를 사용하여 MySQL용 Azure 데이터베이스에 대한 데이터 암호화를 설정하고 관리하는 방법을 알아봅니다.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 37f6f0dc9c1221207273110252bff445d2e1245b
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459103"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-cli"></a>Azure CLI를 사용하여 MySQL용 Azure 데이터베이스에 대한 데이터 암호화

Azure CLI를 사용하여 MySQL용 Azure 데이터베이스에 대한 데이터 암호화를 설정하고 관리하는 방법을 알아봅니다.

## <a name="prerequisites-for-azure-cli"></a>Azure CLI에 대한 필수 구성 조건

* Azure 구독 및 해당 구독에 대한 관리자 권한이 있어야 합니다.
* 고객 관리 키에 사용할 키 자격 증명 모음과 키를 만듭니다. 또한 키 자격 증명 모음에서 제거 보호 및 소프트 삭제를 활성화합니다.

    ```azurecli-interactive
    az keyvault create -g <resource_group> -n <vault_name> --enable-soft-delete true --enable-purge-protection true
    ```

* 생성된 Azure 키 볼트에서 MySQL용 Azure 데이터베이스의 데이터 암호화에 사용할 키를 만듭니다.

    ```azurecli-interactive
    az keyvault key create --name <key_name> -p software --vault-name <vault_name>
    ```

* 기존 키 자격 증명 모음을 사용하려면 고객 관리 키로 사용할 다음 속성이 있어야 합니다.
  * [소프트 삭제](../key-vault/general/overview-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [보호된 제거](../key-vault/general/overview-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* 고객 관리 키로 사용할 키에는 다음 특성이 있어야 합니다.
  * 만료 날짜 없음
  * 사용 안 함 없음
  * **get**, **wrap,** **래핑 해제** 작업 수행

## <a name="set-the-right-permissions-for-key-operations"></a>주요 작업에 적합한 사용 권한 설정

1. MySQL용 Azure 데이터베이스에 대해 관리되는 ID를 가져오는 방법에는 두 가지가 있습니다.

    ### <a name="create-an-new-azure-database-for-mysql-server-with-a-managed-identity"></a>관리되는 ID를 사용하여 MySQL 서버에 대한 새 Azure 데이터베이스를 만듭니다.

    ```azurecli-interactive
    az mysql server create --name -g <resource_group> --location <locations> --storage-size <size>  -u <user>-p <pwd> --backup-retention <7> --sku-name <sku name> --geo-redundant-backup <Enabled/Disabled>  --assign-identity
    ```

    ### <a name="update-an-existing-the-azure-database-for-mysql-server-to-get-a-managed-identity"></a>MySQL 서버에 대한 기존 Azure 데이터베이스를 업데이트하여 관리되는 ID를 가져옵니다.

    ```azurecli-interactive
    az mysql server update --name  <server name>  -g <resource_group> --assign-identity
    ```

2. MySQL 서버의 이름인 **보안 주체에**대한 **키 사용 권한(받기** , 줄**Get** **바꿈,** **줄 바꿈 해제)을**설정합니다.

    ```azurecli-interactive
    az keyvault set-policy --name -g <resource_group> --key-permissions get unwrapKey wrapKey --object-id <principal id of the server>
    ```

## <a name="set-data-encryption-for-azure-database-for-mysql"></a>MySQL용 Azure 데이터베이스에 대한 데이터 암호화 설정

1. Azure 키 자격 증명 모음에서 만든 키를 사용하여 MySQL용 Azure 데이터베이스에 대한 데이터 암호화를 사용하도록 설정합니다.

    ```azurecli-interactive
    az mysql server key create –name  <server name>  -g <resource_group> --kid <key url>
    ```

    주요 URL:https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>복원 또는 복제 서버에 데이터 암호화 사용

MySQL용 Azure 데이터베이스가 키 볼트에 저장된 고객의 관리 키로 암호화된 후 새로 만든 서버 복사본도 암호화됩니다. 로컬 또는 지역 복원 작업을 통해 또는 복제본(로컬/지역 간) 작업을 통해 이 새 복사본을 만들 수 있습니다. 따라서 암호화된 MySQL 서버의 경우 다음 단계를 사용하여 암호화된 복원된 서버를 만들 수 있습니다.

### <a name="creating-a-restoredreplica-server"></a>복원/복제서버 만들기

  *  [복원 서버 만들기](howto-restore-server-cli.md) 
  *  [읽기 복제본 서버 만들기](howto-read-replicas-cli.md) 

### <a name="once-the-server-is-restored-revalidate-data-encryption-the-restored-server"></a>서버가 복원되면 복원된 서버의 데이터 암호화 유효성을 다시 검사합니다.

    ```azurecli-interactive
    az mysql server key create –name  <server name> -g <resource_group> --kid <key url>
    ```

## <a name="additional-capability-for-the-key-being-used-for-the-azure-database-for-mysql"></a>MySQL용 Azure 데이터베이스에 사용되는 키에 대한 추가 기능

### <a name="get-the-key-used"></a>키 사용

    ```azurecli-interactive
    az mysql server key show --name  <server name>  -g <resource_group> --kid <key url>
    ```

    Key url:  https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>

### <a name="list-the-key-used"></a>사용된 키 나열

    ```azurecli-interactive
    az mysql server key list --name  <server name>  -g <resource_group>
    ```

### <a name="drop-the-key-being-used"></a>사용 중인 키 삭제

    ```azurecli-interactive
    az mysql server key delete -g <resource_group> --kid <key url> 
    ```

## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>Azure 리소스 관리자 템플릿을 사용하여 데이터 암호화 사용

Azure 포털 외에도 새 서버및 기존 서버에 대한 Azure 리소스 관리자 템플릿을 사용하여 MySQL 서버용 Azure 데이터베이스에서 데이터 암호화를 활성화할 수도 있습니다.

### <a name="for-a-new-server"></a>새 서버의 경우

미리 만들어진 Azure Resource Manager 템플릿 중 하나를 사용하여 데이터 암호화를 사용하도록 설정한 서버프로 프로비전: [데이터 암호화 예제](https://github.com/Azure/azure-mysql/tree/master/arm-templates/ExampleWithDataEncryption)

이 Azure 리소스 관리자 템플릿은 MySQL 서버에 대한 Azure 데이터베이스를 만들고 **KeyVault** 및 **Key를** 매개 변수로 사용하여 서버에서 데이터 암호화를 활성화합니다.

### <a name="for-an-existing-server"></a>기존 서버의 경우
또한 Azure 리소스 관리자 템플릿을 사용하여 MySQL 서버에 대한 기존 Azure 데이터베이스에서 데이터 암호화를 활성화할 수 있습니다.

* 속성 개체의 속성 에서 이전에 복사한 Azure Key `Uri` Vault 키의 리소스 ID를 전달합니다.

* *2020-01-01-미리 보기를* API 버전으로 사용합니다.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string"
    },
    "serverName": {
      "type": "string"
    },
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Key vault name where the key to use is stored"
      }
    },
    "keyVaultResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "Key vault resource group name where it is stored"
      }
    },
    "keyName": {
      "type": "string",
      "metadata": {
        "description": "Key name in the key vault to use as encryption protector"
      }
    },
    "keyVersion": {
      "type": "string",
      "metadata": {
        "description": "Version of the key in the key vault to use as encryption protector"
      }
    }
  },
  "variables": {
    "serverKeyName": "[concat(parameters('keyVaultName'), '_', parameters('keyName'), '_', parameters('keyVersion'))]"
  },
  "resources": [
    {
      "type": "Microsoft.DBforMySQL/servers",
      "apiVersion": "2017-12-01",
      "kind": "",
      "location": "[parameters('location')]",
      "identity": {
        "type": "SystemAssigned"
      },
      "name": "[parameters('serverName')]",
      "properties": {
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-05-01",
      "name": "addAccessPolicy",
      "resourceGroup": "[parameters('keyVaultResourceGroupName')]",
      "dependsOn": [
        "[resourceId('Microsoft.DBforMySQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.KeyVault/vaults/accessPolicies",
              "name": "[concat(parameters('keyVaultName'), '/add')]",
              "apiVersion": "2018-02-14-preview",
              "properties": {
                "accessPolicies": [
                  {
                    "tenantId": "[subscription().tenantId]",
                    "objectId": "[reference(resourceId('Microsoft.DBforMySQL/servers/', parameters('serverName')), '2017-12-01', 'Full').identity.principalId]",
                    "permissions": {
                      "keys": [
                        "get",
                        "wrapKey",
                        "unwrapKey"
                      ]
                    }
                  }
                ]
              }
            }
          ]
        }
      }
    },
    {
      "name": "[concat(parameters('serverName'), '/', variables('serverKeyName'))]",
      "type": "Microsoft.DBforMySQL/servers/keys",
      "apiVersion": "2020-01-01-preview",
      "dependsOn": [
        "addAccessPolicy",
        "[resourceId('Microsoft.DBforMySQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "serverKeyType": "AzureKeyVault",
        "uri": "[concat(reference(resourceId(parameters('keyVaultResourceGroupName'), 'Microsoft.KeyVault/vaults/', parameters('keyVaultName')), '2018-02-14-preview', 'Full').properties.vaultUri, 'keys/', parameters('keyName'), '/', parameters('keyVersion'))]"
      }
    }
  ]
}

```

## <a name="next-steps"></a>다음 단계

 데이터 암호화에 대한 자세한 내용은 [고객 관리 키가 있는 MySQL 데이터 암호화용 Azure Database를](concepts-data-encryption-mysql.md)참조하십시오.
