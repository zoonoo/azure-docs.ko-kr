---
title: InvalidNetworkConfigurationErrorCode error - Azure HDInsight
description: Azure HDInsight에서 InvalidNetworkConfigurationErrorCode로 인해 클러스터 만들기가 실패하는 다양한 원인
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/12/2021
ms.openlocfilehash: 1b8b5cba0e25e3be6b668054c9f3d2afaa87925d
ms.sourcegitcommit: 5f785599310d77a4edcf653d7d3d22466f7e05e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2021
ms.locfileid: "108064182"
---
# <a name="cluster-creation-fails-with-invalidnetworkconfigurationerrorcode-in-azure-hdinsight"></a>Azure HDInsight에서 InvalidNetworkConfigurationErrorCode로 인한 클러스터 만들기 실패

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 방법을 설명합니다.

"가상 네트워크 구성이 HDInsight 요구 사항과 호환되지 않습니다."라는 설명과 함께 오류 코드 `InvalidNetworkConfigurationErrorCode`가 표시되면 일반적으로 클러스터의 [가상 네트워크 구성](../hdinsight-plan-virtual-network-deployment.md)에 문제가 있음을 나타냅니다. 나머지 오류 설명에 따라 아래 섹션을 수행하여 문제를 해결합니다.

## <a name="hostname-resolution-failed"></a>"호스트 이름을 확인하지 못했습니다."

### <a name="issue"></a>문제

오류 설명에 "호스트 이름을 확인하지 못했습니다."가 포함되어 있습니다.

### <a name="cause"></a>원인

이 오류는 사용자 지정 DNS 구성의 문제를 가리킵니다. 가상 네트워크 내에서 DNS 서버는 해당 가상 네트워크 내에서 호스트 이름을 확인하기 위해 Azure의 재귀 확인자에게 DNS 쿼리를 전달할 수 있습니다(자세한 내용은 [가상 네트워크의 이름 확인](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) 참조). Azure의 재귀 확인자에 대한 액세스는 가상 IP 168.63.129.16을 통해 제공됩니다. 이 IP는 Azure VM에서만 액세스할 수 있습니다. 따라서 온-프레미스 DNS 서버를 사용하는 경우 또는 DNS 서버가 클러스터의 가상 네트워크에 속하지 않는 Azure VM인 경우에는 작동하지 않습니다.

### <a name="resolution"></a>해결 방법

1. 클러스터의 일부인 VM으로 SSH를 수행하고 `hostname -f` 명령을 실행합니다. 그러면 호스트의 정규화된 도메인 이름이 반환됩니다(아래 지침에서는 `<host_fqdn>`이라고 함).

1. 그런 다음 `nslookup <host_fqdn>` 명령을 실행합니다(예: `nslookup hn*.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net`). 이 명령은 이름을 IP 주소로 확인하는 경우 DNS 서버가 제대로 작동하고 있음을 의미합니다. 이 경우 HDInsight를 사용하여 지원 사례를 제출하면 당사에서 문제를 조사합니다. 지원 사례에 실행한 문제 해결 단계를 포함하세요. 이렇게 하면 문제를 더 빠르게 해결하는 데 도움이 됩니다.

1. 위의 명령에서 IP 주소를 반환하지 않는 경우 `nslookup <host_fqdn> 168.63.129.16`을 실행합니다(예: `nslookup hn*.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net 168.63.129.16`). 이 명령을 사용하여 IP를 확인할 수 있는 경우 DNS 서버에서 Azure의 DNS로 쿼리를 전달하지 않거나 클러스터와 동일한 가상 네트워크의 일부인 VM이 아닌 것을 의미합니다.

1. 클러스터의 가상 네트워크에서 사용자 지정 DNS 서버 역할을 할 수 있는 Azure VM이 없으면 이를 먼저 추가해야 합니다. 가상 네트워크에서 DNS 전달자로 구성될 VM을 만듭니다.

1. 가상 네트워크에 VM을 배포한 후 이 VM에 대한 DNS 전달 규칙을 구성합니다. 모든 iDNS 이름 확인 요청을 168.63.129.16에 전달하고 나머지는 DNS 서버로 전달합니다. 사용자 지정 DNS 서버에 대한 이 설정의 예는 [여기](../hdinsight-plan-virtual-network-deployment.md)를 참조하세요.

1. 이 VM의 IP 주소를 Virtual Network DNS 구성에 대한 첫 번째 DNS 항목으로 추가합니다.

---

## <a name="failed-to-connect-to-azure-storage-account"></a>"Azure Storage 계정에 연결하지 못함”

### <a name="issue"></a>문제

오류 설명에 "Azure Storage 계정에 연결하지 못했습니다." 또는 "Azure SQL에 연결하지 못했습니다."가 포함되어 있습니다.

### <a name="cause"></a>원인

Azure Storage 및 SQL에 고정 IP 주소가 없으므로 이러한 서비스에 대한 액세스를 허용하려면 모든 IP에 대한 아웃바운드 연결을 허용해야 합니다. 정확한 해결 단계는 NSG(네트워크 보안 그룹) 또는 UDR(사용자 정의 규칙)을 설정했는지 여부에 따라 달라집니다. 이러한 구성에 대한 자세한 내용은 [네트워크 보안 그룹 및 사용자 정의 경로를 사용하여 HDInsight에서 네트워크 트래픽 제어](../control-network-traffic.md) 섹션을 참조하세요.

### <a name="resolution"></a>해결 방법

* 클러스터에서 [NSG(네트워크 보안 그룹)](../../virtual-network/virtual-network-vnet-plan-design-arm.md)를 사용하는 경우.

    Azure Portal로 이동하여 클러스터를 배포하려는 서브넷과 연결된 NSG를 식별합니다. **아웃바운드 보안 규칙** 섹션에서 인터넷에 대한 아웃바운드 액세스를 제한 없이 허용합니다. 여기서 **우선 순위** 번호가 작을수록 우선 순위가 높음을 의미합니다. 또한 **서브넷** 섹션에서 이 NSG가 클러스터 서브넷에 적용되었는지 확인합니다.

* 클러스터에서 [UDR(사용자 정의 경로)](../../virtual-network/virtual-networks-udr-overview.md)을 사용하는 경우.

    Azure Portal로 이동하여 클러스터를 배포하려는 서브넷과 연결된 경로 테이블을 식별합니다. 서브넷에 대한 경로 테이블을 찾으면 서브넷의 **경로** 섹션을 검사합니다.

    정의된 경로가 있는 경우 클러스터가 배포된 지역의 IP 주소에 대한 경로가 있는지 확인하고 각 경로에 대한 **NextHopType** 이 **인터넷** 인지 확인합니다. 앞서 언급한 문서에 설명된 각 필수 IP 주소에 대해 정의된 경로가 있어야 합니다.

## <a name="failed-to-establish-an-outbound-connection-from-the-cluster-for-the-communication-with-the-hdinsight-resource-provider-please-ensure-that-outbound-connectivity-is-allowed"></a>"HDInsight 리소스 공급자와의 통신을 위해 클러스터에서 아웃바운드 연결을 설정하지 못했습니다. 아웃바운드 연결이 허용되는지 확인하세요."

### <a name="issue"></a>문제

오류 설명에 "HDInsight 리소스 공급자와의 통신을 위해 클러스터에서 아웃바운드 연결을 설정하지 못했습니다. 아웃바운드 연결이 허용되는지 확인하세요."

### <a name="cause"></a>원인

Private Link HDInsight 클러스터를 사용하는 경우 HDInsight 리소스 공급자에 대한 연결을 허용하도록 클러스터에서 아웃바운드 액세스를 구성해야 합니다.

### <a name="resolution"></a>해결 방법

* 이 문제를 해결하려면 [Private Link 설정](../hdinsight-private-link.md)에서 HDInsight Private Link 설정 단계를 참조하세요.
---

## <a name="virtual-network-configuration-is-not-compatible-with-hdinsight-requirement"></a>"Virtual Network 구성이 HDInsight 요구 사항과 호환되지 않습니다."

### <a name="issue"></a>문제

오류 설명에 다음과 비슷한 메시지가 포함됩니다.

```
ErrorCode: InvalidNetworkConfigurationErrorCode
ErrorDescription: Virtual Network configuration is not compatible with HDInsight Requirement. Error: 'Failed to connect to Azure Storage Account; Failed to connect to Azure SQL; HostName Resolution failed', Please follow https://go.microsoft.com/fwlink/?linkid=853974 to fix it.
```

### <a name="cause"></a>원인

사용자 지정 DNS 설정에 문제가 있을 수 있습니다.

### <a name="resolution"></a>해결 방법

168.63.129.16이 사용자 지정 DNS 체인에 있는지 확인합니다. 가상 네트워크 내에서 DNS 서버는 해당 가상 네트워크 내에서 호스트 이름을 확인하기 위해 Azure의 재귀 확인자에게 DNS 쿼리를 전달할 수 있습니다. 자세한 내용은 [가상 네트워크의 이름 확인](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)을 참조하세요. Azure의 재귀 확인자에 대한 액세스는 가상 IP 168.63.129.16을 통해 제공됩니다.

1. [ssh command](../hdinsight-hadoop-linux-use-ssh-unix.md) 명령을 사용하여 클러스터에 연결합니다. CLUSTERNAME을 클러스터 이름으로 바꿔서 아래 명령을 편집하고, 다음 명령을 입력합니다.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 다음 명령을 실행합니다.

    ```bash
    cat /etc/resolv.conf | grep nameserver*
    ```

    다음과 비슷한 결과가 표시됩니다.

    ```output
    nameserver 168.63.129.16
    nameserver 10.21.34.43
    nameserver 10.21.34.44
    ```

    결과에 따라 다음 단계 중 하나를 선택하여 수행합니다.

#### <a name="1686312916-is-not-in-this-list"></a>168.63.129.16이 이 목록에 없음

**옵션 1**  
[Azure HDInsight에 대한 가상 네트워크 계획](../hdinsight-plan-virtual-network-deployment.md)에 설명된 단계를 사용하여 가상 네트워크의 첫 번째 사용자 지정 DNS로 168.63.129.16을 추가합니다. 이러한 단계는 사용자 지정 DNS 서버가 Linux에서 실행되는 경우에만 적용됩니다.

**옵션 2**  
가상 네트워크에 대한 DNS 서버 VM을 배포합니다. 다음 단계를 포함합니다.

* 가상 네트워크에 VM을 만듭니다. 이 VM은 DNS 전달자로 구성됩니다(Linux 또는 Windows VM일 수 있음).
* 이 VM에 대한 DNS 전달 규칙을 구성합니다(모든 iDNS 이름 확인 요청을 168.63.129.16에 전달하고 나머지는 DNS 서버로 전달).
* Virtual Network DNS 구성의 첫 번째 DNS 항목으로 이 VM의 IP 주소를 추가합니다.

#### <a name="1686312916-is-in-the-list"></a>168.63.129.16이 목록에 있음

이 경우 HDInsight를 사용하여 지원 사례를 만들면 당사에서 문제를 조사합니다. 지원 사례에 아래 명령의 결과를 포함합니다. 이렇게 하면 문제를 신속하게 조사하고 해결하는 데 도움이 됩니다.

헤드 노드의 SSH 세션에서 다음을 편집한 후 실행합니다.

```bash
hostname -f
nslookup <headnode_fqdn> (e.g.nslookup hn*.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
dig @168.63.129.16 <headnode_fqdn> (e.g. dig @168.63.129.16 hn*.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
```
### <a name="cause"></a>원인

이 `InvalidNetworkConfigurationErrorCode` 오류 코드의 또 다른 원인은 PowerShell 또는 Azure Runbook에서 더 이상 사용되지 않는 매개 변수 `EnableVmProtection`을 사용하기 때문일 수 있습니다.

### <a name="resolution"></a>해결 방법

[Az PowerShell SDK](/powershell/module/az.network/get-azvirtualnetwork)에 설명된 대로 `Get-AzVirtualNetwork`에 유효한 매개 변수를 사용합니다.

---

### <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [Azure 커뮤니티 지원](https://azure.microsoft.com/support/community/)을 통해 Azure 전문가로부터 답변을 얻습니다.

* [@AzureSupport](https://twitter.com/azuresupport)를 사용하여 연결 – Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결하여 고객 환경을 개선하는 공식 Microsoft Azure 계정입니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](../../azure-portal/supportability/how-to-create-azure-support-request.md)을 참조하세요. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 [Azure 지원 플랜](https://azure.microsoft.com/support/plans/) 중 하나를 통해 기술 지원이 제공됩니다.
