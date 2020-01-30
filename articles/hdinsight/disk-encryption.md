---
title: Azure HDInsight에 대 한 고객 관리 키 디스크 암호화
description: 이 문서에서는 Azure Key Vault에서 사용자 고유의 암호화 키를 사용 하 여 Azure HDInsight 클러스터의 관리 디스크에 저장 된 데이터를 암호화 하는 방법을 설명 합니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/06/2019
ms.openlocfilehash: b452cb986e6f662aeb33c2a475f18695ebc75745
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846090"
---
# <a name="customer-managed-key-disk-encryption"></a>고객 관리형 키 디스크 암호화

Azure HDInsight는 HDInsight 클러스터 Vm에 연결 된 관리 디스크 및 리소스 디스크의 데이터에 대해 BYOK (Bring Your Own Key) 암호화 라고도 하는 고객 관리 키를 지원 합니다. 이 기능을 사용 하면 Azure Key Vault를 사용 하 여 HDInsight 클러스터에서 미사용 데이터를 보호 하는 암호화 키를 관리할 수 있습니다. 클러스터에는 암호화 키가 Microsoft에서 관리 하거나 고객이 관리할 수 있는 하나 이상의 연결 된 Azure Storage 계정이 있을 수 있지만 암호화 서비스는 다릅니다.

이 문서에서는 Azure Storage 계정에 저장 된 데이터를 처리 하지 않습니다. ASE에 대 한 자세한 내용은 [미사용 데이터에 대 한 Azure Storage 암호화](../storage/common/storage-service-encryption.md)를 참조 하세요.

HDInsight의 모든 관리 디스크는 Azure SSE(스토리지 서비스 암호화)로 보호됩니다. 기본적으로 해당 디스크의 데이터는 Microsoft 관리 키를 사용하여 암호화됩니다. HDInsight에서 고객이 관리 하는 키를 사용 하도록 설정 하는 경우 HDInsight의 암호화 키를 제공 하 여 Azure Key Vault를 통해 해당 키를 사용 하 고 관리 합니다.

고객 관리 키 암호화는 추가 비용 없이 클러스터를 만드는 동안 처리 되는 1 단계 프로세스입니다. Azure Key Vault를 통해 HDInsight를 관리 ID로 등록하고 클러스터를 만들 때 암호화 키를 추가하면 됩니다.

클러스터의 각 노드에 있는 리소스 디스크와 관리 디스크는 모두 DEK (대칭 데이터 암호화 키)를 사용 하 여 암호화 됩니다. DEK는 Key Vault의 KEK(Key Encryption Key)를 사용하여 보호됩니다. 암호화 및 암호 해독 프로세스는 전적으로 Azure HDInsight에 의해 처리됩니다.

Azure Portal 또는 Azure CLI를 사용하여 Key Vault의 키를 안전하게 회전할 수 있습니다. 키를 회전 하는 경우 HDInsight 클러스터는 몇 분 내에 새 키를 사용 하기 시작 합니다. "일시 삭제" 키 보호 기능을 사용 하 여 랜 섬 웨어 시나리오를 방지 하 고 실수로 삭제 합니다. 이 보호 기능이 없는 키 자격 증명 모음은 지원 되지 않습니다.

## <a name="get-started-with-customer-managed-keys"></a>고객 관리 키 사용 시작

고객이 관리 하는 키를 사용 하도록 설정 된 HDInsight 클러스터를 만들려면 다음 단계를 진행 합니다.

1. Azure 리소스에 대 한 관리 id 만들기
2. Azure Key Vault 및 키 설정
3. 고객 관리 키를 사용 하는 HDInsight 클러스터 만들기
4. 암호화 키 회전

## <a name="create-managed-identities-for-azure-resources"></a>Azure 리소스에 대 한 관리 id 만들기

Key Vault을 인증 하려면 [Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md), [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md), [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)또는 [Azure CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)를 사용 하 여 사용자 할당 관리 id를 만듭니다. Azure HDInsight에서 관리 id가 작동 하는 방식에 대 한 자세한 내용은 [Azure hdinsight에서 관리 되는 id](hdinsight-managed-identities.md)를 참조 하세요. Key Vault 액세스 정책에 추가할 경우를 위해 관리 ID 리소스 ID를 저장해야 합니다.

## <a name="set-up-the-key-vault-and-keys"></a>Key Vault 및 키 설정

HDInsight는 Azure Key Vault만 지원합니다. 고유한 Key Vault가 있는 경우 Azure Key Vault로 키를 가져올 수 있습니다. 키에 "일시 삭제"가 있어야 합니다. "일시 삭제" 기능은 REST, .NET/C#, PowerShell 및 Azure CLI 인터페이스를 통해 사용할 수 있습니다.

1. 새 Key Vault를 만들려면 [Azure Key Vault](../key-vault/key-vault-overview.md) 빠른 시작을 수행합니다. 기존 키를 가져오는 방법에 대한 자세한 내용은 [키, 비밀 및 인증서 정보](../key-vault/about-keys-secrets-and-certificates.md)를 참조하세요.

1. [Az keyvault update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) CLI 명령을 사용 하 여 키 자격 증명 모음에서 "일시 삭제"를 사용 하도록 설정 합니다.

    ```azurecli
    az keyvault update --name <Key Vault Name> --enable-soft-delete
    ```

1. 키를 만듭니다.

    a. 새 키를 만들려면 **설정** 아래 **키** 메뉴에서 **생성/가져오기**를 선택합니다.

    ![Azure Key Vault에서 새 키를 생성 합니다.](./media/disk-encryption/create-new-key.png "Azure Key Vault에 새 키 생성")

    b. **옵션**을 **생성**으로 설정하고 키에 이름을 지정합니다.

    ![키 이름을 생성 합니다.](./media/disk-encryption/create-key.png "키 이름 생성")

    다. 키 목록에서 만든 키를 선택합니다.

    ![키 자격 증명 모음 키 목록](./media/disk-encryption/key-vault-key-list.png)

    d. HDInsight 클러스터 암호화를 위해 고유한 키를 사용 하는 경우 키 URI를 제공 해야 합니다. **키 식별자**를 복사하고 클러스터를 만들 준비가 될 때까지 어딘가에 저장합니다.

    ![키 식별자 가져오기](./media/disk-encryption/get-key-identifier.png)

1. Key Vault 액세스 정책에 관리 ID를 추가합니다.

    a. 새 Azure Key Vault 액세스 정책을 만듭니다.

    ![새 Azure Key Vault 액세스 정책 만들기](./media/disk-encryption/add-key-vault-access-policy.png)

    b. **주체 선택** 아래에서 직접 만든 사용자가 할당한 관리 ID를 선택합니다.

    ![Azure Key Vault 액세스 정책에 대한 주체 선택 설정](./media/disk-encryption/azure-portal-add-access-policy.png)

    다. **키 권한**을 **가져오기**, **키 래핑 해제** 및 **키 래핑**으로 설정합니다.

    ![Azure Key Vault access policy1에 대 한 키 권한 설정](./media/disk-encryption/add-key-vault-access-policy-keys.png "Azure Key Vault access policy1에 대 한 키 권한 설정")

    d. **비밀 권한**을 **가져오기**, **설정** 및 **삭제**로 설정합니다.

    ![Azure Key Vault access policy2에 대 한 키 권한 설정](./media/disk-encryption/add-key-vault-access-policy-secrets.png "Azure Key Vault access policy2에 대 한 키 권한 설정")

    e. **저장**을 선택합니다.

    ![Azure Key Vault 액세스 정책 저장](./media/disk-encryption/add-key-vault-access-policy-save.png)

## <a name="create-cluster-with-customer-managed-key-disk-encryption"></a>고객이 관리 하는 키 디스크 암호화를 사용 하 여 클러스터 만들기

이제 HDInsight 클러스터를 만들 준비가 되었습니다. 클러스터를 만드는 동안에는 고객 관리 키를 새 클러스터에만 적용할 수 있습니다. 고객 관리 키 클러스터에서 암호화를 제거할 수 없으며, 고객 관리 키를 기존 클러스터에 추가할 수 없습니다.

### <a name="using-the-azure-portal"></a>Azure Portal 사용

클러스터를 만드는 동안 키 버전을 포함한 전체 키 URL을 제공합니다. `https://contoso-kv.vault.azure.net/keys/myClusterKey/46ab702136bc4b229f8b10e8c2997fa4`)을 입력합니다. 또한 클러스터에 관리 ID를 할당하고 키 URI를 제공해야 합니다.

![새 클러스터 만들기](./media/disk-encryption/create-cluster-portal.png)

### <a name="using-azure-cli"></a>Azure CLI 사용

다음 예에서는 Azure CLI를 사용 하 여 디스크 암호화를 사용 하는 새 Apache Spark 클러스터를 만드는 방법을 보여 줍니다. 자세한 내용은 [Azure CLI az hdinsight create](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) 설명서를 참조 하세요.

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

HDInsight 클러스터를 만든 후에 사용 되는 암호화 키를 변경 해야 하는 경우가 있을 수 있습니다. 이는 포털을 통해 쉽게 수행할 수 있습니다. 이 작업의 경우 클러스터는 현재 키와 원래 새 키 모두에 대 한 액세스 권한이 있어야 합니다. 그렇지 않으면 키 회전 작업이 실패 합니다.

### <a name="using-the-azure-portal"></a>Azure Portal 사용

키를 회전 하려면 새 키의 전체 url이 있어야 합니다 ( [Key Vault 및 키 설정](#set-up-the-key-vault-and-keys)의 3 단계 참조). 이 작업을 완료 한 후에는 포털에서 HDInsight 클러스터 속성 섹션으로 이동 하 고 **디스크 암호화 키 URL**에서 **키 변경** 을 클릭 합니다. 새 키 url을 입력 하 고 전송 하 여 키를 회전 합니다.

![디스크 암호화 키 회전](./media/disk-encryption/change-key.png)

### <a name="using-azure-cli"></a>Azure CLI 사용

다음 예제에서는 기존 HDInsight 클러스터에 대 한 디스크 암호화 키를 회전 하는 방법을 보여 줍니다. 자세한 내용은 [Azure CLI az hdinsight rotate-encryption-key](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-rotate-disk-encryption-key) 를 참조 하세요.

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

리소스 디스크 및 데이터/관리 디스크는 암호화 됩니다. OS 디스크는 암호화 되지 않습니다.

**클러스터가 확장 되 면 새 노드에서 고객이 관리 하는 키를 원활 하 게 지원 하나요?**

예. 클러스터는 강화하는 동안 Key Vault의 키에 대한 액세스 권한이 있어야 합니다. 동일한 키를 사용 하 여 클러스터의 관리 디스크 및 리소스 디스크를 모두 암호화 합니다.

**내 위치에서 고객 관리 키를 사용할 수 있나요?**

HDInsight 고객 관리 키는 모든 공용 클라우드와 국가별 클라우드에서 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure HDInsight의 엔터프라이즈 보안 개요](./domain-joined/hdinsight-security-overview.md)
