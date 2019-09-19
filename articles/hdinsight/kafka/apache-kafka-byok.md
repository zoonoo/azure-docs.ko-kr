---
title: Azure HDInsight에서 Apache Kafka에 대 한 고유한 키 가져오기
description: 이 문서에서는 Azure Key Vault에서 사용자 고유의 키를 사용하여 Azure HDInsight의 Apache Kafka에 저장된 데이터를 암호화하는 방법을 설명합니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: ba49944011546db45d25cc87c2c4b93c8b99502a
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122676"
---
# <a name="bring-your-own-key-for-apache-kafka-on-azure-hdinsight"></a>Azure HDInsight에서 Apache Kafka에 대 한 고유한 키 가져오기

Azure HDInsight에는 Apache Kafka에 대한 BYOK(Bring Your Own Key) 지원이 포함됩니다. 이 기능을 사용하면 미사용 데이터를 암호화하는 데 사용되는 키를 소유하고 관리할 수 있습니다.

HDInsight의 모든 관리 디스크는 Azure SSE(스토리지 서비스 암호화)로 보호됩니다. 기본적으로 해당 디스크의 데이터는 Microsoft 관리 키를 사용하여 암호화됩니다. BYOK를 사용하도록 설정한 경우 HDInsight에 대한 암호화 키를 제공하여 Azure Key Vault를 통해 사용하고 관리합니다.

BYOK 암호화는 추가 비용 없이 클러스터를 만드는 동안 처리되는 1단계 프로세스입니다. Azure Key Vault를 통해 HDInsight를 관리 ID로 등록하고 클러스터를 만들 때 암호화 키를 추가하면 됩니다.

Kafka에 대한 모든 메시지(Kafka에서 유지 관리되는 복제본 포함)는 대칭 DEK(데이터 암호화 키)로 암호화됩니다. DEK는 Key Vault의 KEK(Key Encryption Key)를 사용하여 보호됩니다. 암호화 및 암호 해독 프로세스는 전적으로 Azure HDInsight에 의해 처리됩니다.

Azure Portal 또는 Azure CLI를 사용하여 Key Vault의 키를 안전하게 회전할 수 있습니다. 키가 회전될 때 HDInsight Kafka 클러스터는 몇 분 안에 새 키를 사용하기 시작합니다. "일시 삭제" 키 보호 기능을 사용 하 여 랜 섬 웨어 시나리오를 방지 하 고 실수로 삭제 합니다. 이 보호 기능이 없는 키 자격 증명 모음은 지원 되지 않습니다.

## <a name="get-started-with-byok"></a>BYOK 시작
BYOK를 사용 하도록 설정 된 Kafka 클러스터를 만들려면 다음 단계를 진행 합니다.
1. Azure 리소스에 대 한 관리 id 만들기
2. Azure Key Vault 및 키 설정
3. BYOK를 사용 하 여 HDInsight Kafka 클러스터 만들기
4. 암호화 키 회전

## <a name="create-managed-identities-for-azure-resources"></a>Azure 리소스에 대 한 관리 id 만들기

   Key Vault을 인증 하려면 [Azure Portal](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md), [Azure PowerShell](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md), [Azure Resource Manager](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)또는 [Azure CLI](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)를 사용 하 여 사용자 할당 관리 id를 만듭니다. Azure HDInsight에서 관리 id가 작동 하는 방식에 대 한 자세한 내용은 [Azure hdinsight에서 관리 되는 id](../hdinsight-managed-identities.md)를 참조 하세요. Kafka에 대한 관리 ID 및 BYOK에는 Azure Active Directory가 필요하지만 ESP(Encapsulating Security Payload)는 필요하지 않습니다. Key Vault 액세스 정책에 추가할 경우를 위해 관리 ID 리소스 ID를 저장해야 합니다.

   ![Azure Portal에서 사용자가 할당한 관리 ID 만들기](./media/apache-kafka-byok/user-managed-identity-portal.png)

## <a name="setup-the-key-vault-and-keys"></a>Key Vault 및 키 설정

   HDInsight는 Azure Key Vault만 지원합니다. 고유한 Key Vault가 있는 경우 Azure Key Vault로 키를 가져올 수 있습니다. 키에 "일시 삭제"가 있어야 합니다. "일시 삭제" 기능은 REST, .NET/C#, PowerShell 및 Azure CLI 인터페이스를 통해 사용할 수 있습니다.

   1. 새 Key Vault를 만들려면 [Azure Key Vault](../../key-vault/key-vault-overview.md) 빠른 시작을 수행합니다. 기존 키를 가져오는 방법에 대한 자세한 내용은 [키, 비밀 및 인증서 정보](../../key-vault/about-keys-secrets-and-certificates.md)를 참조하세요.

   2. [Az keyvault update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) cli 명령을 사용 하 여 키 자격 증명 모음에서 "일시 삭제"를 사용 하도록 설정 합니다.

        ```Azure CLI
        az keyvault update --name <Key Vault Name> --enable-soft-delete
        ```

   3. 키 만들기

        a. 새 키를 만들려면 **설정** 아래 **키** 메뉴에서 **생성/가져오기**를 선택합니다.

        ![Azure Key Vault에서 새 키를 생성 합니다] . (./media/apache-kafka-byok/kafka-create-new-key.png "Azure Key Vault에서 새 키를 생성 합니다") .

        b. **옵션**을 **생성**으로 설정하고 키에 이름을 지정합니다.

        ![Apache kafka는 키 이름을 생성] 합니다. (./media/apache-kafka-byok/apache-kafka-create-key.png "키 이름 생성")

        c. 키 목록에서 만든 키를 선택합니다.

        ![Apache kafka 키 자격 증명 모음 키 목록](./media/apache-kafka-byok/kafka-key-vault-key-list.png)

        d. Kafka 클러스터 암호화에 고유 키를 사용하는 경우 키 URI를 제공해야 합니다. **키 식별자**를 복사하고 클러스터를 만들 준비가 될 때까지 어딘가에 저장합니다.

        ![Apache kafka 키 식별자 가져오기](./media/apache-kafka-byok/kafka-get-key-identifier.png)

    4. Key Vault 액세스 정책에 관리 ID를 추가합니다.

        a. 새 Azure Key Vault 액세스 정책을 만듭니다.

        ![새 Azure Key Vault 액세스 정책 만들기](./media/apache-kafka-byok/add-key-vault-access-policy.png)

        b. **주체 선택** 아래에서 직접 만든 사용자가 할당한 관리 ID를 선택합니다.

        ![Azure Key Vault 액세스 정책에 대한 주체 선택 설정](./media/apache-kafka-byok/add-key-vault-access-policy-select-principal.png)

        c. **키 권한**을 **가져오기**, **키 래핑 해제** 및 **키 래핑**으로 설정합니다.

        ![Azure Key Vault access policy1에 대 한 키 권한 설정](./media/apache-kafka-byok/add-key-vault-access-policy-keys.png "Azure Key Vault access policy1에 대 한 키 권한 설정")

        d. **비밀 권한**을 **가져오기**, **설정** 및 **삭제**로 설정합니다.

        ![Azure Key Vault access policy2에 대 한 키 권한 설정](./media/apache-kafka-byok/add-key-vault-access-policy-secrets.png "Azure Key Vault access policy2에 대 한 키 권한 설정")

        e. **Save**를 클릭합니다. 

        ![Azure Key Vault 액세스 정책 저장](./media/apache-kafka-byok/add-key-vault-access-policy-save.png)

## <a name="create-hdinsight-cluster"></a>HDInsight 클러스터 만들기

   이제 HDInsight 클러스터를 만들 준비가 되었습니다. BYOK는 클러스터를 만드는 동안 새 클러스터에만 적용할 수 있습니다. BYOK 클러스터에서 암호화를 제거할 수 없고, 기존 클러스터에 BYOK를 추가할 수 없습니다.

   ![Azure Portal의 Kafka 디스크 암호화](./media/apache-kafka-byok/apache-kafka-byok-portal.png)

   클러스터를 만드는 동안 키 버전을 포함한 전체 키 URL을 제공합니다. 예를 들어, `https://contoso-kv.vault.azure.net/keys/kafkaClusterKey/46ab702136bc4b229f8b10e8c2997fa4`을 입력합니다. 또한 클러스터에 관리 ID를 할당하고 키 URI를 제공해야 합니다.

## <a name="rotating-the-encryption-key"></a>암호화 키 회전

   Kafka 클러스터를 만든 후에 사용 하는 암호화 키를 변경 해야 하는 시나리오가 있을 수 있습니다. 이는 포털을 통해 쉽게 수행할 수 있습니다. 이 작업의 경우 클러스터는 현재 키와 원래 새 키 모두에 대 한 액세스 권한이 있어야 합니다. 그렇지 않으면 키 회전 작업이 실패 합니다.

   키를 회전 하려면 새 키의 전체 url이 있어야 합니다 ( [Key Vault 및 키 설정](#setup-the-key-vault-and-keys)의 3 단계 참조). 이를 완료 한 후에는 포털의 Kafka 클러스터 속성 섹션으로 이동 하 고 **디스크 암호화 키 URL**에서 **키 변경** 을 클릭 합니다. 새 키 url을 입력 하 고 전송 하 여 키를 회전 합니다.

   ![Kafka 회전 디스크 암호화 키](./media/apache-kafka-byok/apache-kafka-change-key.png)

## <a name="faq-for-byok-to-apache-kafka"></a>Apache Kafka에 대한 BYOK FAQ

**Kafka 클러스터가 내 Key Vault에 어떻게 액세스하나요?**

   클러스터를 만드는 동안 HDInsight Kafka 클러스터와 관리 ID를 연결합니다. 클러스터를 만들기 전이나 만드는 동안 이 관리 ID를 만들 수 있습니다. 또한 키가 저장된 Key Vault에 대한 액세스 권한을 관리 ID에 부여해야 합니다.

**HDInsight의 모든 Kafka 클러스터에 이 기능을 사용할 수 있나요?**

   BYOK 암호화는 Kafka 1.1 이상 클러스터에만 적용할 수 있습니다.

**다른 토픽/파티션에 대한 서로 다른 키를 포함할 수 있나요?**

   아니요. 클러스터의 모든 관리 디스크는 동일한 키로 암호화됩니다.

**클러스터가 키 자격 증명 모음 또는 키에 대 한 액세스를 잃으면 어떻게 되나요?**
클러스터가 키에 대 한 액세스 권한을 상실 하면 Apache Ambari 포털에 경고가 표시 됩니다. 이 상태에서 **키 변경** 작업은 실패 합니다. 키 액세스가 복원 되 면 Ambari 경고가 표시 되지 않고 키 회전과 같은 작업이 성공적으로 수행 될 수 있습니다.

   ![Apache Kafka 키 액세스 Ambari 경고](./media/apache-kafka-byok/kafka-byok-ambari-alert.png)

**키가 삭제될 경우 클러스터를 어떻게 복구할 수 있나요?**

   "일시 삭제" 사용 키만 지원 되므로 키 자격 증명 모음에서 키를 복구 하는 경우 클러스터에서 키에 대 한 액세스 권한을 다시 확보 해야 합니다. Azure Key Vault 키를 복구 하려면 [AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) 또는 [Az-keyvault](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-recover)를 참조 하세요.

**생산자/소비자 애플리케이션이 BYOK 클러스터 및 비 BYOK 클러스터에서 동시에 작동할 수 있나요?**

   예. BYOK 사용은 생산자/소비자 애플리케이션에 투명합니다. 암호화는 OS 레이어에서 수행됩니다. 기존 생산자/소비자 Kafka 애플리케이션을 변경할 필요가 없습니다.

**OS 디스크/리소스 디스크도 암호화되나요?**

   아니요. OS 디스크 및 리소스 디스크는 암호화되지 않습니다.

**클러스터가 강화될 경우 새 broker가 BYOK를 원활하게 지원할까요?**

   예. 클러스터는 강화하는 동안 Key Vault의 키에 대한 액세스 권한이 있어야 합니다. 동일한 키가 클러스터의 모든 관리 디스크를 암호화하는 데 사용됩니다.

**내 위치에서 BYOK를 사용할 수 있나요?**

   Kafka BYOK는 모든 퍼블릭 클라우드에서 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* Azure Key Vault에 대한 자세한 내용은 [Azure Key Vault란?](../../key-vault/key-vault-overview.md)을 참조하세요.
* Azure Key Vault를 시작하려면 [Azure Key Vault 시작](../../key-vault/key-vault-overview.md)을 참조하세요.
