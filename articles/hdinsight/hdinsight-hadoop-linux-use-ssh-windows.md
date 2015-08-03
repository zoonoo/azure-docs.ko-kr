<properties
   pageTitle="Windows의 Linux 기반 클러스터에서 Hadoop로 SSH 키 사용 | Microsoft Azure"
   description="SSH 키를 생성하고 사용하여 Linux 기반 HDInsight 클러스터에 인증하는 방법을 알아봅니다. PuTTY SSH 클라이언트를 사용하여 Windows 기반 클라이언트에서 클러스터에 연결합니다."
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
   ms.date="07/06/2015"
   ms.author="larryfr"/>

#Windows에서 HDInsight의 Linux 기반 Hadoop과 SSH 사용(미리 보기)

> [AZURE.SELECTOR]
- [Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
- [Linux, Unix, OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

Linux 기반 Azure HDInsight 클러스터는 암호 또는 SSH 키를 통해 SSH(보안 셸) 사용 옵션을 제공합니다. 이 문서에서는 PuTTY SSH 클라이언트를 사용하여 Windows 기반 클라이언트에서 HDInsight에 연결하는 방법을 설명합니다.

> [AZURE.NOTE]이 문서의 단계에서는 Windows 기반 클라이언트를 사용한다고 가정합니다. Linux, Unix 또는 OS X 클라이언트를 사용하는 경우 [Linux, Unix 또는 OS X에서 HDInsight의 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

##필수 조건

* Windows 기반 클라이언트용 **PuTTY** 및 **PuTTYGen**입니다. 이러한 유틸리티는 [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)에서 다운로드할 수 있습니다.

* HTML5를 지원하는 최신 웹 브라우저

또는

* [Mac, Linux 및 Windows용 Azure CLI](../xplat-cli.md)

##SSH 정의

SSH는 원격 서버에서 로그인 및 원격으로 실행하는 명령 유틸리티입니다. Linux 기반 HDInsight와 함께 SSH는 클러스터 헤드 노드에 대한 암호화된 연결을 설정하고 명령에서 입력하는 데 사용하는 명령줄을 제공합니다. 명령은 서버에서 직접 실행됩니다.

##SSH 키 만들기(옵션)

Linux 기반 HDInsight 클러스터를 만드는 경우 SSH 키를 사용할 때 암호 또는 SSH 키를 사용하여 인증하는 옵션이 있습니다. SSH 키는 인증서를 기반으로 보다 안전한 것으로 간주됩니다. 클러스터와 SSH 키를 사용하려는 경우에 다음 정보를 사용합니다.

1. PuTTYGen을 엽니다.

2. **Type of key to generate**에서 **SSH-2 RSA**를 선택하고 **Generate**를 클릭합니다.

	![PuTTYGen 인터페이스](./media/hdinsight-hadoop-linux-use-ssh-windows/puttygen.png)

3. 막대를 채울 때까지 진행률 표시줄 아래 영역에서 마우스를 이동합니다. 마우스를 이동하면 키를 생성하는 데 사용되는 임의의 데이터를 생성합니다.

	![마우스 이동](./media/hdinsight-hadoop-linux-use-ssh-windows/movingmouse.png)

	키가 한 번 생성되면 공용 키가 표시됩니다.

4. 추가 보안을 위해 **Key passphrase** 필드에서 암호를 입력한 다음 **Confirm passphrase** 필드에 동일한 값을 입력할 수 있습니다.

	![암호](./media/hdinsight-hadoop-linux-use-ssh-windows/key.png)

	> [AZURE.NOTE]키에 대한 보안 암호를 사용하는 것이 좋습니다. 그러나 암호를 잊은 경우 복구할 수 있는 방법이 없습니다.

5. **Save private key**를 클릭하여 **.ppk** 파일에 키를 저장합니다. 이 키는 Linux 기반 HDInsight 클러스터에 인증하는 데 사용됩니다.

	> [AZURE.NOTE]Linux 기반 HDInsight 클러스터에 액세스하는 데 사용하기 때문에 이 키를 안전한 위치에 저장해야 합니다.

6. **Save public key**를 클릭하여 **.txt** 파일로 키를 저장합니다. 이 옵션을 사용하면 추가 Linux 기반 HDInsight 클러스터를 만들 때 공개 키를 나중에 다시 사용할 수 있습니다.

	> [AZURE.NOTE]공개 키는 PuTTYGen의 상단에도 표시됩니다. 이 필드를 마우스 오른쪽 단추로 클릭하고, 값을 복사하고, Azure 포털에서 HDInsight 마법사와 같은 양식에 붙여넣을 수 있습니다.

##Linux 기반 HDInsight 클러스터 만들기

Linux 기반 HDInsight 클러스터를 만들 때 이전에 생성한 공개 키를 제공해야 합니다. Windows 기반 클라이언트에서는 두 가지의 Linux 기반 HDInsight 클러스터를 만들 수 있습니다.

* **Azure 포털** - 웹 기반 포털을 사용하여 클러스터를 만듭니다.

* **Mac, Linux 및 Windows용 Azure CLI** - 명령줄 명령을 사용하여 클러스터를 만듭니다.

이러한 각 메서드에는 공개 키가 필요합니다. Linux 기반 HDInsight 클러스터를 만드는 방법에 대한 자세한 내용은 [Linux 기반 HDInsight 클러스터 프로비전](hdinsight-hadoop-provision-linux-clusters.md)을 참조하세요.

###Azure 포털

Linux 기반 HDInsight 클러스터를 만들려면 포털을 사용하는 경우 다음 양식에 사용자 이름 및 암호 또는 공개 키를 입력해야 합니다.

![공개 키를 묻는 양식의 이미지](./media/hdinsight-hadoop-linux-use-ssh-windows/ssh-key.png)

이렇게 하면 지정된 사용자에 대한 로그인이 생성되고 암호 인증 또는 SSH 키 인증을 사용하면 됩니다.

###Mac, Linux 및 Windows용 Azure 명령줄 인터페이스

[Mac, Linux 및 Windows용 Azure CLI](../xplat-cli.md)를 사용하여 `azure hdinsight cluster create` 명령을 통해 새 클러스터를 만들 수 있습니다.

이 명령 사용에 대한 자세한 내용은 [사용자 지정 옵션을 사용하여 HDInsight에서 Hadoop Linux 클러스터 프로비전](hdinsight-hadoop-provision-linux-clusters.md)을 참조하세요.

##Linux 기반 HDInsight 클러스터에 연결

1. PuTTY를 엽니다.

	![putty 인터페이스](./media/hdinsight-hadoop-linux-use-ssh-windows/putty.png)

2. 사용자 계정을 생성할 때 SSH 키를 제공한 경우 다음 단계를 수행하여 클러스터에 인증할 때 사용하려는 개인 키를 선택해야 합니다.

	**Category**에서 **Connection**, **SSH**를 차례로 확장하고 **Auth**를 선택합니다. 마지막으로 **Browse**를 클릭하고 개인 키가 포함된 .ppk 파일을 선택합니다.

	![putty 인터페이스, 개인 키 선택](./media/hdinsight-hadoop-linux-use-ssh-windows/puttykey.png)

3. **Category**에서 **Session**을 선택합니다. **Basic options for your PuTTY session** 화면에서 **Host name (or IP address)** 필드에 HDInsight 서버의 SSH 주소를 입력합니다. SSH 주소는 **-ssh.azurehdinsight.net**이 뒤에 오는 클러스터 이름입니다. 예를 들면 **mycluster-ssh.azurehdinsight.net**과 같습니다.

	![ssh 주소가 입력된 putty 인터페이스](./media/hdinsight-hadoop-linux-use-ssh-windows/puttyaddress.png)

4. 나중에 사용하기 위해 연결 정보를 저장하려면 **Saved Sessions** 아래에 이 연결의 이름을 입력한 후 **Save**를 클릭합니다. 연결이 저장 세션의 목록에 추가됩니다.

5. **Open**을 클릭하여 클러스터에 연결합니다.

	> [AZURE.NOTE]처음으로 클러스터에 연결한 경우 보안 경고를 받게 됩니다. 이것은 정상입니다. 서버의 RSA2 키를 계속 캐시하려면 **예**를 선택합니다.

6. 메시지가 표시되면 클러스터를 생성할 때 입력한 사용자를 입력합니다. 사용자에 대한 암호를 제공한 경우 사용자를 입력하라는 메시지도 나타납니다.

###작업자 노드에 연결

작업자 노드는 Azure 데이터 센터 외부에서 직접 액세스할 수 없으며, SSH를 통해 클러스터 헤드 노드에서 액세스할 수 있습니다.

사용자 계정을 만들 때 SSH 키를 제공한 경우 작업자 노드에 연결하려면 다음 단계를 수행하여 클러스터에 인증할 때 개인 키를 사용해야 합니다.

1. [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)에서 Pageant를 설치합니다. 이 유틸리티는 PuTTY의 SSH 키를 캐시하는 데 사용됩니다.

2. Pageant를 실행합니다. 상태 트레이의 아이콘이 최소화됩니다. 아이콘을 마우스 오른쪽 단추로 클릭하고 **키 추가**를 선택합니다.

    ![키 추가](./media/hdinsight-hadoop-linux-use-ssh-windows/addkey.png)

3. 찾아보기 대화 상자가 나타나면 키가 포함된 .ppk 파일을 선택하고 **열기**를 클릭합니다. 그러면 Pageant에 키가 추가되어 클러스터에 연결할 때 PuTTY에 제공됩니다.

    > [AZURE.IMPORTANT]SSH 키를 사용하여 계정의 보안을 유지하는 경우에는 이전 단계를 완료해야 작업자 노드에 연결할 수 있습니다.

4. PuTTY를 엽니다.

5. SSH 키를 사용하여 인증하는 경우 **Category** 섹션에서 **Connection**, **SSH**를 차례로 확장하고 **Auth**를 선택합니다.

    **Authentication parameters** 섹션에서 **Allow agent forwarding**을 사용하도록 설정합니다. 그러면 작업자 노드에 연결할 때 PuTTY에서 클러스터 헤드 노드에 대한 연결을 통해 인증서 인증을 자동으로 전달할 수 있습니다.

    ![에이전트 전달 허용](./media/hdinsight-hadoop-linux-use-ssh-windows/allowforwarding.png)

6. 앞에서 설명한 대로 클러스터에 연결합니다. 인증에 SSH 키를 사용하는 경우에는 키를 선택할 필요가 없습니다. Pageant에 추가된 SSH 키가 클러스터를 인증하는 데 사용됩니다.

7. 연결이 설정되면 다음을 사용하여 클러스터의 노드 목록을 검색합니다. *ADMINPASSWORD*를 클러스터 관리 계정의 암호로 바꿉니다. *CLUSTERNAME*을 클러스터의 이름으로 바꿉니다.

        curl --user admin:ADMINPASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/hosts

    `host_name`을 포함하여 클러스터의 노드에 대한 정보가 JSON 형식으로 반환되며, 여기에는 각 노드의 FQDN(정규화된 도메인 이름)이 포함됩니다. 다음은 **curl** 명령에서 반환되는 `host_name` 항목의 예입니다.

        "host_name" : "workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net"

8. 연결하려는 작업자 노드 목록을 가져왔으면 PuTTY 세션에서 다음 명령을 사용하여 작업자 노드에 대한 연결을 엽니다.

        ssh USERNAME@FQDN

    *USERNAME*을 SSH 사용자 이름으로, *FQDN*을 작업자 노드의 FQDN으로 바꿉니다. 예를 들면 `workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net`과 같습니다.

    > [AZURE.NOTE]암호를 사용하여 SSH 세션을 인증하는 경우 암호를 입력하라는 메시지가 다시 표시됩니다. SSH 키를 사용하는 경우에는 아무 메시지 없이 연결이 완료됩니다.

9. 세션이 설정되면 작업자 노드에 연결되었음을 나타내도록 PuTTY 세션에 대한 프롬프트가 `username@headnode`에서 `username@workernode`로 변경됩니다. 이 시점부터 실행하는 모든 명령은 작업자 노드에서 실행됩니다.

10. 작업자 노드에 대한 작업 수행을 마쳤으면 `exit` 명령을 사용하여 작업자 노드의 세션을 닫습니다. 그러면 `username@headnode` 프롬프트가 반환됩니다.

##계정 추가

클러스터에 계정에 더 추가해야 하는 경우 다음 단계를 수행합니다.

1. 이전에 설명된 것처럼 새로운 사용자 계정에 대한 새로운 공개 키 및 개인 키를 생성합니다.

2. SSH 세션에서 클러스터로 다음 명령을 사용하여 새 사용자를 추가합니다.

		sudo adduser --disabled-password <username>

	이렇게 하면 새 사용자 계정이 생성되지만 암호 인증이 비활성화됩니다.

3. 다음 명령을 사용하여 키를 보유하는 파일 및 디렉터리를 만듭니다.

        sudo mkdir -p /home/<username>/.ssh
        sudo touch /home/<username>/.ssh/authorized_keys
        sudo nano /home/<username>/.ssh/authorized_keys

4. Nano 편집기를 열면 새로운 사용자 계정에 대한 공개 키 콘텐츠를 복사하고 붙여넣습니다. 마지막으로 **Ctrl-X**를 사용하여 파일을 저장하고 편집기를 종료합니다.

	![예제 키가 표시된 nano 편집기의 이미지](./media/hdinsight-hadoop-linux-use-ssh-windows/nano.png)

5. 새 사용자 계정에 .ssh 폴더 및 내용의 소유권을 변경하려면 다음 명령을 사용합니다.

		sudo chown -hR <username>:<username> /home/<username>/.ssh

6. 이제 새 사용자 계정 및 개인 키를 사용하여 서버에 인증할 수 있습니다.

##<a id="tunnel"></a>SSH 터널링

SSH는 웹 요청과 같은 로컬 요청을 HDInsight 클러스터에 터널링하는 데 사용할 수도 있습니다. HDInsight 클러스터 헤드 노드에서 발생하는 경우 요청이 요청된 리소스에 라우팅됩니다.

클러스터에서 헤드 또는 작업자 노드에 대한 내부 도메인 이름을 사용하는 HDInsight 클러스터의 웹 기반 서비스에 엑세스할 때 가장 유용합니다. 예를 들어 Ambari 웹 페이지의 일부 섹션은 **headnode0.mycluster.d1.internal.cloudapp.net**과 같은 내부 도메인 이름을 사용합니다. 이 이름은 클러스터 외부에서 확인할 수 없지만 SSH를 통해 터널링된 요청은 클러스터 내에서 발생하고 올바르게 확인됩니다.

SSH 터널을 만들고 브라우저를 구성하여 클러스터에 연결하려면 다음 단계를 따르세요.

1. PuTTY를 열고 위의 [Linux 기반 클러스터에 연결](#connect-to-a-linux-based-hdinsight-cluster) 섹션에서 설명된 대로 연결 정보를 입력합니다.

2. 대화 상자의 왼쪽에 있는 **Category** 섹션에서 **Connection**, **SSH**를 차례로 확장한 다음 **Tunnels**를 선택합니다.

3. **Options controlling SSH port forwarding** 양식에 다음 정보를 제공합니다.

	* **Source port** - 전달하려는 클라이언트의 포트입니다. 예를 들면 **9876**과 같습니다.

	* **Destination** - Linux 기반 HDInsight 클러스터의 SSH 주소입니다. 예를 들면 **mycluster-ssh.azurehdinsight.net**과 같습니다.

	* **Dynamic** - 동적 SOCKS 프록시 라우팅을 활성화합니다.

	![터널링 옵션 이미지](./media/hdinsight-hadoop-linux-use-ssh-windows/puttytunnel.png)

4. **Add**를 클릭하여 설정을 추가한 다음 **Open**을 클릭하여 SSH 연결을 엽니다.

5. 메시지가 표시되면 서버에 로그인합니다. 이 SSH 세션을 설정하고 터널을 사용하도록 설정합니다.

6. **localhost:9876**을 **SOCKS v5** 프록시로 사용하려면 Firefox와 같은 클라이언트 프로그램을 구성합니다. Firefox 설정은 다음과 같습니다.

	![Firefox 설정 이미지](./media/hdinsight-hadoop-linux-use-ssh-windows/socks.png)

	> [AZURE.NOTE]**Remote DNS**를 선택하면 HDInsight 클러스터를 통해 DNS 요청이 확인됩니다. 선택하지 않은 경우 DNS가 로컬로 확인됩니다.

	Firefox에서 프록시 설정을 사용하거나 사용하지 않도록 설정한 상태에서 [http://www.whatismyip.com/](http://www.whatismyip.com/)과 같은 사이트를 방문하여 트래픽이 터널을 통해 라우팅되는지 확인할 수 있습니다. 활성화되어 있는 동안 IP 주소는 Microsoft Azure 데이터 센터에 있는 컴퓨터의 주소입니다.

###브라우저 확장

작동하는 터널을 사용하여 브라우저를 구성하는 동안 보통 터널을 통해 모든 트래픽을 라우팅하지 않으려고 할 수 있습니다. [FoxyProxy](http://getfoxyproxy.org/) 같은 브라우저 확장 프로그램이 URL 요청에 대해 일치하는 패턴을 지원하므로(FoxyProxy Standard 또는 Plus만 해당) 특정 URL에 대한 요청만 터널을 통해 전송됩니다.

FoxyProxy 표준을 설치한 경우 터널을 통해 HDInsight에 대한 트래픽을 전달만 하도록 구성하려면 다음 단계를 따르세요.

1. 브라우저에서 FoxyProxy 확장 프로그램을 엽니다. 예를 들어, Firefox에서 주소 필드 옆에 있는 FoxyProxy 아이콘을 선택합니다.

	![foxyproxy 아이콘](./media/hdinsight-hadoop-linux-use-ssh-windows/foxyproxy.png)

2. **Add New Proxy**를 선택하고 **General** 탭을 선택한 다음 **HDInsightProxy**의 프록시 이름을 입력합니다.

	![foxyproxy 일반](./media/hdinsight-hadoop-linux-use-ssh-windows/foxygeneral.png)

3. **Proxy Details** 탭을 선택하고 다음 필드를 입력합니다.

	* **Host or IP Address** - 로컬 컴퓨터에서 SSH 터널을 사용한 이후의 localhost입니다.

	* **Port** - SSH 터널에 사용한 포트입니다.

	* **SOCKS proxy** - 브라우저에서 터널을 프록시로 사용할 수 있도록 하려면 선택합니다.

	* **SOCKS v5** - 필요한 버전의 프록시를 설정하려면 선택합니다.

	![foxyproxy 프록시](./media/hdinsight-hadoop-linux-use-ssh-windows/foxyproxyproxy.png)

4. **URL Patterns** 탭을 선택한 다음 **Add New Pattern**을 선택합니다. 다음을 사용하여 패턴을 정의하고 **OK**를 클릭합니다.

	* **Pattern Name** - **headnode** - 패턴의 이름입니다.

	* **URL pattern** - ***headnode*** - **headnode**라는 단어가 포함된 모든 URL과 일치하는 패턴을 정의합니다.

	![foxyproxy 패턴](./media/hdinsight-hadoop-linux-use-ssh-windows/foxypattern.png)

4. **OK**를 클릭하여 프록시를 추가하고 **Proxy Settings**를 닫습니다.

5. FoxyProxy 대화 상자의 위쪽에서 **Select Mode**를 **Use proxies based on their pre-defined patterns and priorities**로 변경한 다음 **Close**를 클릭합니다.

	![foxyproxy 선택 모드](./media/hdinsight-hadoop-linux-use-ssh-windows/selectmode.png)

이러한 단계를 따른 후에는 **headnode** 문자열이 포함된 URL에 대한 요청만 SSL 터널을 통해 라우팅됩니다.

##다음 단계

이제 SSH 키를 사용하여 인증하는 방법을 배웠으므로 HDInsight에서 Hadoop과 MapReduce를 함께 사용하는 방법에 알아봅니다.

* [HDInsight에서 Hive 사용](hdinsight-use-hive.md)

* [HDInsight에서 Pig 사용](hdinsight-use-pig.md)

* [HDInsight에서 MapReduce 작업 사용](hdinsight-use-mapreduce.md)

<!---HONumber=July15_HO4-->