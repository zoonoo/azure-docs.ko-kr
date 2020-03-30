---
title: 디스크 암호화가 있는 Azure HDInsight 클러스터는 키 볼트 액세스 권한을 잃게 됩니다.
description: Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 해결 가능성
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.openlocfilehash: 2ae389be25cd8633a53a49cf000796c1510733a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76965165"
---
# <a name="scenario-azure-hdinsight-clusters-with-disk-encryption-lose-key-vault-access"></a>시나리오: 디스크 암호화가 있는 Azure HDInsight 클러스터가 키 볼트 액세스 권한을 잃게 됩니다.

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 에 대해 설명합니다.

## <a name="issue"></a>문제

RHC(리소스 상태 센터) `The HDInsight cluster is unable to access the key for BYOK encryption at rest`경고는 클러스터 노드가 KV(고객 키 볼트)에 대한 액세스 권한을 상실한 BYOK(사용자 고유키 가져오기) 클러스터에 대해 표시됩니다. 아파치 암바리 UI에서도 비슷한 경고를 볼 수 있습니다.

## <a name="cause"></a>원인

이 경고는 클러스터 노드에서 KV에 액세스할 수 있도록 하여 관리되는 ID를 할당받은 사용자에 대한 네트워크 연결, KV 상태 및 액세스 정책을 보장합니다. 이 경고는 후속 노드 재부팅시 브로커 종료가 임박했다는 경고일 뿐이며 노드가 재부팅될 때까지 클러스터가 계속 작동합니다.

아파치 Ambari UI로 이동하여 디스크 암호화 **키 자격 증명 모음 상태에서**경고에 대한 자세한 정보를 찾을 수 있습니다. 이 경고에는 확인 실패 이유에 대한 세부 정보가 표시됩니다.

## <a name="resolution"></a>해결 방법

### <a name="kvaad-outage"></a>KV/AAD 중단

자세한 내용은 Azure 키 볼트 가용성 및 중복 성 및 Azure 상태 페이지를 [참조하십시오.](../../key-vault/key-vault-disaster-recovery-guidance.md)https://status.azure.com/

### <a name="kv-accidental-deletion"></a>KV 우발적 삭제

* KV에서 삭제된 키를 자동으로 복구합니다. 자세한 내용은 [삭제된 키 복구](https://docs.microsoft.com/rest/api/keyvault/recoverdeletedkey)를 참조하십시오.
* KV 팀에 연락하여 우발적인 삭제를 복구하십시오.

### <a name="kv-access-policy-changed"></a>KV 액세스 정책이 변경되었습니다.

KV에 액세스하기 위해 HDI 클러스터에 할당된 관리 ID를 할당된 사용자에 대한 액세스 정책을 복원합니다.

### <a name="key-permitted-operations"></a>주요 허용 작업

KV의 각 키에 대해 허용된 작업 집합을 선택할 수 있습니다. BYOK 키에 대해 랩 및 래핑 해제 작업을 사용하도록 설정되었는지 확인합니다.

### <a name="expired-key"></a>만료된 키

만료가 지났고 키가 회전되지 않으면 백업 HSM에서 키를 복원하거나 KV 팀에 문의하여 만료 날짜를 지웁히 십시오.

### <a name="kv-firewall-blocking-access"></a>KV 방화벽 차단 액세스

BYOK 클러스터 노드가 KV에 액세스할 수 있도록 KV 방화벽 설정을 수정합니다.

### <a name="nsg-rules-on-virtual-network-blocking-access"></a>가상 네트워크 차단 액세스에 대한 NSG 규칙

클러스터에 연결된 가상 네트워크와 연결된 NSG 규칙을 확인합니다.

## <a name="mitigation-and-prevention-steps"></a>완화 및 예방 단계

### <a name="kv-accidental-deletion"></a>KV 우발적 삭제

* 리소스 잠금 [집합으로](../../azure-resource-manager/management/lock-resources.md)키 볼트 구성.
* 하드웨어 보안 모듈에 키를 백업합니다.

### <a name="key-deletion"></a>키 삭제

키를 삭제하기 전에 클러스터를 삭제해야 합니다.

### <a name="kv-access-policy-changed"></a>KV 액세스 정책이 변경되었습니다.

정기적으로 액세스 정책을 감사하고 테스트합니다.

### <a name="expired-key"></a>만료된 키

* HSM에 키를 백업합니다.
* 만료 집합 없이 키를 사용 합니다.
* 만료를 설정해야 하는 경우 만료 날짜 전에 키를 회전합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure 커뮤니티 지원을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/community/)

* 연결 [@AzureSupport](https://twitter.com/azuresupport) - 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정입니다. Azure 커뮤니티를 올바른 리소스( 답변, 지원 및 전문가)에 연결합니다.

* 추가 도움이 필요한 경우 [Azure 포털](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원을** 선택하거나 도움말 + 지원 허브를 **엽니다.** 자세한 내용은 Azure [지원 요청을 만드는 방법을](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)검토합니다. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 기술 지원은 [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 제공됩니다.
