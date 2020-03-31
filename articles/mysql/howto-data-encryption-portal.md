---
title: 데이터 암호화 - Azure 포털 - MySQL용 Azure 데이터베이스
description: Azure 포털을 사용하여 MySQL용 Azure 데이터베이스에 대한 데이터 암호화를 설정하고 관리하는 방법을 알아봅니다.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 78a290b1e2984719645fb4d4ff253ab021a0826e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299042"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-portal"></a>Azure 포털을 사용 하 여 MySQL에 대 한 Azure 데이터베이스에 대 한 데이터 암호화

Azure 포털을 사용하여 MySQL용 Azure 데이터베이스에 대한 데이터 암호화를 설정하고 관리하는 방법을 알아봅니다.

## <a name="prerequisites-for-azure-cli"></a>Azure CLI에 대한 필수 구성 조건

* Azure 구독 및 해당 구독에 대한 관리자 권한이 있어야 합니다.
* Azure 키 자격 증명 모음에서 고객 관리 키에 사용할 키 자격 증명 모음및 키를 만듭니다.
* 키 자격 증명 모음에는 고객 관리 키로 사용할 다음 속성이 있어야 합니다.
  * [소프트 삭제](../key-vault/key-vault-ovw-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [보호된 제거](../key-vault/key-vault-ovw-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* 고객 관리 키로 사용할 키에는 다음 특성이 있어야 합니다.
  * 만료 날짜 없음
  * 사용 안 함 없음
  * 가져오기, 키 래핑, 키 래핑 해제 작업도 수행 가능

## <a name="set-the-right-permissions-for-key-operations"></a>주요 작업에 적합한 사용 권한 설정

1. 키 자격 증명 모음에서 **액세스 정책** > **추가 정책입니다.**

   ![키 볼트의 스크린샷, 액세스 정책 및 액세스 추가 정책이 강조 표시되어 있습니다.](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. **키 사용 권한을**선택하고 **[[] []**[] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [ **Wrap** **Unwrap** **Principal** 서버 주체를 기존 보안 주체 목록에서 찾을 수 없는 경우 서버 주체를 등록해야 합니다. 데이터 암호화를 처음 설정하려고 할 때 서버 주체를 등록하라는 메시지가 표시되고 실패합니다.

   ![액세스 정책 개요](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. **저장**을 선택합니다.

## <a name="set-data-encryption-for-azure-database-for-mysql"></a>MySQL용 Azure 데이터베이스에 대한 데이터 암호화 설정

1. MySQL용 Azure 데이터베이스에서 **데이터 암호화를** 선택하여 고객 관리 키를 설정합니다.

   ![데이터 암호화가 강조 표시된 MySQL용 Azure 데이터베이스의 스크린샷](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. 키 자격 증명 모음 및 키 쌍을 선택하거나 키 식별자를 입력할 수 있습니다.

   ![데이터 암호화 옵션이 강조 표시된 MySQL용 Azure 데이터베이스의 스크린샷](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. **저장**을 선택합니다.

4. 임시 파일(임시 파일 포함)이 완전히 암호화되도록 하려면 서버를 다시 시작합니다.

## <a name="restore-or-create-a-replica-of-the-server"></a>서버의 복제본 복원 또는 만들기

MySQL용 Azure 데이터베이스가 키 볼트에 저장된 고객의 관리 키로 암호화된 후 새로 만든 서버 복사본도 암호화됩니다. 로컬 또는 지역 복원 작업을 통해 또는 복제본(로컬/지역 간) 작업을 통해 이 새 복사본을 만들 수 있습니다. 따라서 암호화된 MySQL 서버의 경우 다음 단계를 사용하여 암호화된 복원된 서버를 만들 수 있습니다.

1. 서버에서 > 복원 **개요를**선택합니다.**Restore**

   ![개요 및 복원강조 표시된 MySQL용 Azure 데이터베이스 스크린샷](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   또는 **설정** 제목 아래에서 복제 지원 서버의 경우 **복제**를 선택합니다.

   ![복제가 강조 표시된 MySQL용 Azure 데이터베이스의 스크린샷](media/concepts-data-access-and-security-data-encryption/mysql-replica.png)

2. 복원 작업이 완료되면 생성된 새 서버가 기본 서버의 키로 암호화됩니다. 그러나 서버의 기능 및 옵션은 비활성화되어 서버에 액세스할 수 없습니다. 이렇게 하면 새 서버의 ID가 키 자격 증명 모음에 액세스할 수 있는 권한이 아직 부여되지 않았기 때문에 데이터 조작을 방지할 수 있습니다.

   ![액세스할 수 없는 상태가 강조 표시된 MySQL용 Azure 데이터베이스의 스크린샷](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. 서버에 액세스할 수 있도록 하려면 복원된 서버의 키를 다시 유효성을 검사합니다. **데이터 암호화** > **유효성 을 다시 유효성 을 다시 확인합니다.**

   > [!NOTE]
   > 새 서버의 서비스 주체에 키 자격 증명 모음에 대한 액세스 권한을 부여해야 하므로 유효성 을 다시 검사하는 첫 번째 시도가 실패합니다. 서비스 주체를 생성하려면 다시 **유효성 검사 키를**선택하여 오류를 표시하지만 서비스 주체를 생성합니다. 그런 다음 이 문서의 앞에서 [다음 단계를](#set-the-right-permissions-for-key-operations) 참조하십시오.

   ![재검증 단계가 강조 표시된 MySQL용 Azure 데이터베이스의 스크린샷](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   새 서버에 대한 키 자격 증명 모음 액세스 권한을 부여해야 합니다.

4. 서비스 주체를 등록한 후 키를 다시 유효성을 다시 검사하고 서버가 정상적인 기능을 다시 시작합니다.

   ![복원된 기능을 보여주는 MySQL용 Azure 데이터베이스의 스크린샷](media/concepts-data-access-and-security-data-encryption/restore-successful.png)


## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>Azure 리소스 관리자 템플릿을 사용하여 데이터 암호화 사용

Azure 포털 외에도 새 서버및 기존 서버에 대한 Azure 리소스 관리자 템플릿을 사용하여 MySQL 서버용 Azure 데이터베이스에서 데이터 암호화를 활성화할 수도 있습니다.

### <a name="for-a-new-server"></a>새 서버의 경우

미리 만들어진 Azure Resource Manager 템플릿 중 하나를 사용하여 데이터 암호화를 사용하도록 설정한 서버프로 프로비전: [데이터 암호화 예제](https://github.com/Azure/azure-mysql/tree/master/arm-templates/ExampleWithDataEncryption)

이 Azure 리소스 관리자 템플릿은 MySQL 서버에 대한 Azure 데이터베이스를 만들고 **KeyVault** 및 **Key를** 매개 변수로 사용하여 서버에서 데이터 암호화를 활성화합니다.

### <a name="for-an-existing-server"></a>기존 서버의 경우
또한 Azure 리소스 관리자 템플릿을 사용하여 MySQL 서버에 대한 기존 Azure 데이터베이스에서 데이터 암호화를 활성화할 수 있습니다.

* 속성 개체의 속성 에서 이전에 복사한 Azure `keyVaultKeyUri` Key Vault 키의 URI를 전달합니다.

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
