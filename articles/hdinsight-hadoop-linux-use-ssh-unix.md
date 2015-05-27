<properties
   pageTitle="Linux, Unix 또는 OS X에서 Linux 기반 HDInsight의 Hadoop과 SSH 키 사용 | Aure"
   description="SSH 키를 생성하고 사용하여 Linux 기반 HDInsight 클러스터에 인증하는 방법을 알아봅니다."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="03/20/2015"
   ms.author="larryfr"/>

# Linux, Unix 또는 OS X에서 HDInsight의 Linux 기반 Hadoop과 SSH 사용(미리 보기)

> [AZURE.SELECTOR]
- [Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
- [Linux, Unix, OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

Linux 기반 Azure HDInsight 클러스터는 암호 또는 SSH 키를 통해 SSH(보안 셸) 사용 옵션을 제공합니다. 이 문서에서는 Linux, Unix 또는 OS X 클라이언트에서 HDInsight와 함께 SSH를 사용하는 방법을 설명합니다.

> [AZURE.NOTE]이 문서의 단계에서는 Linux, Unix 또는 OS X 클라이언트를 사용한다고 가정합니다. `ssh` 및 `ssh-keygen`(Windows용 Git와 같은)을 제공하는 패키지가 설치되어 있는 경우 Windows 기반 클라이언트에서 다음 단계를 수행할 수 있지만 Windows 기반 클라이언트가 [Windows에서 Linux 기반 HDInsight(Hadoop)과 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows.md) 단계를 따르는 것이 좋습니다.

## 필수 조건

* Linux, Unix 및 OS X 클라이언트용 **ssh-keygen** 및 **ssh**. 이 유틸리티는 일반적으로 운영 체제와 함께 제공되거나 패키지 관리 시스템을 통해 사용할 수 있습니다.

* HTML5를 지원하는 최신 웹 브라우저

또는

* [Mac, Linux 및 Windows용 Azure CLI](xplat-cli.md)

## SSH 정의

SSH는 원격 서버에서 로그인 및 원격으로 실행하는 명령 유틸리티입니다. Linux 기반 HDInsight와 함께 SSH는 클러스터 헤드 노드에 대한 암호화된 연결을 설정하고 명령에서 입력하는 데 사용하는 명령줄을 제공합니다. 명령은 서버에서 직접 실행됩니다.

## SSH 키 만들기(옵션)

Linux 기반 HDInsight 클러스터를 만드는 경우 SSH 키를 사용할 때 암호 또는 SSH 키를 사용하여 인증하는 옵션이 있습니다. SSH 키는 인증서를 기반으로 보다 안전한 것으로 간주됩니다. 클러스터와 SSH 키를 사용하려는 경우에 다음 정보를 사용합니다.

1. 터미널 세션을 열고 다음 명령을 사용하여 기존 SSH 키가 있는지 확인합니다.

		ls -al ~/.ssh

	디렉터리 목록에서 다음 파일을 찾습니다. 이들은 공개 SSH 키에 대한 일반 이름입니다.

	* id_dsa.pub
	* id_ecdsa.pub
	* id_ed25519.pub
	* id_rsa.pub

2. 기존 파일을 사용하지 않으려는 경우 또는 기존 SSH 키가 없는 경우 다음을 사용하여 새 파일을 생성합니다.

		ssh-keygen -t rsa

	다음 정보를 묻는 메시지가 나타납니다.

	* 파일 위치 - 위치는 기본적으로 \~/.ssh/id\\_rsa로 설정됩니다.
	* 암호 - 이것을 다시 입력하라는 메시지가 표시됩니다.

		> [AZURE.NOTE]키에 대한 보안 암호를 사용하는 것이 좋습니다. 그러나 암호를 잊은 경우 복구할 수 있는 방법이 없습니다.

	명령이 완료되면 개인 키(예: **id_rsa**)와 공개 키(예: **id_rsa.pub**), 이렇게 두 개의 새 파일이 생성됩니다.

## Linux 기반 HDInsight 클러스터 만들기

Linux 기반 HDInsight 클러스터를 만들 때 이전에 생성한 공개 키를 제공해야 합니다. Linux, Unix 또는 OS X 클라이언트에서 HDInsight 클러스터를 만들 수 있는 두 가지 방법이 있습니다.

* **Azure 포털** - 웹 기반 포털을 사용하여 클러스터를 만듭니다.

* **Mac, Linux 및 Windows용 Azure CLI** - 명령줄 명령을 사용하여 클러스터를 만듭니다.

이러한 각 메서드는 각각의 암호 또는 공개 키가 필요합니다. Linux 기반 HDInsight 클러스터를 만드는 방법에 대한 자세한 내용은 <a href="/documentation/articles/hdinsight-hadoop-provision-linux-clusters/" target="_blank">Linux 기반 HDInsight 클러스터 프로비전</a>을 참조하세요.

### Azure 포털

포털을 사용하여 Linux 기반 HDInsight 클러스터를 만들 때 **SSH 사용자 이름**을 입력하고 **암호** 또는 **SSH 공개 키**를 선택하여 입력합니다. **SSH 공개 키**를 선택한 경우 다음 형식으로 공개 키(확장자가 **.pub**인 파일에 포함됨)를 붙여 넣어야 합니다.

![공개 키를 묻는 양식의 이미지](./media/hdinsight-hadoop-linux-use-ssh-unix/ssh-key.png)

> [AZURE.NOTE]키 파일은 단순한 텍스트 파일입니다. 내용은 다음과 유사합니다.
> ```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCelfkjrpYHYiks4TM+r1LVsTYQ4jAXXGeOAF9Vv/KGz90pgMk3VRJk4PEUSELfXKxP3NtsVwLVPN1l09utI/tKHQ6WL3qy89WVVVLiwzL7tfJ2B08Gmcw8mC/YoieT/YG+4I4oAgPEmim+6/F9S0lU2I2CuFBX9JzauX8n1Y9kWzTARST+ERx2hysyA5ObLv97Xe4C2CQvGE01LGAXkw2ffP9vI+emUM+VeYrf0q3w/b1o/COKbFVZ2IpEcJ8G2SLlNsHWXofWhOKQRi64TMxT7LLoohD61q2aWNKdaE4oQdiuo8TGnt4zWLEPjzjIYIEIZGk00HiQD+KCB5pxoVtp user@system
> ```

이렇게 하면 제공한 암호 또는 공개 키를 사용하여 특정 사용자 로그인을 생성합니다.

### Mac, Linux 및 Windows용 Azure 명령줄 인터페이스

[Mac, Linux 및 Windows용 Azure CLI](xplat.md)를 사용하여 `azure hdinsight cluster create` 명령을 통해 새 클러스터를 만들 수 있습니다.

이 명령은 사용에 대한 자세한 내용은 <a href="../hdinsight-hadoop-provision-linux-clusters/" target="_blank">사용자 지정 옵션을 사용하여 HDInsight에서 Hadoop Linux 클러스터 프로비전</a>을 참조하세요.

## Linux 기반 HDInsight 클러스터에 연결

터미널 세션에서 SSH 명령을 통해 주소 및 사용자 이름을 제공하여 클러스터 헤드 노드에 연결합니다.

* **SSH 주소** - **-ssh.azurehdinsight.net**이 뒤에 오는 클러스터 이름입니다. 예를 들면 **mycluster-ssh.azurehdinsight.net**과 같습니다.

* **사용자 이름** - 클러스터를 만들 때 제공한 SSH 사용자 이름입니다.

다음 예제에서는 **mycluster** 클러스터에 **me** 사용자로 연결합니다.

	ssh me@mycluster-ssh.azurehdinsight.net

사용자 계정에 대한 암호를 사용하는 경우 암호를 입력하라는 메시지가 나타납니다.

암호로 보호된 SSH 키를 사용한 경우 암호를 입력하라는 메시지가 나타납니다. 그렇지 않은 경우 SSH에서 클라이언트의 로컬 개인 키 중 하나를 사용하여 자동으로 인증을 시도합니다.

> [AZURE.NOTE]SSH가 자동으로 올바른 개인 키로 인증하지 않는 경우, **-i** 매개 변수를 사용하고 개인 키에 대한 경로를 지정합니다. 다음 예제에서는 `~/.ssh/id_rsa`에서 개인 키를 로드합니다.
>
> `ssh -i ~/.ssh/id_rsa me@mycluster-ssh.azurehdinsight.net`

### 작업자 노드에 연결

작업자 노드는 Azure 데이터 센터 외부에서 직접 액세스할 수 없으며, SSH를 통해 클러스터 헤드 노드에서 액세스할 수 있습니다.

SSH 키를 사용하여 사용자 계정을 인증하는 경우 클라이언트에서 다음 단계를 완료해야 합니다.

1. 텍스트 편집기를 사용하여 `~/.ssh/config`를 엽니다. 이 파일이 존재하지 않으면 터미널에 `touch ~/.ssh/config`를 입력하여 만들 수 있습니다.

2. 파일에 다음을 추가합니다. *CLUSTERNAME*을 HDInsight 클러스터의 이름으로 바꿉니다.

        Host CLUSTERNAME-ssh.azurehdinsight.net
          ForwardAgent yes

    그러면 HDInsight 클러스터에 대한 SSH 에이전트 전달이 구성됩니다.

3. 터미널에서 다음 명령을 사용하여 SSH 에이전트 전달을 테스트합니다.

        echo "$SSH_AUTH_SOCK"

    다음과 유사한 정보가 반환됩니다.

        /tmp/ssh-rfSUL1ldCldQ/agent.1792

    아무 것도 반환되지 않으면 **ssh-agent**가 실행되고 있지 않음을 나타냅니다. 운영 체제 설명서에서 **ssh-agent**를 설치 및 구성하는 특정 단계를 참조하거나 <a href="http://mah.everybody.org/docs/ssh" target="_blank">ssh와 함께 ssh-agent 사용</a>을 참조하세요.

4. **ssh-agent**가 실행 중인지 확인한 후에는 다음을 사용하여 SSH 개인 키를 에이전트에 추가합니다.

        ssh-add ~/.ssh/id_rsa

    개인 키가 다른 파일에 저장된 경우 `~/.ssh/id_rsa`를 해당 파일의 경로로 바꿉니다.

다음 단계를 사용하여 클러스터의 작업자 노드에 연결합니다.

> [AZURE.IMPORTANT]SSH 키를 사용하여 계정을 인증하는 경우 이전 단계를 완료하여 에이전트 전달이 작동하는지 확인해야 합니다.

1. 앞에서 설명한 대로 SSH를 사용하여 HDInsight 클러스터에 연결합니다.

2. 연결되면 다음을 사용하여 클러스터의 노드 목록을 검색합니다. *ADMINPASSWORD*를 클러스터 관리 계정의 암호로 바꿉니다. *CLUSTERNAME*을 클러스터의 이름으로 바꿉니다.

        curl --user admin:ADMINPASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/hosts

    `host_name`을 포함하여 클러스터의 노드에 대한 정보가 JSON 형식으로 반환되며, 여기에는 각 노드의 FQDN(정규화된 도메인 이름)이 포함됩니다. 다음은 **curl** 명령에서 반환되는 `host_name` 항목의 예입니다.

        "host_name" : "workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net"

3. 연결하려는 작업자 노드 목록을 가져왔으면 서버의 SSH 세션에서 다음 명령을 사용하여 작업자 노드에 대한 연결을 엽니다.

        ssh USERNAME@FQDN

    *USERNAME*을 SSH 사용자 이름으로, *FQDN*을 작업자 노드의 FQDN으로 바꿉니다. 예를 들면 `workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net`과 같습니다.

    > [AZURE.NOTE]암호를 사용하여 SSH 세션을 인증하는 경우 암호를 입력하라는 메시지가 다시 표시됩니다. SSH 키를 사용하는 경우에는 아무 메시지 없이 연결이 완료됩니다.

4. 세션이 설정되면 작업자 노드에 연결되었음을 나타내도록 터미널 프롬프트가 `username@headnode`에서 `username@workernode`로 변경됩니다. 이 시점부터 실행하는 모든 명령은 작업자 노드에서 실행됩니다.

4. 작업자 노드에 대한 작업 수행을 마쳤으면 `exit` 명령을 사용하여 작업자 노드의 세션을 닫습니다. 그러면 `username@headnode` 프롬프트가 반환됩니다.

## 계정 추가

1. [SSH 키 만들기](#create-an-ssh-key-optional) 섹션에서 설명된 것처럼 새로운 사용자 계정에 대한 새로운 공개 키 및 개인 키를 생성합니다.

	> [AZURE.NOTE]개인 키는 사용자가 클러스터에 연결하는 데 사용하거나 생성 후 이러한 클라이언트에 안전하게 전송하는 클라이언트에서 생성되어야 합니다.

1. SSH 세션에서 클러스터로 다음 명령을 사용하여 새 사용자를 추가합니다.

		sudo adduser --disabled-password <username>

	이렇게 하면 새 사용자 계정이 생성되지만 암호 인증이 비활성화됩니다.

2. 다음 명령을 사용하여 키를 보유하는 파일 및 디렉터리를 만듭니다.

		sudo mkdir -p /home/<username>/.ssh
		sudo touch /home/<username>/.ssh/authorized_keys
		sudo nano /home/<username>/.ssh/authorized_keys

3. Nano 편집기를 열면 새로운 사용자 계정에 대한 공개 키 콘텐츠를 복사하고 붙여넣습니다. 마지막으로 **Ctrl-X**를 사용하여 파일을 저장하고 편집기를 종료합니다.

	![예제 키가 표시된 nano 편집기의 이미지](./media/hdinsight-hadoop-linux-use-ssh-unix/nano.png)

4. 새 사용자 계정에 .ssh 폴더 및 내용의 소유권을 변경하려면 다음 명령을 사용합니다.

		sudo chown -hR <username>:<username> /home/<username>/.ssh

5. 이제 새 사용자 계정 및 개인 키를 사용하여 서버에 인증할 수 있습니다.

## <a id="tunnel"></a>SSH 터널링

SSH는 웹 요청과 같은 로컬 요청을 HDInsight 클러스터에 터널링하는 데 사용할 수도 있습니다. HDInsight 클러스터 헤드 노드에서 발생하는 경우 요청이 요청된 리소스에 라우팅됩니다.

클러스터에서 헤드 또는 작업자 노드에 대한 내부 도메인 이름을 사용하는 HDInsight 클러스터의 웹 기반 서비스에 액세스하는 데 가장 유용합니다. 예를 들어 Ambari 웹 페이지의 일부 섹션은 **headnode0.mycluster.d1.internal.cloudapp.net**과 같은 내부 도메인 이름을 사용합니다. 이 이름은 클러스터 외부에서 확인할 수 없지만 SSH를 통해 터널링된 요청은 클러스터 내에서 발생하고 올바르게 확인됩니다.

SSH 터널을 만들고 브라우저를 구성하여 클러스터에 연결하려면 다음 단계를 따르세요.

1. 다음 명령은 클러스터 헤드 노드에 SSH 터널을 생성하는 데 사용할 수 있습니다.

		ssh -C2qTnNf -D 9876 username@clustername-ssh.azurehdinsight.net

	로컬 포트 9876에서 SSH를 통해 클러스터에 트래픽을 라우팅하는 연결을 만듭니다. 옵션은 다음과 같습니다.

	* **D 8080** - 터널을 통해 로컬 포트에서 트래픽 라우팅

	* **C** - 웹 트래픽은 대부분 텍스트이므로 모든 데이터 압축

	* **2** - SSH가 프로토콜 버전 2만 시도하도록 강요

	* **q** - 자동 모드

	* **T** - 포트 전달 후 허위 tty 할당 비활성화

	* **n** - 포트 전달 후 STDIN 읽지 않음

	* **N** - 포트 전달 후 원격 명령 실행 안 함

	* **f** - 백그라운드에서 실행

	SSH 키를 사용하여 클러스터를 구성한 경우에는 `-i` 매개 변수를 사용하여 개인 SSH 키에 대한 경로를 지정할 수 있습니다.

	명령이 완료되면 로컬 컴퓨터에서 9876 포트로 전송되는 트래픽이 SSL(Secure Sockets Layer)을 통해 클러스터 헤드 노드에 라우팅되며 생성된 곳에 나타납니다.

2. **localhost:9876**을 **SOCKS v5** 프록시로 사용하려면 Firefox와 같은 클라이언트 프로그램을 구성합니다. Firefox 설정은 다음과 같습니다.

	![Firefox 설정 이미지](./media/hdinsight-hadoop-linux-use-ssh-unix/socks.png)

	> [AZURE.NOTE]**Remote DNS**를 선택하면 HDInsight 클러스터를 통해 DNS(Domain Name System) 요청이 확인됩니다. 선택하지 않은 경우 DNS가 로컬로 확인됩니다.

	Firefox에서 프록시 설정을 사용하거나 사용하지 않도록 설정한 상태에서 <a href="http://www.whatismyip.com/" target="_blank">http://www.whatismyip.com/</a>과 같은 사이트를 방문하여 트래픽이 터널을 통해 라우팅되는지 확인할 수 있습니다. 설정이 활성화되어 있는 동안 IP 주소는 Microsoft Azure 데이터 센터에 있는 컴퓨터의 주소입니다.

### 브라우저 확장

작동하는 터널을 사용하여 브라우저를 구성하는 동안 보통 터널을 통해 모든 트래픽을 라우팅하지 않으려고 할 수 있습니다. <a href="http://getfoxyproxy.org/" target="_blank">FoxyProxy</a> 같은 브라우저 확장 프로그램이 URL 요청에 대해 일치하는 패턴을 지원하므로(FoxyProxy Standard 또는 Plus만 해당) 특정 URL에 대한 요청만 터널을 통해 전송됩니다.

FoxyProxy 표준을 설치한 경우 터널을 통해 HDInsight에 대한 트래픽을 전달만 하도록 구성하려면 다음 단계를 따르세요.

1. 브라우저에서 FoxyProxy 확장 프로그램을 엽니다. 예를 들어, Firefox에서 주소 필드 옆에 있는 FoxyProxy 아이콘을 선택합니다.

	![foxyproxy 아이콘](./media/hdinsight-hadoop-linux-use-ssh-unix/foxyproxy.png)

2. **Add New Proxy**를 선택하고 **General** 탭을 선택한 다음 **HDInsightProxy**의 프록시 이름을 입력합니다.

	![foxyproxy 일반](./media/hdinsight-hadoop-linux-use-ssh-unix/foxygeneral.png)

3. **Proxy Details** 탭을 선택하고 다음 필드를 입력합니다.

	* **Host or IP Address** - 로컬 컴퓨터에서 SSH 터널을 사용한 이후의 localhost입니다.

	* **Port** - SSH 터널에 사용한 포트입니다.

	* **SOCKS proxy** - 브라우저에서 터널을 프록시로 사용할 수 있도록 하려면 선택합니다.

	* **SOCKS v5** - 필요한 버전의 프록시를 설정하려면 선택합니다.

	![foxyproxy 프록시](./media/hdinsight-hadoop-linux-use-ssh-unix/foxyproxyproxy.png)

4. **URL Patterns** 탭을 선택한 다음 **Add New Pattern**을 선택합니다. 다음을 사용하여 패턴을 정의하고 **OK**를 클릭합니다.

	* **Pattern Name** - **headnode** - 패턴의 이름입니다.

	* **URL pattern** - ***headnode*** - **headnode**라는 단어가 포함된 모든 URL과 일치하는 패턴을 정의합니다.

	![foxyproxy 패턴](./media/hdinsight-hadoop-linux-use-ssh-unix/foxypattern.png)

4. **OK**를 클릭하여 프록시를 추가하고 **Proxy Settings**를 닫습니다.

5. FoxyProxy 대화 상자의 위쪽에서 **Select Mode**를 **Use proxies based on their pre-defined patterns and priorities**로 변경한 다음 **Close**를 클릭합니다.

	![foxyproxy 선택 모드](./media/hdinsight-hadoop-linux-use-ssh-unix/selectmode.png)

이러한 단계를 따른 후에는 **headnode** 문자열이 포함된 URL에 대한 요청만 SSL 터널을 통해 라우팅됩니다.

## 다음 단계

이제 SSH 키를 사용하여 인증하는 방법을 배웠으므로 HDInsight에서 Hadoop과 MapReduce를 함께 사용하는 방법에 알아봅니다.

* [HDInsight에서 Hive 사용](hdinsight-use-hive.md)

* [HDInsight에서 Pig 사용](hdinsight-use-pig.md)

* [HDInsight에서 MapReduce 작업 사용](hdinsight-use-mapreduce.md)

<!--HONumber=54-->