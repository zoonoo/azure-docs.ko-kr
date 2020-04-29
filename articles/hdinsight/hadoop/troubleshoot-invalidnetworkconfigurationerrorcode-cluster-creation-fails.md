---
title: InvalidNetworkConfigurationErrorCode 오류-Azure HDInsight
description: Azure HDInsight에서 InvalidNetworkConfigurationErrorCode를 사용 하 여 실패 한 클러스터 만들기에 대 한 다양 한 이유
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: 6dd4db999cb130c9816ad023888a4333e968c224
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "76720387"
---
# <a name="cluster-creation-fails-with-invalidnetworkconfigurationerrorcode-in-azure-hdinsight"></a>Azure HDInsight에서 InvalidNetworkConfigurationErrorCode를 사용 하 여 클러스터 만들기 실패

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대 한 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

"Virtual Network 구성이 HDInsight 요구 `InvalidNetworkConfigurationErrorCode` 사항과 호환 되지 않습니다."와 같은 오류 코드가 표시 되는 경우 일반적으로 클러스터의 [가상 네트워크 구성](../hdinsight-plan-virtual-network-deployment.md) 에 문제가 있음을 나타냅니다. 나머지 오류 설명에 따라 아래 섹션을 따라 문제를 해결 합니다.

## <a name="hostname-resolution-failed"></a>"호스트 이름을 확인 하지 못했습니다."

### <a name="issue"></a>문제

오류 설명에 "호스트 이름 확인에 실패 했습니다."가 포함 되어 있습니다.

### <a name="cause"></a>원인

이 오류는 사용자 지정 DNS 구성의 문제를 가리킵니다. 가상 네트워크 내의 DNS 서버는 Azure의 재귀 확인자에 DNS 쿼리를 전달 하 여 해당 가상 네트워크 내에서 호스트 이름을 확인할 수 있습니다 (자세한 내용은 [가상 네트워크의 이름 확인](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) 참조). Azure의 재귀 확인자에 대한 액세스는 가상 IP 168.63.129.16을 통해 제공됩니다. 이 IP는 Azure Vm 에서만 액세스할 수 있습니다. 온-프레미스 DNS 서버를 사용 하는 경우 또는 DNS 서버가 클러스터의 가상 네트워크에 속하지 않는 Azure VM 인 경우에는 작동 하지 않습니다.

### <a name="resolution"></a>해결 방법

1. 클러스터의 일부인 VM으로 Ssh를 실행 하 고 명령을 `hostname -f`실행 합니다. 그러면 호스트의 정규화 된 도메인 이름이 반환 됩니다 (아래 지침 `<host_fqdn>` 에서 참조).

1. 그런 다음 명령을 `nslookup <host_fqdn>` 실행 합니다 (예: `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net`). 이 명령은 이름을 IP 주소로 확인 하는 경우 DNS 서버가 제대로 작동 하 고 있음을 의미 합니다. 이 경우 HDInsight를 사용 하 여 지원 사례를 제기 하 고 문제를 조사 합니다. 지원 사례에는 실행 한 문제 해결 단계를 포함 합니다. 이렇게 하면 문제를 더 빠르게 해결 하는 데 도움이 됩니다.

1. 위의 명령에서 IP 주소를 반환 하지 않는 경우 (예 `nslookup <host_fqdn> 168.63.129.16` :)를 `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net 168.63.129.16`실행 합니다. 이 명령을 사용 하 여 IP를 확인할 수 있는 경우 DNS 서버에서 Azure의 DNS로 쿼리를 전달 하지 않거나 클러스터와 동일한 가상 네트워크의 일부인 VM이 아닌 것을 의미 합니다.

1. 클러스터의 가상 네트워크에서 사용자 지정 DNS 서버 역할을 할 수 있는 Azure VM이 없으면이를 먼저 추가 해야 합니다. DNS 전달자로 구성 될 가상 네트워크에 VM을 만듭니다.

1. 가상 네트워크에 VM을 배포한 후이 VM에 대 한 DNS 전달 규칙을 구성 합니다. 모든 Idn 이름 확인 요청을 168.63.129.16에 전달 하 고 나머지는 DNS 서버로 전달 합니다. 사용자 지정 DNS 서버에 대 한이 설치의 예는 [다음과](../hdinsight-plan-virtual-network-deployment.md) 같습니다.

1. Virtual Network DNS 구성에 대 한 첫 번째 DNS 항목으로이 VM의 IP 주소를 추가 합니다.

---

## <a name="failed-to-connect-to-azure-storage-account"></a>"Azure Storage 계정에 연결 하지 못했습니다."

### <a name="issue"></a>문제

오류 설명에 "Azure Storage 계정에 연결 하지 못했습니다." 또는 "Azure SQL에 연결 하지 못했습니다."가 포함 되어 있습니다.

### <a name="cause"></a>원인

Azure Storage 및 SQL에 고정 IP 주소가 없으므로 이러한 서비스에 대 한 액세스를 허용 하려면 모든 Ip에 대 한 아웃 바운드 연결을 허용 해야 합니다. 정확한 해결 단계는 NSG (네트워크 보안 그룹) 또는 UDR (사용자 정의 규칙)을 설정 했는지 여부에 따라 달라 집니다. 이러한 구성에 대 한 자세한 내용은 [네트워크 보안 그룹 및 사용자 정의 경로를 사용 하 여 HDInsight로 네트워크 트래픽 제어](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ip) 섹션을 참조 하세요.

### <a name="resolution"></a>해결 방법

* 클러스터가 [NSG (네트워크 보안 그룹)](../../virtual-network/virtual-network-vnet-plan-design-arm.md)를 사용 하는 경우

    Azure Portal로 이동 하 여 클러스터가 배포 되는 서브넷과 연결 된 NSG를 식별 합니다. **아웃 바운드 보안 규칙** 섹션에서 제한 없이 인터넷에 대 한 아웃 바운드 액세스를 허용 합니다. 여기에서 **우선** 순위가 더 작은 것은 더 높은 우선 순위를 의미 합니다. 또한 **서브넷** 섹션에서이 nsg가 클러스터 서브넷에 적용 되는지 확인 합니다.

* 클러스터가 [UDR (사용자 정의 경로)](../../virtual-network/virtual-networks-udr-overview.md)를 사용 하는 경우

    Azure Portal로 이동 하 여 클러스터가 배포 되는 서브넷과 연결 된 경로 테이블을 식별 합니다. 서브넷에 대 한 경로 테이블을 찾았으면 **경로 섹션을 검사 합니다.**

    경로가 정의 되어 있으면 클러스터가 배포 된 지역에 대 한 IP 주소에 대 한 경로가 있는지 확인 하 고 각 경로에 대 한 **NextHopType** 는 **인터넷**입니다. 앞서 언급 한 문서에서 설명 하는 각 필수 IP 주소에 대해 정의 된 경로가 있어야 합니다.

---

## <a name="virtual-network-configuration-is-not-compatible-with-hdinsight-requirement"></a>"가상 네트워크 구성이 HDInsight 요구 사항과 호환 되지 않습니다."

### <a name="issue"></a>문제

오류 설명에는 다음과 비슷한 메시지가 포함 됩니다.

```
ErrorCode: InvalidNetworkConfigurationErrorCode
ErrorDescription: Virtual Network configuration is not compatible with HDInsight Requirement. Error: 'Failed to connect to Azure Storage Account; Failed to connect to Azure SQL; HostName Resolution failed', Please follow https://go.microsoft.com/fwlink/?linkid=853974 to fix it.
```

### <a name="cause"></a>원인

사용자 지정 DNS 설정에 문제가 있을 수 있습니다.

### <a name="resolution"></a>해결 방법

168.63.129.16이 사용자 지정 DNS 체인에 있는지 확인 합니다. 가상 네트워크 내에서 DNS 서버는 해당 가상 네트워크 내에서 호스트 이름을 확인하기 위해 Azure의 재귀 확인자에게 DNS 쿼리를 전달할 수 있습니다. 자세한 내용은 [가상 네트워크에서 이름 확인](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)을 참조 하세요. Azure의 재귀 확인자에 대한 액세스는 가상 IP 168.63.129.16을 통해 제공됩니다.

1. [Ssh 명령을](../hdinsight-hadoop-linux-use-ssh-unix.md) 사용 하 여 클러스터에 연결 합니다. CLUSTERNAME을 클러스터의 이름으로 바꿔서 아래 명령을 편집 하 고 명령을 입력 합니다.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 다음 명령을 실행하십시오.

    ```bash
    cat /etc/resolv.conf | grep nameserver*
    ```

    다음과 유사한 결과가 표시됩니다.

    ```output
    nameserver 168.63.129.16
    nameserver 10.21.34.43
    nameserver 10.21.34.44
    ```

    결과에 따라 다음 단계 중 하나를 선택 하 여 수행 합니다.

#### <a name="1686312916-is-not-in-this-list"></a>168.63.129.16이 목록에 없습니다.

**옵션 1**  
[Azure HDInsight에 대 한 가상 네트워크 계획](../hdinsight-plan-virtual-network-deployment.md)에 설명 된 단계를 사용 하 여 가상 네트워크에 대 한 첫 번째 사용자 지정 DNS로 168.63.129.16를 추가 합니다. 이러한 단계는 사용자 지정 DNS 서버가 Linux에서 실행 되는 경우에만 적용 됩니다.

**옵션 2**  
가상 네트워크에 대 한 DNS 서버 VM을 배포 합니다. 다음 단계를 포함합니다.

* 가상 네트워크에 VM을 만듭니다 .이 VM은 DNS 전달자로 구성 됩니다 (Linux 또는 windows VM 일 수 있음).
* 이 VM에 대 한 DNS 전달 규칙을 구성 합니다 (모든 Idn 이름 확인 요청을 168.63.129.16에 전달 하 고 나머지는 DNS 서버로 전달).
* Virtual Network DNS 구성의 첫 번째 DNS 항목으로이 VM의 IP 주소를 추가 합니다.

#### <a name="1686312916-is-in-the-list"></a>168.63.129.16는 목록에 있습니다.

이 경우 HDInsight를 사용 하 여 지원 사례를 만들면 문제를 조사 합니다. 지원 사례에 아래 명령의 결과를 포함 합니다. 이렇게 하면 문제를 신속 하 게 조사 하 고 해결 하는 데 도움이 됩니다.

헤드 노드의 ssh 세션에서 다음을 편집 하 고 실행 합니다.

```bash
hostname -f
nslookup <headnode_fqdn> (e.g.nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
dig @168.63.129.16 <headnode_fqdn> (e.g. dig @168.63.129.16 hn0-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
```

---

### <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure [커뮤니티 지원을](https://azure.microsoft.com/support/community/)통해 azure 전문가 로부터 답변을 받으세요.

* 연결 방법 [@AzureSupport](https://twitter.com/azuresupport) -Azure 커뮤니티를 적절 한 리소스 (답변, 지원 및 전문가)에 연결 하 여 고객 환경을 개선 하기 위한 공식 Microsoft Azure 계정입니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택 하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)을 참조 하세요. 구독 관리 및 청구 지원에 대 한 액세스는 Microsoft Azure 구독에 포함 되며, [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 기술 지원이 제공 됩니다.
