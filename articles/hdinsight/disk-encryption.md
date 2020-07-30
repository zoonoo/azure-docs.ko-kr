---
title: Azure HDInsight에 대 한 고객 관리 키 디스크 암호화
description: 이 문서에서는 Azure Key Vault에서 사용자 고유의 암호화 키를 사용 하 여 Azure HDInsight 클러스터의 관리 디스크에 저장 된 데이터를 암호화 하는 방법을 설명 합니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: a8bb9dc5aa6ebbd4ef7fb1b9550670a3c6298333
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87387849"
---
# <a name="customer-managed-key-disk-encryption"></a>고객 관리형 키 디스크 암호화

Azure HDInsight는 HDInsight 클러스터 가상 컴퓨터에 연결 된 관리 디스크 및 리소스 디스크의 데이터에 대해 고객이 관리 하는 키 암호화를 지원 합니다. 이 기능을 사용 하면 Azure Key Vault를 사용 하 여 HDInsight 클러스터에서 미사용 데이터를 보호 하는 암호화 키를 관리할 수 있습니다.

HDInsight의 모든 관리 디스크는 Azure SSE(스토리지 서비스 암호화)로 보호됩니다. 기본적으로 해당 디스크의 데이터는 Microsoft 관리 키를 사용하여 암호화됩니다. HDInsight에서 고객이 관리 하는 키를 사용 하도록 설정 하는 경우 HDInsight의 암호화 키를 제공 하 여 Azure Key Vault를 통해 해당 키를 사용 하 고 관리 합니다.

이 문서는 Azure Storage 계정에 저장 된 데이터를 처리 하지 않습니다. Azure Storage 암호화에 대 한 자세한 내용은 [미사용 데이터에 대 한 Azure Storage 암호화](../storage/common/storage-service-encryption.md)를 참조 하세요. 클러스터에는 암호화 키가 Microsoft에서 관리 하거나 고객이 관리할 수 있는 하나 이상의 연결 된 Azure Storage 계정이 있을 수 있지만 암호화 서비스는 다릅니다.

## <a name="introduction"></a>소개

고객 관리 키 암호화는 추가 비용 없이 클러스터를 만드는 동안 처리 되는 1 단계 프로세스입니다. Azure Key Vault를 통해 HDInsight를 관리 ID로 등록하고 클러스터를 만들 때 암호화 키를 추가하면 됩니다.

클러스터의 각 노드에 있는 리소스 디스크와 관리 디스크는 모두 DEK (대칭 데이터 암호화 키)를 사용 하 여 암호화 됩니다. DEK는 Key Vault의 KEK(Key Encryption Key)를 사용하여 보호됩니다. 암호화 및 암호 해독 프로세스는 전적으로 Azure HDInsight에 의해 처리됩니다.

키 자격 증명 모음 방화벽이 디스크 암호화 키가 저장 된 키 자격 증명 모음에서 사용 되는 경우 클러스터를 배포할 지역에 대 한 HDInsight 지역별 리소스 공급자 IP 주소를 key vault 방화벽 구성에 추가 해야 합니다. HDInsight는 신뢰할 수 있는 Azure key vault 서비스가 아니기 때문에 필요 합니다.

Azure Portal 또는 Azure CLI를 사용하여 Key Vault의 키를 안전하게 회전할 수 있습니다. 키를 회전 하는 경우 HDInsight 클러스터는 몇 분 내에 새 키를 사용 하기 시작 합니다. [일시 삭제](../key-vault/general/soft-delete-overview.md) 키 보호 기능을 사용 하도록 설정 하 여 랜 섬 웨어 시나리오를 방지 하 고 실수로 삭제 합니다. 이 보호 기능이 없는 키 자격 증명 모음은 지원 되지 않습니다.

|클러스터 유형 |OS 디스크 (관리 디스크) |데이터 디스크 (관리 디스크) |임시 데이터 디스크 (로컬 SSD) |
|---|---|---|---|
|Kafka, 가속화 된 쓰기를 사용 하는 HBase|[SSE 암호화](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#encryption)|SSE 암호화 + 선택적 CMK 암호화|선택적 CMK 암호화|
|다른 모든 클러스터 (Spark, 대화형, Hadoop, 가속 없는 쓰기 없는 HBase)|SSE 암호화|해당 없음|선택적 CMK 암호화|

## <a name="get-started-with-customer-managed-keys"></a>고객 관리 키 사용 시작

고객이 관리 하는 키를 사용 하도록 설정 된 HDInsight 클러스터를 만들려면 다음 단계를 진행 합니다.

1. Azure 리소스에 대 한 관리 id 만들기
1. Azure Key Vault 만들기
1. 키 만들기
1. 액세스 정책 만들기
1. 고객 관리 키를 사용 하는 HDInsight 클러스터 만들기
1. 암호화 키 회전

## <a name="create-managed-identities-for-azure-resources"></a>Azure 리소스에 대 한 관리 id 만들기

사용자 할당 관리 id를 만들어 Key Vault에 인증 합니다.

특정 단계에 대 한 [사용자 할당 관리 Id 만들기를](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) 참조 하세요. Azure HDInsight에서 관리 id가 작동 하는 방식에 대 한 자세한 내용은 [Azure hdinsight에서 관리 되는 id](hdinsight-managed-identities.md)를 참조 하세요. Key Vault 액세스 정책에 추가할 경우를 위해 관리 ID 리소스 ID를 저장해야 합니다.

## <a name="create-azure-key-vault"></a>Azure Key Vault 만들기

키 자격 증명 모음을 만듭니다. 특정 단계는 [Azure Key Vault 만들기](../key-vault/secrets/quick-create-portal.md) 를 참조 하세요.

HDInsight는 Azure Key Vault만 지원합니다. 고유한 Key Vault가 있는 경우 Azure Key Vault로 키를 가져올 수 있습니다. 키 자격 증명 모음에서 **일시 삭제** 를 사용 하도록 설정 해야 합니다. 기존 키를 가져오는 방법에 대한 자세한 내용은 [키, 비밀 및 인증서 정보](../key-vault/about-keys-secrets-and-certificates.md)를 참조하세요.

## <a name="create-key"></a>키 만들기

1. 새 키 자격 증명 모음에서 **설정**  >  **키**  >  **+ 생성/가져오기**로 이동 합니다.

    ![Azure Key Vault에 새 키 생성](./media/disk-encryption/create-new-key.png "Azure Key Vault에 새 키 생성")

1. 이름을 입력 하 고 **만들기**를 선택 합니다. **RSA**의 기본 **키 유형을** 유지 합니다.

    ![키 이름을 생성 합니다.](./media/disk-encryption/create-key.png "키 이름 생성")

1. **키** 페이지로 돌아가면 만든 키를 선택 합니다.

    ![키 자격 증명 모음 키 목록](./media/disk-encryption/key-vault-key-list.png)

1. 버전을 선택 하 여 **키 버전** 페이지를 엽니다. HDInsight 클러스터 암호화를 위해 고유한 키를 사용 하는 경우 키 URI를 제공 해야 합니다. **키 식별자**를 복사하고 클러스터를 만들 준비가 될 때까지 어딘가에 저장합니다.

    ![키 식별자 가져오기](./media/disk-encryption/get-key-identifier.png)

## <a name="create-access-policy"></a>액세스 정책 만들기

1. 새 키 자격 증명 모음에서 **설정**  >  **액세스 정책**  >  **+ 액세스 정책 추가**로 이동 합니다.

    ![새 Azure Key Vault 액세스 정책 만들기](./media/disk-encryption/key-vault-access-policy.png)

1. **액세스 정책 추가** 페이지에서 다음 정보를 제공 합니다.

    |속성 |설명|
    |---|---|
    |키 권한|**가져오기**, **키 래핑**및 **키 래핑**을 선택 합니다.|
    |비밀 권한|**가져오기**, **설정**및 **삭제**를 선택 합니다.|
    |보안 주체 선택|이전에 만든 사용자 할당 관리 id를 선택 합니다.|

    ![Azure Key Vault 액세스 정책에 대한 주체 선택 설정](./media/disk-encryption/azure-portal-add-access-policy.png)

1. **추가**를 선택합니다.

1. **저장**을 선택합니다.

    ![Azure Key Vault 액세스 정책 저장](./media/disk-encryption/add-key-vault-access-policy-save.png)

## <a name="create-cluster-with-customer-managed-key-disk-encryption"></a>고객이 관리 하는 키 디스크 암호화를 사용 하 여 클러스터 만들기

이제 HDInsight 클러스터를 만들 준비가 되었습니다. 클러스터를 만드는 동안에는 고객 관리 키를 새 클러스터에만 적용할 수 있습니다. 고객 관리 키 클러스터에서 암호화를 제거할 수 없으며, 고객 관리 키를 기존 클러스터에 추가할 수 없습니다.

### <a name="using-the-azure-portal"></a>Azure Portal 사용

클러스터를 만드는 동안 키 버전을 포함 하 여 전체 **키 식별자**를 제공 합니다. 예: `https://contoso-kv.vault.azure.net/keys/myClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. 또한 클러스터에 관리 ID를 할당하고 키 URI를 제공해야 합니다.

![새 클러스터 만들기](./media/disk-encryption/create-cluster-portal.png)

### <a name="using-azure-cli"></a>Azure CLI 사용

다음 예에서는 Azure CLI를 사용 하 여 디스크 암호화를 사용 하는 새 Apache Spark 클러스터를 만드는 방법을 보여 줍니다. 자세한 내용은 [Azure CLI az hdinsight create](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create)를 참조 하세요.

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \
-p "HttpPassword1234!" --workernode-data-disks-per-node 2 \
--storage-account MyStorageAccount \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--assign-identity MyMSI
```

### <a name="using-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿 사용

다음 예제에서는 Azure Resource Manager 템플릿을 사용 하 여 디스크 암호화를 사용 하도록 설정 된 새 Apache Spark 클러스터를 만드는 방법을 보여 줍니다. 자세한 내용은 [ARM 템플릿 이란?](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)을 참조 하세요.

이 예제에서는 PowerShell을 사용 하 여 템플릿을 호출 합니다.

```powershell
$templateFile = "azuredeploy.json"
$ResourceGroupName = "MyResourceGroup"
$clusterName = "MyCluster"
$password = ConvertTo-SecureString 'HttpPassword1234!' -AsPlainText -Force
$diskEncryptionVaultUri = "https://MyKeyVault.vault.azure.net"
$diskEncryptionKeyName = "SparkClusterKey"
$diskEncryptionKeyVersion = "00000000000000000000000000000000"
$managedIdentityName = "MyMSI"

New-AzResourceGroupDeployment `
  -Name mySpark `
  -TemplateFile $templateFile `
  -ResourceGroupName $ResourceGroupName `
  -clusterName $clusterName `
  -clusterLoginPassword $password `
` -sshPassword $password `
  -diskEncryptionVaultUri $diskEncryptionVaultUri `
  -diskEncryptionKeyName $diskEncryptionKeyName `
  -diskEncryptionKeyVersion $diskEncryptionKeyVersion `
  -managedIdentityName $managedIdentityName
```

리소스 관리 템플릿의 내용은 `azuredeploy.json` 다음과 같습니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "0.9.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "metadata": {
        "description": "The name of the HDInsight cluster to create."
      }
    },
    "clusterLoginUserName": {
      "type": "string",
      "defaultValue": "admin",
      "metadata": {
        "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
      }
    },
    "clusterLoginPassword": {
      "type": "securestring",
      "metadata": {
        "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "The location where all azure resources will be deployed."
      }
    },
    "sshUserName": {
      "type": "string",
      "defaultValue": "sshuser",
      "metadata": {
        "description": "These credentials can be used to remotely access the cluster."
      }
    },
    "sshPassword": {
      "type": "securestring",
      "metadata": {
        "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
      }
    },
    "headNodeSize": {
      "type": "string",
      "defaultValue": "Standard_D12_v2",
      "metadata": {
        "description": "The VM size of the head nodes."
      }
    },
    "workerNodeSize": {
      "type": "string",
      "defaultValue": "Standard_D13_v2",
      "metadata": {
        "description": "The VM size of the worker nodes."
      }
    },
    "diskEncryptionVaultUri": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault DNSname."
      }
    },
    "diskEncryptionKeyName": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault key name."
      }
    },
    "diskEncryptionKeyVersion": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault key version for the selected key."
      }
    },
    "managedIdentityName": {
      "type": "string",
      "metadata": {
        "description": "The user-assigned managed identity."
      }
    }
  },
  "variables": {
    "defaultStorageAccount": {
      "name": "[uniqueString(resourceGroup().id)]",
      "type": "Standard_LRS"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('defaultStorageAccount').name]",
      "location": "[parameters('location')]",
      "apiVersion": "2019-06-01",
      "sku": {
        "name": "[variables('defaultStorageAccount').type]"
      },
      "kind": "Storage",
      "properties": {}
    },
    {
      "apiVersion": "2018-06-01-preview",
      "name": "[parameters('clusterName')]",
      "type": "Microsoft.HDInsight/clusters",
      "location": "[parameters('location')]",
      "properties": {
        "clusterVersion": "3.6",
        "osType": "Linux",
        "tier": "standard",
        "clusterDefinition": {
          "kind": "spark",
          "componentVersion": {
            "Spark": "2.3"
          },
          "configurations": {
            "gateway": {
              "restAuthCredential.isEnabled": true,
              "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
              "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
            }
          }
        },
        "storageProfile": {
          "storageaccounts": [
            {
              "name": "[replace(replace(reference(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccount').name), '2019-06-01').primaryEndpoints.blob,'https://',''),'/','')]",
              "isDefault": true,
              "container": "[parameters('clusterName')]",
              "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccount').name), '2019-06-01').keys[0].value]"
            }
          ]
        },
        "computeProfile": {
          "roles": [
            {
              "name": "headnode",
              "minInstanceCount": 1,
              "targetInstanceCount": 2,
              "hardwareProfile": {
                "vmSize": "[parameters('headNodeSize')]"
              },
              "osProfile": {
                "linuxOperatingSystemProfile": {
                  "username": "[parameters('sshUserName')]",
                  "password": "[parameters('sshPassword')]"
                },
              },
            },
            {
              "name": "workernode",
              "targetInstanceCount": 1,
              "hardwareProfile": {
                "vmSize": "[parameters('workerNodeSize')]"
              },
              "osProfile": {
                "linuxOperatingSystemProfile": {
                  "username": "[parameters('sshUserName')]",
                  "password": "[parameters('sshPassword')]"
                },
              },
            }
          ]
        },
        "minSupportedTlsVersion": "1.2",
        "diskEncryptionProperties": {
          "vaultUri": "[parameters('diskEncryptionVaultUri')]",
          "keyName": "[parameters('diskEncryptionKeyName')]",
          "keyVersion": "[parameters('diskEncryptionKeyVersion')]",
          "msiResourceId": "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('managedIdentityName'))]"
        }
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('managedIdentityName'))]": {}
        }
      }
    }
  ]
}
```

## <a name="rotating-the-encryption-key"></a>암호화 키 회전

HDInsight 클러스터를 만든 후에 사용 되는 암호화 키를 변경 해야 하는 경우가 있을 수 있습니다. 이는 포털을 통해 쉽게 수행할 수 있습니다. 이 작업의 경우 클러스터는 현재 키와 원래 새 키 모두에 대 한 액세스 권한이 있어야 합니다. 그렇지 않으면 키 회전 작업이 실패 합니다.

### <a name="using-the-azure-portal"></a>Azure Portal 사용

키를 회전 하려면 기본 키 자격 증명 모음 URI가 필요 합니다. 이 작업을 완료 한 후에는 포털에서 HDInsight 클러스터 속성 섹션으로 이동 하 고 **디스크 암호화 키 URL**에서 **키 변경** 을 클릭 합니다. 새 키 url을 입력 하 고 전송 하 여 키를 회전 합니다.

![디스크 암호화 키 회전](./media/disk-encryption/change-key.png)

### <a name="using-azure-cli"></a>Azure CLI 사용

다음 예제에서는 기존 HDInsight 클러스터에 대 한 디스크 암호화 키를 회전 하는 방법을 보여 줍니다. 자세한 내용은 [Azure CLI az hdinsight rotate-encryption-key](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-rotate-disk-encryption-key)를 참조 하세요.

```azurecli
az hdinsight rotate-disk-encryption-key \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--name MyCluster \
--resource-group MyResourceGroup
```

## <a name="faq-for-customer-managed-key-encryption"></a>고객 관리 키 암호화에 대 한 FAQ

**HDInsight 클러스터에서 내 키 자격 증명 모음에 액세스 하는 방법**

Hdinsight는 HDInsight 클러스터와 연결 하는 관리 id를 사용 하 여 Azure Key Vault 인스턴스에 액세스 합니다. 클러스터를 만들기 전이나 만드는 동안 이 관리 ID를 만들 수 있습니다. 또한 키가 저장된 Key Vault에 대한 액세스 권한을 관리 ID에 부여해야 합니다.

**HDInsight의 모든 클러스터에서이 기능을 사용할 수 있나요?**

고객 관리 키 암호화는 Spark 2.1 및 2.2을 제외한 모든 클러스터 유형에 사용할 수 있습니다.

**여러 키를 사용 하 여 여러 디스크 또는 폴더를 암호화할 수 있나요?**

아니요, 모든 관리 디스크 및 리소스 디스크는 동일한 키로 암호화 됩니다.

**클러스터가 키 자격 증명 모음 또는 키에 대 한 액세스를 잃으면 어떻게 되나요?**

클러스터가 키에 대 한 액세스 권한을 상실 하면 Apache Ambari 포털에 경고가 표시 됩니다. 이 상태에서 **키 변경** 작업은 실패 합니다. 키 액세스가 복원 되 면 Ambari 경고가 표시 되지 않고 키 회전과 같은 작업이 성공적으로 수행 될 수 있습니다.

![키 액세스 Ambari 경고](./media/disk-encryption/ambari-alert.png)

**키가 삭제될 경우 클러스터를 어떻게 복구할 수 있나요?**

"일시 삭제" 사용 키만 지원 되므로 키 자격 증명 모음에서 키를 복구 하는 경우 클러스터에서 키에 대 한 액세스 권한을 다시 확보 해야 합니다. Azure Key Vault 키를 복구 하려면 [AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) 또는 [Az-keyvault](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-recover)를 참조 하세요.

**암호화 된 디스크 유형은 무엇입니까? OS 디스크/리소스 디스크도 암호화 되나요?**

리소스 디스크 및 데이터/관리 디스크는 암호화 됩니다. OS 디스크가 암호화 되지 않았습니다.

**클러스터가 확장 되 면 새 노드에서 고객이 관리 하는 키를 원활 하 게 지원 하나요?**

예. 클러스터는 강화하는 동안 Key Vault의 키에 대한 액세스 권한이 있어야 합니다. 동일한 키를 사용 하 여 클러스터의 관리 디스크 및 리소스 디스크를 모두 암호화 합니다.

**내 위치에서 고객 관리 키를 사용할 수 있나요?**

HDInsight 고객 관리 키는 모든 공용 클라우드와 국가별 클라우드에서 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* Azure Key Vault에 대 한 자세한 내용은 [Azure Key Vault 항목](../key-vault/general/overview.md)을 참조 하세요.
* [Azure HDInsight의 엔터프라이즈 보안 개요](./domain-joined/hdinsight-security-overview.md).
