---
title: "Windows, Linux, Unix 또는 OS X에서 HDInsight(Hadoop)과 함께 SSH 사용 | Microsoft Docs"
description: " SSH(보안 셸)를 사용하여 HDInsight에 액세스할 수 있습니다. 이 문서에서는 Windows, Linux, Unix 또는 OS X 클라이언트에서 HDInsight와 함께 SSH를 사용하는 방법을 설명합니다.."
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
ms.date: 02/27/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cfaade8249a643b77f3d7fdf466eb5ba38143f18
ms.openlocfilehash: 4cde035f75bfa3c448f12e9ebf2896b9a54a6873
ms.lasthandoff: 03/01/2017

---
# <a name="use-ssh-with-hdinsight-hadoop-from-bash-on-windows-10-linux-unix-or-os-x"></a>Windows 10, Linux, Unix 또는 OS X의 Bash에서 HDInsight(Hadoop)와 함께 SSH 사용

> [!div class="op_single_selector"]
> * [PuTTY(Windows)](hdinsight-hadoop-linux-use-ssh-windows.md)
> * [SSH(Windows, Linux, Unix, OS X)](hdinsight-hadoop-linux-use-ssh-unix.md)

[SSH(보안 셸)](https://en.wikipedia.org/wiki/Secure_Shell)를 사용하여 Linux 기반 HDInsight 클러스터에 로그인하고 명령줄 인터페이스로 명령을 실행할 수 있습니다. 이 문서에서는 SSH에 대한 기본 정보와, HDInsight에서 SSH를 사용하기 위한 특정 정보를 제공합니다.

## <a name="what-is-ssh"></a>SSH 정의

SSH는 비보안 네트워크를 통해 원격 서버와 완전하게 통신할 수 있는 암호화 네트워크 프로토콜입니다. SSH는 원격 서버에 보안 명령줄 로그인을 제공하는 데 사용됩니다. 이 경우, HDInsight 클러스터의 헤드 노드 또는 가장자리 노드입니다.

SSH를 사용하여 네트워크 트래픽을 클라이언트에서 HDInsight 클러스터로 터널링할 수도 있습니다. 터널을 사용하여 인터넷에 직접 노출되지 않는 HDInsight 클러스터의 서비스에 액세스할 수 있습니다. HDInsight에서의 SSH 사용에 대한 자세한 내용은 [HDInsight에서 SSH 터널링 사용](hdinsight-linux-ambari-ssh-tunnel.md)을 참조하세요.

## <a name="ssh-clients"></a>SSH 클라이언트

많은 운영 체제에서는 `ssh` 및 `scp` 명령줄 유틸리티를 통해 SSH 클라이언트 기능을 제공합니다.

* __ssh__: 원격 명령줄 세션을 수립하고 터널을 만드는 데 사용할 수 있는 일반 SSH 클라이언트
* __scp__: SSH 프로토콜을 사용하여 로컬 및 원격 시스템 간에 파일을 복사하는 유틸리티.

Windows 10 1주년 버전에는 Bash가 개발자 기능으로 제공됩니다. 여기에는 `ssh`, `scp` 및 기타 Linux 명령이 제공됩니다. Bash on Windows 10 사용에 대한 자세한 내용은 [Ubuntu on Windows의 Bash](https://msdn.microsoft.com/commandline/wsl/about)를 참조하세요.

Windows를 사용하며 Bash on Windows 10 액세스 권한이 없는 경우 다음과 같은 SSH 클라이언트를 사용할 수 있습니다.

* [Git For Windows](https://git-for-windows.github.io/): `ssh` 및 `scp` 명령줄 유틸리티를 제공합니다.
* [Cygwin](https://cygwin.com/): `ssh` 및 `scp` 명령줄 유틸리티를 제공합니다.

> [!NOTE]
> 이 문서의 단계에서는 `ssh` 명령에 대한 액세스 권한이 있다고 가정합니다. PuTTY 또는 MobaXterm 등의 클라이언트를 사용할 경우 해당 제품의 설명서에서 상응하는 명령 및 매개 변수를 참조하세요.

## <a name="ssh-authentication"></a>SSH 인증 

암호 또는 [공개 키 암호화(https://en.wikipedia.org/wiki/Public-key_cryptography)](https://en.wikipedia.org/wiki/Public-key_cryptography)를 통해 SSH 연결을 인증할 수 있습니다. 키는 암호와 달리 여러 공격에 취약하지 않으므로 키를 사용하는 것이 가장 안전한 옵션입니다. 그러나 키는 암호보다 만들고 관리하기가 훨씬 복잡합니다.

공개 키 암호화를 사용할 때는 _공개_ 및 _개인_ 키 쌍을 만듭니다.

* **공개 키**는 HDInsight 클러스터의 노드나, 사용자가 공개 키 암호화에 사용하려는 다른 서비스에 로드됩니다.

* **개인 키**는 신원을 확인하기 위해 SSH 클라이언트를 사용하여 로그인할 때 HDInsight 클러스터에 제시하는 항목입니다. 이 개인 키는 보호해야 하는 한편, 공유하면 안됩니다.

    개인 키에 대한 암호를 만들어 추가적인 보안을 더할 수 있습니다. 암호를 사용하는 경우 SSH를 사용하여 인증할 때 암호를 입력해야 합니다.

### <a name="create-a-public-and-private-key"></a>공개 키 및 개인 키 만들기

`ssh-keygen` 유틸리티는 HDInsight에서 사용할 공개 키 및 개인 키를 만드는 가장 쉬운 방법입니다. 명령줄에서 다음 명령을 사용하여 HDInsight에 사용할 새 키 쌍을 만듭니다.

> [!NOTE]
> MobaXTerm 또는 puTTY 같은 GUI SSH 클라이언트를 사용할 경우 키 생성 방법은 해당 클라이언트의 설명서를 참조하세요.

    ssh-keygen -t rsa -b 2048

다음 정보를 묻는 메시지가 나타납니다.

* 파일 위치: 위치는 기본적으로 `~/.ssh/id_rsa`입니다.

* 선택적 암호: 암호를 입력한 경우 HDInsight 클러스터에 인증할 때 다시 입력해야 합니다.

> [!IMPORTANT]
> 암호는 개인 키에 대한 암호입니다. 개인 키를 사용하여 인증할 때는 먼저 암호를 입력해야 키를 사용할 수 있습니다. 다른 사람이 개인 키를 알게 되더라도 암호 없이는 사용할 수 없습니다.
>
> 암호를 잊은 경우에는 복구하거나 재설정할 방법이 없습니다.

명령을 마친 후에는 다음 두 파일이 새로 생깁니다.

* __id\_rsa__: 이 파일에는 개인 키가 들어 있습니다.

    > [!WARNING]
    > 공개 키로 보호되는 서비스에 대한 무단 액세스를 방지하기 위해 이 파일에 대한 액세스를 제한합니다.

* __id\_rsa.pub__: 이 파일에는 공개 키가 들어 있습니다. HDInsght 클러스터를 만들 때 이 파일을 사용합니다.

    > [!NOTE]
    > _공개_ 키에 누가 액세스할 수 있는지는 중요하지 않습니다. 자체적으로 모든 공개 키는 개인 키를 확인할 수 있습니다. SSH 서버와 같은 서비스는 개인 키를 사용하여 인증할 때 신원 확인을 위해 공개 키를 사용합니다.

## <a name="configure-ssh-on-hdinsight"></a>HDInsight에서 SSH 구성

Linux 기반 HDInsight 클러스터를 만들 때는 _SSH 사용자 이름_과, _암호_ 또는 _공개 키_ 중 하나를 제공해야 합니다. 클러스터를 만들 때 이 정보를 사용하여 HDInsight 클러스터 노드에서 로그인을 만듭니다. 암호나 공개 키는 사용자 계정 보호에 사용됩니다.

클러스터를 만들 때 SSH 구성에 대한 자세한 내용은 다음 문서 중 하나를 참조하세요.

* [Azure Portal을 사용하여 HDInsight 만들기](hdinsight-hadoop-create-linux-clusters-portal.md)
* [Azure CLI를 사용하여 HDInsight 만들기](hdinsight-hadoop-create-linux-clusters-azure-cli.md)
* [Azure PowerShell을 사용하여 HDInsight 만들기](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Azure Resource Manager 템플릿을 사용하여 HDInsight 만들기](hdinsight-hadoop-create-linux-clusters-arm-templates.md)
* [.NET SDK를 사용하여 HDInsight 만들기](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)
* [REST를 사용하여 HDInsight 만들기](hdinsight-hadoop-create-linux-clusters-curl-rest.md)

### <a name="additional-ssh-users"></a>추가 SSH 사용자

추가 SSH 사용자를 만든 후 클러스터에 추가할 수는 있지만 권장되는 방법은 아닙니다.

* 새 SSH 사용자가 클러스터의 각 노드에 추가되어야 합니다.

* 새 SSH 사용자는 기본 사용자와 동일한 HDInsight 액세스 권한을 같습니다. SSH 사용자 계정을 기반으로 HDInsight의 데이터나 작업에 대한 액세스를 제한할 방법은 없습니다.

사용자별로 액세스를 제한하려면 도메인 가입 HDInsight 클러스터를 사용해야 합니다. 도메인 가입 HDInsight 클러스터는 Active Directory를 사용하여 클러스터 리소스 액세스를 제어합니다.

도메인 가입 HDInsight 클러스터를 사용하면 SSH에 연결한 후 Active Directory를 사용하여 인증할 수 있습니다. 여러 사용자가 SSH를 사용하여 연결한 후 자신의 Active Directory 계정에 인증할 수 있습니다. 자세한 내용은 [도메인 가입 HDInsight](#domainjoined) 섹션을 참조하세요.

##<a name="a-idconnecta-connect-to-hdinsight"></a><a id="connect"></a>HDInsight에 연결

HDInsight 클러스터의 모든 노드에서 SSH 서버를 실행하는 동안에는 공용 인터넷을 통해 헤드 노드나 가장자리 노드에만 연결할 수 있습니다.

* _헤드 노드_ 연결에는 `CLUSTERNAME-ssh.azurehdinsight.net`을 사용합니다. 여기서 __CLUSTERNAME__은 HDInsight 클러스터의 이름입니다. 포트 22에서 연결하면(SSH 기본값) 기본 헤드 노드에 연결합니다. 포트 23은 보조 헤드 노드에 연결합니다.

* _가장자리 노드_에 연결하려면 `EDGENAME.CLUSTERNAME-ssh.azurehdinsight.net`을 사용합니다. 여기서 __EDGENAME__은 가장자리 노드의 이름이고 __CLUSTERNAME__은 HDInsight 클러스터의 이름입니다. 가장자리 노드에 연결할 때는 포트 22를 사용합니다.

다음 예제에서는 SSH 사용자 이름 __sshuser__를 사용하여 이름이 __myhdi__인 클러스터의 헤드 노드와 가장자리 노드에 연결하는 방법을 설명합니다. 가장자리 노드의 이름은 __myedge__입니다.

| 원하는 작업.. | 사용 기능... |
| ----- | ----- |
| 기본 헤드 노드에 연결 | `ssh sshuser@myhdi-ssh.azurehdinsight.net` |
| 보조 헤드 노드에 연결 | `ssh -p 23 sshuser@myhdi-ssh.azurehdinsight.net` |
| 에지 노드에 연결 | `ssh sshuser@edge.myhdi-ssh.azurehdinsight.net` |

암호를 사용하여 SSH 계정을 보호할 경우 암호를 입력하라는 메시지가 나타납니다.

공개 키를 사용하여 SSH 계정을 보호할 경우 `-i` 스위치로 일치하는 개인 키의 경로를 지정해야 할 수 있습니다. 다음 예제에서는 `-i` 스위치 사용을 보여 줍니다.

    ssh -i /path/to/public.key sshuser@myhdi-ssh.azurehdinsight.net

### <a name="connect-to-other-nodes"></a>다른 노드에 연결

작업자 노드와 Zookeeper 노드는 클러스터 외부에서 직접 액세스할 수 없으며, 클러스터 헤드 노드 또는 가장자리 노드에서 액세스할 수 있습니다. 다음은 다른 노드에 연결하는 일반적인 단계입니다.

1. SSH를 사용하여 헤드 또는 가장자리 노드에 연결:

        ssh sshuser@myhdi-ssh.azurehdinsight.net

2. 헤드 또는 가장자리 노드에 대한 SSH 연결에서는 `ssh` 명령을 사용하여 클러스터의 작업자 노드에 연결합니다.

        ssh sshuser@wn0-myhdi

    클러스터에서 작업자 노드 목록을 검색하려면 [Ambari REST API를 사용하여 HDInsight 관리](hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes) 문서에서 클러스터 노드의 정규화된 도메인 이름 검색 방법 예제를 참조하세요.

SSH 계정이 암호로 보호되는 경우 암호를 입력하라는 메시지가 표시되고 연결이 설정됩니다.

SSH 키를 사용하여 사용자 계정을 인증할 경우 로컬 환경이 SSH 에이전트 전달에 맞게 구성되었는지 확인해야 합니다.

> [!IMPORTANT]
> 다음 단계에서는 Linux/UNIX 기반 시스템이며 Bash on Windows 10으로 작업한다고 가정합니다. 이 단계가 시스템에서 작동하지 않는다면 해당 SSH 클라이언트에 대한 설명서를 참조해야 할 수 있습니다.

1. 텍스트 편집기를 사용하여 `~/.ssh/config`를 엽니다. 이 파일이 존재하지 않으면 명령줄에 `touch ~/.ssh/config`를 입력하여 만들 수 있습니다.

2. 파일에 다음을 추가합니다. *CLUSTERNAME* 을 HDInsight 클러스터의 이름으로 바꿉니다.

        Host CLUSTERNAME-ssh.azurehdinsight.net
          ForwardAgent yes

    이렇게 입력하면 HDInsight 클러스터에 대한 SSH 에이전트 전달이 구성됩니다.

3. 터미널에서 다음 명령을 사용하여 SSH 에이전트 전달을 테스트합니다.

        echo "$SSH_AUTH_SOCK"

    이 명령은 다음 텍스트와 유사한 정보를 반환합니다.

        /tmp/ssh-rfSUL1ldCldQ/agent.1792

    아무 것도 반환되지 않으면 `ssh-agent`가 실행되지 않는 것입니다. `ssh-agent` 설치 및 구성에 관한 특정 단계는 [ssh에서 ssh-agent 사용(http://mah.everybody.org/docs/ssh)](http://mah.everybody.org/docs/ssh)의 에이전트 시작 스크립트나 SSH 클라이언트 설명서를 참조하십시오.

4. **ssh-agent** 가 실행 중인지 확인한 후에는 다음을 사용하여 SSH 개인 키를 에이전트에 추가합니다.

        ssh-add ~/.ssh/id_rsa

    개인 키가 다른 파일에 저장된 경우 `~/.ssh/id_rsa` 를 해당 파일의 경로로 바꿉니다.

<a id="domainjoined"></a>
### <a name="domain-joined-hdinsight"></a>도메인 가입 HDInsight

[도메인에 가입된 HDInsight](hdinsight-domain-joined-introduction.md)는 HDInsight에서 Hadoop와 함께 Kerberos를 통합합니다. SSH 사용자는 Active Directory 도메인 사용자가 아니기 때문에 Active Directory로 인증될 때까지 Hadoop 명령을 실행할 수 없습니다. 다음 단계를 사용하여 Active Directory에 SSH 세션을 인증합니다.

1. SSH를 사용하여 도메인에 가입된 HDInsight 클러스터에 연결합니다. 예를 들어 다음 명령은 이름이 __sshuser__인 SSH 계정을 사용하여 이름이 __myhdi__인 HDInsight 클러스터에 연결합니다.

        ssh sshuser@myhdi-ssh.azurehdinsight.net

2. 도메인 사용자 및 암호를 사용하여 다음으로 인증합니다.

        kinit

     대화 상자가 나타나면 도메인 사용자 이름과 해당 도메인 사용자의 암호를 입력합니다.

    도메인에 가입된 HDInsight 클러스터에 대한 도메인 사용자 구성에 관한 자세한 내용은 [도메인에 가입된 HDInisight 클러스터 구성](hdinsight-domain-joined-configure.md)을 참조하세요.

`kinit` 명령을 사용하여 인증한 후에는 `hdfs dfs -ls /` 또는 `hive` 같은 Hadoop 명령을 사용할 수 있습니다.

## <a name="a-idtunnelassh-tunneling"></a><a id="tunnel"></a>SSH 터널링

SSH는 웹 요청과 같은 로컬 요청을 HDInsight 클러스터에 터널링하는 데 사용할 수 있습니다. 요청이 클러스터에 전달된 다음 클러스터 내에서 해결됩니다.

> [!IMPORTANT]
> SSH 터널은 일부 Hadoop 서비스의 웹 UI에 액세스하기 위한 요구 사항입니다. 예를 들어 작업 기록 UI와 리소스 관리자 UI는 둘 다 SSH 터널을 사용해서만 액세스할 수 있습니다.

SSH 터널 만들기 및 사용에 대한 자세한 내용은 [SSH 터널링을 사용하여 Ambari 웹 UI, JobHistory, NameNode, Oozie 및 기타 웹 UI에 액세스](hdinsight-linux-ambari-ssh-tunnel.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

이제 SSH 키를 사용하여 인증하는 방법을 배웠으므로 HDInsight에서 Hadoop과 MapReduce를 함께 사용하는 방법에 알아봅니다.

* [HDInsight에서 Hive 사용](hdinsight-use-hive.md)
* [HDInsight에서 Pig 사용](hdinsight-use-pig.md)
* [HDInsight에서 MapReduce 작업 사용](hdinsight-use-mapreduce.md)

[preview-portal]: https://portal.azure.com/

