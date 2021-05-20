---
title: 디스크 암호화를 사용하는 Azure HDInsight 클러스터는 Key Vault 액세스를 손실합니다.
description: Azure HDInsight 클러스터와 상호 작용할 때 Key Vault 액세스 문제에 대한 문제 해결 단계 및 가능한 해결 방법입니다.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.openlocfilehash: ce2929ca84746de1ab8b51882f3004c3699f17ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943112"
---
# <a name="scenario-azure-hdinsight-clusters-with-disk-encryption-lose-key-vault-access"></a>시나리오: 디스크 암호화를 사용하는 Azure HDInsight 클러스터는 Key Vault 액세스를 손실합니다.

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 방법을 설명합니다.

## <a name="issue"></a>문제

RHC(Resource Health Center) 경고 `The HDInsight cluster is unable to access the key for BYOK encryption at rest`는 클러스터 노드가 고객 KV(Key Vault)에 대한 액세스 권한을 손실한 BYOK(Bring Your Own Key) 클러스터에 대해 표시됩니다. Apache Ambari UI에서도 유사한 경고가 표시될 수 있습니다.

## <a name="cause"></a>원인

이 경고는 클러스터 노드에서 KV에 액세스할 수 있도록 하여 네트워크 연결, KV 상태 및 사용자 할당 관리 ID에 대한 액세스 정책을 보장합니다. 이 경고는 이후에 노드가 다시 부팅될 때 발생한 broker 종료가 임박했다는 경고일 뿐이며, 노드가 다시 부팅될 때까지 클러스터는 계속 작동합니다.

Apache Ambari UI로 이동하여 **디스크 암호화 Key Vault 상태** 에서 경고에 대한 자세한 정보를 확인합니다. 이 경고에는 확인 실패 이유에 대한 세부 정보가 포함되어 있습니다.

## <a name="resolution"></a>해결 방법

### <a name="kvaad-outage"></a>KV/AAD 중단

https://status.azure.com/ 에 대한 자세한 내용은 [Azure Key Vault 가용성 및 중복성](../../key-vault/general/disaster-recovery-guidance.md) 및 Azure 상태 페이지를 참조하세요.

### <a name="kv-accidental-deletion"></a>KV 실수로 삭제

* KV에서 삭제된 키를 자동 복구로 복원합니다. 자세한 내용은 [삭제된 키 복구](/rest/api/keyvault/recoverdeletedkey)를 참조하세요.
* 실수로 삭제한 경우 복구하려면 KV 팀에 문의하세요.

### <a name="kv-access-policy-changed"></a>KV 액세스 정책이 변경됨

KV에 액세스하기 위해 HDI 클러스터에 할당된 사용자 할당 관리 ID에 대한 액세스 정책을 복원합니다.

### <a name="key-permitted-operations"></a>키 허용 작업

KV의 각 키에 대해 허용되는 작업 세트를 선택할 수 있습니다. BYOK 키에 대해 래핑 및 래핑 해제 작업을 사용하도록 설정했는지 확인합니다.

### <a name="expired-key"></a>만료된 키

만료 기간이 지났지만 키가 회전되지 않은 경우 백업 HSM에서 키를 복원하거나 KV 팀에 문의하여 만료 날짜를 지우세요.

### <a name="kv-firewall-blocking-access"></a>KV 방화벽 차단 액세스

BYOK 클러스터 노드가 KV에 액세스할 수 있도록 KV 방화벽 설정을 수정합니다.

### <a name="nsg-rules-on-virtual-network-blocking-access"></a>가상 네트워크 액세스 차단에 대한 NSG 규칙

클러스터에 연결된 가상 네트워크와 관련된 NSG 규칙을 확인합니다.

## <a name="mitigation-and-prevention-steps"></a>완화 및 방지 단계

### <a name="kv-accidental-deletion"></a>KV 실수로 삭제

* [리소스 잠금 집합](../../azure-resource-manager/management/lock-resources.md)을 사용하여 Key Vault를 구성합니다.
* 하드웨어 보안 모듈에 키를 백업합니다.

### <a name="key-deletion"></a>키 삭제

키를 삭제하기 전에 클러스터를 삭제해야 합니다.

### <a name="kv-access-policy-changed"></a>KV 액세스 정책이 변경됨

정기적으로 액세스 정책을 감사 및 테스트합니다.

### <a name="expired-key"></a>만료된 키

* HSM에 키를 백업합니다.
* 만료가 설정되지 않은 키를 사용합니다.
* 만료를 설정해야 하는 경우 만료 날짜 전에 키를 회전합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [Azure 커뮤니티 지원](https://azure.microsoft.com/support/community/)을 통해 Azure 전문가로부터 답변을 얻습니다.

* [@AzureSupport](https://twitter.com/azuresupport)(고객 환경을 개선하기 위한 공식 Microsoft Azure 계정)에 연결합니다. Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결합니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](../../azure-portal/supportability/how-to-create-azure-support-request.md)을 참조하세요. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 [Azure 지원 플랜](https://azure.microsoft.com/support/plans/) 중 하나를 통해 기술 지원이 제공됩니다.