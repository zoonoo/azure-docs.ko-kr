<properties 
   pageTitle="Linux 기반 HDInsight에서 Hadoop 클러스터 프로비전 | Azure" 
   description="관리 포털, 명령줄 및.NET SDK를 사용하여 Linux 기반 HDInsight에서 Hadoop 클러스터를 프로비전하는 방법을 알아봅니다." 
   services="hdinsight" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="02/18/2015"
   ms.author="nitinme"/>


# 사용자 지정 옵션을 사용하여 HDInsight의 Hadoop Linux 클러스터 프로비전(미리 보기)

이 문서에서는 Azure 관리 포털, PowerShell, 명령줄 도구 또는 HDInsight .NET SDK를 사용하여 Azure HDInsight에서 사용자 지정 방식으로 Hadoop Linux 클러스터를 프로비전하는 다양한 방법을 알아봅니다.

## HDInsight 클러스터의 정의

Hadoop 또는 BigData에 대해 이야기할 때마다 클러스터를 언급한 이유가 궁금하셨나요? 그것은 바로 Hadoop을 사용하면 여러 다른 클러스터 노드에 분산된 대규모 데이터를 처리할 수 있기 때문입니다. 클러스터는 하나의 마스터(헤드 노드 또는 이름 노드라고도 부름)와 개수에 관계없는 슬레이브(데이터 노드라고도 부름)가 있는 마스터/슬레이브 아키텍처를 갖습니다. 자세한 내용은 <a href="http://go.microsoft.com/fwlink/?LinkId=510084" target="_blank">Apache Hadoop</a>를 참조하세요.

![HDInsight Cluster][img-hdi-cluster]

HDInsight 클러스터는 Hadoop 구현 세부 정보를 추출하므로 여러 다른 클러스터 노드와 통신하는 방법을 걱정할 필요가 없습니다. HDInsight 클러스터를 프로비전할 때는 Hadoop과 관련 응용 프로그램을 포함하는 Azure 계산 리소스를 프로비전합니다. 자세한 내용은 [HDInsight의 Hadoop 소개](../hdinsight-hadoop-introduction/)(영문)을 참조하세요. 변동 대상 데이터는 HDInsight의 컨텍스트에서 Azure Blob 저장소( *Azure Storage - Blob* 또는 WASB라고도 함)에 저장됩니다. 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용](../hdinsight-use-blob-storage/)을 참조하세요.

이 문서에서는 클러스터를 프로비전하는 여러 다른 방법에 대한 지침을 제공합니다. 클러스터를 프로비전하는 빠른 시작 접근 방법을 원하는 경우 [Linux에서 Azure HDInsight 시작](../hdinsight-hadoop-linux-get-started)(영문)을 참조하세요.

**필수 조건:**

이 문서를 시작하기 전에 다음이 있어야 합니다.

- Azure 구독. Azure는 구독 기반 플랫폼입니다. HDInsight PowerShell cmdlet은 구독을 사용하여 작업을 수행합니다. 구독을 얻는 방법에 대한 자세한 내용은 <a href="http://azure.microsoft.com/ pricing/purchase-options/" target="_blank">구매 옵션</a>, <a href="http://azure.microsoft.com/ pricing/member-offers/" target="_blank">멤버 제안</a>또는 <a href="http://azure.microsoft.com/ pricing/free-trial/" target="_blank">무료 평가판</a>을 참조하세요.
- SSH 키. Linux에 연결으로 연결하려면 SSH를 사용하여 클러스터합니다. SSH 키를 생성하는 방법에 대한 지침은 다음 문서를 참조
	-  Linux 컴퓨터에서 - [Linux에서 Linux 기반 HDInsight(Hadoop), Unix 또는 OS X으로 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix).
	-  Windows 컴퓨터에서 - [Windows에서 Linux 기반 HDInsight(Hadoop)으로 SSH 사용](../hdinsight-hadoop-linux-use-ssh-windows).

## 이 문서의 내용

* [구성 옵션](#configuration)
* [HDInsight Linux 클러스터 프로비전에 대한 옵션](#options)
* [다음 단계](#nextsteps)

## <a id="configuration"></a>구성 옵션

### Linux 기반 클러스터

HDInsight는 Azure에서 Linux 클러스터를 프로비전의 옵션을 제공합니다. Linux 기반으로 작성된 Hadoop 에코 시스템 구성 요소와 쉽게 통합 또는 Linux 또는 Unix를 사용하고 기존 Hadoop Linux 기반 솔루션에서 마이그레이션에 익숙한 경우에 Linux 클러스터를 프로비전합니다. Linux에서 Azure HDInsight에 대한 자세한 내용은 [HDInsight에서 Hadoop 소개](../hdinsight-hadoop-introduction)를 참조하세요. 

### 저장소 추가

구성 중에는 Azure Blob 저장소 계정과 기본 컨테이너를 지정해야 합니다. 이 컨테이너는 클러스터에서 기본 저장소 위치로 사용됩니다. 필요한 경우 역시 클러스터에 연결되는 추가 Blob도 지정할 수 있습니다.

보조 Blob 저장소 사용에 대한 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용](http://azure.microsoft.com/ documentation/articles/hdinsight-use-blob-storage/)을 참조하세요.

### 메타 저장소

메타 저장소는 Hive 테이블, 파티션, 스키마, 열 등에 대한 정보를 포함합니다. 이 정보는 Hive가 HDFS 또는 HDInsight용 WASB에서 데이터가 저장되는 위치를 찾는 데 사용됩니다. Hive는 기본적으로 포함된 데이터베이스를 사용하여 이 정보를 저장합니다.

HDInsight 클러스터를 프로비전할 때는 Hive용 메타 저장소를 포함할 SQL 데이터베이스를 지정할 수 있습니다. 그러면 메타데이터 정보가 SQL 데이터베이스에 외부 저장되므로 클러스터를 삭제해도 해당 정보를 보존할 수 있습니다.

> [WACOM.NOTE] 이제 .NET SDK를 사용하여 Linux용 HDInsight를 프로비전하는 동안에 메타 스토어를 사용하는 옵션만 사용할 수 있습니다. 자세한 내용은 [.NET SDK를 사용하여 Linux에서 HDInsight 클러스터 프로비전](#sdk)을 참조하세요.


## <a id="options"></a> HDInsight Linux 클러스터 프로비전에 대한 옵션

Windows 컴퓨터와 Linux 컴퓨터에서 HDInsight Hadoop Linux 클러스터를 프로비전 할 수 있습니다. 다음 표에서 서로 다른 운영 체제에서 사용할 수 있는 프로비전 옵션에 대한 정보를 제공하고 각각에 대한 지침을 연결합니다.

다음 운영 체제를 실행하는 컴퓨터에서 Linux 클러스터 프로비전| Azure 관리 포털 사용하기 | 플랫폼간 CLI 사용하기 | .NET SDK 사용하기 | PowerShell 사용하기
-----------------| ------------------------| -------------------| ---------- | ---------
Linux| [여기](#portal) 클릭 |[여기](#cli) 클릭| 해당 사항 없음 | 곧 사용 가능
Windows | [여기](#portal) 클릭 | [여기](#cli) 클릭 | [여기](#sdk) 클릭 | 곧 사용 가능

### <a id="portal"></a> Azure 관리 포털 사용

HDInsight 클러스터는 Azure Blob 저장소 컨테이너를 기본 파일 시스템으로 사용합니다. HDInsight 클러스터를 만들려면 먼저 동일한 데이터 센터에 위치한 Azure 저장소 계정이 필요합니다. 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용](../hdinsight-use-blob-storage)을 참조하세요. Azure 저장소 계정 만들기에 대한 자세한 내용은 [저장소 계정을 만드는 방법][azure-create-storageaccount]을 참조하세요.


> [WACOM.NOTE] 현재 **동아시아**, **동남아시아**, **북유럽**, **서유럽**, **미국 동부**, **미국 서부**, **미국 북중부**, **미국 남중부** 지역에서만 HDInsight 클러스터를 호스트할 수 있습니다.

**사용자 지정 만들기 옵션을 사용하여 HDInsight 클러스터를 만들려면**

1. [Azure 관리 포털][azure-management-portal]에 로그인합니다.
2. 페이지 아래에서 **+ 새로 만들기**를 클릭한 후 **데이터 서비스**, **HDINSIGHT**, **사용자 지정 만들기**를 차례로 클릭합니다.
3. **클러스터 세부 정보** 페이지에서 다음 값을 입력하거나 선택합니다.

	![Provide Hadoop HDInsight cluster details](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page1.png)

    <table border='1'>
		<tr><th>속성</th><th>값</th></tr>
		<tr><td>클러스터 이름</td>
			<td><p>클러스터의 이름 </p>
				<ul>
				<li>DNS 이름은 영숫자로 시작되고 끝나야 하며 대시를 포함할 수 있습니다.</li>
				<li>필드는 3자에서 63자의 문자열이어야 합니다.</li>
				</ul></td></tr>
		<tr><td>클러스터 유형</td>
			<td>클러스터 유형으로 <strong>Hadoop</strong>.</td></tr>
		<tr><td>운영 체제</td>
			<td>Linux에서 HDInsight을 프로비전하려면 <b>Ubuntu 12.04 LTS 미리 보기</b>를 선택합니다. Windows 클러스터를 프로비전하려면, <a href="http://azure.microsoft.com/ documentation/articles/hdinsight-provision-clusters/" target="_blank">Windows 기반 HDInsight에서 Hadoop 클러스터 프로비전</a>을 참조하세요.</td></tr>
		<tr><td>HDInsight 버전</td>
			<td>버전 선택. Linux에서 HDInsight의 기본값은 HDInsight 버전 3.2이며 Hadoop 2.5를 사용합니다.</td></tr>
		</table>

	테이블에 표시되는 대로 값을 입력하거나 선택하고 오른쪽 화살표를 클릭합니다.

4. **클러스터 구성** 페이지에서 다음 값을 입력하거나 선택합니다.

	<table border="1">
	<tr><th>이름</th><th>값</th></tr>
	<tr><td>데이터 노드</td><td>배포하려는 데이터 노드 수입니다. 테스트 목적으로 단일 노드 클러스터를 만듭니다. <br />클러스터 크기 제한은 Azure 구독에 따라 다릅니다. 제한을 늘리려면 Azure 청구 지원 팀에 문의하세요.</td></tr>
	<tr><td>지역/가상 네트워크</td><td><p>마지막 절차에서 만든 저장소 계정과 동일한 지역을 선택합니다. HDInsight는 저장소 계정이 동일한 지역에 있어야 합니다. 구성의 뒷부분에서 여기서 지정한 지역과 동일한 지역에 있는 저장소 계정만 선택할 수 있습니다.</p><p>사용 가능한 지역은 <strong>동아시아</strong>, <strong>동남아시아</strong>, <strong>북유럽</strong>, <strong>서유럽</strong>, <strong>미국 동부</strong>, <strong>미국 서부</strong>, <strong>미국 중북부</strong>, <strong>미국 중남부</strong><br/></p></td></tr>
	</table>



5. **클러스터 사용자 구성** 페이지에서 다음 값을 제공합니다.

    ![Provide Hadoop HDInsight cluster user](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page3.png)

    <table border='1'>
		<tr><th>속성</th><th>값</th></tr>
		<tr><td>HTTP 암호</td>
			<td>기본 HTTP 사용자의 암호를 지정합니다. <i>admin</i>.</td></tr>
		<tr><td>SSH 사용자 이름</td>
			<td>SSH 사용자 이름을 지정합니다. 이 사용자 이름은 HDInsight 클러스터 노드에서 원격 SSH 세션을 시작하려면 사용합니다.</td></tr>
		<tr><td>SSH 인증 유형</td>
			<td>SSH 사용자를 인증하는 암호를 사용할지 또는 SSH 키를 사용할지 여부를 지정합니다.</td></tr>
		<tr><td>SSH 암호</td>
			<td>인증 유형으로 암호를 선택한 경우 SSH 사용자를 인증하기 위해 SSH 암호를 지정합니다. 원격 Linux 컴퓨터에서 SSH 세션을 시작하려고 할 때 이 암호에 대한 메시지가 표시됩니다.</td></tr>
		<tr><td>SSH 공개키</td>
			<td>인증 유형으로 키를 선택한 경우 이미 생성한 SSH 공용 키를 지정합니다. Linux 클러스터의 메모와 함께 SSH 세션을 시작하면 이 공용 키와 연결된 개인 키를 사용합니다.<br>
			Linux 컴퓨터에서 SSH 키를 생성하는 방법에 대한 지침을 보려면 <a href="http://azure.microsoft.com/ documentation/articles/hdinsight-hadoop-linux-use-ssh-unix/" target="_blank">여기를 참조하세요</a>. Windows 컴퓨터에서 SSH 키를 생성하는 방법에 대한 지침을 보려면 <a href="http://azure.microsoft.com/ documentation/articles/hdinsight-hadoop-linux-use-ssh-windows/" target="_blank">여기를 참조하세요</a>.
		</td></tr>
		</table>

	> [WACOM.NOTE] 암호 인증보다 더 안전하기 때문에 SSH와 함께 SSH 공개키 인증을 사용하는 것을 좋습니다.

	오른쪽 화살표를 클릭합니다.


6. **저장소 계정** 페이지에서 다음 값을 제공합니다.

    ![Provide storage account for Hadoop HDInsight cluster](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page4.png)

	<table border='1'>
		<tr><th>속성</th><th>값</th></tr>
		<tr><td>저장소 계정</td>
			<td>HDInsight 클러스터의 기본 파일 시스템으로 사용할 Azure 저장소 계정을 지정합니다. 세 가지 옵션 중 하나를 선택할 수 있습니다.
			<ul>
				<li>기존 저장소 사용</li>
				<li>새 저장소 만들기</li>
				<li>다른 구독의 저장소 사용</li>
			</ul>
			</td></tr>
		<tr><td>계정 이름</td>
			<td><ul>
				<li>기존 저장소를 사용하도록 선택할 경우 <strong>계정 이름</strong>으로 기존 저장소 계정을 선택합니다. 드롭다운에는 클러스터를 프로비전하도록 선택한 동일한 데이터 센터에 있는 저장소 계정만 표시됩니다.</li>
				<li>새 저장소 만들기 <strong>또는</strong> 다른 구독의 저장소 사용 <strong>옵션을 선택한 경우</strong> 저장소 계정 이름을 입력해야 합니다.</li>
			</ul></td></tr>
		<tr><td>계정 키</td>
			<td>다른 구독의 저장소 사용 <strong>옵션을 선택한 경우</strong> 해당 저장소 계정의 계정 키를 지정합니다.</td></tr>
		<tr><td>기본 컨테이너</td>
			<td><p>HDInsight 클러스터의 기본 파일 시스템으로 사용할 저장소 계정의 기본 컨테이너를 지정합니다. 새 저장소 만들기 <strong>필드에</strong> 대해 <strong>기존 저장소 사용</strong> 을 선택했으며 해당 계정에 기존 컨테이너가 없는 경우 기본적으로 클러스터 이름과 같은 이름의 컨테이너가 만들어집니다. 클러스터의 이름을 가진 컨테이너가 이미 있는 경우에는 컨테이너 이름에 시퀀스 번호가 추가됩니다. 예를 들어 mycontainer1, mycontainer2 등과 같습니다. 그렇지만 기존 저장소 계정에 지정한 클러스터 이름과는 다른 이름의 컨테이너가 들어 있으면 해당 컨테이너도 사용할 수 있습니다.</p>
            <p>새 저장소를 만들거나 다른 Azure 구독의 저장소를 사용하도록 선택한 경우 기본 컨테이너 이름을 지정해야 합니다.</p>
        </td></tr>
		<tr><td>추가 저장소 계정</td>
			<td>HDInsight는 여러 저장소 계정을 지원합니다. 클러스터에서 사용할 수 있는 추가 저장소 계정의 수에는 제한이 없습니다. 하지만 관리 포털을 사용하여 클러스터를 만드는 경우에는 UI 제약으로 인해 7개로 제한됩니다. 지정하는 각 추가 저장소 계정은 마법사에 계정 정보를 지정할 수 있는 저장소 계정 페이지를 더합니다. 예를 들어 위의 스크린샷에서 하나의 추가 저장소 계정이 선택되면 5페이지가 대화 상자에 추가됩니다.</td></tr>
	</table>

	오른쪽 화살표를 클릭합니다.

7. 클러스터에 대한 추가 저장소를 구성하도록 선택한 경우 **저장소 계정** 페이지에서 추가 저장소 계정에 대한 계정 정보를 입력합니다.

	![Provide additional storage details for HDInsight cluster](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page5.png)

    여기서 다시 기존 저장소에서 선택하거나, 새 저장소를 만들거나, 다른 Azure 구독의 저장소를 사용하기 위한 옵션이 제공됩니다. 값을 제공하는 절차는 이전 단계와 비슷합니다.

    > [WACOM.NOTE] HDInsight 클러스터의 Azure 저장소 계정을 선택하고 나면 계정을 삭제할 수 없으며 다른 계정으로 변경할 수도 없습니다.

 	추가 저장소 계정을 지정한 후 클러스터 프로비전을 시작하려면 확인 표시를 클릭합니다. 

### <a id="cli"></a> 플랫폼 간 명령줄 사용

> [WACOM.NOTE] 2014/8/29일 현재 크로스 플랫폼 명령줄 인터페이스를 사용하여 클러스터를 Azure 가상 네트워크와 연결할 수는 없습니다.

HDInsight 클러스터를 프로비전하는 다른 옵션은 플랫폼 간 명령줄 인터페이스입니다. 명령줄 도구는Node.js로 구현되며 Windows, Mac, Linux를 포함하여 Node.js를 지원하는 플랫폼에서 사용할 수 있습니다. 명령줄 도구는 오픈 소스입니다.  소스 코드는 GitHub( <a href= "https://github.com/Azure/azure-sdk-tools-xplat">https://github.com/Azure/azure-sdk-tools-xplat</a>)에서 관리합니다. 명령줄 인터페이스 사용 방법에 대한 일반적인 설명은 [Mac 및 Linux에서 Azure 명령줄 도구를 사용하는 방법][azure-command-line-tools]을 참조하세요. 포괄적인 참조 설명서는 [Mac 및 Linux용 Azure 명령줄 도구][azure-command-line-tool]를 참조하세요.

- [Azure 크로스 플랫폼 명령줄 설정](#clilin)
- [Windows Azure 크로스 플랫폼 명령줄 설정](#cliwin)
- [Azure 크로스 플랫폼 명령줄을 사용하여 HDInsight 클러스터 프로비전](#cliprovision)

#### <a id="clilin"></a>Linux 크로스 플랫폼 명령줄 설정

Azure 명령줄 도구를 사용하여 Linux 컴퓨터를 설정하려면 다음 절차를 수행합니다.

- NPM을 사용하여 크로스 플랫폼 명령줄 설치
- Azure 구독에 연결

**NPM을 사용하여 명령줄 인터페이스를 설치하려면**

1.	Linux 컴퓨터에서 터미널 창을 열고 다음 명령을 실행합니다.

		sudo npm install -g azure-cli

2.	다음 명령을 실행하여 설치를 확인합니다.

		azure hdinsight -h

	여러 수준에서 *-h* 스위치를 사용하여 도움말 정보를 표시할 수 있습니다. 예를 들면 다음과 같습니다.

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Azure 구독에 연결하려면**

명령줄 인터페이스를 사용하려면 먼저 워크스테이션과 Azure 사이의 연결을 구성해야 합니다. Azure 구독 정보는 명령줄 인터페이스에서 계정에 연결하는 데 사용됩니다. 이 정보는 Azure의 게시 설정 파일에서 구할 수 있습니다. 그런 다음 게시 설정 파일을 영구적 로컬 구성 설정으로 가져와서 명령줄 인터페이스에서 후속 작업에 사용할 수 있습니다. 게시 설정을 한 번만 가져와야 합니다.

> [WACOM.NOTE] 게시 설정 파일에는 중요 정보가 포함되어 있습니다. 파일을 삭제하거나 파일이 포함된 사용자 폴더를 암호화하는 추가 단계를 수행하는 것이 좋습니다. Windows에서 폴더 속성을 수정하거나 BitLocker를 사용합니다. 


1.	터미널 창을 엽니다.
2.	Azure 구독에 로그인하려면 다음 명령을 실행합니다.

		azure account download

	![HDI.Linux.CLIAccountDownloadImport](./media/hdinsight-hadoop-provision-linux-clusters/HDI.Linux.CLIAccountDownloadImport.png)

	이 명령은 게시 설정 파일을 다운로드할 웹 페이지를 시작합니다. 웹 페이지가 열리지 않으면 브라우저 페이지를 열고 터미널 창에서 해당 링크를 클릭한 다음 포털에 로그인합니다. 

3.	컴퓨터에 게시 설정 파일을 다운로드합니다.
5.	명령 프롬프트 창에서 다음 명령을 실행하여 게시 설정 파일을 가져옵니다.

		azure account import <path/to/the/file>

	
#### <a id="cliwin"></a>Windows 크로스 플랫폼 명령줄 설정

Azure 명령줄 도구를 사용하여 Windows 컴퓨터를 설정하려면 다음 절차를 수행합니다.

- 크로스 플랫폼 명령줄 설치(NPM 또는 Windows installer를 사용하여)
- Azure 계정 게시 설정 다운로드 및 가져오기


명령줄 인터페이스는 *Node.js Package Manager (NPM)* 또는 Windows Installer를 사용하여 설치할 수 있습니다. 두 옵션 중 하나만 사용해서 설치하는 것이 좋습니다.

**NPM을 사용하여 명령줄 인터페이스를 설치하려면**

1.	**www.nodejs.org**로 이동합니다.
2.	**INSTALL**을 클릭하고 기본 설정을 사용하여 지침을 따릅니다.
3.	워크스테이션에서 **명령 프롬프트**(또는 *Azure Command Prompt* 또는  *Developer Command Prompt for VS2012*)를 엽니다.
4.	명령 프롬프트 창에서 다음 명령을 실행합니다.

		npm install -g azure-cli

	> [WACOM.NOTE] NPM 명령을 찾을 수 없다는 오류 메시지가 나타나는 경우 PATH 환경 변수에 있는 다음 경로를 확인합니다. <i>C:\Program Files (x86)\nodejs;C:\Users\[username]\AppData\Roaming\npm</i> 또는 <i>C:\Program Files\nodejs;C:\Users\[username]\AppData\Roaming\npm</i>

5.	다음 명령을 실행하여 설치를 확인합니다.

		azure hdinsight -h

	여러 수준에서 *-h* 스위치를 사용하여 도움말 정보를 표시할 수 있습니다. 예를 들면 다음과 같습니다.

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Windows Installer를 사용하여 명령줄 인터페이스를 설치하려면**

1.	**http://azure.microsoft.com/ko-kr/downloads/**로 이동합니다.
2.	**명령줄 도구** 섹션으로 스크롤하여 **크로스 플랫폼 명령줄 인터페이스**를 클릭하고 웹 플랫폼 설치 관리자 마법사를 따릅니다.

**게시 설정을 다운로드하거나 가져오려면**

명령줄 인터페이스를 사용하려면 먼저 워크스테이션과 Azure 사이의 연결을 구성해야 합니다. Azure 구독 정보는 명령줄 인터페이스에서 계정에 연결하는 데 사용됩니다. 이 정보는 Azure의 게시 설정 파일에서 구할 수 있습니다. 그런 다음 게시 설정 파일을 영구적 로컬 구성 설정으로 가져와서 명령줄 인터페이스에서 후속 작업에 사용할 수 있습니다. 게시 설정을 한 번만 가져와야 합니다.

> [WACOM.NOTE] 게시 설정 파일에는 중요 정보가 포함되어 있습니다. 파일을 삭제하거나 파일이 포함된 사용자 폴더를 암호화하는 추가 단계를 수행하는 것이 좋습니다. Windows에서 폴더 속성을 수정하거나 BitLocker를 사용합니다.


1.	**명령 프롬프트**를 엽니다.
2.	다음 명령을 실행하여 게시 설정 파일을 다운로드합니다.

		azure account download

	![HDI.CLIAccountDownloadImport][image-cli-account-download-import]

	이 명령은 게시 설정 파일을 다운로드할 웹 페이지를 시작합니다.

3.	파일을 저장하라는 메시지가 표시되면 **저장**을 클릭하고 파일을 저장해야 하는 위치를 제공합니다.
5.	명령 프롬프트 창에서 다음 명령을 실행하여 게시 설정 파일을 가져옵니다.

		azure account import <path/to/the/file>

	
#### <a id="cliprovision"></a>Azure 크로스 플랫폼 명령줄을 사용하여 HDInsight 클러스터 프로비전

플랫폼 간 명령줄을 사용하여 HDInsight 클러스터를 프로비전하려면 다음 절차가 필요합니다.

- Azure 저장소 계정 만들기
- 클러스터 프로비전


**Azure 저장소 계정을 만들려면**

HDInsight는 Azure Blob 저장소 컨테이너를 기본 파일 시스템으로 사용합니다. HDInsight 클러스터를 만들려면 먼저 Azure 저장소 계정이 필요합니다. 저장소 계정은 동일한 데이터 센터에 위치해야 합니다.

- 명령 프롬프트 창에서 다음 명령을 실행하여 Azure 저장소 계정을 만듭니다.

		azure storage account create [options] <StorageAccountName>

	위치를 묻는 메시지가 표시되면 HDINsight 클러스터를 프로비전할 수 있는 위치를 선택합니다. 저장소는 HDInsight 클러스터와 동일한 위치에 있어야 합니다. 현재 **동아시아**, **동남아시아**, **북유럽**, **서유럽**, **미국 동부**, **미국 서부**, **미국 북중부**, **미국 남중부** 지역에서만 HDInsight 클러스터를 호스트할 수 있습니다.  

Azure 관리 포털을 사용하여 Azure 저장소 계정을 만드는 방법에 대한 자세한 내용은 [저장소 계정 만들기, 관리 또는 삭제][azure-create-storageaccount]를 참조하세요.

저장소 계정이 이미 있지만 계정 이름과 계정 키를 모르는 경우 다음 명령을 사용하여 정보를 검색할 수 있습니다.

	-- 저장소 계정 나열
	Azure 저장소 계정 목록

	-- 저장소 계정에 대한 정보 표시
	Azure 저장소 계정 보기 <StorageAccountName>

	-- 저장소 계정에 대한 키 나열
	azure 저장소 계정 키 목록 < StorageAccountName >

관리 포털을 사용하여 정보를 가져오는 방법에 대한 자세한 내용은 [저장소 계정 만들기, 관리 또는 삭제][azure-create-storageaccount]의 *How to: View, copy and regenerate storage access keys* 섹션을 참조하세요.

또한 HDInsight 클러스터를 사용하려면 저장소 계정 내에 컨테이너가 있어야 합니다. 제공한 저장소 계정에 아직 컨테이너가 없을 때  *azure hdinsight cluster create*를 실행하면 컨테이너 이름을 묻는 메시지가 표시되고 해당 이름으로 컨테이너가 만들어집니다. 그렇지만 미리 컨테이너를 만들려면 다음 명령을 사용할 수 있습니다.

	azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]

저장소 계정 및 Blob 컨테이너가 준비되면 클러스터를 만들 준비가 되었습니다.

**HDInsight 클러스터를 프로비전하려면**

- 명령 프롬프트 창에서 다음 명령을 실행합니다.

		azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey <storageAccountKey> --storageContainer <SorageContainerName> --nodes <NumberOfNodes> --location <DataCenterLocation> --username <HDInsightClusterUsername> --clusterPassword <HDInsightClusterPassword> --OSType Linux --SshUserName <SSH username> --SshPassword <SSH user password>

	![HDI.CLIClusterCreation][image-cli-clustercreation]


**구성 파일을 사용하여 HDInsight 클러스터를 프로비전하려면**

일반적으로 HDInsight 클러스터를 프로비전하고, 해당 작업을 실행한 후에 비용을 줄이기 위해 클러스터를 삭제합니다. 명령줄 인터페이스에는 클러스터를 프로비전할 때마다 다시 사용할 수 있도록 구성을 파일에 저장하는 옵션이 있습니다.

- 명령 프롬프트 창에서 다음 명령을 실행합니다.


		#Create the config file
		azure hdinsight cluster config create <file>

		#Add commands to create a basic cluster
		azure hdinsight cluster config set <file> --clusterName <ClusterName> --nodes <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --username "<Username>" --clusterPassword "<UserPassword>" --OSType Linux --SshUserName "<SSH username>" --SshPassword <SSH user password>

		#If requred, include commands to use additional blob storage with the cluster
		azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
		       --storageAccountKey "<StorageAccountKey>"

		#Run this command to create a cluster using the config file
		azure hdinsight cluster create --config <file>

	>[WACOM.NOTE] 메타스토어에 사용되는 Azure SQL 데이터베이스는 Azure HDInsight를 비롯한 다른 Azure 서비스로의 연결을 허용해야 합니다. Azure SQL 데이터베이스 대시보드의 오른쪽에서 서버 이름을 클릭합니다. 이 서버는 SQL 데이터베이스 인스턴스가 실행되는 서버입니다. 서버 보기에서 **구성**을 클릭하고 **Microsoft Azure 서비스**에 대해 **예**를 클릭한 다음 **저장**을 클릭합니다.


	![HDI.CLIClusterCreationConfig][image-cli-clustercreation-config]


**클러스터 세부 정보를 나열하고 표시하려면**

- 클러스터 세부 정보를 나열하고 표시하려면 다음 명령을 사용합니다.

		azure hdinsight cluster list
		azure hdinsight cluster show <ClusterName>

	![HDI.CLIListCluster][image-cli-clusterlisting]


**클러스터를 삭제하려면**

- 클러스터를 삭제하려면 다음 명령을 사용합니다.

		azure hdinsight cluster delete <ClusterName>



### <a id="sdk"></a> HDInsight .NET SDK 사용
HDInsight .NET SDK는 .NET 응용 프로그램에서 HDInsight로 더 쉽게 작업하도록 지원하는 .NET 클라이언트 라이브러리를 제공합니다.

SDK를 사용하여 Linux에서 HDInsight 클러스터를 프로비전하려면 다음 절차를 수행해야 합니다.

- HDInsight .NET SDK 설치
- 자체 서명된 인증서 만들기
- 콘솔 응용 프로그램 만들기
- 응용 프로그램 실행


**HDInsight .NET SDK를 설치하려면**

[NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started)(영문)에서 최근에 게시된 SDK 빌드를 설치할 수 있습니다. 지침은 다음 절차에서 설명합니다.

**자체 서명된 인증서를 만들려면**

자체 서명 인증서를 만들어 워크스테이션에 설치한 다음 Azure 구독에 업로드합니다. 관련 지침은 [자체 서명된 인증서 만들기](http://go.microsoft.com/fwlink/?LinkId=511138)를 참조하세요.


**Visual Studio 콘솔 응용 프로그램을 만들려면**

1. Visual Studio 2013을 엽니다.

2. 파일 메뉴에서 **새로 만들기**와 **프로젝트**를 차례로 클릭합니다.

3. 새 프로젝트에서 다음 값을 입력하거나 선택합니다.

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">속성</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">값</th></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">범주</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Templates/Visual C#/Windows</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">템플릿</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Console Application</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">이름</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateHDICluster</td></tr>
	</table>

4. **확인**을 클릭하여 프로젝트를 만듭니다.

5. **도구** 메뉴에서 **Nuget 패키지 관리**, **패키지 관리 콘솔**을 차례로 클릭합니다.

6. 콘솔에서 다음 명령을 실행하여 패키지를 설치합니다.

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	.NET 라이브러리 및 이에 대한 참조가 현재의 Visual Studio 프로젝트에 추가됩니다.

7. 솔루션 탐색기에서 **Program.cs**를 두 번 클릭하여 엽니다.

8. 파일 맨 위에 다음 using 문을 추가합니다.

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;


9. Main() 함수에서 다음 코드를 복사하여 붙여넣습니다.

        string thumbprint = "<CertificateThumbprint>";  
        string subscriptionid = "<AzureSubscriptionID>";
        string clustername = "<HDInsightClusterName>";
        string location = "<MicrosoftDataCenter>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string containername = "<HDInsightDefaultContainerName>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;
		string version = "<version>";
        string sshusername = "<ssh user name>";
        string sshpublickey = "<ssh public key>;
		
		//if required, provide details of the Hive and Oozie metastore that you want to configure. ServerName is the name of the server on which the SQL databases are provisioned. HiveStoreSqlDatabaseName and OozieStoreSqlDatabaseName are the names of databases created on the server. You can also use the same database for both Hive and Oozie metastores
		Metastore hiveMetastore = new Metastore("<ServerName>.database.windows.net", "<HiveStoreSqlDatabaseName>", "<SqlDatabaseUser>", "<SqlDatabasePassword>");
        Metastore oozieMetastore = new Metastore("<ServerName>.database.windows.net", "<OozieStoreSqlDatabaseName>", "<SqlDatabaseUser>", "<SqlDatabasePassword>");

        // Get the certificate object from certificate store using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.Thumbprint == thumbprint);

        // Create an HDInsightClient object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
        var client = HDInsightClient.Connect(creds);

		// Supply th cluster information
        ClusterCreateParametersV2 clusterInfo = new ClusterCreateParametersV2()
        {
            Name = clustername,
            Location = location,
            DefaultStorageAccountName = storageaccountname,
            DefaultStorageAccountKey = storageaccountkey,
            DefaultStorageContainer = containername,
            UserName = username,
            Password = password,
            ClusterSizeInNodes = clustersize
			Version = version,
            OSType = OSType.Linux,
            SshUserName = sshusername,
            SshPublicKey = sshpublickey,
			HiveMetastore = hiveMetastore,		//only if you created a hivemetastore object earlier
            OozieMetastore = oozieMetastore		//only if you created an ooziemetastore object earlier
        };

		// Configure Hive and Oozie if you opted for the metastores earlier
		clusterInfo.HiveConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("datanucleus.connectionPoolingType", "none"));
		clusterInfo.OozieConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("oozie.service.AuthorizationService.security.enabled", "false"));
        

		// Create the cluster
        Console.WriteLine("Creating the HDInsight cluster ...");

        ClusterDetails cluster = client.CreateCluster(clusterInfo);

        Console.WriteLine("Created cluster: {0}.", cluster.ConnectionUrl);
        Console.WriteLine("Press ENTER to continue.");
        Console.ReadKey();

10. main() 함수 시작 부분의 변수를 바꿉니다.

**응용 프로그램을 실행하려면**

Visual Studio에 응용 프로그램이 열려 있을 때 **F5** 키를 눌러 응용 프로그램을 실행합니다. 콘솔 창이 열리고 응용 프로그램의 상태가 표시되며 HDInsight 클러스터를 만들려면 몇 분정도 걸릴 수 있습니다.



## <a id="nextsteps"></a> 다음 단계
이 문서에서는 Linux에서 HDInsight Hadoop 클러스터를 프로비전하는 여러 가지 방법에 대해 알아보았습니다. 자세한 내용은 다음 문서를 참조하세요.

- [Linux에서 HDInsight와 함께 작업](../hdinsight-hadoop-linux-information). Linux에서 HDInsight 클러스터 관련 작업의 미묘한 차이를 알 수 있습니다.
- [Ambari를 사용하여 HDInsight 클러스터 관리](../hdinsight-hadoop-manage-ambari). Ambari 웹 또는 Ambari REST API를 사용하여 HDInsight 클러스터에서 Linux 기반 Hadoop을 모니터링하고 관리하는 방법에 대해 알아봅니다. 
- [HDInsight와 함께 MapReduce 사용][hdinsight-use-mapreduce]. 클러스터에서 MapReduce 작업을 실행하는 다양한 방법에 대해 알아봅니다.
- [HDInsight에서 MapReduce 사용][hdinsight-use-hive]. 클러스터에서 Hive 쿼리를 실행하는 다양한 방법에 대해 알아봅니다.
- [HDInsight에서 Pig 사용][hdinsight-use-pig]. 클러스터에서 Pig 작업 실행의 다양한 방법에 대해 알아봅니다.
- [HDInsight에서 Azure Blob 저장소 사용](../hdinsight-use-blob-storage). HDInsight가 HDInsight 클러스터에서 데이터를 저장하는데 Azure Blob 저장소를 사용하는 방법에 대해 알아봅니다.
- [HDInsight에 데이터 업로드][hdinsight-upload-data]. HDInsight 클러스터에서 Azure Blob 저장소에 저장된 데이터로 작업하는 방법에 대해 알아봅니다.

[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[hdinsight-hbase-custom-provision]: http://azure.microsoft.com/ documentation/articles/hdinsight-hbase-get-started/

[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/

[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: http://msdn.microsoft.com/library/windowsazure/dn479228.aspx

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli/
[azure-command-line-tool]: ../command-line-tools/
[azure-create-storageaccount]: ../storage-create-storage-account/

[azure-purchase-options]: http://azure.microsoft.com/ pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/ pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/ pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/ documentation/articles/hdinsight-administer-use-management-portal/#rdp


[Powershell-install-configure]: ../install-configure-powershell/

[image-hdi-customcreatecluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
[image-hdi-customcreatecluster-clusteruser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
[image-hdi-customcreatecluster-storageaccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
[image-hdi-customcreatecluster-addonstorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png




[image-cli-account-download-import]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIListClusters.png "List and show clusters"

[image-hdi-ps-provision]: ./media/hdinsight-provision-clusters/HDI.ps.provision.png

[img-hdi-cluster]: ./media/hdinsight-provision-clusters/HDI.Cluster.png

  [89e2276a]: /ko-kr/documentation/articles/hdinsight-use-sqoop/ "Use Sqoop with HDInsight"

<!--HONumber=45--> 
