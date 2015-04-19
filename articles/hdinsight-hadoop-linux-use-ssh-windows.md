<properties
   pageTitle="Windows에서 HDInsight의 Linux 기반 Hadoop과 SSH 키 사용 | Aure"
   description="SSH 키를 생성하고 사용하여 Linux 기반 HDInsight 클러스터에 인증하는 방법을 알아봅니다."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

##Windows에서 HDInsight의 Linux 기반 Hadoop과 SSH 사용(미리 보기)

HDInsight 클러스터 Linux 기반 암호 또는 SSH 키를 사용하여 SSH 액세스 보안의 옵션을 제공합니다. 이 문서에서는 PuTTY SSH 클라이언트를 사용하여 Windows 클라이언트에서 HDInsight에 연결하는 방법을 설명합니다.

> [AZURE.NOTE] 이 문서의 단계에서는 Windows 클라이언트를 사용한다고 가정합니다. Linux, Unix 또는 OS X 클라이언트를 사용하는 경우 [Linux, Unix 또는 OS X에서 HDInsight의 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

##필수 조건

* Windows 클라이언트용 **PuTTY** 및 **PuTTYGen**입니다. 이러한 유틸리티는 <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html</a>에서 사용할 수 있습니다.

* HTML5를 지원하는 모뎀 웹 브라우저

OR

* Azure PowerShell

OR

* Azure 플랫폼간 명령줄 도구

##SSH 정의

SSH는 원격 서버에서 로그인 및 원격으로 실행하는 명령 유틸리티입니다. Linux 기반 HDInsight와 함께 SSH는 클러스터 헤드 노드에 보안 연결을 설정하고 명령에서 입력하는 데 사용하는 명령줄을 제공합니다. 명령은 서버에서 직접 실행됩니다.

##SSH 키 만들기(옵션)

Linux 기반 HDInsight 클러스터를 만들 때, SSH 키를 사용할 때 암호 또는 SSH 키를 사용하여 인증하는 옵션이 있습니다. SSH 키는 인증서를 기반으로 보다 안전한 것으로 간주됩니다. 클러스터와 SSH 키를 사용하려는 경우에 다음 정보를 사용합니다.

1. **PuTTYGen**을 엽니다.

2. **키의 유형을 생성**하려는 경우 **SSH 2 RSA**를 클릭한 다음 **생성**을 클릭합니다.

	![PuTTYGen interface](./media/hdinsight-hadoop-linux-use-ssh-windows/puttygen.png)

3. 막대를 채울 때까지 진행률 표시줄 아래 영역에서 마우스를 이동합니다. 마우스를 이동하면 키를 생성하는 데 사용되는 임의의 데이터를 생성합니다.

	![moving the mouse around](./media/hdinsight-hadoop-linux-use-ssh-windows/movingmouse.png)

	키가 한 번 생성되면 공용 키가 표시됩니다.

4. 추가 보안을 위해 **키 암호** 필드에서 암호를 입력한 다음 **암호 확인** 필드에 동일한 값을 입력할 수 있습니다.

	![passphrase](./media/hdinsight-hadoop-linux-use-ssh-windows/key.png)
	
	> [AZURE.NOTE] 키에 대한 보안 암호를 사용하는 것이 좋습니다. 그러나 암호를 잊은 경우 복구할 수 있는 방법이 없습니다.

5. **개인 키 저장**을 클릭하여 **.ppk** 파일에 키를 저장합니다. 이 키는 Linux 기반 HDInsight 클러스터에 인증하는 데 사용됩니다.

	> [AZURE.NOTE] Linux 기반 HDInsight 클러스터에 액세스하는 데 사용하기 때문에 이 키를 안전한 위치에 저장해야 합니다.

6. **공개 키 저장**을 클릭하여 **.txt** 파일로 키를 저장합니다. 이 옵션을 사용하면 추가 Linux 기반 HDInsight 클러스터를 만들 때 공개 키를 나중에 다시 사용할 수 있습니다.

	> [AZURE.NOTE] 공개 키는 **PuTTYGen**의 상단에도 표시됩니다. 이 필드를 마우스 오른쪽 단추로 클릭하고, 값을 복사하고, Azure 관리 포털에서 HDInsight 마법사와 같은 양식에 붙여넣을 수 있습니다.

##Linux 기반 HDInsight 클러스터 만들기

Linux 기반 HDInsight 클러스터를 만들 때 이전에 생성한 **공개 키**를 제공해야 합니다. Windows 클라이언트에서는 두 가지의 Linux 기반 HDInsight 클러스터를 만들 수 있습니다.

* **Azure 관리 포털** - 웹 기반 포털을 사용하여 클러스터 생성

* **Azure 플랫폼 간 명령줄 인터페이스(xplat-cli)** - 명령줄 명령을 사용하여 클러스터 생성

이러한 각 메서드에는 **공개 키**가 필요합니다. Linux 기반 HDInsight 클러스터를 만드는 방법에 대한 자세한 내용은 <a href="./hdinsight-hadoop-provision-linux-clusters/" target="_blank">Linux 기반 HDInsight 클러스터 프로비전</a>을 참조하세요.

###Azure 관리 포털

Linux 기반 HDInsight 클러스터를 만들려면 포털을 사용하는 경우 다음과 같은 형식에 사용자 이름 및 암호 또는 공개 키를 입력해야 합니다.

![Image of form asking for public key](./media/hdinsight-hadoop-linux-use-ssh-windows/ssh-key.png)

이렇게 하면 지정된 사용자에 대한 로그인이 생성되고 암호 인증 또는 SSH 키 인증을 사용하면 됩니다.

###Azure 플랫폼간 명령줄 인터페이스

<a href="../xplat-cli/" target="_brad">Azure 플랫폼 간 명령줄 인터페이스</a>를 사용하면  `azure hdinsight cluster create` 명령을 사용하여 새 클러스터를 만들 수 있습니다.

이 명령은 사용에 대한 자세한 내용은 <a href="../hdinsight-hadoop-provision-linux-clusters/" target="_blank">사용자 지정 옵션을 사용하여 HDInsight에서 Hadoop Linux 클러스터 프로비전</a>을 참조하세요.

##<a id="connect"></a>Linux 기반 HDInsight 클러스터에 연결

1. PuTTY를 엽니다.

	![putty interface](./media/hdinsight-hadoop-linux-use-ssh-windows/putty.png)

2. 사용자 계정을 생성할 때 **SSH 키**를 제공한 경우 다음 단계를 수행하여 클러스터에 인증할 때 사용하려는 **개인 키**를 선택해야 합니다.

	**범주**에서 **연결**, **SSH**를 확장하고 **인증**을 선택합니다. 마지막으로, **찾아보기**를 클릭하고 **개인 키**가 포함된 **.ppk**를 선택하십시오.

	![putty interface, select private key](./media/hdinsight-hadoop-linux-use-ssh-windows/puttykey.png)

3. **범주**에서 **세션**을 선택합니다. **PuTTY 세션에 대한 기본 옵션** 화면에서 **호스트 이름(또는 IP 주소)** 필드에 HDInsight 서버의 SSH 주소를 입력합니다. SSH 주소는 **-ssh.azurehdinsight.net** 뒤의 클러스터 이름입니다. 예를 들면 **mycluster-ssh.azurehdinsight.net**입니다.

	![putty interface with ssh address entered](./media/hdinsight-hadoop-linux-use-ssh-windows/puttyaddress.png)

4. 나중에 사용할 연결 정보를 저장하려면 **저장 세션** 아래 이 연결을 위한 이름을 입력한 후 **저장**을 클릭합니다. 연결이 저장 세션의 목록에 추가됩니다.

5. **열기**를 클릭하여 클러스터에 연결합니다.

	> [AZURE.NOTE] 처음으로 클러스터에 연결한 경우 보안 경고를 받게 됩니다. 이것은 정상입니다. 서버의 RSA2 키를 계속 캐시하려면 **예**를 선택합니다.

6. 메시지가 표시되면 클러스터를 생성할 때 입력한 사용자를 입력합니다. 사용자에 대한 **암호**를 제공한 경우 사용자를 입력하라는 메시지도 나타납니다.

##추가 계정 추가

2. 이전에 설명된 것처럼 새로운 사용자 계정에 대한 새로운 **공개 키** 및 **개인 키**를 생성합니다.

1. SSH 세션에서 클러스터로 다음 명령을 사용하여 새 사용자를 추가합니다.

		sudo adduser --disabled-password <username> 

	이렇게 하면 새 사용자 계정이 생성되지만 암호 인증이 비활성화됩니다.

2. 다음 명령을 사용하여 키를 보유하는 파일 및 디렉터리를 만듭니다.

		sudo mkdir -p /home/<username>/.ssh
		sudo touch /home/<username>/.ssh/authorized_keys
		sudo nano /home/<username>/.ssh/authorized_keys

3. Nano 편집기를 열면 새로운 사용자 계정에 대한 **공개 키** 콘텐츠를 복사하고 붙여넣습니다. 마지막으로 **Ctrl X**를 사용하여 파일을 저장하고 편집기를 종료합니다.

	![image of nano editor with example key](./media/hdinsight-hadoop-linux-use-ssh-windows/nano.png)

4. 새 사용자 계정에 .ssh 폴더 및 내용의 소유권을 변경하려면 다음 명령을 사용합니다.

		sudo chown -hR <username>:<username> /home/<username>/.ssh

5. 이제 새 사용자 계정 및 **개인 키**를 사용하여 서버에 인증할 수 있습니다.

##<a id="tunnel"></a>SSH 터널링

SSH는 웹 요청과 같은 로컬 요청을 HDInsight 클러스터에 터널링하는 데 사용할 수도 있습니다. HDInsight 클러스터 헤드 노드에서 발생하는 경우 요청이 요청된 리소스에 라우팅됩니다.

클러스터에서 헤드 또는 작업자 노드에 대한 내부 도메인 이름을 사용하는 HDInsight 클러스터의 웹 기반 서비스에 엑세스할 때 가장 유용합니다. 예를 들어 Ambari 웹 페이지의 일부 섹션은 **headnode0.mycluster.d1.internal.cloudapp.net**과 같은 내부 도메인 이름을 사용합니다. 이 이름은 클러스터 외부에서 확인할 수 없지만 SSH를 통해 터널링된 요청은 클러스터 내에서 발생하고 올바르게 확인됩니다.

SSH 터널을 만들고 브라우저를 구성하여 클러스터에 연결하려면 다음 단계를 따르세요.

1. PuTTY를 열고 [연결] 섹션에서 설명된 연결 정보를(#connect) 입력합니다.

2. 대화 상자의 왼쪽에 있는 **범주** 섹션에서 **연결**, **SSH**를 확장하고, 마지막으로 **터널**을 선택합니다.

2. **SSH 포트 전달을 제어하는 옵션** 양식에 다음 정보를 제공합니다.

	* **원본 포트** -전달하고자 하는 클라이언트의 포트입니다. 예: **9876**

	* **대상** - Linux 기반 HDInsight 클러스터에 대한 SSH 주소입니다. 예를 들면 **mycluster-ssh.azurehdinsight.net**입니다.

	* **동적** - 동적 SOCKS 프록시 라우팅 활성화

	![image of tunneling options](./media/hdinsight-hadoop-linux-use-ssh-windows/puttytunnel.png)

3. 마지막으로 **추가**를 선택하여 설정을 추가한 다음 **열기**를 선택하여 SSH 연결을 엽니다.

4. 메시지가 표시되면 서버에 로그인합니다. 이 SSH 세션을 설정하고 터널을 사용하도록 설정합니다.

2. **localhost:9876**을 **SOCKS v5** 프록시로 사용하려면 Firefox와 같은 클라이언트 프로그램을 구성합니다. Firefox 설정은 다음과 같습니다.

	![image of Firefox settings](./media/hdinsight-hadoop-linux-use-ssh-windows/socks.png)

	> [AZURE.NOTE] **원격 DNS**를 선택하면 HDInsight 클러스터를 사용하여 DNS 요청을 해결합니다. 선택하지 않은 경우 DNS가 로컬로 해결됩니다.

	Firefox에서 프록시 설정을 사용하거나 사용하지 않도록 설정한 상태에서 <a href="http://www.whatismyip.com/" target="_blank">http://www.whatismyip.com/</a> 같은 사이트를 방문하여 트래픽이 터널을 통해 라우팅되는지 확인할 수 있습니다. 활성화되어 있는 동안 IP 주소는 Microsoft Azure 데이터 센터에 있는 컴퓨터의 주소입니다.

###브라우저 확장

작동하는 터널을 사용하여 브라우저를 구성하는 동안 보통 터널을 통해 모든 트래픽을 라우팅하지 않으려고 할 수 있습니다. <a href="http://getfoxyproxy.org/" target="_blank">FoxyProxy</a> 같은 브라우저 확장 프로그램이 URL 요청에 대해 일치하는 패턴을 지원하므로(FoxyProxy Standard 또는 Plus만 해당) 특정 URL에 대한 요청만 터널을 통해 전송됩니다.

**FoxyProxy 표준**을 설치한 경우 터널을 통해 HDInsight에 대한 트래픽을 전달만 하도록 구성하려면 다음 단계를 따르세요.

1. 브라우저에서 FoxyProxy 확장 프로그램을 엽니다. 예를 들어, Firefox에서 주소 필드 옆에 있는 FoxyProxy 아이콘을 선택합니다.

	![foxyproxy icon](./media/hdinsight-hadoop-linux-use-ssh-windows/foxyproxy.png)

2. **새 프록시 추가**, **일반** 탭을 선택한 다음 **HDInsight**의 프록시 이름을 입력하십시오.

	![foxyproxy general](./media/hdinsight-hadoop-linux-use-ssh-windows/foxygeneral.png)

3. **프록시 세부 정보 탭**을 선택하고 다음 필드를 채웁니다.

	* **호스트 또는 IP 주소** - 로컬 컴퓨터에서 SSH 터널을 사용한 이후의 localhost

	* **포트** - SSH 터널에 대해 사용되는 포트

	* **SOCKS 프록시** - 터널을 프록시로 사용하려는 브라우저를 활성화하려면 이 항목 선택

	* **SOCKS v5** - 프록시에 대한 필요한 버전을 설정하려면 이 항목 선택

	![foxyproxy proxy](./media/hdinsight-hadoop-linux-use-ssh-windows/foxyproxyproxy.png)

4. **URL 패턴** 탭을 선택한 다음 **새 패턴 추가**를 선택합니다. 다음을 따라 패턴을 정의한 다음 **확인**을 클릭합니다.

	* **패턴 이름** - **headnode** - 패턴에 대한 친숙한 이름

	* **URL 패턴** - **\*headnode\*** - **headnode** 단어가 포함된 모든 URL과 일치하는 패턴을 정의합니다.

	![foxyproxy pattern](./media/hdinsight-hadoop-linux-use-ssh-windows/foxypattern.png)

4. 프록시를 추가하고 **프록시 설정**을 닫으려면 **확인** 선택

5. FoxyProxy 대화 상자의 상단에서 **선택 모드**를 **미리 정의된 패턴 및 우선 순위에 따른 프록시 사용**으로 변경한 다음 **닫기**를 선택합니다.

	![foxyproxy select mode](./media/hdinsight-hadoop-linux-use-ssh-windows/selectmode.png)

다음 이 단계를 따른 후 **headnode** 문자열이 포함된 URL에 대한 요청만 SSL 터널을 통해 라우팅됩니다.

##다음 단계

이제 SSH 키를 사용하여 인증하는 방법을 배웠으므로 HDInsight에서 Hadoop과 MapReduce를 함께 사용하는 방법에 알아봅니다.

* [HDInsight에서 Hive 사용](hdinsight-use-hive.md)

* [HDInsight에서 Pig 사용](hdinsight-use-pig.md)

* [HDInsight에서 MapReduce 작업 사용](hdinsight-use-mapreduce.md)
 
<!--HONumber=47-->
