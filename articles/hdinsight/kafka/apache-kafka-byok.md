---
title: Azure HDInsight의 Apache Kafka에 대한 Bring Your Own Key(미리 보기)
description: 이 문서에서는 Azure Key Vault에서 사용자 고유의 키를 사용하여 Azure HDInsight의 Apache Kafka에 저장된 데이터를 암호화하는 방법을 설명합니다.
services: hdinsight
ms.service: hdinsight
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 61a4be19000265910493963db9f29df143a7e21c
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58360353"
---
# <a name="bring-your-own-key-for-apache-kafka-on-azure-hdinsight-preview"></a>Azure HDInsight의 Apache Kafka에 대한 Bring Your Own Key(미리 보기)

Azure HDInsight에는 Apache Kafka에 대한 BYOK(Bring Your Own Key) 지원이 포함됩니다. 이 기능을 사용하면 미사용 데이터를 암호화하는 데 사용되는 키를 소유하고 관리할 수 있습니다. 

HDInsight의 모든 관리 디스크는 Azure SSE(저장소 서비스 암호화)로 보호됩니다. 기본적으로 해당 디스크의 데이터는 Microsoft 관리 키를 사용하여 암호화됩니다. BYOK를 사용하도록 설정한 경우 HDInsight에 대한 암호화 키를 제공하여 Azure Key Vault를 통해 사용하고 관리합니다. 

BYOK 암호화는 추가 비용 없이 클러스터를 만드는 동안 처리되는 1단계 프로세스입니다. Azure Key Vault를 통해 HDInsight를 관리 ID로 등록하고 클러스터를 만들 때 암호화 키를 추가하면 됩니다.

Kafka에 대한 모든 메시지(Kafka에서 유지 관리되는 복제본 포함)는 대칭 DEK(데이터 암호화 키)로 암호화됩니다. DEK는 Key Vault의 KEK(Key Encryption Key)를 사용하여 보호됩니다. 암호화 및 암호 해독 프로세스는 전적으로 Azure HDInsight에 의해 처리됩니다. 

Azure Portal 또는 Azure CLI를 사용하여 Key Vault의 키를 안전하게 회전할 수 있습니다. 키가 회전될 때 HDInsight Kafka 클러스터는 몇 분 안에 새 키를 사용하기 시작합니다. 랜섬웨어 시나리오 및 실수로 인한 삭제를 방지하려면 “제거 안 함” 및 “일시 삭제” 키 보호 기능을 사용하도록 설정합니다. 이러한 보호 기능이 없는 키는 지원되지 않습니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="get-started-with-byok"></a>BYOK 시작

1. Azure 리소스에 대한 관리 ID 만들기

   Key Vault에 인증을 사용 하 여 관리 되는 사용자 할당 id를 만듭니다는 [Azure portal](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)를 [Azure PowerShell](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)합니다 [Azure Resource Manager](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md), 또는 [ Azure CLI](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)합니다. Azure HDInsight에서 관리 되는 identities 작업에 대 한 자세한 내용은 참조 하세요. [Azure HDInsight에서 id 관리](../hdinsight-managed-identities.md)합니다. Kafka에 대한 관리 ID 및 BYOK에는 Azure Active Directory가 필요하지만 ESP(Encapsulating Security Payload)는 필요하지 않습니다. Key Vault 액세스 정책에 추가할 경우를 위해 관리 ID 리소스 ID를 저장해야 합니다.

   ![Azure Portal에서 사용자가 할당한 관리 ID 만들기](./media/apache-kafka-byok/user-managed-identity-portal.png)

2. 기존 키 자격 증명 모음을 가져오거나 새로 만듭니다.

   HDInsight는 Azure Key Vault만 지원합니다. 고유한 Key Vault가 있는 경우 Azure Key Vault로 키를 가져올 수 있습니다. 키에 대한 “일시 삭제” 및 “제거 안 함”이 사용하도록 설정되어야 합니다. “일시 삭제” 및 “제거 안 함” 기능은 REST, .NET/C#, PowerShell 및 Azure CLI 인터페이스를 통해 제공됩니다.

   새 Key Vault를 만들려면 [Azure Key Vault](../../key-vault/key-vault-overview.md) 빠른 시작을 수행합니다. 기존 키를 가져오는 방법에 대한 자세한 내용은 [키, 비밀 및 인증서 정보](../../key-vault/about-keys-secrets-and-certificates.md)를 참조하세요.

   새 키를 만들려면 **설정** 아래 **키** 메뉴에서 **생성/가져오기**를 선택합니다.

   ![Azure Key Vault에 새 키 생성](./media/apache-kafka-byok/kafka-create-new-key.png)

   **옵션**을 **생성**으로 설정하고 키에 이름을 지정합니다.

   ![Azure Key Vault에 새 키 생성](./media/apache-kafka-byok/kafka-create-a-key.png)

   키 목록에서 만든 키를 선택합니다.

   ![Azure Key Vault 키 목록](./media/apache-kafka-byok/kafka-key-vault-key-list.png)

   Kafka 클러스터 암호화에 고유 키를 사용하는 경우 키 URI를 제공해야 합니다. **키 식별자**를 복사하고 클러스터를 만들 준비가 될 때까지 어딘가에 저장합니다.

   ![키 식별자 복사](./media/apache-kafka-byok/kafka-get-key-identifier.png)
   
3. Key Vault 액세스 정책에 관리 ID를 추가합니다.

   새 Azure Key Vault 액세스 정책을 만듭니다.

   ![새 Azure Key Vault 액세스 정책 만들기](./media/apache-kafka-byok/add-key-vault-access-policy.png)

   **주체 선택** 아래에서 직접 만든 사용자가 할당한 관리 ID를 선택합니다.

   ![Azure Key Vault 액세스 정책에 대한 주체 선택 설정](./media/apache-kafka-byok/add-key-vault-access-policy-select-principal.png)

   **키 권한**을 **가져오기**, **키 래핑 해제** 및 **키 래핑**으로 설정합니다.

   ![Azure Key Vault 액세스 정책에 대한 키 권한 설정](./media/apache-kafka-byok/add-key-vault-access-policy-keys.png)

   **비밀 권한**을 **가져오기**, **설정** 및 **삭제**로 설정합니다.

   ![Azure Key Vault 액세스 정책에 대한 키 권한 설정](./media/apache-kafka-byok/add-key-vault-access-policy-secrets.png)

4. HDInsight 클러스터 만들기

   이제 HDInsight 클러스터를 만들 준비가 되었습니다. BYOK는 클러스터를 만드는 동안 새 클러스터에만 적용할 수 있습니다. BYOK 클러스터에서 암호화를 제거할 수 없고, 기존 클러스터에 BYOK를 추가할 수 없습니다.

   ![Azure Portal의 Kafka 디스크 암호화](./media/apache-kafka-byok/apache-kafka-byok-portal.png)

   클러스터를 만드는 동안 키 버전을 포함한 전체 키 URL을 제공합니다. 예: `https://contoso-kv.vault.azure.net/keys/kafkaClusterKey/46ab702136bc4b229f8b10e8c2997fa4` 또한 클러스터에 관리 ID를 할당하고 키 URI를 제공해야 합니다.

## <a name="faq-for-byok-to-apache-kafka"></a>Apache Kafka에 대한 BYOK FAQ

**Kafka 클러스터가 내 Key Vault에 어떻게 액세스하나요?**

   클러스터를 만드는 동안 HDInsight Kafka 클러스터와 관리 ID를 연결합니다. 클러스터를 만들기 전이나 만드는 동안 이 관리 ID를 만들 수 있습니다. 또한 키가 저장된 Key Vault에 대한 액세스 권한을 관리 ID에 부여해야 합니다.

**HDInsight의 모든 Kafka 클러스터에 이 기능을 사용할 수 있나요?**

   BYOK 암호화는 Kafka 1.1 이상 클러스터에만 적용할 수 있습니다.

**다른 토픽/파티션에 대한 서로 다른 키를 포함할 수 있나요?**

   아니요. 클러스터의 모든 관리 디스크는 동일한 키로 암호화됩니다.

**키가 삭제될 경우 클러스터를 어떻게 복구할 수 있나요?**

   “일시 삭제” 사용 키만 지원되므로, 키가 Key Vault에서 복원될 경우 클러스터는 키에 대한 액세스 권한을 다시 얻어야 합니다. 참조는 Azure Key Vault 키를 복원 하려면 [복원 AzKeyVaultKey](/powershell/module/az.keyvault/restore-azkeyvaultkey)합니다.

**생산자/소비자 애플리케이션이 BYOK 클러스터 및 비 BYOK 클러스터에서 동시에 작동할 수 있나요?**

   예. BYOK 사용은 생산자/소비자 애플리케이션에 투명합니다. 암호화는 OS 레이어에서 수행됩니다. 기존 생산자/소비자 Kafka 애플리케이션을 변경할 필요가 없습니다.

**OS 디스크/리소스 디스크도 암호화되나요?**

   아니요. OS 디스크 및 리소스 디스크는 암호화되지 않습니다.

**클러스터가 강화될 경우 새 broker가 BYOK를 원활하게 지원할까요?**

   예. 클러스터는 강화하는 동안 Key Vault의 키에 대한 액세스 권한이 있어야 합니다. 동일한 키가 클러스터의 모든 관리 디스크를 암호화하는 데 사용됩니다.

**내 위치에서 BYOK를 사용할 수 있나요?**

   Kafka BYOK는 모든 공용 클라우드에서 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* Azure Key Vault에 대한 자세한 내용은 [Azure Key Vault란?](../../key-vault/key-vault-whatis.md)을 참조하세요.
* Azure Key Vault를 시작하려면 [Azure Key Vault 시작](../../key-vault/key-vault-overview.md)을 참조하세요.
