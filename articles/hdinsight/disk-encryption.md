---
title: Azure HDInsight를 위한 고객 관리 키 디스크 암호화
description: 이 문서에서는 Azure Key Vault에서 자체 암호화 키를 사용하여 Azure HDInsight 클러스터의 관리디스크에 저장된 데이터를 암호화하는 방법을 설명합니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: c0521f384a333c3054397fb0ec7c2ab907e54f67
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411760"
---
# <a name="customer-managed-key-disk-encryption"></a>고객 관리형 키 디스크 암호화

Azure HDInsight는 HDInsight 클러스터 가상 머신에 연결된 관리 디스크 및 리소스 디스크의 데이터에 대한 고객 관리 키 암호화를 지원합니다. 이 기능을 사용하면 Azure Key Vault를 사용하여 HDInsight 클러스터에서 미사용 데이터를 보호하는 암호화 키를 관리할 수 있습니다.

HDInsight의 모든 관리 디스크는 Azure SSE(스토리지 서비스 암호화)로 보호됩니다. 기본적으로 해당 디스크의 데이터는 Microsoft 관리 키를 사용하여 암호화됩니다. HDInsight에 대해 고객 관리 키를 사용하도록 설정하는 경우 Azure Key Vault를 사용하여 해당 키를 사용하고 관리할 수 있는 HDInsight의 암호화 키를 제공합니다.

이 문서에서는 Azure Storage 계정에 저장된 데이터를 다루지 않습니다. Azure 저장소 암호화에 대한 자세한 내용은 [미사용 데이터에 대한 Azure 저장소 암호화를](../storage/common/storage-service-encryption.md)참조하십시오. 클러스터에 암호화 키가 Microsoft 에서 관리하거나 고객이 관리할 수도 있지만 암호화 서비스가 다를 수 있는 하나 이상의 연결된 Azure Storage 계정이 있을 수 있습니다.

## <a name="introduction"></a>소개

고객 관리 키 암호화는 추가 비용 없이 클러스터 를 만드는 동안 처리되는 한 단계 프로세스입니다. Azure Key Vault를 통해 HDInsight를 관리 ID로 등록하고 클러스터를 만들 때 암호화 키를 추가하면 됩니다.

클러스터의 각 노드에 있는 리소스 디스크와 관리되는 디스크는 모두 DEK(대칭 데이터 암호화 키)로 암호화됩니다. DEK는 Key Vault의 KEK(Key Encryption Key)를 사용하여 보호됩니다. 암호화 및 암호 해독 프로세스는 전적으로 Azure HDInsight에 의해 처리됩니다.

디스크 암호화 키가 저장된 키 자격 증명 모음에서 키 볼트 방화벽을 사용하도록 설정하면 클러스터가 배포될 지역의 HDInsight 지역 리소스 공급자 IP 주소를 키 볼트 방화벽 구성에 추가해야 합니다. HDInsight는 신뢰할 수 있는 Azure 키 자격 증명 모음 서비스가 아니기 때문에 이 필요합니다.

Azure Portal 또는 Azure CLI를 사용하여 Key Vault의 키를 안전하게 회전할 수 있습니다. 키가 회전하면 HDInsight 클러스터가 몇 분 안에 새 키를 사용하기 시작합니다. 소프트 [삭제](../key-vault/key-vault-ovw-soft-delete.md) 키 보호 기능을 활성화하여 랜섬웨어 시나리오 및 우발적인 삭제로부터 보호합니다. 이 보호 기능이 없는 키 자격 증명 모음은 지원되지 않습니다.

|클러스터 유형 |OS 디스크(관리디스크) |데이터 디스크(관리디스크) |임시 데이터 디스크(로컬 SSD) |
|---|---|---|---|
|카프카, 가속 쓰기와 HBase|[SSE 암호화](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#encryption)|SSE 암호화 + 선택적 CMK 암호화|CMK 암호화 옵션|
|다른 모든 클러스터(스파크, 인터랙티브, 하두롭, 가속 쓰기 없이 HBase)|SSE 암호화|해당 없음|CMK 암호화 옵션|

## <a name="get-started-with-customer-managed-keys"></a>고객 관리 키 사용 시작

고객 관리 키 지원 HDInsight 클러스터를 만들려면 다음 단계를 살펴보겠습니다.

1. Azure 리소스에 대해 관리되는 ID 만들기
1. Azure Key Vault 만들기
1. 키 만들기
1. 액세스 정책 만들기
1. 고객 관리 키를 사용하도록 설정한 HDInsight 클러스터 만들기
1. 암호화 키 회전

## <a name="create-managed-identities-for-azure-resources"></a>Azure 리소스에 대해 관리되는 ID 만들기

키 볼트에 인증할 사용자 할당된 관리되는 ID를 만듭니다.

특정 단계에 대해 [사용자가 할당한 관리되는 ID 만들기를](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) 참조하십시오. Azure HDInsight에서 관리되는 ID가 작동하는 방식에 대한 자세한 내용은 [Azure HDInsight의 관리ID](hdinsight-managed-identities.md)를 참조하십시오. Key Vault 액세스 정책에 추가할 경우를 위해 관리 ID 리소스 ID를 저장해야 합니다.

## <a name="create-azure-key-vault"></a>Azure Key Vault 만들기

키 자격 증명 모음을 만듭니다. 특정 단계에 대한 [Azure 키 자격 증명 모음 만들기를](../key-vault/quick-create-portal.md) 참조하십시오.

HDInsight는 Azure Key Vault만 지원합니다. 고유한 Key Vault가 있는 경우 Azure Key Vault로 키를 가져올 수 있습니다. 키 자격 증명 모음에는 **소프트 삭제가** 활성화되어 있어야 합니다. 기존 키를 가져오는 방법에 대한 자세한 내용은 [키, 비밀 및 인증서 정보](../key-vault/about-keys-secrets-and-certificates.md)를 참조하세요.

## <a name="create-key"></a>키 만들기

1. 새 키 자격 증명 모음에서 **설정** > **키** > **+ 생성/가져오기**로 이동합니다.

    ![Azure Key Vault에 새 키 생성](./media/disk-encryption/create-new-key.png "Azure Key Vault에 새 키 생성")

1. 이름을 제공한 다음 **을 선택합니다.** **RSA의**기본 **키 유형을** 유지 합니다.

    ![키 이름을 생성합니다.](./media/disk-encryption/create-key.png "키 이름 생성")

1. **키** 페이지로 돌아가면 만든 키를 선택합니다.

    ![키 볼트 키 목록](./media/disk-encryption/key-vault-key-list.png)

1. 키 버전 페이지를 열려면 버전을 **선택합니다.** HDInsight 클러스터 암호화에 고유한 키를 사용하는 경우 키 URI를 제공해야 합니다. **키 식별자**를 복사하고 클러스터를 만들 준비가 될 때까지 어딘가에 저장합니다.

    ![키 식별자 받기](./media/disk-encryption/get-key-identifier.png)

## <a name="create-access-policy"></a>액세스 정책 만들기

1. 새 키 자격 증명 모음에서 **설정** > **액세스 정책** > **+ 액세스 정책 추가로**이동합니다.

    ![새 Azure Key Vault 액세스 정책 만들기](./media/disk-encryption/key-vault-access-policy.png)

1. 액세스 **추가 정책** 페이지에서 다음 정보를 제공합니다.

    |속성 |Description|
    |---|---|
    |주요 권한|**[받기]** 키 **둘러싸기**및 **둘러싸기 키를**선택합니다.|
    |비밀 권한|**[ 받기**] **및** **삭제를**선택한다.|
    |보안 주체 선택|이전에 만든 사용자 할당관리 ID를 선택합니다.|

    ![Azure Key Vault 액세스 정책에 대한 주체 선택 설정](./media/disk-encryption/azure-portal-add-access-policy.png)

1. **추가**를 선택합니다.

1. **저장**을 선택합니다.

    ![Azure 키 볼트 액세스 정책 저장](./media/disk-encryption/add-key-vault-access-policy-save.png)

## <a name="create-cluster-with-customer-managed-key-disk-encryption"></a>고객이 관리하는 키 디스크 암호화를 사용하여 클러스터 만들기

이제 HDInsight 클러스터를 만들 준비가 되었습니다. 고객 관리 키는 클러스터 를 만드는 동안 새 클러스터에만 적용할 수 있습니다. 고객이 관리하는 키 클러스터에서 암호화를 제거할 수 없으며 고객 관리 키를 기존 클러스터에 추가할 수 없습니다.

### <a name="using-the-azure-portal"></a>Azure Portal 사용

클러스터를 만드는 동안 키 버전을 포함하여 전체 **Key 식별자를**제공합니다. `https://contoso-kv.vault.azure.net/keys/myClusterKey/46ab702136bc4b229f8b10e8c2997fa4`)을 입력합니다. 또한 클러스터에 관리 ID를 할당하고 키 URI를 제공해야 합니다.

![새 클러스터 만들기](./media/disk-encryption/create-cluster-portal.png)

### <a name="using-azure-cli"></a>Azure CLI 사용

다음 예제에서는 Azure CLI를 사용하여 디스크 암호화가 활성화된 새 Apache Spark 클러스터를 만드는 방법을 보여 주습니다. 자세한 내용은 [Azure CLI az hdinsight create를](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create)참조하십시오.

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \
-p "HttpPassword1234!" --workernode-data-disks-per-node 2 \
--storage-account MyStorageAccount \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--assign-identity MyMSI
```

## <a name="rotating-the-encryption-key"></a>암호화 키 회전

HDInsight 클러스터를 만든 후 HDInsight 클러스터에서 사용하는 암호화 키를 변경하려는 시나리오가 있을 수 있습니다. 포털을 통해 쉽게 사용할 수 있습니다. 이 작업의 경우 클러스터는 현재 키와 의도한 새 키모두에 액세스할 수 있어야 하며, 그렇지 않으면 회전 키 작업이 실패합니다.

### <a name="using-the-azure-portal"></a>Azure Portal 사용

키를 회전하려면 기본 키 자격 증명 모음 URI가 필요합니다. 이 작업을 완료하면 포털의 HDInsight 클러스터 속성 섹션으로 이동하여 **디스크 암호화 키 URL**아래의 키 **변경을** 클릭합니다. 새 키 URL을 입력하고 제출하여 키를 회전시다.

![디스크 암호화 키 회전](./media/disk-encryption/change-key.png)

### <a name="using-azure-cli"></a>Azure CLI 사용

다음 예제에서는 기존 HDInsight 클러스터에 대한 디스크 암호화 키를 회전하는 방법을 보여 주십습니다. 자세한 내용은 [Azure CLI az hdinsight 회전 디스크 암호화 키를](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-rotate-disk-encryption-key)참조하십시오.

```azurecli
az hdinsight rotate-disk-encryption-key \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--name MyCluster \
--resource-group MyResourceGroup
```

## <a name="azure-resource-manager-templates"></a>Azure 리소스 관리자 템플릿

리소스 관리자 템플릿을 사용하여 고객 관리 키를 사용하려면 다음 변경 내용으로 템플릿을 업데이트합니다.

1. **azuredeploy.json** 파일에서 리소스에 다음 속성을 추가합니다" 개체:

    ```json
       "diskEncryptionProperties":
         {
                 "vaultUri": "[parameters('diskEncryptionVaultUri')]",
                  "keyName": "[parameters('diskEncryptionKeyName')]",
                  "keyVersion": "[parameters('diskEncryptionKeyVersion')]",
                   "msiResourceId": "[parameters('diskEncryptionMsiResourceId')]"
         }

1. In the **azuredeploy.parameters.json** file, add the following parameters. You can get the values of these parameters from the Key Vault URI and the managed Identity. For example, if you have the following URI and identity values,
    * Sample key vault URI: https://<KeyVault_Name>.vault.azure.net/keys/clusterkey/<Cluster_Key_Value>
    * Sample user-assigned managed identity: "/subscriptions/<subscriptionID>/resourcegroups/<ResourceGroup_Name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI_Name>

    The parameters in the **azuredeploy.parameters.json** file are:

    ```json
   "diskEncryptionVaultUri": {
            "value": "https://<KeyVault_Name>.vault.azure.net"
        },
        "diskEncryptionKeyName": {
            "value": "clusterkey"
        },
        "diskEncryptionKeyVersion": {
            "value": "<Cluster_Key_Value>"
        },
        "diskEncryptionMsiResourceId": {
            "value": "/subscriptions/<subscriptionID>/resourcegroups/<ResourceGroup_Name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI_Name>"
        }
    ```

## <a name="faq-for-customer-managed-key-encryption"></a>고객 관리 키 암호화를 위한 FAQ

**HDInsight 클러스터는 내 키 자격 증명 모음에 어떻게 액세스합니까?**

HDInsight는 HDInsight 클러스터와 연결하는 관리되는 ID를 사용하여 Azure 키 볼트 인스턴스에 액세스합니다. 클러스터를 만들기 전이나 만드는 동안 이 관리 ID를 만들 수 있습니다. 또한 키가 저장된 Key Vault에 대한 액세스 권한을 관리 ID에 부여해야 합니다.

**이 기능은 HDInsight의 모든 클러스터에서 사용할 수 있습니까?**

Spark 2.1 및 2.2를 제외한 모든 클러스터 유형에 대해 고객 관리 키 암호화를 사용할 수 있습니다.

**여러 키를 사용하여 다른 디스크 또는 폴더를 암호화할 수 있습니까?**

아니요, 모든 관리되는 디스크와 리소스 디스크는 동일한 키로 암호화됩니다.

**클러스터가 키 자격 증명 모음 또는 키에 액세스할 수 없게 되면 어떻게 됩니까?**

클러스터가 키에 액세스할 수 없게 되면 아파치 암바리 포털에 경고가 표시됩니다. 이 **상태에서는 키 변경** 작업이 실패합니다. 키 액세스가 복원되면 Ambari 경고가 사라지고 키 회전과 같은 작업을 성공적으로 수행할 수 있습니다.

![키 액세스 암바리 경고](./media/disk-encryption/ambari-alert.png)

**키가 삭제될 경우 클러스터를 어떻게 복구할 수 있나요?**

"소프트 삭제" 활성화 된 키만 지원 되므로 키 자격 증명 모음에서 키를 복구 하는 경우 클러스터 키에 대 한 액세스 권한을 다시 해야 합니다. Azure 키 볼트 키를 복구하려면 [Undo-AzKeyVaultKey제거](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) 또는 [az 키볼트 키 복구](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-recover)를 참조하십시오.

**어떤 디스크 유형이 암호화되나요? OS 디스크/리소스 디스크도 암호화되어 있습니까?**

리소스 디스크와 데이터/관리 디스크는 암호화됩니다. OS 디스크는 암호화되지 않습니다.

**클러스터를 확장하면 새 노드가 고객 관리 키를 원활하게 지원합니까?**

예. 클러스터는 강화하는 동안 Key Vault의 키에 대한 액세스 권한이 있어야 합니다. 동일한 키가 클러스터의 관리디스크와 리소스 디스크를 암호화하는 데 사용됩니다.

**고객 관리 키가 내 위치에서 사용할 수 있습니까?**

HDInsight 고객 관리 키는 모든 퍼블릭 클라우드 및 국가 클라우드에서 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* Azure 키 자격 증명 모음에 대한 자세한 내용은 [Azure 키 자격 증명 모음입니다.](../key-vault/key-vault-overview.md)
* [Azure HDInsight의 엔터프라이즈 보안 개요](./domain-joined/hdinsight-security-overview.md).
