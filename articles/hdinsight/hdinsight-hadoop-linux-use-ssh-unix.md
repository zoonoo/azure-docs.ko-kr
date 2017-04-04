---
title: "Windows, Linux, Unix 또는 OS X에서 HDInsight(Hadoop)과 함께 SSH 사용 | Microsoft Docs"
description: " SSH(보안 셸)를 사용하여 HDInsight에 액세스할 수 있습니다. 이 문서에서는 Windows, Linux, Unix 또는 OS X 클라이언트에서 HDInsight와 함께 연결하기 위해 SSH를 사용하는 방법을 설명합니다."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: a6a16405-a4a7-4151-9bbf-ab26972216c5
ms.service: hdinsight
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/16/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 89d44476e9de8ac32195efaf66535cdd9fb4260e
ms.lasthandoff: 03/25/2017

---
# <a name="connect-to-hdinsight-hadoop-using-ssh"></a>SSH를 사용하여 HDInsight(Hadoop)에 연결

[SSH (보안 셸)](https://en.wikipedia.org/wiki/Secure_Shell)을 사용하여 HDInsight에 안전하게 연결하는 방법을 알아봅니다. HDInsight는 클러스터 내에서 노드에 대한 운영 체제로 Linux(Ubuntu)를 사용할 수 있습니다. Linux 기반 클러스터의 헤드 및 에지 노드에 연결하고 해당 노드에서 직접 명령을 실행하는 데 SSH를 사용할 수 있습니다.

다음 테이블에서는 SSH를 사용하여 HDInsight에 연결할 때 필요한 주소와 포트 정보를 포함합니다.

| 주소 | 포트 | 다음에 연결... |
| ----- | ----- | ----- |
| `<edgenodename>.<clustername>-ssh.azurehdinsight.net` | 22 | 에지 노드(존재하는 경우) |
| `<clustername>-ssh.azurehdinsight.net` | 22 | 기본 헤드 노드 |
| `<clustername>-ssh.azurehdinsight.net` | 23 | 보조 헤드 노드 |

> [!NOTE]
> `<edgenodename>`을 에지 노드의 이름으로 바꿉니다. 에지 노드를 사용하는 방법에 대한 자세한 내용은 [HDInsight에서 에지 노드 사용](hdinsight-apps-use-edge-node.md#access-an-edge-node)을 참조하세요.
>
> `<clustername>` 을 HDInsight 클러스터 이름으로 바꿉니다.
>
> 에지 노드가 있는 경우 __에지 노드에 항상 연결__하는 것이 좋습니다. 헤드 노드는 클러스터의 상태에 중요한 서비스를 호스트합니다. 에지 노드는 배치한 내용을 실행합니다.

## <a name="ssh-clients"></a>SSH 클라이언트

대부분의 운영 체제는 `ssh` 클라이언트를 제공합니다. Microsoft Windows는 기본적으로 SSH 클라이언트를 제공하지 않습니다. Windows용 SSH 클라이언트는 다음 패키지에서 각각 제공됩니다.

* [Windows 10의 Ubuntu에 있는 Bash](https://msdn.microsoft.com/commandline/wsl/about):는 `ssh` 명령은 Windows 명령줄에서 Bash를 통해 제공됩니다.

* [Git(https://git-scm.com/)](https://git-scm.com/): `ssh` 명령은 GitBash 명령줄을 통해 제공됩니다.

* [GitHub 데스크탑(https://desktop.github.com/)](https://desktop.github.com/): `ssh` 명령은 Git Shell 명령줄을 통해 제공됩니다. GitHub 데스크탑은 Bash, Windows 명령 프롬프트 또는 PowerShell을 Git Shell의 명령줄로 사용하여 구성될 수 있습니다.

* [OpenSSH(https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH): PowerShell 팀은 OpenSSH를 Windows에 이식하여 테스트 릴리스를 제공합니다.

    > [!WARNING]
    > OpenSSH 패키지는 SSH 서버 구성 요소 `sshd`를 포함합니다. 이 구성 요소는 다른 사용자가 연결할 수 있도록 시스템에서 SSH 서버를 시작합니다. 시스템에서 SSH 서버를 호스트하려는 것이 아니라면 이 구성 요소를 구성하거나 포트 22를 열지 않습니다. HDInsight와 통신할 필요가 없습니다.

[PuTTY(http://www.chiark.greenend.org.uk/~sgtatham/putty/)](http://www.chiark.greenend.org.uk/~sgtatham/putty/) 및 [MobaXterm(http://mobaxterm.mobatek.net/)](http://mobaxterm.mobatek.net/)과 같은 몇 가지 그래픽 SSH 클라이언트가 있습니다. 이러한 클라이언트를 사용하여 HDInsight에 연결할 수 있는 반면 서버에 연결하는 프로세스는 `ssh` 유틸리티를 사용하는 것과 다릅니다. 자세한 내용은 사용하는 그래픽 클라이언트의 설명서를 참조하세요.

## <a id="sshkey"></a>인증: SSH 키

SSH 키는 [공개 키 암호화](https://en.wikipedia.org/wiki/Public-key_cryptography)를 사용하여 클러스터를 보호합니다. SSH 키는 암호보다 더 안전하고 HDInsight 클러스터를 보호하는 쉬운 방법을 제공합니다.

키를 사용하여 SSH 계정을 보호한 경우 클라이언트는 연결할 때 일치하는 개인 키를 제공해야 합니다.

* 대부분의 클라이언트는 __기본 키__를 사용하도록 구성될 수 있습니다. 예를 들어 `ssh` 클라이언트는 Linux 및 Unix 환경의 `~/.ssh/id_rsa`에서 개인 키를 찾습니다.

* __개인 키에 대한 경로__를 지정할 수 있습니다. `ssh` 클라이언트에서 `-i` 매개 변수를 사용하여 개인 키에 대한 경로를 지정합니다. 예: `ssh -i ~/.ssh/hdinsight sshuser@myedge.mycluster-ssh.azurehdinsight.net`

* 서로 다른 서버에서 사용하기 위한 __여러 개인 키__가 있는 경우 [ssh-agent(https://en.wikipedia.org/wiki/Ssh-agent)](https://en.wikipedia.org/wiki/Ssh-agent)와 같은 유틸리티를 사용하여 자동으로 사용할 키를 선택할 수 있습니다.

> [!IMPORTANT]
>
> 암호를 사용하여 개인 키를 보호하는 경우 키를 사용할 때 암호를 입력해야 합니다. `ssh-agent`와 같은 유틸리티는 편의상 암호를 캐시할 수 있습니다.

### <a name="create-an-ssh-key-pair"></a>SSH 키 쌍 만들기

`ssh-keygen` 명령을 사용하여 공용 및 개인 키 파일을 만듭니다. 다음 명령은 HDInsight와 함께 사용할 수 있는 2048비트 RSA 키 쌍을 생성합니다.

    ssh-keygen -t rsa -b 2048

키 생성 프로세스 중에 정보를 묻는 메시지가 나타납니다. 예를 들어, 키가 저장되는 위치 또는 암호를 사용할지 여부를 묻습니다. 프로세스가 완료된 후에 공개 키와 개인 키라는 두 개의 파일이 만들어집니다.

* __공개 키__는 HDInsight 클러스터를 만드는 데 사용됩니다. 공개 키의 확장명은 `.pub`입니다.

* __개인 키__는 HDInsight 클러스터에 클라이언트를 인증하는 데 사용됩니다.

> [!IMPORTANT]
> 암호를 사용하여 키를 보호할 수 있습니다. 이것이 사용자의 개인 키의 암호입니다. 누군가 사용자의 개인 키를 얻게 되더라도 키를 사용하기 위해 암호가 있어야 합니다.

### <a name="create-hdinsight-using-the-public-key"></a>공용 키를 사용하여 HDInsight 만들기

| 생성 방법 | 공개 키를 사용하는 방법 |
| ------- | ------- |
| **Azure 포털** | __클러스터 로그인으로 동일한 암호 사용__의 선택을 취소한 다음 __공개 키__를 SSH 인증 유형으로 선택합니다. 마지막으로 공개 키 파일을 선택하거나 __SSH 공개 키__ 필드에 파일의 텍스트 내용을 붙여 넣습니다.</br>![HDInsight 클러스터 생성의 SSH 공개 키 대화 상자](./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-public-key.png) |
| **Azure PowerShell** | `New-AzureRmHdinsightCluster` cmdlet의 `-SshPublicKey` 매개 변수를 사용하여 공개 키의 내용을 문자열로 전달합니다.|
| **Azure CLI 1.0** | `azure hdinsight cluster create` 명령의 `--sshPublicKey` 매개 변수를 사용하여 공개 키의 내용을 문자열로 전달합니다. |
| **Resource Manager 템플릿** | 템플릿에서 SSH 키를 사용하는 예제는 [SSH 키를 사용하여 Linux에서 HDInsight 배포](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-publickey/)를 참조하세요. [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-publickey/azuredeploy.json) 파일의 `publicKeys` 요소는 클러스터를 만들 때 Azure에 키를 전달하는 데 사용됩니다. |

## <a id="sshpassword"></a>인증: 암호

SSH 계정은 암호를 사용하여 보호될 수 있습니다. SSH를 사용하여 HDInsight에 연결할 경우 암호를 입력하라는 메시지가 표시됩니다.

> [!WARNING]
> SSH에 대한 암호 인증을 사용하는 것은 권장되지 않습니다. 암호는 추측할 수 있고 무차별 암호 대입 공격에 취약합니다. 대신 [인증하기 위한 SSH 키](#sshkey)를 사용하는 것이 좋습니다.

### <a name="create-hdinsight-using-a-password"></a>암호를 사용하여 HDInsight 만들기

| 생성 방법 | 암호를 지정하는 방법 |
| --------------- | ---------------- |
| **Azure 포털** | 기본적으로 SSH 사용자 계정에는 클러스터 로그인 계정인 동일한 암호가 있습니다. 다른 암호를 사용하려면 __클러스터 로그인으로 동일한 암호 사용__의 선택을 취소한 다음 __SSH 암호__ 필드에 암호를 입력합니다.</br>![HDInsight 클러스터 생성의 SSH 암호 대화 상자](./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-password.png)|
| **Azure PowerShell** | `New-AzureRmHdinsightCluster` cmdlet의 `--SshCredential` 매개 변수를 사용하고 SSH 사용자 계정 이름 및 암호를 포함하는 `PSCredential` 개체를 전달합니다. |
| **Azure CLI 1.0** | `azure hdinsight cluster create` 명령의 `--sshPassword` 매개 변수를 사용하여 암호 값을 제공합니다. |
| **Resource Manager 템플릿** | 템플릿에서 암호를 사용하는 예제는 [SSH 암호를 사용하여 Linux에서 HDInsight 배포](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-password/)를 참조하세요. [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-password/azuredeploy.json) 파일의 `linuxOperatingSystemProfile` 요소는 클러스터를 만들 때 Azure에 SSH 계정 이름 및 암호를 전달하는 데 사용됩니다.|

### <a name="change-the-ssh-password"></a>SSH 암호 변경

SSH 사용자 계정 암호를 변경하는 방법에 대한 내용은 [HDInsight 관리](hdinsight-administer-use-portal-linux.md#change-passwords) 문서의 __암호 변경__ 섹션을 참조하세요.

## <a id="domainjoined"></a>인증: 도메인에 조인된 HDInsight

__도메인에 조인된 HDInsight 클러스터__를 사용하는 경우 SSH와 연결한 후에 `kinit` 명령을 사용해야 합니다. 이 명령은 도메인 사용자 및 암호를 묻는 메시지를 표시하고 클러스터와 연결된 Azure Active Directory 도메인을 사용하여 세션을 인증합니다.

자세한 내용은 [도메인에 조인된 HDInsight 구성](hdinsight-domain-joined-configure.md)을 참조하세요.

## <a name="connect-to-worker-and-zookeeper-nodes"></a>작업자 및 Zookeeper 노드에 연결

작업자 노드와 Zookeeper 노드는 인터넷에서 직접 액세스할 수 없으며, 클러스터 헤드 노드 또는 에지 노드에서 액세스할 수 있습니다. 다음은 다른 노드에 연결하는 일반적인 단계입니다.

1. SSH를 사용하여 헤드 또는 가장자리 노드에 연결:

        ssh sshuser@myedge.mycluster-ssh.azurehdinsight.net

2. 헤드 또는 가장자리 노드에 대한 SSH 연결에서는 `ssh` 명령을 사용하여 클러스터의 작업자 노드에 연결합니다.

        ssh sshuser@wn0-myhdi

    클러스터에서 노드의 도메인 이름 목록을 검색하려면 [Ambari REST API를 사용하여 HDInsight 관리](hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes) 문서에 있는 예제를 참조하세요.

SSH 계정이 __암호__를 사용하여 보호되는 경우 암호를 입력하라는 메시지가 표시되고 연결이 설정됩니다.

__SSH 키__를 사용하여 SSH 계정을 보호할 경우 로컬 환경이 SSH 에이전트 전달에 맞게 구성되었는지 확인해야 합니다.

> [!NOTE]
> 클러스터에서 모든 노드에 직접 액세스하는 다른 방법은 Azure Virtual Network에 HDInsight를 설치하는 것입니다. 그런 다음 동일한 가상 네트워크에 원격 컴퓨터를 조인하고 클러스터에 있는 모든 노드에 직접 액세스할 수 있습니다.
>
> 자세한 내용은 [HDInsight와 함께 가상 네트워크 사용](hdinsight-extend-hadoop-virtual-network.md)을 참조하십시오.

### <a name="configure-ssh-agent-forwarding"></a>SSH 에이전트 전달 구성

> [!IMPORTANT]
> 다음 단계에서는 Linux/UNIX 기반 시스템이며 Bash on Windows 10으로 작업한다고 가정합니다. 이 단계가 시스템에서 작동하지 않는다면 해당 SSH 클라이언트에 대한 설명서를 참조해야 할 수 있습니다.

1. 텍스트 편집기를 사용하여 `~/.ssh/config`를 엽니다. 이 파일이 존재하지 않으면 명령줄에 `touch ~/.ssh/config`를 입력하여 만들 수 있습니다.

2. `config` 파일에 다음 텍스트를 추가합니다.

        Host <edgenodename>.<clustername>-ssh.azurehdinsight.net
          ForwardAgent yes

    __호스트__ 정보를 SSH를 사용하여 연결한 노드의 주소로 바꿉니다. 이전 예제에서는 에지 노드를 사용합니다. 이 항목은 지정된 노드에 대한 SSH 에이전트 전달을 구성합니다.

3. 터미널에서 다음 명령을 사용하여 SSH 에이전트 전달을 테스트합니다.

        echo "$SSH_AUTH_SOCK"

    이 명령은 다음 텍스트와 유사한 정보를 반환합니다.

        /tmp/ssh-rfSUL1ldCldQ/agent.1792

    아무 것도 반환되지 않으면 `ssh-agent`가 실행되지 않는 것입니다. `ssh-agent` 설치 및 구성에 관한 특정 단계는 [ssh에서 ssh-agent 사용(http://mah.everybody.org/docs/ssh)](http://mah.everybody.org/docs/ssh)의 에이전트 시작 스크립트나 SSH 클라이언트 설명서를 참조하십시오.

4. **ssh-agent** 가 실행 중인지 확인한 후에는 다음을 사용하여 SSH 개인 키를 에이전트에 추가합니다.

        ssh-add ~/.ssh/id_rsa

    개인 키가 다른 파일에 저장된 경우 `~/.ssh/id_rsa` 를 해당 파일의 경로로 바꿉니다.

5. SSH를 사용하여 클러스터 에지 노드 또는 헤드 노드에 연결합니다. SSH 명령을 사용하여 작업자 또는 zookeeper 노드에 연결합니다. 전달된 키를 사용하여 연결이 설정됩니다.

## <a name="next-steps"></a>다음 단계

* [HDInsight와 함께 SSH 터널링 사용](hdinsight-linux-ambari-ssh-tunnel.md)
* [HDInsight와 함께 가상 네트워크 사용](hdinsight-extend-hadoop-virtual-network.md)
* [HDInsight에서 에지 노드 사용](hdinsight-apps-use-edge-node.md#access-an-edge-node)
