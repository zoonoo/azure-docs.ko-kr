<properties linkid="manage-services-hdinsight-get-started-hdinsight" urlDisplayName="Get Started" pageTitle="Get started with the HDInsight Emulator | Azure" metaKeywords="hdinsight, Azure hdinsight, hdinsight azure, get started hdinsight, emulator, hdinsight emulator" description="Learn how to use HDInsight Emulator for Azure." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" title="Get started with the HDInsight Emulator" author="jgao" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author></tags>



# HDInsight Emulator 시작

이 자습서에서는 Microsoft HDInsight Emulator for Azure(이전의 HDInsight Server Developer Preview)에서 Hadoop을 사용하는 방법을 설명합니다. HDInsight Emulator에는 Azure HDInsight와 동일한 Hadoop 에코시스템의 구성 요소가 제공됩니다. 배포된 버전에 대한 정보를 포함한 자세한 내용은 [Azure HDInsight에 포함된 Hadoop 버전][](영문)을 참조하세요.

HDInsight Emulator는 Azure HDInsight에 로컬 개발 환경을 제공합니다. Hadoop에 익숙한 경우 HDFS를 사용하여 Emulator를 시작할 수 있습니다. 하지만 HDInsight에서 기본 파일 시스템은 Azure Blob 저장소(WASB, 일명 Azure 저장소 - Blob)이므로 결국 WASB를 사용하여 작업을 개발하게 됩니다. Azure 저장소 에뮬레이터를 사용하여 WASB에 대한 개발을 시작할 수 있습니다(데이터의 작은 하위 집합만 사용하려는 경우 - HDInsight Emulator의 구성을 변경할 필요는 없으며 저장소 계정 이름만 다르면 됨). 그다음에 다시 일부 데이터만 사용하여 Azure 저장소에 대해 로컬로 작업을 테스트합니다(HDInsight Emulator의 구성을 변경해야 함). 마지막으로, 작업의 계산 부분을 HDInsight로 이동하고 프로덕션 데이터에 대해 작업을 실행할 준비가 되었습니다.

> [WACOM.NOTE] HDInsight Emulator는 단일 노드 배포만 사용할 수 있습니다.

HDInsight를 사용하는 자습서는 [Azure HDInsight 사용 시작][](영문)을 참조하세요.

**필수 조건**
 이 자습서를 시작하기 전에 다음이 있어야 합니다.

- HDInsight Emulator에는 64비트 버전의 Windows가 필요합니다. 다음 요구 사항 중 하나는 반드시 충족되어야 합니다.

	- Windows 7 서비스 팩 1
	- Windows Server 2008 R2 서비스 팩 1
	- Windows 8
	- Windows Server 2012
	- 
- Azure PowerShell을 설치하고 구성합니다. 자세한 내용은 [Azure PowerShell 설치 및 구성][]을 참조하세요.

## 자습서 내용

* [HDInsight Emulator 설치][]
* [단어 계산 샘플 실행][]
* [시작 샘플 실행][]
* [Azure Blob 저장소에 연결][]
* [HDInsight PowerShell 실행][]
* [다음 단계][]

## <a name="install"></a>HDInsight Emulator 설치

Microsoft HDInsight Emulator는 Microsoft 웹 플랫폼 설치 관리자를 통해 설치할 수 있습니다.

> [WACOM.NOTE] HDInsight Emulator는 현재 영어 OS만 지원합니다.


> [WACOM.NOTE] Microsoft HDInsight Developer Preview가 설치되어 있으면 우선 제어판/프로그램 및 기능에서 다음 두 가지 구성 요소를 제거해야 합니다.
><ul>
<li>HDInsight Developer Preview</li>
<li>Hortonworks Data Platform Developer Preview</li>
</ul>


**HDInsight Emulator를 설치하려면**

1. Internet Explorer을 열고 [Microsoft HDInsight Emulator for Azure 설치 페이지][]로 이동합니다.
2. **지금 설치**를 클릭합니다.
3. 페이지 아래쪽에서 HDINSIGHT.exe 설치를 묻는 메시지가 나타나면 **실행**을 클릭합니다.
4. 설치를 완료하기 위해 뜨는 **사용자 계정 컨트롤** 창에서 **예** 단추를 클릭합니다. 웹 플랫폼 설치 관리자 4.6 창이 나타납니다.
5. 페이지 아래쪽에서 **설치**를 클릭합니다.
6. 사용 조건에 동의하려면 **동의함**을 클릭합니다.
7. 웹 플랫폼 설치 관리자에서 **다음 제품이 설치되었습니다.**라고 표시되는지 확인한 후 **마침**을 클릭합니다.
8. 웹 플랫폼 설치 관리자 4.6 창을 닫으려면 **끝내기**를 클릭합니다.

    바탕 화면에 세 개의 아이콘이 설치되어야 합니다. 세 개의 아이콘은 다음과 같이 연결됩니다.

	- **Hadoop Command Line**: MapReduce, Pig 및 Hive 작업의 Hadoop 명령 프롬프트가 HDInsight Emulator에서 실행됩니다.

	- **Hadoop Name Node Status**: NameNode는 HDFS의 모든 파일에 대해 트리 기반 디렉터리를 유지 관리합니다. 또한 모든 파일의 데이터가 Hadoop 클러스터에 보관되는 위치를 추적합니다. 클라이언트는 NameNode와 통신하여 모든 파일의 데이터 노드가 저장되는 위치를 파악합니다.

	- **Hadoop MapReduce Status**: MapReduce 작업을 클러스터에 있는 노드에 할당하는 작업 추적기입니다.

    몇 가지 로컬 서비스도 설치되어야 합니다. 다음은 서비스 창의 스크린샷입니다.

    ![HDI.Emulator.Services][]

    HDInsight Server 설치 및 실행에 대해 알려진 문제는 [HDInsight Emulator 릴리스 정보][]를 참조하세요. 설치 로그는 **C:\HadoopFeaturePackSetup\\HadoopFeaturePackSetupTools\gettingStarted.winpkg.install.log**에 있습니다.




## <a name="runwordcount"></a>단어 계산 MapReduce 작업 실행

이제 워크스테이션에 HDInsight Emulator가 구성되었습니다. MapReduce 작업을 실행하여 설치를 테스트할 수 있습니다. 우선 일부 텍스트 파일을 HDFS에 업로드한 후 단어 계산 MapReduce 작업을 실행하여 해당 파일의 단어 사용 빈도를 계산합니다.

단어 계산 MapReduce 프로그램은 *hadoop-examples.jar*에 패키지되어 있습니다. jar 파일은 *C:\Hadoop\hadoop-1.1.0-SNAPSHOT* 폴더에 있습니다.

jar 명령의 구문은 다음과 같습니다.

    hadoop jar <jar> [mainClass] args...

일부 fs 명령도 사용할 수 있습니다. Hadoop 명령에 대한 자세한 내용은 [Hadoop 명령 매뉴얼][](영문)을 참조하세요.

단어 계산 MapReduce 작업에는 두 개의 인수(입력 폴더 및 출력 폴더)가 필요합니다. 입력 폴더로는 *hdfs://localhost/user/HDIUser*를 사용하고 출력 디렉터리로는 *hdfs://localhost/user/HDIUser/WordCount_Output*을 사용합니다. 출력 폴더는 기존 폴더여선 안 됩니다. 그렇지 않으면 MapReduce 작업이 실패합니다. MapReduce 작업을 두 번째 실행하려면 다른 출력 폴더를 지정하거나 기존 출력 폴더를 삭제해야 합니다.

**단어 계산 MapReduce 작업을 실행하려면**

1. 바탕 화면에서 **Hadoop Command Line**을 두 번 클릭하여 Hadoop 명령줄 창을 엽니다. 현재 폴더가 다음과 같아야 합니다.

        c:\Hadoop\hadoop-1.1.0-SNAPSHOT>

    그렇지 않으면 다음 명령을 실행합니다.

        cd %hadoop_home%

2. 다음 Hadoop 명령을 실행하여 입력 및 출력 파일을 저장할 HDFS 폴더를 만듭니다.

        hadoop fs -mkdir /user/HDIUser

3. 다음 Hadoop 명령을 실행하여 일부 로컬 파일을 HDFS에 복사합니다.

        hadoop fs -copyFromLocal *.txt /user/HDIUser/

4. 다음 명령을 실행하여 /user/HDIUser 폴더의 파일을 나열합니다.

        hadoop fs -ls /user/HDIUser

    다음 파일이 표시되어야 합니다.

		c:\Hadoop\hadoop-1.1.0-SNAPSHOT>hadoop fs -ls /user/HDIUser
		Found 8 items
		-rw-r--r--   1 username supergroup      16372 2013-10-30 12:07 /user/HDIUser/CHANGES.branch-1-win.txt
		-rw-r--r--   1 username supergroup     463978 2013-10-30 12:07 /user/HDIUser/CHANGES.txt
		-rw-r--r--   1 username supergroup       6631 2013-10-30 12:07 /user/HDIUser/Jira-Analysis.txt
		-rw-r--r--   1 username supergroup      13610 2013-10-30 12:07 /user/HDIUser/LICENSE.txt
		-rw-r--r--   1 username supergroup       1663 2013-10-30 12:07 /user/HDIUser/Monarch-CHANGES.txt
		-rw-r--r--   1 username supergroup        103 2013-10-30 12:07 /user/HDIUser/NOTICE.txt
		-rw-r--r--   1 username supergroup       2295 2013-10-30 12:07 /user/HDIUser/README.Monarch.txt
		-rw-r--r--   1 username supergroup       1397 2013-10-30 12:07 /user/HDIUser/README.txt

5. 다음 명령을 실행하여 단어 계산 MapReduce 작업을 실행합니다.

        hadoop jar hadoop-examples.jar wordcount /user/HDIUser/*.txt /user/HDIUser/WordCount_Output

6. 다음 명령을 실행하여 출력 파일에서 "windows"를 포함하는 단어를 나열합니다.

		hadoop fs -cat /user/HDIUser/WordCount_Output/part-r-00000 | findstr "windows"

    다음과 같이 출력되어야 합니다.

		c:\Hadoop\hadoop-1.1.0-SNAPSHOT>hadoop fs -cat /user/HDIUser/WordCount_Output/pa
		rt-r-00000 | findstr "windows"
		windows 12
		windows+java6.  1
		windows.        3


## <a name="rungetstartedsamples"></a> 시작 샘플 실행

HDInsight Emulator를 설치하면 새 사용자가 Windows에서 Apache Hadoop 기반의 서비스 학습을 시작하도록 일부 샘플이 제공됩니다. 이러한 샘플에서는 일반적으로 빅데이터 집합을 처리할 때 필요한 일부 작업을 다룹니다. 샘플을 학습하다 보면 MapReduce 프로그래밍 모델 및 에코시스템과 관련된 개념을 익힐 수 있습니다.

샘플은 IIS W3C 로그 데이터 시나리오 처리에 대해 구성되어 있습니다. 데이터 집합을 다양한 크기로 만들고 HDFS 또는 WASB(Azure Blob 저장소)로 가져오도록 데이터 생성 도구가 제공됩니다. 자세한 내용은 [HDInsight에 Azure Blob 저장소 사용][]을 참조하세요. 그다음에 MapReduce, Pig 또는 Hive 작업이 PowerShell 스크립트가 생성한 데이터 페이지에서 실행될 수 있습니다. 사용된 Pig 및 Hive 스크립트가 둘 다 MapReduce 프로그램으로 컴파일된다는 점에 유의하세요. 사용자가 일련의 작업을 실행하여 이러한 다른 기술을 사용한 효과 및 처리 작업 실행에 대한 데이터 크기의 효과를 관찰할 수도 있습니다.

### 섹션 내용

- [IIS w3c 로그 데이터 시나리오][]
- [샘플 w3c 로그 데이터 로드][]
- [Java MapReduce 작업 실행][]
- [Hive 작업 실행][]
- [Pig 작업 실행][]
- [샘플 다시 빌드][]

### <a name="scenarios"></a>IIS w3c 로그 데이터 시나리오

w3c 시나리오는 IIS W3C 로그 데이터를 세 가지 크기(1MB, 500MB, 2GB)로 생성하고 HDFS나 WASB로 가져옵니다. 이 시나리오는 세 가지 작업 유형을 제공하는 데 각각 C#, Java, Pig 및 Hive로 구현합니다.

- **totalhits**: 지정한 페이지의 총 요청 수를 계산합니다.
- **avgtime**: 페이지당 요청에 걸린 평균 시간(초)을 계산합니다.
- **errors**: 상태가 404 또는 500이었던 요청의 페이지 및 시간당 오류 수를 계산합니다.

이러한 샘플 및 설명서에서는 핵심 Hadoop 기술에 대한 심층 연구 또는 전체 구현은 제공하지 않습니다. 사용된 클러스터에는 단일 노드만 있으므로 이 릴리스에서는 노드를 더 추가해도 소용이 없습니다.

### <a name="loaddata"></a>샘플 W3c 로그 데이터 로드

HDFS로 데이터를 생성하고 가져오는 작업은 PowerShell 스크립트 importdata.ps1을 사용하여 이루어집니다.

**샘플 w3c 로그 데이터를 가져오려면**

1. 바탕 화면에서 Hadoop 명령줄을 엽니다.
2. 다음 명령을 실행하여 디렉터리를 **C:\\Hadoop\\GettingStarted**로 변경합니다.

		cd \Hadoop\GettingStarted

3. 다음 명령을 실행하여 HDFS로 데이터를 생성하고 가져옵니다.

		powershell -File importdata.ps1 w3c -ExecutionPolicy unrestricted 

    대신 WASB로 데이터를 로드하려면 [Azure Blob 저장소에 연결][]을 참조하세요.

4. Hadoop 명령줄에서 다음 명령을 실행하여 가져온 파일을 HDFS에 나열합니다.

		hadoop fs -lsr /w3c

    다음과 유사하게 출력되어야 합니다.

		c:\Hadoop\GettingStarted\w3c>hadoop fs -lsr /w3c
		drwxr-xr-x   - username supergroup          0 2013-10-30 13:29 /w3c/input
		drwxr-xr-x   - username supergroup          0 2013-10-30 13:29 /w3c/input/large
		-rw-r--r--   1 username supergroup  543692369 2013-10-30 13:29 /w3c/input/large/data_w3c_large.txt
		drwxr-xr-x   - username supergroup          0 2013-10-30 13:28 /w3c/input/medium
		-rw-r--r--   1 username supergroup  272394671 2013-10-30 13:28 /w3c/input/medium/data_w3c_medium.txt
		drwxr-xr-x   - username supergroup          0 2013-10-30 13:28 /w3c/input/small
		-rw-r--r--   1 username supergroup    1058328 2013-10-30 13:28 /w3c/input/small/data_w3c_small.txt

5. 다음 명령을 실행하여 데이터 파일 중 하나를 콘솔 창에 표시합니다.

		hadoop fs -cat /w3c/input/small/data_w3c_small.txt

이제 HDFS로 데이터 파일을 만들고 가져왔습니다. 다른 Hadoop 작업을 실행할 수 있습니다.

### <a name="javamapreduce"></a>Java MapReduce 작업 실행

MapReduce는 Hadoop의 기본 계산 엔진입니다. 기본적으로 Java로 구현되지만 C#을 사용하는 Hadoop 스트리밍 및 .NET을 활용하는 예도 있습니다. MapReduce 작업을 실행하는 구문은 다음과 같습니다.

	hadoop jar <jarFileName>.jar <className> <inputFiles> <outputFolder>

jar 파일과 원본 파일은 C:\Hadoop\GettingStarted\Java 폴더에 있습니다.

**웹 페이지 요청 횟수를 계산하기 위해 MapReduce 작업을 실행하려면**

1. Hadoop 명령줄을 엽니다.
2. 다음 명령을 실행하여 디렉터리를 **C:\Hadoop\GettingStarted**로 변경합니다.

		cd \Hadoop\GettingStarted

3. 다음 명령을 실행하여 출력 폴더가 있는 경우 출력 디렉터리를 제거합니다. MapReduce 작업은 출력 폴더가 이미 있으면 실패합니다.

		hadoop fs -rmr /w3c/output

4. 다음 명령을 실행합니다.

		hadoop jar .\Java\w3c_scenarios.jar "microsoft.hadoop.w3c.TotalHitsForPage" "/w3c/input/small/data_w3c_small.txt" "/w3c/output"

    다음 표에서는 명령의 요소에 대해 설명합니다.
	<table border="1">
	<tr><td>매개 변수</td><td>참고</td></tr>
	<tr><td>w3c\_scenarios.jar</td><td>jar 파일은 C:\Hadoop\GettingStarted\Java 폴더에 있습니다.</td></tr>
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

5. 다음 명령을 실행하여 출력 파일을 표시합니다.

		hadoop fs -cat /w3c/output/part-00000

    다음과 유사하게 출력됩니다.

		c:\Hadoop\GettingStarted\Java>hadoop fs -cat /w3c/output/part-00000
		/Default.aspx   3409
		/Info.aspx      1115
		/UserService    1130

    따라서 Default.aspx 페이지는 3409번 요청됩니다.

### <a name="hive"></a>Hive 작업 실행
강력한 SQL 기술을 가진 분석가에게는 Hive 쿼리 엔진이 익숙하게 느껴질 것입니다. SQL과 유사한 인터페이스 및 HDFS용 관계형 데이터 모델을 제공합니다. Hive는 일종의 SQL인 HiveQL(또는 HQL)이라는 언어를 사용합니다.

**Hive 작업을 실행하려면**

1. Hadoop 명령줄을 엽니다.
2. 디렉터리를 **C:\Hadoop\GettingStarted** 폴더로 변경합니다.
3. 다음 명령을 실행하여 **/w3c/hive/input** 폴더가 있는 경우 이 폴더를 제거합니다. 해당 폴더가 있으면 Hive 작업이 실패합니다.

		hadoop fs -rmr /w3c/hive/input

4. 다음 명령을 실행하여 **/w3c/hive/input** 폴더를 만들고 워크스테이션의 데이터 파일을 HDFS에 복사합니다.

        hadoop fs -mkdir /w3c/hive/input
        hadoop fs -cp /w3c/input/small/data_w3c_small.txt /w3c/hive/input

5. 다음 명령을 실행하여 **w3ccreate.hql** 스크립트 파일을 실행합니다. 이 스크립트는 Hive 테이블을 만들고 데이터를 그 Hive 테이블로 로드합니다.

		C:\Hadoop\hive-0.9.0\bin\hive.cmd -f ./Hive/w3c/w3ccreate.hql -hiveconf "input=/w3c/hive/input/data_w3c_small.txt"

    HiveQL 스크립트는 다음과 같습니다.
		
		DROP TABLE w3c;

		CREATE TABLE w3c(
		 logdate string,
		 logtime string,
		 c_ip string,
		 cs_username string,
		 s_ip string,
		 s_port string,
		 cs_method string,
		 cs_uri_stem string,
		 cs_uri_query string,
		 sc_status int,
		 sc_bytes int,
		 cs_bytes int,
		 time_taken int,
		 cs_agent string, 
		 cs_Referrer string)
		ROW FORMAT delimited
		FIELDS TERMINATED BY ' ';

		LOAD DATA INPATH '${hiveconf:input}' OVERWRITE INTO TABLE w3c;

    다음과 유사하게 출력됩니다.

		c:\Hadoop\GettingStarted>C:\Hadoop\hive-0.9.0\bin\hive.cmd -f ./Hive/w3c/w3ccrea	te.hql -hiveconf "input=/w3c/hive/input/data_w3c_small.txt"
		Hive history file=c:\hadoop\hive-0.9.0\logs\history/hive_job_log_username_201310311452_1053491002.txt
		Logging initialized using configuration in file:/C:/Hadoop/hive-0.9.0/conf/hive-log4j.properties
		OK
		Time taken: 0.616 seconds
		OK
		Time taken: 0.139 seconds
		Loading data to table default.w3c
		Moved to trash: hdfs://localhost:8020/apps/hive/warehouse/w3c
		OK
		Time taken: 0.573 seconds

6. 다음 명령을 실행하여 **w3ctotalhitsbypate.hql** HiveQL 스크립트 파일을 실행합니다.

        C:\Hadoop\hive-0.9.0\bin\hive.cmd -f ./Hive/w3c/w3ctotalhitsbypage.hql

    다음 표에서는 명령의 요소에 대해 설명합니다.
	<table border="1">
	<tr><td>파일</td><td>설명</td></tr>
	<tr><td>C:\Hadoop\hive-0.9.0\bin\hive.cmd</td><td>Hive 명령 스크립트입니다.</td></tr>
	<tr><td>C:\Hadoop\GettingStarted\Hive\w3c\w3ctotalhitsbypage.hql</td><td> Hive 스크립트 파일을 다음 중 하나로 대체할 수 있습니다.
	<ul>
	<li>C:\Hadoop\GettingStarted\Hive\w3c\w3caveragetimetaken.hql</li>
	<li>C:\Hadoop\GettingStarted\Hive\w3c\w3cerrorsbypage.hql</li>
	</ul>
	</td></tr>

	</table>

	The w3ctotalhitsbypage.hql HiveQL script is:

		SELECT filtered.cs_uri_stem,COUNT(*) 
		FROM (
		  SELECT logdate,cs_uri_stem from w3c WHERE logdate NOT RLIKE '.*#.*'
		) filtered
		GROUP BY (filtered.cs_uri_stem);

    마지막 출력은 다음과 유사합니다.

		MapReduce Total cumulative CPU time: 3 seconds 47 msec
		Ended Job = job_201310291309_0006
		MapReduce Jobs Launched:
		Job 0: Map: 1  Reduce: 1   Cumulative CPU: 3.047 sec   HDFS Read: 1058546 HDFS W
		rite: 53 SUCCESS
		Total MapReduce CPU Time Spent: 3 seconds 47 msec
		OK
		/Default.aspx   3409
		/Info.aspx      1115
		/UserService    1130
		Time taken: 34.68 seconds

각 작업의 첫 번째 단계로서 테이블이 생성되고 이전에 만든 파일의 테이블로 데이터가 로드된다는 점에 유의하세요. 다음 명령을 사용하여 HDFS의 /Hive 노드에서 해당 파일을 찾을 수 있습니다.

	hadoop fs -lsr /apps/hive/







### <a name="pig"></a>Pig 작업 실행



Pig 프로세싱은 *Pig Latin*이라는 데이터 흐름 언어를 사용합니다. Pig Latin 추상화 계층은 MapReduce보다 풍부한 데이터 구조를 제공하며, SQL이 RDBMS 시스템에 대해 수행하는 Hadoop에 대해 수행합니다.

**Pig 작업을 실행하려면**

1. Hadoop 명령줄을 엽니다.
2. 디렉터리를 C:\\Hadoop\\GettingStarted 폴더로 변경합니다.
3. 다음 명령을 실행하여 Pig 작업을 제출합니다.

		C:\Hadoop\pig-0.9.3-SNAPSHOT\bin\pig.cmd -f ".\Pig\w3c\TotalHitsForPage.pig" -p "input=/w3c/input/small/data_w3c_small.txt"

    다음 표에서는 명령의 요소를 보여 줍니다.
	<table border="1">
	<tr><td>파일</td><td>설명</td></tr>
	<tr><td>C:\Hadoop\pig-0.9.3-SNAPSHOT\bin\pig.cmd</td><td>Pig 명령 스크립트입니다.</td></tr>
	<tr><td>C:\Hadoop\GettingStarted\Pig\w3c\TotalHitsForPage.pig</td><td>Pig Latin 스크립트 파일을 다음 중 하나로 대체할 수 있습니다.
	<ul>
	<li>C:\Hadoop\GettingStarted\Pig\w3c\AverageTimeTaken.pig</li>
	<li>C:\Hadoop\GettingStarted\Pig\w3c\ErrorsByPage.pig</li>
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

		(/Info.aspx,1115)
		(/UserService,1130)
		(/Default.aspx,3409)

Pig 스크립트가 MapReduce 작업으로 컴파일되고 잠재적으로는 둘 이상의 이러한 작업에 컴파일되므로 사용자는 Pig 작업을 처리하는 동안 여러 개의 MapReduce 작업을 볼 수도 있습니다.


### <a name="rebuild"></a>샘플 다시 빌드
샘플에는 현재 필수 이진 파일이 모두 포함되어 있으므로 빌드할 필요가 없습니다. Java 또는 .NET 샘플로 변경하려면 msbuild나 포함된 PowerShell 스크립트를 사용하여 다시 빌드할 수 있습니다.

**샘플을 다시 빌드하려면**

1. Hadoop 명령줄을 엽니다.
2. 다음 명령을 실행합니다.

		powershell -F buildsamples.ps1






## <a name="blobstorage"></a>Azure Blob 저장소에 연결
Azure HDInsight는 Azure Blob 저장소를 기본 파일 시스템으로 사용합니다. 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용][HDInsight에 Azure Blob 저장소 사용]을 참조하세요.

로컬 저장소 대신 Azure Blob 저장소를 사용하도록 HDInsight Emulator에서 로컬 클러스터를 구성할 수 있습니다. 이 섹션에서는 다음 내용을 다룹니다.

- 저장소 에뮬레이터에 연결
- Azure Blob 저장소에 연결
- Azure Blob 저장소를 HDInsight Emulator의 기본 파일 시스템으로 구성

### 저장소 에뮬레이터에 연결

Azure 저장소 에뮬레이터에는 [Azure SDK for .NET][]이 제공됩니다. 저장소 에뮬레이터는 자동으로 시작되지 않습니다. 수동으로 시작해야 합니다. 응용 프로그램 이름은 *Azure 저장소 에뮬레이터*입니다. 에뮬레이터를 시작/중지하려면 Windows 시스템 트레이에서 파란색 Azure 아이콘을 마우스 오른쪽 단추로 클릭한 다음 Show Storage Emulator UI를 클릭합니다.

> [WACOM.NOTE] 저장소 에뮬레이터를 시작할 때 다음 오류 메시지가 나타날 수도 있습니다.

><pre><code>The process cannot access the file because it is being used by another process.
</code></pre>

><p>Hadoop Hive 서비스 중 하나도 포트 10000을 사용하기 때문입니다. 문제를 해결하려면 다음 절차를 따르세요.</p>

><ol>
<li>services.msc를 사용하여 두 Hadoop Hive 서비스(Apache Hadoop hiveserver 및 Apache Hadoop Hiveserver2)를 중지합니다.</li>
<li>Blob 저장소 에뮬레이터를 시작합니다.</li>
<li>두 Hadoop Hive 서비스를 다시 시작합니다.</li>
</ol>



저장소 에뮬레이터 액세스 구문은 다음과 같습니다.

	wasb://<ContainerName>@storageemulator

예를 들면 다음과 같습니다.

	hadoop fs -ls wasb://myContainer@storageemulator

> [WACOM.NOTE] 다음 오류 메시지가 나타나는 경우

><pre><code>ls: No FileSystem for scheme: wasb</code></pre>

>Developer Preview 버전을 여전히 사용하기 때문입니다. 이 문서의 HDInsight Emulator 설치 섹션에 나온 지침에 따라 Developer Preview 버전을 제거한 후 해당 응용 프로그램을 다시 설치합니다.

### Azure Blob 저장소에 연결
저장소 계정 만들기에 관한 지침은 [저장소 계정을 만드는 방법][]을 참조하세요.

**컨테이너를 만들려면**

1. [관리 포털][]에 로그인합니다.
2. 왼쪽에서 **저장소**를 클릭합니다. 구독 아래에 저장소 계정 목록이 표시됩니다.
3. 목록에서 컨테이너를 만들 위치의 저장소 계정을 클릭합니다.
4. 페이지 위쪽에서 **컨테이너**를 클릭합니다.
5. 페이지 아래쪽에서 **추가**를 클릭합니다.
6. **이름**을 입력하고 **액세스**를 선택합니다. 세 개의 액세스 수준 중 아무거나 사용할 수 있습니다. 기본값은 **개인**입니다.
7.  **확인**을 클릭하여 변경 내용을 저장합니다. 포털에 새 컨테이너가 나열되어 표시됩니다.

구성 파일에 계정 이름과 계정 키를 추가해야 Azure 저장소 계정에 액세스할 수 있습니다.

**Azure 저장소 계정에 대한 연결을 구성하려면**

1. 메모장에서 **C:\Hadoop\hadoop-1.1.0-SNAPSHOT\conf\core-site.xml**을 엽니다.
2. 다음 \<property\> 태그를 다른 \<property\> 태그 옆에 추가합니다.

		<property>
		    <name>fs.azure.account.key.<StorageAccountName>.blob.core.windows.net</name>
		    <value><StorageAccountKey></value>
		</property>

    &lt;StorageAccountName> 및 &lt;StorageAccountKey>는 저장소 계정 정보와 일치하는 값으로 대체해야 합니다.

3. 변경 내용을 저장합니다. Hadoop 서비스를 다시 시작할 필요는 없습니다.

다음 구문을 사용하여 저장소 계정에 액세스합니다.

	wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/

예를 들면 다음과 같습니다.

	hadoop fs -ls wasb://myContainer@myStorage.blob.core.windows.net/


### Azure Blob 저장소 컨테이너를 기본 파일 시스템으로 구성

Azure HDInsight에서와 마찬가지로 Azure Blob 저장소 컨테이너도 기본 파일 시스템으로 사용할 수 있습니다.



**Azure Blob 저장소 컨테이너를 사용하여 기본 파일 시스템을 구성하려면**

1. 메모장에서 **C:\Hadoop\hadoop-1.1.0-SNAPSHOT\conf\core-site.xml**을 엽니다.
2. 다음 \<property\> 태그를 찾습니다.

		<property>
		  <name>fs.default.name</name>
		  <!-- cluster variant -->
		  <value>hdfs://localhost:8020</value>
		  <description>The name of the default file system.  Either the	literal string "local" or a host:port for NDFS.</description>
		  <final>true</final>
		</property>

3. 찾은 태그를 다음 \<property\> 태그로 바꿉니다.

		<property>
		  <name>fs.default.name</name>
		  <!-- cluster variant -->
		  <!--<value>hdfs://localhost:8020</value>-->
		  <value>wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net</value>
		  <description>The name of the default file system.  Either the	literal string "local" or a host:port for NDFS.</description>
		  <final>true</final>
		</property>
		
		<property>
		  <name>dfs.namenode.rpc-address</name>
		  <value>hdfs://localhost:8020</value>
		  <description>A base for other temporary directories.</description>
		</property>

    &lt;StorageAccountName> 및 &lt;StorageAccountKey>는 저장소 계정 정보와 일치하는 값으로 대체해야 합니다.

4. 변경 내용을 저장합니다.
5. 관리자 모드(관리자 권한으로 실행)로 화면에서 Hadoop 명령줄을 엽니다.
6. 다음 명령을 실행하여 Hadoop 서비스를 다시 시작합니다.

		C:\Hadoop\stop-onebox.cmd
		C:\Hadoop\start-onebox.cmd

7. 다음 명령을 실행하여 기본 파일 시스템과의 연결을 테스트합니다.

		hadoop fs -ls /

    다음 명령은 동일한 폴더의 내용을 나열합니다.
	
		hadoop fs -ls wasb:///
		hadoop fs -ls wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/
		hadoop fs -ls wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/

    HDFS에 액세스하려면 다음 명령을 사용합니다.
	
		hadoop fs -ls hdfs://localhost:8020/
	

## <a name="powershell"></a>HDInsight PowerShell 실행
일부 HDInsight PowerShell cmdlet은 HDInsight Emulator에서 지원됩니다. 이러한 cmdlet은 다음과 같습니다.

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

Get-Credential을 호출하면 프롬프트가 나타납니다. 사용자 이름에 **hadoop**을 사용해야 합니다. 암호는 아무 문자열이나 사용할 수 있습니다. 클러스터 이름은 항상 **http://localhost:50111**입니다.

Hadoop 작업 제출에 대한 자세한 내용은 [Hadoop 작업을 프로그래밍 방식으로 제출][]을 참조하세요. HDInsight PowerShell cmdlet에 대한 자세한 내용은 [HDInsight cmdlet 참조][]를 참조하세요.



## <a name="nextsteps"></a>다음 단계
이 자습서에서는 HDInsight Emulator를 설치했으며 일부 Hadoop 작업을 실행했습니다. 자세한 내용은 다음 문서를 참조하세요.

- [Azure HDInsight를 사용하여 시작][Azure HDInsight 사용 시작]
- [HDInsight용 Java MapReduce 프로그램 개발][]
- [HDInsight용 C# Hadoop 스트리밍 MapReduce 프로그램 개발][]
- [HDInsight Emulator 릴리스 정보][]
- [HDInsight 토론을 위한 MSDN 포럼][]

  [Azure HDInsight에 포함된 Hadoop 버전]: ../hdinsight-component-versioning/
  [Azure HDInsight 사용 시작]: ../hdinsight-get-started/
  [Azure PowerShell 설치 및 구성]: ../install-configure-powershell/
  [HDInsight Emulator 설치]: #install
  [단어 계산 샘플 실행]: #runwordcount
  [시작 샘플 실행]: #rungetstartedsamples
  [Azure Blob 저장소에 연결]: #blobstorage
  [HDInsight PowerShell 실행]: #powershell
  [다음 단계]: #nextsteps
  [Microsoft HDInsight Emulator for Azure 설치 페이지]: http://www.microsoft.com/web/gallery/install.aspx?appid=HDINSIGHT
  [HDI.Emulator.Services]: ./media/hdinsight-get-started-emulator/HDI.Emulator.Services.png
  [HDInsight Emulator 릴리스 정보]: ../hdinsight-emulator-release-notes/
  [Hadoop 명령 매뉴얼]: http://hadoop.apache.org/docs/r1.1.1/commands_manual.html
  [HDInsight에 Azure Blob 저장소 사용]: ../hdinsight-use-blob-storage/
  [IIS w3c 로그 데이터 시나리오]: #scenarios
  [샘플 w3c 로그 데이터 로드]: #loaddata
  [Java MapReduce 작업 실행]: #javamapreduce
  [Hive 작업 실행]: #hive
  [Pig 작업 실행]: #pig
  [샘플 다시 빌드]: #rebuild
  [Azure SDK for .NET]: http://azure.microsoft.com/en-us/downloads/
  [저장소 계정을 만드는 방법]: ../storage-create-storage-account/
  [관리 포털]: https://manage.windowsazure.com/
  [Hadoop 작업을 프로그래밍 방식으로 제출]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [HDInsight cmdlet 참조]: http://msdn.microsoft.com/en-us/library/windowsazure/dn479228.aspx
  [HDInsight용 Java MapReduce 프로그램 개발]: ../hdinsight-develop-deploy-java-mapreduce/
  [HDInsight용 C# Hadoop 스트리밍 MapReduce 프로그램 개발]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
  [HDInsight 토론을 위한 MSDN 포럼]: http://social.msdn.microsoft.com/Forums/en-US/hdinsight
 