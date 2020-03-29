---
title: 잘못된 네트워크구성오류코드 오류 - Azure HDInsight
description: Azure HDInsight에서 잘못된 네트워크구성오류코드로 클러스터 생성실패의 여러 가지 이유
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: 6dd4db999cb130c9816ad023888a4333e968c224
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720387"
---
# <a name="cluster-creation-fails-with-invalidnetworkconfigurationerrorcode-in-azure-hdinsight"></a>Azure HDInsight에서 잘못된 네트워크구성오류코드로 클러스터 생성실패

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 에 대해 설명합니다.

"가상 네트워크 `InvalidNetworkConfigurationErrorCode` 구성이 HDInsight 요구 사항과 호환되지 않습니다"라는 설명이 있는 오류 코드가 표시되면 일반적으로 클러스터의 [가상 네트워크 구성에](../hdinsight-plan-virtual-network-deployment.md) 문제가 있음을 나타냅니다. 나머지 오류 설명에 따라 아래 섹션을 따라 문제를 해결하십시오.

## <a name="hostname-resolution-failed"></a>"호스트 이름 해결 실패"

### <a name="issue"></a>문제

오류 설명에는 "HostName 확인 실패"가 포함되어 있습니다.

### <a name="cause"></a>원인

이 오류는 사용자 지정 DNS 구성에 문제가 있습니다. 가상 네트워크 내의 DNS 서버는 DNS 쿼리를 Azure의 재귀 해결 방법으로 전달하여 해당 가상 네트워크 내의 호스트 이름을 확인할 수 있습니다(자세한 내용은 [가상 네트워크의 이름 확인](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) 참조). Azure의 재귀 확인자에 대한 액세스는 가상 IP 168.63.129.16을 통해 제공됩니다. 이 IP는 Azure VM에서만 액세스할 수 있습니다. 따라서 OnPrem DNS 서버를 사용 하거나 DNS 서버는 클러스터의 가상 네트워크의 일부가 아닌 Azure VM 인 경우 작동 하지 않습니다.

### <a name="resolution"></a>해결 방법

1. 클러스터의 일부인 VM에 Ssh를 넣고 명령을 `hostname -f`실행합니다. 이렇게 하면 호스트의 정규화된 도메인 이름(아래 `<host_fqdn>` 지침에서 참조)이 반환됩니다.

1. 그런 다음 명령을 `nslookup <host_fqdn>` 실행합니다(예: `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net`). 이 명령으로 이름이 IP 주소로 확인되면 DNS 서버가 올바르게 작동하고 있습니다. 이 경우 HDInsight를 통해 지원 사례를 제기하고 문제를 조사합니다. 지원 사례에서 실행한 문제 해결 단계를 포함합니다. 이렇게 하면 문제를 더 빨리 해결하는 데 도움이 됩니다.

1. 위의 명령이 IP 주소를 반환하지 않는 `nslookup <host_fqdn> 168.63.129.16` 경우 실행합니다(예: `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net 168.63.129.16`). 이 명령이 IP를 확인할 수 있는 경우 DNS 서버가 Azure의 DNS로 쿼리를 전달하지 않거나 클러스터와 동일한 가상 네트워크의 일부인 VM이 아님을 의미합니다.

1. 클러스터의 가상 네트워크에서 사용자 지정 DNS 서버역할을 할 수 있는 Azure VM이 없는 경우 먼저 이 서버를 추가해야 합니다. DNS 전달자로 구성되는 가상 네트워크에서 VM을 만듭니다.

1. 가상 네트워크에 배포된 VM이 있으면 이 VM에서 DNS 전달 규칙을 구성합니다. 모든 iDNS 이름 확인 요청을 168.63.129.16으로 전달하고 나머지는 DNS 서버로 전달합니다. [다음은](../hdinsight-plan-virtual-network-deployment.md) 사용자 지정 DNS 서버에 대한 이 설정의 예입니다.

1. 이 VM의 IP 주소를 가상 네트워크 DNS 구성의 첫 번째 DNS 항목으로 추가합니다.

---

## <a name="failed-to-connect-to-azure-storage-account"></a>"Azure 저장소 계정에 연결하지 못했습니다."

### <a name="issue"></a>문제

오류 설명에는 "Azure 저장소 계정에 연결하지 못했습니다" 또는 "Azure SQL에 연결하지 못했습니다"가 포함되어 있습니다.

### <a name="cause"></a>원인

Azure 저장소 및 SQL에는 고정IP 주소가 없으므로 이러한 서비스에 액세스할 수 있도록 모든 IP에 대한 아웃바운드 연결을 허용해야 합니다. 정확한 해결 단계는 NSG(네트워크 보안 그룹) 또는 사용자 정의 규칙(UDR)을 설정했는지 여부에 따라 달라집니다. 이러한 구성에 대한 자세한 내용은 [네트워크 보안 그룹 및 사용자 정의 경로와 함께 HDInsight를 사용하여 네트워크 트래픽 제어](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ip) 섹션을 참조하십시오.

### <a name="resolution"></a>해결 방법

* 클러스터에서 [NSG(네트워크 보안 그룹)를](../../virtual-network/virtual-network-vnet-plan-design-arm.md)사용하는 경우.

    Azure 포털로 이동하여 클러스터가 배포되는 서브넷과 연결된 NSG를 식별합니다. **아웃바운드 보안 규칙** 섹션에서 는 제한 없이 인터넷에 대한 아웃바운드 액세스를 허용합니다(우선 **순위가** 작을수록 우선 순위가 더 높다는 의미). 또한 **서브넷** 섹션에서 이 NSG가 클러스터 서브넷에 적용되는지 확인합니다.

* 클러스터에서 [UDR(사용자 정의 경로)을](../../virtual-network/virtual-networks-udr-overview.md)사용하는 경우.

    Azure 포털로 이동하여 클러스터가 배포되는 서브넷과 연결된 경로 테이블을 식별합니다. 서브넷의 경로 테이블을 찾으면 서브넷의 **경로** 섹션을 검사합니다.

    정의된 경로가 있는 경우 클러스터가 배포된 지역의 IP 주소에 대한 경로가 있는지, 각 경로에 대한 **NextHopType이** **인터넷인지**확인합니다. 전술한 문서에 설명된 각 필수 IP 주소에 대해 정의된 경로가 있어야 합니다.

---

## <a name="virtual-network-configuration-is-not-compatible-with-hdinsight-requirement"></a>"가상 네트워크 구성이 HDInsight 요구 사항과 호환되지 않습니다."

### <a name="issue"></a>문제

오류 설명에는 다음과 유사한 메시지가 포함됩니다.

```
ErrorCode: InvalidNetworkConfigurationErrorCode
ErrorDescription: Virtual Network configuration is not compatible with HDInsight Requirement. Error: 'Failed to connect to Azure Storage Account; Failed to connect to Azure SQL; HostName Resolution failed', Please follow https://go.microsoft.com/fwlink/?linkid=853974 to fix it.
```

### <a name="cause"></a>원인

사용자 지정 DNS 설정에 문제가 있는 것일 수 있습니다.

### <a name="resolution"></a>해결 방법

168.63.129.16이 사용자 지정 DNS 체인에 있는지 확인합니다. 가상 네트워크 내에서 DNS 서버는 해당 가상 네트워크 내에서 호스트 이름을 확인하기 위해 Azure의 재귀 확인자에게 DNS 쿼리를 전달할 수 있습니다. 자세한 내용은 [가상 네트워크의 이름 확인](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)을 참조하십시오. Azure의 재귀 확인자에 대한 액세스는 가상 IP 168.63.129.16을 통해 제공됩니다.

1. [ssh 명령을](../hdinsight-hadoop-linux-use-ssh-unix.md) 사용하여 클러스터에 연결합니다. CLUSTERNAME을 클러스터 이름으로 바꿉니다 아래 명령을 편집한 다음 명령을 입력합니다.

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

    결과에 따라 다음 단계 중 하나를 선택합니다.

#### <a name="1686312916-is-not-in-this-list"></a>168.63.129.16이 목록에 없습니다.

**옵션 1**  
[Azure HDInsight에](../hdinsight-plan-virtual-network-deployment.md)대한 가상 네트워크 계획에 설명된 단계를 사용하여 가상 네트워크에 대한 첫 번째 사용자 지정 DNS로 168.63.129.16을 추가합니다. 이러한 단계는 사용자 지정 DNS 서버가 Linux에서 실행되는 경우에만 적용됩니다.

**옵션 2**  
가상 네트워크에 DNS 서버 VM을 배포합니다. 다음 단계를 포함합니다.

* 가상 네트워크에서 VM을 생성하여 DNS 전달자로 구성됩니다(Linux 또는 Windows VM일 수 있음).
* 이 VM에서 DNS 전달 규칙을 구성합니다(모든 iDNS 이름 확인 요청을 168.63.129.16으로 전달하고 나머지는 DNS 서버로 전달).
* 가상 네트워크 DNS 구성에 대한 첫 번째 DNS 항목으로 이 VM의 IP 주소를 추가합니다.

#### <a name="1686312916-is-in-the-list"></a>168.63.129.16이 목록에 있습니다.

이 경우 HDInsight를 사용하여 지원 사례를 만들고 문제를 조사해 보겠습니다. 지원 사례에 아래 명령의 결과를 포함합니다. 이렇게 하면 문제를 더 빨리 조사하고 해결하는 데 도움이 됩니다.

헤드 노드의 ssh 세션에서 편집한 다음 다음을 실행합니다.

```bash
hostname -f
nslookup <headnode_fqdn> (e.g.nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
dig @168.63.129.16 <headnode_fqdn> (e.g. dig @168.63.129.16 hn0-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
```

---

### <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure 커뮤니티 지원을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/community/)

* 연결 [@AzureSupport](https://twitter.com/azuresupport) - Azure 커뮤니티를 올바른 리소스(답변, 지원 및 전문가)에 연결하여 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정과 연결합니다.

* 추가 도움이 필요한 경우 [Azure 포털](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원을** 선택하거나 도움말 + 지원 허브를 **엽니다.** 자세한 내용은 Azure [지원 요청을 만드는 방법을](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)검토합니다. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 기술 지원은 [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 제공됩니다.
