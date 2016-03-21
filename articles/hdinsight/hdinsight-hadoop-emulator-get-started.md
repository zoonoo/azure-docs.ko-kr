<properties
	pageTitle="HDInsight에 대해 Hadoop 에뮬레이터 시작 | Microsoft Azure"
	description="MapReduce 자습서 및 다른 샘플과 함께 설치된 에뮬레이터를 사용하여 Hadoop 에코 시스템에 대해 알아봅니다. HDInsight 에뮬레이터는 Hadoop 샌드박스처럼 작동합니다."
	keywords="에뮬레이터, hadoop 에코 시스템, hadoop 샌드박스, mapreduce 자습서"
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	authors="nitinme"
	documentationCenter=""
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article" 
	ms.date="11/29/2015"
	ms.author="nitinme"/>

# HDInsight Emulator와 Hadoop 샌드박스를 사용하여 Hadoop 에코 시스템에서 시작

이 자습서에서는 Microsoft HDInsight Emulator for Azure(이전의 HDInsight Server Developer Preview)에서 Hadoop 클러스터를 시작하는 방법을 설명합니다. HDInsight Emulator에는 Azure HDInsight와 동일한 Hadoop 에코시스템의 구성 요소가 제공됩니다. 배포된 버전에 대한 정보를 포함한 자세한 내용은 [Azure HDInsight에 포함된 Hadoop 버전](hdinsight-component-versioning.md)(영문)을 참조하세요.

에뮬레이터 설치되면 단어 개수에 대해 MapReduce 자습서를 따르고 샘플을 실행합니다.

> [AZURE.NOTE] HDInsight Emulator에는 Hadoop 클러스터만 포함될 수 있으며, HBase나 Storm은 포함되지 않습니다.


HDInsight Emulator에서는 Hadoop 샌드박스와 매우 비슷한 로컬 개발 환경을 제공합니다. Hadoop에 익숙한 경우 Hadoop 분산 파일 시스템(HDFS)을 사용하여 HDInsight Emulator를 시작할 수 있습니다. HDInsight에서 기본 파일 시스템은 Azure Blob 저장소입니다. 따라서 Azure Blob 저장소를 사용하여 작업을 개발하게 됩니다. HDInsight Emulator와 Azure Blob 저장소를 사용하려면 에뮬레이터의 구성을 변경해야 합니다.

> [AZURE.NOTE] HDInsight Emulator는 단일 노드 배포만 사용할 수 있습니다.


## 필수 조건
이 자습서를 시작하기 전에 다음이 있어야 합니다.

- HDInsight Emulator에는 64비트 버전의 Windows가 필요합니다. 다음 요구 사항 중 하나는 반드시 충족되어야 합니다.

	- Windows 10
	- Windows 8
	- Windows Server 2012

- **Azure PowerShell**. [Azure PowerShell 설치 및 사용](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)을 참조하세요.


##<a name="install"></a>HDInsight Emulator 설치

Microsoft HDInsight Emulator는 Microsoft 웹 플랫폼 설치 관리자를 통해 설치할 수 있습니다.

> [AZURE.NOTE] 현재 HDInsight Emulator에서는 영어 운영 체제만 지원합니다. 이전 버전의 에뮬레이터가 설치되어 있으면 최신 버전의 에뮬레이터를 설치하기 전에 제어판/프로그램 및 기능에서 다음 두 가지 구성 요소를 제거해야 합니다.
><ul>
> <li>Azure용 Microsoft HDInsight 에뮬레이터 또는 HDInsight Developer Preview 중 설치되어 있는 구성 요소</li>
> <li>Hortonworks Data Platform</li>
> </ul>


**HDInsight Emulator 설치**

1. Internet Explorer을 열고 [Microsoft HDInsight Emulator for Azure 설치 페이지][hdinsight-emulator-install]로 이동합니다.
2. **지금 설치**를 클릭합니다.
3. 페이지 아래쪽에서 HDINSIGHT.exe 설치를 묻는 메시지가 나타나면 **실행**을 클릭합니다.
4. 설치를 완료하기 위해 뜨는 **사용자 계정 컨트롤** 창에서 **예** 단추를 클릭합니다. 웹 플랫폼 설치 관리자 창이 나타납니다.
6. 페이지 아래쪽에서 **설치**를 클릭합니다.
7. 사용 조건에 동의하려면 **동의함**을 클릭합니다.
8. 웹 플랫폼 설치 관리자에서 **다음 제품이 설치되었습니다.**라고 표시되는지 확인한 후 **마침**을 클릭합니다.
9. **끝내기**를 클릭하여 웹 플랫폼 설치 관리자 창을 닫습니다.

**HDInsight Emulator 설치 확인**

바탕 화면에 세 개의 아이콘이 설치되어야 합니다. 세 개의 아이콘은 다음과 같이 연결됩니다.

- **Hadoop 명령줄** - MapReduce, Pig 및 Hive 작업의 Hadoop 명령 프롬프트가 HDInsight Emulator에서 실행됩니다.

- **Hadoop NameNode 상태** - NameNode는 HDFS의 모든 파일에 대해 트리 기반 디렉터리를 유지 관리합니다. 또한 모든 파일의 데이터가 Hadoop 클러스터에 보관되는 위치를 계속 추적합니다. 클라이언트는 NameNode와 통신하여 모든 파일의 데이터 노드가 저장되는 위치를 파악합니다.

- **Hadoop Yarn 상태** - MapReduce 작업을 클러스터에 있는 노드에 할당하는 작업 추적기입니다.

몇 가지 로컬 서비스도 설치되어야 합니다. 다음은 서비스 창의 스크린샷입니다.

![Hadoop 에코 시스템 서비스는 에뮬레이터 창에 나열됩니다.][image-hdi-emulator-services]

HDInsight Emulator와 관련된 서비스는 기본적으로 시작되지 않습니다. 서비스를 시작하려면 Hadoop 명령줄을 사용하여 **start\_local\_hdp\_services.cmd** under C:\\hdp(기본 위치)를 실행합니다. 컴퓨터를 다시 시작한 후 서비스를 자동으로 시작하려면**set-onebox-autostart.cmd**를 실행합니다.

HDInsight Emulator 설치 및 실행에 대해 알려진 문제는 [HDInsight Emulator 릴리스 정보](hdinsight-emulator-release-notes.md)를 참조하세요. 설치 로그는 **C:\\HadoopFeaturePackSetup\\HadoopFeaturePackSetupTools\\gettingStarted.winpkg.install.log**에 있습니다.

##<a name="vstools"></a>에뮬레이터를 사용하여 Visual Studio용 HDInsight 도구 시작

Visual Studio용 HDInsight 도구를 사용하여 HDInsight Emulator에 연결할 수 있습니다. Azure에서 HDInsight 클러스터와 Visual Studio 도구를 사용하는 방법에 대한 정보는 [Visual Studio용 HDInsight Hadoop 도구 사용 시작](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md)을 참조하세요.

### 에뮬레이터용 HDInsight 도구 설치

HDInsight Visual Studio 도구를 설치하는 방법에 대한 지침은 [여기](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md#installation)를 참조하세요.

### HDInsight Emulator에 연결

1. Visual Studio를 엽니다.
2. **보기** 메뉴에서 **서버 탐색기**를 클릭하여 서버 탐색기 창을 엽니다.
3. **Azure**를 확장하고 **HDInsight**를 클릭한 다음 **HDInsight Emulator에 연결**을 클릭합니다.

	 ![Visual Studio 보기: 메뉴에서 HDInsight 에뮬레이터에 연결합니다.](./media/hdinsight-hadoop-emulator-get-started/hdi.emulator.connect.vs.png)

4. HDInsight Emulator에 연결 대화 상자에서 WebHCat, HiveServer2 및 WebHDFS 끝점의 값을 확인하고 **다음**을 클릭합니다. 에뮬레이터의 기본 구성을 변경하지 않은 경우 기본적으로 입력된 값이 작동해야 합니다. 변경한 경우 대화 상자의 값을 업데이트하고 다음을 클릭합니다.

	![설정으로 HDInsight 에뮬레이터 대화 상자에 연결합니다.](./media/hdinsight-hadoop-emulator-get-started/hdi.emulator.connect.vs.dialog.png)

5. 연결이 설정되면 **완료**를 클릭합니다. 이제 서버 탐색기에 HDInsight Emulator가 표시되어야 합니다.

	![HDInsight 로컬 에뮬레이터, Hadoop 샌드박스를 보여주는 서버 탐색기에 연결합니다.](./media/hdinsight-hadoop-emulator-get-started/hdi.emulator.vs.connected.png)

연결이 설정되고 나면 Azure HDInsight 클러스터에서 사용하는 방식과 똑같이 에뮬레이터와 HDInsight VS 도구를 사용할 수 있습니다. Azure HDInsight 클러스터와 VS 도구를 사용하는 방법에 대한 지침은 [Visual Studio용 HDInsight Hadoop 도구 사용](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md)을 참조하세요.

## 문제 해결: HDInsight Emulator에 HDInsight 도구 연결

1. HDInsight Emulator를 연결하는 중에 대화 상자에 HiveServer2가 성공적으로 연결되었음이 표시되어도, C:\\hdp\\hive-*version*\\conf\\hive-site.xml의 Hive 구성 파일에서 수동으로 **hive.security.authorization.enabled prop 속성**을 **false**로 설정하고 로컬 에뮬레이터를 다시 시작해야 합니다. Visual Studio용 HDInsight 도구는 테이블에서 상위 100개의 행을 미리 보는 경우에만 HiveServer2에 연결됩니다. 이러한 쿼리를 사용하지 않으려는 경우 Hive 구성을 현상태 그대로 두면 됩니다.

2. HDInsight Emulator를 실행 중인 컴퓨터에서 동적 IP 할당(DHCP)을 사용하는 경우 C:\\hdp\\hadoop-*version*\\etc\\hadoop\\core-site.xml을 업데이트하고 **hadoop.proxyuser.hadoop.hosts** 속성의 값을 (*)로 변경해야 할 수도 있습니다. 그러면 Hadoop 사용자가 Visual Studio에 입력한 사용자를 가장하도록 모든 호스트에서 연결할 수 있습니다.

		<property>
			<name>hadoop.proxyuser.hadoop.hosts</name>
			<value>*</value>
		</property>

3. Visual Studio에서 WebHCat 서비스에 연결하려고 하면 오류가 발생할 수 있습니다(“오류”: “job\_XXXX\_0001 작업을 찾을 수 없음”). 이 경우 WebHCat 서비스를 다시 시작하고 다시 시도해야 합니다. WebHCat 서비스를 다시 시작하려면 **서비스** MMC를 시작하고 **Apache Hadoop Templeton**(WebHCat 서비스의 이전 이름)을 마우스 오른쪽 단추로 클릭한 다음 **다시 시작**을 클릭합니다.

##<a name="runwordcount"></a>단어 계산 MapReduce 자습서

이제 워크스테이션에 HDInsight Emulator를 구성했으므로 이 MapReduce 자습서를 시도하여 설치를 테스트할 수 있습니다. 먼저 일부 데이터 파일을 HDFS에 업로드한 후 단어 계산 MapReduce 작업을 실행하여 해당 파일에서 특정 단어의 빈도를 계산합니다.

단어 계산 MapReduce 프로그램은 *hadoop-mapreduce-examples-2.4.0.2.1.3.0-1981.jar*에 패키지되어 있습니다. jar 파일은 *C:\\hdp\\hadoop-2.4.0.2.1.3.0-1981\\share\\hadoop\\mapreduce* 폴더에 있습니다.

단어 계산 MapReduce 작업은 다음의 두 인수를 사용합니다.

- 입력 폴더. **hdfs://localhost/user/HDIUser*를 입력 폴더로 사용합니다.
- 출력 폴더. **hdfs://localhost/user/HDIUser/WordCount_Output*을 출력 폴더로 사용합니다. 출력 폴더는 기존 폴더가 아니어야 합니다. 그렇지 않으면 MapReduce 작업이 실패합니다. MapReduce 작업을 두 번째 실행하려면 다른 출력 폴더를 지정하거나 기존 출력 폴더를 삭제해야 합니다.

**단어 계산 MapReduce 작업 실행**

1. 바탕 화면에서 **Hadoop 명령줄**을 두 번 클릭하여 Hadoop 명령줄 창을 엽니다. 현재 폴더가 다음과 같아야 합니다.

		c:\hdp\hadoop-2.4.0.2.1.3.0-1981

	그렇지 않으면 다음 명령을 실행합니다.

		cd %hadoop_home%

2. 다음 Hadoop 명령을 실행하여 입력 및 출력 파일을 저장할 HDFS 폴더를 만듭니다.

		hadoop fs -mkdir /user
		hadoop fs -mkdir /user/HDIUser

3. 다음 Hadoop 명령을 실행하여 일부 로컬 텍스트 파일을 HDFS에 복사합니다.

		hadoop fs -copyFromLocal C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common*.txt /user/HDIUser

4. 다음 명령을 실행하여 /user/HDIUser 폴더의 파일을 나열합니다.

		hadoop fs -ls /user/HDIUser

	다음 파일이 표시되어야 합니다.

		C:\hdp\hadoop-2.4.0.2.1.3.0-1981>hadoop fs -ls /user/HDIUser
		Found 4 items
		-rw-r--r--   1 username hdfs     574261 2014-09-08 12:56 /user/HDIUser/CHANGES.txt
		-rw-r--r--   1 username hdfs      15748 2014-09-08 12:56 /user/HDIUser/LICENSE.txt
		-rw-r--r--   1 username hdfs        103 2014-09-08 12:56 /user/HDIUser/NOTICE.txt
		-rw-r--r--   1 username hdfs       1397 2014-09-08 12:56 /user/HDIUser/README.txt

5. 다음 명령을 실행하여 단어 계산 MapReduce 작업을 실행합니다.

		C:\hdp\hadoop-2.4.0.2.1.3.0-1981>hadoop jar C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\hadoop\mapreduce\hadoop-mapreduce-examples-2.4.0.2.1.3.0-1981.jar wordcount /user/HDIUser/*.txt /user/HDIUser/WordCount_Output

6. 다음 명령을 실행하여 출력 파일에서 "windows"를 포함하는 단어의 수를 나열합니다.

		hadoop fs -cat /user/HDIUser/WordCount_Output/part-r-00000 | findstr "windows"

	다음과 같이 출력되어야 합니다.

		C:\hdp\hadoop-2.4.0.2.1.3.0-1981>hadoop fs -cat /user/HDIUser/WordCount_Output/part-r-00000 | findstr "windows"
		windows 4
		windows.        2
		windows/cygwin. 1

Hadoop 명령에 대한 자세한 내용은 [Hadoop 명령 매뉴얼][hadoop-commands-manual](영문)을 참조하세요.

##<a name="rungetstartedsamples"></a> 샘플 웹 로그 데이터 분석

HDInsight Emulator를 설치하면 사용자가 Windows에서 Apache Hadoop 기반의 서비스 학습을 시작할 수 있도록 일부 샘플이 제공됩니다. 이러한 샘플에서는 일반적으로 빅 데이터 집합을 처리할 때 필요한 일부 작업을 다룹니다. 위의 MapReduce 자습서에서 빌드된 샘플로 MapReduce 프로그래밍 모델 및 에코시스템과 관련된 개념을 익힐 수 있습니다.

샘플 데이터는 IIS W3C(World Wide Web Consortium) 로그 데이터 처리를 위해 구성됩니다. 데이터 집합을 다양한 크기로 만들고 HDFS 또는 Azure Blob 저장소로 가져오도록 데이터 생성 도구가 제공됩니다. (자세한 내용은 [HDInsight에 Azure Blob 저장소 사용](hdinsight-hadoop-use-blob-storage.md)을 참조하세요.) Azure PowerShell 스크립트를 통해 생성된 데이터 페이지에서 MapReduce, Pig 또는 Hive 작업을 실행할 수 있습니다. Pig 및 Hive 스크립트는 MapReduce를 통한 추상화 계층이며 최종적으로는 MapReduce 프로그램으로 컴파일됩니다. 일련의 작업을 실행하여 이처럼 서로 다른 기술을 사용하는 경우의 결과와 처리 작업 실행에 대한 데이터 크기의 영향을 파악할 수 있습니다.

### 섹션 내용

- [IIS W3C 로그 데이터 시나리오](#scenarios)
- [샘플 W3C 로그 데이터 로드](#loaddata)
- [Java MapReduce 작업 실행](#javamapreduce)
- [Hive 작업 실행](#hive)
- [Pig 작업 실행](#pig)
- [샘플 다시 빌드](#rebuild)

###<a name="scenarios"></a>IIS W3C 로그 데이터 시나리오

W3C 시나리오에서는 IIS W3C 로그 데이터를 세 가지 크기(1MB(소형), 500MB(중형) 및 2GB(대형))로 생성하여 HDFS 또는 Azure Blob 저장소에 가져옵니다. 이 시나리오는 세 가지 작업 유형을 제공하는 데 각각 C#, Java, Pig 및 Hive로 구현합니다.

- **totalhits** - 지정한 페이지의 총 요청 수를 계산합니다.
- **avgtime** - 페이지당 요청에 걸린 평균 시간(초)을 계산합니다.
- **errors** - 상태가 404 또는 500이었던 요청의 페이지 및 시간당 오류 수를 계산합니다.

이러한 샘플 및 설명서에서는 핵심 Hadoop 기술에 대한 심층 연구 또는 전체 구현은 제공하지 않습니다. 사용된 클러스터에는 단일 노드만 있으므로 이 릴리스에서는 노드를 더 추가해도 소용이 없습니다.

###<a name="loaddata"></a>샘플 W3C 로그 데이터 로드

HDFS로 데이터를 생성하고 가져오는 작업은 Azure PowerShell 스크립트 importdata.ps1을 사용하여 이루어집니다.

**샘플 W3C 로그 데이터 가져오기**

1. 바탕 화면에서 Hadoop 명령줄을 엽니다.
2. 디렉터리를 **C:\\hdp\\GettingStarted**로 변경합니다.
3. 다음 명령을 실행하여 HDFS로 데이터를 생성하고 가져옵니다.

		powershell -File importdata.ps1 w3c -ExecutionPolicy unrestricted

	대신 Azure Blob 저장소로 데이터를 로드하려면 [Azure Blob 저장소에 연결](#blobstorage)을 참조하세요.

4. Hadoop 명령줄에서 다음 명령을 실행하여 가져온 파일을 HDFS에 나열합니다.

		hadoop fs -ls -R /w3c

	다음과 유사하게 출력되어야 합니다.

		C:\hdp\GettingStarted>hadoop fs -ls -R /w3c
		drwxr-xr-x   - username hdfs          0 2014-09-08 15:40 /w3c/input
		drwxr-xr-x   - username hdfs          0 2014-09-08 15:41 /w3c/input/large
		-rw-r--r--   1 username hdfs  543683503 2014-09-08 15:41 /w3c/input/large/data_w3c_large.txt
		drwxr-xr-x   - username hdfs          0 2014-09-08 15:40 /w3c/input/medium
		-rw-r--r--   1 username hdfs  272435159 2014-09-08 15:40 /w3c/input/medium/data_w3c_medium.txt
		drwxr-xr-x   - username hdfs          0 2014-09-08 15:39 /w3c/input/small
		-rw-r--r--   1 username hdfs    1058423 2014-09-08 15:39 /w3c/input/small/data_w3c_small.txt

5. 파일 내용을 확인하려면 다음 명령을 실행하여 데이터 파일 중 하나를 콘솔 창에 표시합니다.

		hadoop fs -cat /w3c/input/small/data_w3c_small.txt

이제 데이터 파일을 만들고 HDFS로 가져왔으므로 다른 Hadoop 작업을 실행할 수 있습니다.

###<a name="javamapreduce"></a> Java MapReduce 작업 실행

MapReduce는 Hadoop의 기본 계산 엔진입니다. 기본적으로 Java로 구현되지만 C#을 사용하는 Hadoop 스트리밍 및 .NET을 활용하는 예도 있습니다. MapReduce 작업을 실행하는 구문은 다음과 같습니다.

	hadoop jar <jarFileName>.jar <className> <inputFiles> <outputFolder>

jar 파일과 원본 파일은 C:\\Hadoop\\GettingStarted\\Java 폴더에 있습니다.

**웹 페이지 요청 횟수를 계산하기 위해 MapReduce 작업 실행**

1. Hadoop 명령줄을 엽니다.
2. 디렉터리를 **C:\\hdp\\GettingStarted**로 변경합니다.
3. 다음 명령을 실행하여 출력 폴더가 있는 경우 출력 디렉터리를 제거합니다. MapReduce 작업은 출력 폴더가 이미 있으면 실패합니다.

		hadoop fs -rm -r /w3c/output

3. 다음 명령을 실행합니다.

		hadoop jar .\Java\w3c_scenarios.jar "microsoft.hadoop.w3c.TotalHitsForPage" "/w3c/input/small/data_w3c_small.txt" "/w3c/output"

	다음 표에서는 명령의 요소에 대해 설명합니다.
	<table border="1">
	<tr><td>매개 변수</td><td>참고</td></tr>
	<tr><td>w3c_scenarios.jar</td><td>jar 파일은 C:\hdp\GettingStarted\Java 폴더에 있습니다.</td></tr>
	<tr><td>microsoft.hadoop.w3c.TotalHitsForPage</td><td>형식은 다음 중 하나로 대체될 수 있습니다.
	<ul>
	<li>microsoft.hadoop.w3c.AverageTimeTaken</li>
	<li>microsoft.hadoop.w3c.ErrorsByPage</li>
	</ul></td></tr>
	<tr><td>/w3c/input/small/data_w3c_small.txt</td><td>입력 파일은 다음으로 대체될 수 있습니다.
	<ul>
	<li>/w3c/input/medium/data_w3c_medium.txt</li>
	<li>/w3c/input/large/data_w3c_large.txt</li>
	</ul></td></tr>
	<tr><td>/w3c/output</td><td>출력 폴더 이름입니다.</td></tr>
	</table>

4. 다음 명령을 실행하여 출력 파일을 표시합니다.

		hadoop fs -cat /w3c/output/part-00000

	다음과 유사하게 출력됩니다.

		c:\Hadoop\GettingStarted>hadoop fs -cat /w3c/output/part-00000
		/Default.aspx   3380
		/Info.aspx      1135
		/UserService    1126

	따라서 Default.aspx 페이지는 3360번 요청됩니다. 위에 표에 나와 있는 대로 값을 바꿔 명령을 다시 실행해 보고 데이터의 크기와 작업 유형에 따라 출력이 어떻게 변경되는지를 살펴보세요.

### <a name="hive"></a>Hive 작업 실행
고급 SQL(Structured Query Language) 기술을 숙지하고 있는 분석가들이 즐겨 사용하는 Hive 쿼리 엔진은 SQL과 유사한 인터페이스 및 HDFS용 관계형 데이터 모델을 제공합니다. Hive는 SQL과 매우 비슷한 HiveQL이라는 언어를 사용하며, Java 기반 MapReduce 프레임워크를 통한 추상화 계층을 제공합니다. Hive 쿼리는 런타임에 MapReduce로 컴파일됩니다.

**Hive 작업 실행**

1. Hadoop 명령줄을 엽니다.
2. 디렉터리를 **C:\\hdp\\GettingStarted**로 변경합니다.
3. 다음 명령을 실행하여 **/w3c/hive/input** 폴더가 있는 경우 이 폴더를 제거합니다. 해당 폴더가 있으면 Hive 작업이 실패합니다.

		hadoop fs -rmr /w3c/hive/input

4. 다음 명령을 실행하여 **/w3c/hive/input** 폴더를 만들고 데이터 파일을 /hive/input 폴더에 복사합니다.

        hadoop fs -mkdir /w3c/hive
		hadoop fs -mkdir /w3c/hive/input

		hadoop fs -cp /w3c/input/small/data_w3c_small.txt /w3c/hive/input

5. 다음 명령을 실행하여 **w3ccreate.hql** 스크립트 파일을 실행합니다. 이 스크립트는 Hive 테이블을 만들고 데이터를 그 Hive 테이블로 로드합니다.

	> [AZURE.NOTE] 이 단계에서는 HDInsight Visual Studio 도구를 사용하여 Hive 쿼리도 실행할 수 있습니다. Visual Studio를 열고 새 프로젝트를 만든 다음 HDInsight 템플릿에서 **Hive 응용 프로그램**을 선택합니다. 프로젝트를 연 다음 새 항목으로 쿼리를 추가합니다. 쿼리는 **C:/hdp/GettingStarted/Hive/w3c**에 있습니다. 프로젝트에 쿼리를 추가하고 나면 **${hiveconf:input}**을 **/w3c/hive/input**으로 바꾼 다음 **제출**을 누릅니다.

		C:\hdp\hive-0.13.0.2.1.3.0-1981\bin\hive.cmd -f ./Hive/w3c/w3ccreate.hql -hiveconf "input=/w3c/hive/input/data_w3c_small.txt"

	다음과 유사하게 출력됩니다.

		Logging initialized using configuration in file:/C:/hdp/hive-0.13.0.2.1.3.0-1981	/conf/hive-log4j.properties
		OK
		Time taken: 1.137 seconds
		OK
		Time taken: 4.403 seconds
		Loading data to table default.w3c
		Moved: 'hdfs://HDINSIGHT02:8020/hive/warehouse/w3c' to trash at: hdfs://HDINSIGHT02:8020/user/<username>/.Trash/Current
		Table default.w3c stats: [numFiles=1, numRows=0, totalSize=1058423, rawDataSize=0]
		OK
		Time taken: 2.881 seconds

6. 다음 명령을 실행하여 **w3ctotalhitsbypage.hql** HiveQL 스크립트 파일을 실행합니다.

	> [AZURE.NOTE] 앞에서 설명한 대로 HDInsight Visual Studio 도구도 사용하여 이 쿼리를 실행할 수 있습니다.

        C:\hdp\hive-0.13.0.2.1.3.0-1981\bin\hive.cmd -f ./Hive/w3c/w3ctotalhitsbypage.hql

	다음 표에서는 명령의 요소에 대해 설명합니다.
	<table border="1">
	<tr><td>파일</td><td>설명</td></tr>
	<tr><td>C:\hdp\hive-0.13.0.2.1.3.0-1981\bin\hive.cmd</td><td>Hive 명령 스크립트입니다.</td></tr>
	<tr><td>C:\hdp\GettingStarted\Hive\w3c\w3ctotalhitsbypage.hql</td><td> Hive 스크립트 파일을 다음 중 하나로 대체할 수 있습니다.
	<ul>
	<li>C:\hdp\GettingStarted\Hive\w3c\w3caveragetimetaken.hql</li>
	<li>C:\hdp\GettingStarted\Hive\w3c\w3cerrorsbypage.hql</li>
	</ul>
	</td></tr>
	
	</table>

	w3ctotalhitsbypage.hql HiveQL 스크립트는 다음과 같습니다.

		SELECT filtered.cs_uri_stem,COUNT(*)
		FROM (
		  SELECT logdate,cs_uri_stem from w3c WHERE logdate NOT RLIKE '.*#.*'
		) filtered
		GROUP BY (filtered.cs_uri_stem);

	마지막 출력은 다음과 유사합니다.

		MapReduce Total cumulative CPU time: 5 seconds 391 msec
		Ended Job = job_1410201800143_0008
		MapReduce Jobs Launched:
		Job 0: Map: 1  Reduce: 1   Cumulative CPU: 5.391 sec   HDFS Read: 1058638 HDFS Write: 53 SUCCESS
		Total MapReduce CPU Time Spent: 5 seconds 391 msec
		OK
		/Default.aspx   3380
		/Info.aspx      1135
		/UserService    1126
		Time taken: 49.304 seconds, Fetched: 3 row(s)

각 작업의 첫 번째 단계로서 테이블이 생성되고 이전에 만든 파일의 테이블로 데이터가 로드된다는 점에 유의하세요. 다음 명령을 사용하여 HDFS의 /Hive 노드에서 작성된 파일을 찾을 수 있습니다.

	hadoop fs -lsr /apps/hive/

### <a name="pig"></a>Pig 작업 실행

Pig 프로세싱은 *Pig Latin*이라는 데이터 흐름 언어를 사용합니다. Pig Latin 추상화 계층은 MapReduce보다 풍부한 데이터 구조를 제공하며, SQL이 관계형 데이터베이스 관리 시스템에 대해 수행하는 Hadoop에 대해 수행합니다.


**Pig 작업 실행**

1. Hadoop 명령줄을 엽니다.
2. 디렉터리를 **C:\\hdp\\GettingStarted** 폴더로 변경합니다.
3. 다음 명령을 실행하여 Pig 작업을 제출합니다.

		C:\hdp\pig-0.12.1.2.1.3.0-1981\bin\pig.cmd -f ".\Pig\w3c\TotalHitsForPage.pig" -p "input=/w3c/input/small/data_w3c_small.txt"

	다음 표에서는 명령의 요소를 보여 줍니다.
	<table border="1">
	<tr><td>파일</td><td>설명</td></tr>
	<tr><td>C:\hdp\pig-0.12.1.2.1.3.0-1981\bin\pig.cmd</td><td>Pig 명령 스크립트입니다.</td></tr>
	<tr><td>C:\hdp\GettingStarted\Pig\w3c\TotalHitsForPage.pig</td><td> Pig Latin 스크립트 파일을 다음 중 하나로 대체할 수 있습니다.
	<ul>
	<li>C:\hdp\GettingStarted\Pig\w3c\AverageTimeTaken.pig</li>
	<li>C:\hdp\GettingStarted\Pig\w3c\ErrorsByPage.pig</li>
	</ul>
	</td></tr>
	<tr><td>/w3c/input/small/data_w3c_small.txt</td><td> 매개 변수를 다음과 같은 더 큰 파일로 대체할 수 있습니다.
	
	<ul>
	<li>/w3c/input/medium/data_w3c_medium.txt</li>
	<li>/w3c/input/large/data_w3c_large.txt</li>
	</ul>
	
	</td></tr>
	</table>

	다음과 유사하게 출력되어야 합니다.

		(/Info.aspx,1135)
		(/UserService,1126)
		(/Default.aspx,3380)

Pig 스크립트는 MapReduce 작업으로 컴파일되며 둘 이상의 MapReduce 작업에 컴파일될 수도 있으므로 Pig 작업을 처리하는 동안 여러 개의 MapReduce 작업이 실행될 수도 있습니다.

<!---
### <a name="rebuild"></a>Rebuild the samples
The samples currently contain all the required binaries, so building is not required. If you'd like to make changes to the Java or .NET samples, you can rebuild them by using either the Microsoft Build Engine (MSBuild) or the included Azure PowerShell script.


**To rebuild the samples**

1. Open a Hadoop command line.
2. Run the following command:

		powershell -F buildsamples.ps1
--->

##<a name="blobstorage"></a>Azure Blob 저장소에 연결
HDInsight Emulator는 HDFS를 기본 파일 시스템으로 사용합니다. 그러나 Azure HDInsight는 Azure Blob 저장소를 기본 파일 시스템으로 사용합니다. 로컬 저장소 대신 Azure Blob 저장소를 사용하도록 HDInsight Emulator를 구성할 수 있습니다. Azure에서 저장소 컨테이너를 만들어 HDInsight Emulator에 연결하려면 다음 지침을 따릅니다.

>[AZURE.NOTE] HDInsight에서 Azure Blob 저장소를 만드는 방법에 대한 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용](hdinsight-hadoop-use-blob-storage.md)을 참조하세요.

아래 지침에 따라 작업을 시작하기 전에 먼저 저장소 계정을 만들어야 합니다. 자세한 내용은 [저장소 계정을 만드는 방법](../storage/storage-create-storage-account.md)을 참조하세요.

**컨테이너 만들기**

1. [Azure 포털](https://ms.portal.azure.com/)에 로그인합니다.
2. 왼쪽의 **새로 만들기**를 클릭하고 **데이터 + 저장소**를 클릭한 다음 **저장소**를 클릭합니다.
3. 저장소 계정 블레이드에서 아래 화면 캡처에 표시된 대로 속성을 구성합니다.
	
	![저장소 계정 만들기](./media/hdinsight-hadoop-emulator-get-started/hdi.emulator.create.storage.png)

	**시작 보드에 고정**을 선택하고 **만들기**를 클릭합니다.
4. 저장소 계정이 만들어지면 새 저장소 계정 블레이드에서 **컨테이너**를 클릭하여 컨테이너 블레이드를 열고 **추가**를 클릭합니다.
5. 컨테이너의 이름을 입력한 다음 **선택**을 클릭합니다.

	![컨테이너 만들기](./media/hdinsight-hadoop-emulator-get-started/hdi.emulator.create.container.png)

구성 파일에 계정 이름과 계정 키를 추가해야 Azure 저장소 계정에 액세스할 수 있습니다.

**Azure 저장소 계정에 대한 연결 구성**

1. 메모장에서 **C:\\hdp\\hadoop-2.4.0.2.1.3.0-1981\\etc\\hadoop\\core-site.xml**을 엽니다.
2. 다음 <property> 태그를 다른 <property> 태그 옆에 추가합니다.

		<property>
		    <name>fs.azure.account.key.<StorageAccountName>.blob.core.windows.net</name>
		    <value><StorageAccountKey></value>
		</property>

	<StorageAccountName> 및 <StorageAccountKey>는 저장소 계정 정보와 일치하는 값으로 대체해야 합니다.

3. 변경 내용을 저장합니다. Hadoop 서비스를 다시 시작할 필요는 없습니다.

다음 구문을 사용하여 저장소 계정에 액세스합니다.

	wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/

예:

	hadoop fs -ls wasb://myContainer@myStorage.blob.core.windows.net/


##<a name="powershell"></a> Azure PowerShell 실행
HDInsight Emulator에서는 일부 Azure PowerShell cmdlet도 지원됩니다. 이러한 cmdlet은 다음과 같습니다.

- HDInsight 작업 정의 cmdlet

	- New-AzureHDInsightSqoopJobDefinition
	- New-AzureHDInsightStreamingMapReduceJobDefinition
	- New-AzureHDInsightPigJobDefinition
	- New-AzureHDInsightHiveJobDefinition
	- New-AzureHDInsightMapReduceJobDefinition
- Start-AzureHDInsightJob
- Get-AzureHDInsightJob
- Wait-AzureHDInsightJob

다음은 Hadoop 작업 제출 샘플입니다.

	$creds = Get-Credential (hadoop as username, password can be anything)
	$hdinsightJob = <JobDefinition>
	Start-AzureHDInsightJob -Cluster http://localhost:50111 -Credential $creds -JobDefinition $hdinsightJob

Get-Credential을 호출하면 프롬프트가 나타납니다. 사용자 이름으로 **hadoop**을 사용해야 합니다. 암호는 아무 문자열이나 사용할 수 있습니다. 클러스터 이름은 항상 ****http://localhost:50111**입니다.

Hadoop 작업 제출에 대한 자세한 내용은 [Hadoop 작업을 프로그래밍 방식으로 제출](hdinsight-submit-hadoop-jobs-programmatically.md)을 참조하세요. HDInsight의 Azure PowerShell cmdlet에 대한 자세한 내용은 [HDInsight cmdlet 참조][hdinsight-powershell-reference]를 참조하세요.


##<a name="remove"></a> HDInsight Emulator 제거
Emulator를 설치한 컴퓨터에서 제어판을 열고 **프로그램**에서 **프로그램 제거**를 클릭합니다. 설치된 프로그램 목록에서 **Microsoft HDInsight Emulator for Azure**를 마우스 오른쪽 단추로 클릭하고 **제거**를 클릭합니다.


##<a name="nextsteps"></a> 다음 단계
이 MapReduce 자습서에서는 HDInsight Emulator, Hadoop 샌드박스를 설치했으며 일부 Hadoop 작업을 실행했습니다. 자세한 내용은 다음 문서를 참조하세요.

- [Azure HDInsight 사용 시작](hdinsight-hadoop-linux-tutorial-get-started.md)
- [HDInsight용 Java MapReduce 프로그램 개발](hdinsight-develop-deploy-java-mapreduce.md)
- [HDInsight용 C# Hadoop 스트리밍 MapReduce 프로그램 개발](hdinsight-hadoop-develop-deploy-streaming-jobs.md)
- [HDInsight Emulator 릴리스 정보](hdinsight-emulator-release-notes.md)
- [HDInsight 토론을 위한 MSDN 포럼](http://social.msdn.microsoft.com/Forums/hdinsight)



[azure-sdk]: http://azure.microsoft.com/downloads/
[azure-create-storage-account]: ../storage-create-storage-account.md
[azure-management-portal]: https://manage.windowsazure.com/
[netstat-url]: http://technet.microsoft.com/library/ff961504.aspx

[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce.md

[hdinsight-emulator-install]: http://www.microsoft.com/web/gallery/install.aspx?appid=HDINSIGHT
[hdinsight-emulator-release-notes]: hdinsight-emulator-release-notes.md

[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-develop-deploy-streaming]: hdinsight-hadoop-develop-deploy-streaming-jobs.md
[hdinsight-versions]: hdinsight-component-versioning.md

[Powershell-install-configure]: powershell-install-configure.md

[hadoop-commands-manual]: http://hadoop.apache.org/docs/r1.1.1/commands_manual.html

[image-hdi-emulator-services]: ./media/hdinsight-hadoop-emulator-get-started/HDI.Emulator.Services.png
 

<!---HONumber=AcomDC_0309_2016-->