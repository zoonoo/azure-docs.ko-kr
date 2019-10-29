---
title: InvalidNetworkConfigurationErrorCode 오류-Azure HDInsight
description: Azure HDInsight에서 InvalidNetworkConfigurationErrorCode를 사용 하 여 실패 한 클러스터 만들기에 대 한 다양 한 이유
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: 5b8d031af9dbe6019d71e2a1caa3d3f25d4024ea
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044459"
---
# <a name="cluster-creation-fails-with-invalidnetworkconfigurationerrorcode-in-azure-hdinsight"></a>Azure HDInsight에서 InvalidNetworkConfigurationErrorCode를 사용 하 여 클러스터 만들기 실패

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대 한 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

"Virtual Network 구성이 HDInsight 요구 사항과 호환 되지 않습니다."와 같은 오류 코드 `InvalidNetworkConfigurationErrorCode` 표시 되는 경우 일반적으로 클러스터의 [가상 네트워크 구성](../hdinsight-plan-virtual-network-deployment.md) 에 문제가 있음을 나타냅니다. 나머지 오류 설명에 따라 아래 섹션을 따라 문제를 해결 합니다.

## <a name="hostname-resolution-failed"></a>"호스트 이름을 확인 하지 못했습니다."

### <a name="issue"></a>문제

오류 설명에 "호스트 이름 확인에 실패 했습니다."가 포함 되어 있습니다.

### <a name="cause"></a>원인

이 오류는 사용자 지정 DNS 구성의 문제를 가리킵니다. 가상 네트워크 내의 DNS 서버는 Azure의 재귀 확인자에 DNS 쿼리를 전달 하 여 해당 가상 네트워크 내에서 호스트 이름을 확인할 수 있습니다 (자세한 내용은 [가상 네트워크의 이름 확인](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) 참조). Azure의 재귀 확인자에 대한 액세스는 가상 IP 168.63.129.16을 통해 제공됩니다. 이 IP는 Azure Vm 에서만 액세스할 수 있습니다. 온-프레미스 DNS 서버를 사용 하 고 있거나 DNS 서버가 클러스터 vNet의 일부가 아닌 Azure VM 인 경우에는 작동 하지 않습니다.

### <a name="resolution"></a>해상도

1. 클러스터의 일부인 VM으로 Ssh를 실행 하 고 명령 `hostname -f`를 실행 합니다. 그러면 호스트의 정규화 된 도메인 이름이 반환 됩니다 (아래 지침에서 `<host_fqdn>` 이라고 함).

1. 그런 다음 명령 `nslookup <host_fqdn>` (예: `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net`)를 실행 합니다. 이 명령은 이름을 IP 주소로 확인 하는 경우 DNS 서버가 제대로 작동 하 고 있음을 의미 합니다. 이 경우 HDInsight를 사용 하 여 지원 사례를 발생 시키고 문제를 조사 합니다. 지원 사례에는 실행 한 문제 해결 단계를 포함 합니다. 이렇게 하면 문제를 더 빠르게 해결 하는 데 도움이 됩니다.

1. 위의 명령에서 IP 주소를 반환 하지 않는 경우 `nslookup <host_fqdn> 168.63.129.16` (예: `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net 168.63.129.16`)를 실행 합니다. 이 명령을 사용 하 여 IP를 확인할 수 있는 경우 DNS 서버는 Azure의 DNS로 쿼리를 전달 하 고 있지 않거나 클러스터와 동일한 vNet의 일부인 VM이 아님을 의미 합니다.

1. 클러스터의 vNet에서 사용자 지정 DNS 서버 역할을 할 수 있는 Azure VM이 없으면이를 먼저 추가 해야 합니다. DNS 전달자로 구성 될 vNet에서 VM을 만듭니다.

1. VNet에 VM을 배포한 후이 VM에 대 한 DNS 전달 규칙을 구성 합니다. 모든 Idn 이름 확인 요청을 168.63.129.16에 전달 하 고 나머지는 DNS 서버로 전달 합니다. 사용자 지정 DNS 서버에 대 한이 설치의 예는 [다음과](../hdinsight-plan-virtual-network-deployment.md) 같습니다.

1. Virtual Network DNS 구성에 대 한 첫 번째 DNS 항목으로이 VM의 IP 주소를 추가 합니다.

---

## <a name="failed-to-connect-to-azure-storage-account"></a>"Azure Storage 계정에 연결 하지 못했습니다."

### <a name="issue"></a>문제

오류 설명에 "Azure Storage 계정에 연결 하지 못했습니다." 또는 "Azure SQL에 연결 하지 못했습니다."가 포함 되어 있습니다.

### <a name="cause"></a>원인

Azure Storage 및 SQL에 고정 IP 주소가 없으므로 이러한 서비스에 액세스할 수 있도록 모든 Ip에 대 한 아웃 바운드 연결을 허용 해야 합니다. 정확한 해결 단계는 NSG (네트워크 보안 그룹) 또는 UDR (사용자 정의 규칙)을 설정 했는지 여부에 따라 달라 집니다. 이러한 구성에 대 한 자세한 내용은 [네트워크 보안 그룹 및 사용자 정의 경로를 사용 하 여 HDInsight로 네트워크 트래픽 제어](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ip) 섹션을 참조 하세요.

### <a name="resolution"></a>해상도

* 클러스터가 [NSG (네트워크 보안 그룹)](../../virtual-network/virtual-network-vnet-plan-design-arm.md)를 사용 하는 경우

    Azure Portal로 이동 하 여 클러스터가 배포 되는 서브넷과 연결 된 NSG를 식별 합니다. **아웃 바운드 보안 규칙** 섹션에서 제한 없이 인터넷에 대 한 아웃 바운드 액세스를 허용 합니다. 여기에서 **우선** 순위가 더 작은 것은 더 높은 우선 순위를 의미 합니다. 또한 **서브넷** 섹션에서이 nsg가 클러스터 서브넷에 적용 되는지 확인 합니다.

* 클러스터가 [UDR (사용자 정의 경로)](../../virtual-network/virtual-networks-udr-overview.md)를 사용 하는 경우

    Azure Portal로 이동 하 여 클러스터가 배포 되는 서브넷과 연결 된 경로 테이블을 식별 합니다. 서브넷에 대 한 경로 테이블을 찾았으면 **경로 섹션을 검사 합니다.**

    경로가 정의 되어 있으면 클러스터가 배포 된 지역에 대 한 IP 주소에 대 한 경로가 있는지 확인 하 고 각 경로에 대 한 **NextHopType** 는 **인터넷**입니다. 앞서 언급 한 문서에서 설명 하는 각 필수 IP 주소에 대해 정의 된 경로가 있어야 합니다.

---

### <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure [커뮤니티 지원을](https://azure.microsoft.com/support/community/)통해 azure 전문가 로부터 답변을 받으세요.

* [@AzureSupport](https://twitter.com/azuresupport) 연결-Azure 커뮤니티를 적절 한 리소스 (답변, 지원 및 전문가)에 연결 하 여 고객 환경을 개선 하기 위한 공식 Microsoft Azure 계정입니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택 하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)을 참조 하세요. 구독 관리 및 청구 지원에 대 한 액세스는 Microsoft Azure 구독에 포함 되며, [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 기술 지원이 제공 됩니다.
