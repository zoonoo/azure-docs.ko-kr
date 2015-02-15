<properties 
	pageTitle="HDInsight Emulator 시작" 
	description="HDInsight Emulator for Azure를 사용하는 방법에 대해 알아봅니다." 
	editor="cgronlun" 
	manager="paulettm" 
	services="hdinsight" 
	author="nitinme" 
	authors="nitinme" 
	documentationCenter=""/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/06/2015" 
	ms.author="nitinme"/>

# HDInsight Emulator 시작 

이 자습서에서는 Microsoft HDInsight Emulator for Azure(이전의 HDInsight Server Developer Preview)에서 Hadoop 클러스터를 시작하는 방법을 설명합니다. HDInsight Emulator에는 Azure HDInsight와 동일한 Hadoop 에코시스템의 구성 요소가 제공됩니다. 배포된 버전에 대한 정보를 포함한 자세한 내용은 [Azure HDInsight에 포함된 Hadoop 버전][hdinsight-versions](영문)을 참조하세요.

>[AZURE.NOTE] HDInsight Emulator는 Hadoop 클러스터만 포함하며 HBase는 포함하지 않습니다.

HDInsight Emulator는 Azure HDInsight에 로컬 개발 환경을 제공합니다. Hadoop에 익숙한 경우 HDFS를 사용하여 Emulator를 시작할 수 있습니다. HDInsight의 기본 파일 시스템은 Azure Blob 저장소(WASB, Azure 저장소 - Blob)입니다. 따라서 WASB를 사용하여 작업을 개발하게 됩니다. HDInsight Emulator에서 WASB를 사용하려면 HDInsight Emulator 구성을 변경해야 합니다. 

> [AZURE.NOTE] HDInsight Emulator는 단일 노드 배포만 사용할 수 있습니다. 


**필수 조건**	
이 자습서를 시작하기 전에 다음이 있어야 합니다.

- HDInsight Emulator에는 64비트 버전의 Windows가 필요합니다. 다음 요구 사항 중 하나는 반드시 충족되어야 합니다.

	- Windows 7 서비스 팩 1
	- Windows Server 2008 R2 서비스 팩 1
	- Windows 8 
	- Windows Server 2012

- Azure PowerShell을 설치하고 구성합니다. 자세한 내용은 [Azure PowerShell 설치 및 구성][powershell-install-configure]을 참조하세요. 

## 자습서 내용

* [HDInsight Emulator 설치](#install)
* [단어 계산 샘플 실행](#runwordcount)
* [시작 샘플 실행](#rungetstartedsamples)
* [Azure Blob 저장소에 연결](#blobstorage)
* [HDInsight PowerShell 실행](#powershell)
* [HDInsight Emulator 제거](#remove)
* [다음 단계](#nextsteps)

##<a name="install"></a>HDInsight Emulator 설치

Microsoft HDInsight Emulator는 Microsoft 웹 플랫폼 설치 관리자를 통해 설치할 수 있습니다.  

> [AZURE.NOTE] HDInsight Emulator는 현재 영어 OS만 지원합니다. 이전 버전의 Emulator가 설치되어 있으면 최신 버전의 Emulator를 설치하기 전에 제어판/프로그램 및 기능에서 다음 두 가지 구성 요소를 제거해야 합니다.
><ul>
<li>Microsoft HDInsight Emulator for Microsoft Azure 또는 HDInsight Developer Preview 중 설치되어 있는 구성 요소</li>
<li>Hortonworks Data Platform</li>
</ul>


**HDInsight Emulator를 설치하려면**

1. Internet Explorer을 열고 [Microsoft HDInsight Emulator for Azure 설치 페이지][hdinsight-emulator-install]로 이동합니다.
2. **지금 설치**를 클릭합니다. 
3. 페이지 아래쪽에서 HDINSIGHT.exe 설치를 묻는 메시지가 나타나면 **실행**을 클릭합니다. 
4. 설치를 완료하기 위해 뜨는 **사용자 계정 컨트롤** 창에서 **예** 단추를 클릭합니다. 웹 플랫폼 설치 관리자 대화 상자가 나타납니다.
6. 페이지 아래쪽에서 **설치**를 클릭합니다.
7. 사용 조건에 동의하려면 **동의함**을 클릭합니다.
8. 웹 플랫폼 설치 관리자에서 **다음 제품이 설치되었습니다.** 상태가 표시되는지 확인한 후 **마침**을 클릭합니다.
9. **끝내기**를 클릭하여 웹 플랫폼 설치 관리자 창을 닫습니다.

**HDInsight Emulator 설치를 확인하려면**
	
바탕 화면에 세 개의 아이콘이 설치되어야 합니다. 세 개의 아이콘은 다음과 같이 연결됩니다. 
	
- **Hadoop 명령줄**: MapReduce, Pig 및 Hive 작업의 Hadoop 명령 프롬프트가 HDInsight Emulator에서 실행됩니다.

- **Hadoop 이름 노드 상태**: NameNode는 HDFS의 모든 파일에 대해 트리 기반 디렉터리를 유지 관리합니다. 또한 모든 파일의 데이터가 Hadoop 클러스터에 보관되는 위치를 추적합니다. 클라이언트는 NameNode와 통신하여 모든 파일의 데이터 노드가 저장되는 위치를 파악합니다.
	
- **Hadoop Yarn 상태**: MapReduce 작업을 클러스터에 있는 노드에 할당하는 작업 추적기입니다.

몇 가지 로컬 서비스도 설치되어야 합니다. 다음은 서비스 창의 스크린샷입니다.

![HDI.Emulator.Services][image-hdi-emulator-services]

HDInsight Emulator와 관련된 서비스는 기본적으로 시작되지 않습니다. 서비스를 시작하려면 Hadoop 명령줄을 사용하여 <시스템 드라이브\>\hdp에서 **start\_local\_hdp_services.cmd**를 실행합니다. 컴퓨터를 다시 시작한 후 서비스를 자동으로 시작하려면 **set-onebox-autostart.cmd**를 실행합니다.  

HDInsight Server 설치 및 실행에 대해 알려진 문제는 [HDInsight Emulator 릴리스 정보][hdinsight-emulator-release-notes]를 참조하세요. 설치 로그는 **C:\HadoopFeaturePackSetup\HadoopFeaturePackSetupTools\gettingStarted.winpkg.install.log**에 있습니다.


##<a name="runwordcount"></a>단어 계산 MapReduce 작업 실행

이제 워크스테이션에 HDInsight Emulator가 구성되었습니다.  MapReduce 작업을 실행하여 설치를 테스트할 수 있습니다. 먼저 일부 데이터 파일을 HDFS에 업로드한 후 단어 계산 MapReduce 작업을 실행하여 해당 파일에서 특정 단어의 빈도를 계산합니다. 

단어 계산 MapReduce 프로그램은  *hadoop-mapreduce-examples-2.4.0.2.1.3.0-1981.jar*에 패키지되어 있습니다.  jar 파일은  *C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\hadoop\mapreduce* 폴더에 있습니다.

단어 계산 MapReduce 작업은 다음의 두 인수를 사용합니다.

- 입력 폴더.  *hdfs://localhost/user/HDIUser*를 입력 폴더로 사용합니다.
- 출력 폴더.  *hdfs://localhost/user/HDIUser/WordCount_Output*을 출력 디렉터리로 사용합니다. 출력 폴더는 기존 폴더여선 안 됩니다. 그렇지 않으면 MapReduce 작업이 실패합니다. MapReduce 작업을 두 번째 실행하려면 다른 출력 폴더를 지정하거나 기존 출력 폴더를 삭제해야 합니다. 

**단어 계산 MapReduce 작업을 실행하려면**

1. 바탕 화면에서 **Hadoop 명령줄**을 두 번 클릭하여 Hadoop 명령줄 창을 엽니다.  현재 폴더가 다음과 같아야 합니다.

		c:\hdp\hadoop-2.4.0.2.1.3.0-1981

	그렇지 않으면 다음 명령을 실행합니다.

		cd %hadoop_home%

2. 다음 Hadoop 명령을 실행하여 입력 및 출력 파일을 저장할 HDFS 폴더를 만듭니다.

		hadoop fs -mkdir /user
		hadoop fs -mkdir /user/HDIUser
	
3. 다음 Hadoop 명령을 실행하여 일부 로컬 텍스트 파일을 HDFS에 복사합니다.

		hadoop fs -copyFromLocal C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common\*.txt /user/HDIUser

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

		C:\hdp\hadoop-2.4.0.2.1.3.0-1981> hadoop jar C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\hadoop\mapreduce\hadoop-mapreduce-examples-2.4.0.2.1.3.0-1981.jar wordcount /user/HDIUser/*.txt /user/HDIUser/WordCount_Output

6. 다음 명령을 실행하여 출력 파일에서 "windows"를 포함하는 단어의 수를 나열합니다.

		hadoop fs -cat /user/HDIUser/WordCount_Output/part-r-00000 | findstr "windows"

	다음과 같이 출력되어야 합니다.

		C:\hdp\hadoop-2.4.0.2.1.3.0-1981>hadoop fs -cat /user/HDIUser/WordCount_Output/part-r-00000 | findstr "windows"
		windows 4
		windows.        2
		windows/cygwin. 1

Hadoop 명령에 대한 자세한 내용은 [Hadoop 명령 매뉴얼][hadoop-commands-manual](영문)을 참조하세요.

##<a name="rungetstartedsamples"></a> 시작 샘플 실행

HDInsight Emulator를 설치하면 사용자가 Windows에서 Apache Hadoop 기반의 서비스 학습을 시작할 수 있도록 일부 샘플이 제공됩니다. 이러한 샘플에서는 일반적으로 빅 데이터 집합을 처리할 때 필요한 일부 작업을 다룹니다. 샘플을 학습하다 보면 MapReduce 프로그래밍 모델 및 에코시스템과 관련된 개념을 익힐 수 있습니다.

샘플은 IIS W3C 로그 데이터 시나리오 처리에 대해 구성되어 있습니다. 데이터 집합을 다양한 크기로 만들고 HDFS 또는 WASB(Azure Blob 저장소)로 가져오도록 데이터 생성 도구가 제공됩니다. 자세한 내용은 [HDInsight에 Azure Blob 저장소 사용][hdinsight-storage]을 참조하세요. PowerShell 스크립트를 통해 생성된 데이터 페이지에서 MapReduce, Pig 또는 Hive 작업을 실행할 수 있습니다. Pig 및 Hive 스크립트는 MapReduce를 통한 추상화 계층이며 최종적으로는 MapReduce 프로그램으로 컴파일됩니다. 사용자는 일련의 작업을 실행하여 이처럼 서로 다른 기술을 사용하는 경우의 결과와 처리 작업 실행에 대한 데이터 크기의 영향을 파악할 수 있습니다. 

### 섹션 내용

- [IIS w3c 로그 데이터 시나리오](#scenarios)
- [샘플 w3c 로그 데이터 로드](#loaddata)
- [Java MapReduce 작업 실행](#javamapreduce)
- [Hive 작업 실행](#hive)
- [Pig 작업 실행](#pig)
- [샘플 다시 빌드](#rebuild)

###<a name="scenarios"></a>IIS w3c 로그 데이터 시나리오

w3c 시나리오는 IIS W3C 로그 데이터를 세 가지 크기(1MB(소형), 500MB(중형), 2GB(대형))로 생성하고 HDFS 또는 WASB로 가져옵니다. 이 시나리오는 세 가지 작업 유형을 제공하는 데 각각 C#, Java, Pig 및 Hive로 구현합니다.

- **totalhits**: 지정한 페이지의 총 요청 수를 계산합니다. 
- **avgtime**: 페이지당 요청에 걸린 평균 시간(초)을 계산합니다. 
- **오류**: 상태가 404 또는 500이었던 요청의 페이지 및 시간당 오류 수를 계산합니다. 

이러한 샘플 및 설명서에서는 핵심 Hadoop 기술에 대한 심층 연구 또는 전체 구현은 제공하지 않습니다. 사용된 클러스터에는 단일 노드만 있으므로 이 릴리스에서는 노드를 더 추가해도 소용이 없습니다. 

###<a name="loaddata"></a>샘플 W3c 로그 데이터 로드

HDFS로 데이터를 생성하고 가져오는 작업은 PowerShell 스크립트 importdata.ps1을 사용하여 이루어집니다.

**샘플 w3c 로그 데이터를 가져오려면**

1. 바탕 화면에서 Hadoop 명령줄을 엽니다.
2. 디렉터리를 **C:\hdp\GettingStarted**로 변경합니다.
3. 다음 명령을 실행하여 HDFS로 데이터를 생성하고 가져옵니다.

		powershell -File importdata.ps1 w3c -ExecutionPolicy unrestricted 

	대신 WASB로 데이터를 로드하려면 [Azure Blob 저장소에 연결](#blobstorage)을 참조하세요.

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

jar 파일과 원본 파일은 C:\Hadoop\GettingStarted\Java 폴더에 있습니다.

**웹 페이지 요청 횟수를 계산하기 위해 MapReduce 작업을 실행하려면**

1. Hadoop 명령줄을 엽니다.
2. 디렉터리를 **C:\hdp\GettingStarted**로 변경합니다.
3. 다음 명령을 실행하여 출력 폴더가 있는 경우 출력 디렉터리를 제거합니다.  MapReduce 작업은 출력 폴더가 이미 있으면 실패합니다.

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

		c:\Hadoop\GettingStarted\Java>hadoop fs -cat /w3c/output/part-00000
		/Default.aspx   3360
		/Info.aspx      1156
		/UserService    1137

	따라서 Default.aspx 페이지는 3360번 요청됩니다. 위에 표에 나와 있는 대로 값을 바꿔 명령을 다시 실행해 보고 데이터의 크기와 작업 유형에 따라 출력이 어떻게 변경되는지를 살펴보세요.

### <a name="hive"></a>Hive 작업 실행
고급 SQL 기술을 숙지하고 있는 분석가들이 즐겨 사용하는 Hive 쿼리 엔진은 SQL과 유사한 인터페이스 및 HDFS용 관계형 데이터 모델을 제공합니다. Hive는 SQL과 매우 비슷한 HiveQL이라는 언어를 사용하며, Java 기반 MapReduce 프레임워크를 통한 추상화 계층을 제공합니다. Hive 쿼리는 런타임에 MapReduce로 컴파일됩니다.

**Hive 작업을 실행하려면**

1. Hadoop 명령줄을 엽니다.
2. 디렉터리를 **C:\hdp\GettingStarted**로 변경합니다.
3. 다음 명령을 실행하여 **/w3c/hive/input** 폴더가 있는 경우 이 폴더를 제거합니다.  해당 폴더가 있으면 Hive 작업이 실패합니다.

		hadoop fs -rmr /w3c/hive/input

4. 다음 명령을 실행하여 **/w3c/hive/input** 폴더를 만들고 데이터 파일을 /hive/input 폴더에 복사합니다.

        hadoop fs -mkdir /w3c/hive
		hadoop fs -mkdir /w3c/hive/input
        
		hadoop fs -cp /w3c/input/small/data_w3c_small.txt /w3c/hive/input

5. 다음 명령을 실행하여 **w3ccreate.hql** 스크립트 파일을 실행합니다.  이 스크립트는 Hive 테이블을 만들고 데이터를 그 Hive 테이블로 로드합니다.
        
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

6. 다음 명령을 실행하여 **w3ctotalhitsbypage.hql** 스크립트 파일을 실행합니다.  

        C:\hdp\hive-0.13.0\bin\hive.cmd -f ./Hive/w3c/w3ctotalhitsbypage.hql

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
		/Default.aspx   3360
		/Info.aspx      1156
		/UserService    1137
		Time taken: 49.304 seconds, Fetched: 3 row(s)

각 작업의 첫 번째 단계로서 테이블이 생성되고 이전에 만든 파일의 테이블로 데이터가 로드된다는 점에 유의하세요. 다음 명령을 사용하여 HDFS의 /Hive 노드에서 해당 파일을 찾을 수 있습니다.

	hadoop fs -lsr /apps/hive/

### <a name="pig"></a>Pig 작업 실행

Pig 프로세싱은  *Pig Latin*이라는 데이터 흐름 언어를 사용합니다. Pig Latin 추상화 계층은 MapReduce보다 풍부한 데이터 구조를 제공하며, SQL이 RDBMS 시스템에 대해 수행하는 Hadoop에 대해 수행합니다. 


**Pig 작업을 실행하려면**

1. Hadoop 명령줄을 엽니다.
2. 디렉터리를 **C:\hdp\GettingStarted** 폴더로 변경합니다.
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

		(/Info.aspx,1156)
		(/UserService,1137)
		(/Default.aspx,3360)
		
Pig 스크립트는 MapReduce 작업으로 컴파일되며 둘 이상의 MapReduce 작업에 컴파일될 수도 있으므로 Pig 작업을 처리하는 동안 여러 개의 MapReduce 작업이 실행될 수도 있습니다.


### <a name="rebuild"></a>샘플 다시 빌드
현재 샘플에는 필수 이진 파일이 모두 포함되어 있으므로 빌드할 필요가 없습니다. Java 또는 .NET 샘플로 변경하려면 msbuild나 포함된 PowerShell 스크립트를 사용하여 다시 빌드할 수 있습니다.

**샘플을 다시 빌드하려면**

1. Hadoop 명령줄을 엽니다.
2. 다음 명령을 실행합니다.

		powershell -F buildsamples.ps1


##<a name="blobstorage"></a>Azure Blob 저장소에 연결
HDInsight Emulator는 HDFS를 기본 파일 시스템으로 사용합니다. 그러나 Azure HDInsight는 Azure Blob 저장소를 기본 파일 시스템으로 사용합니다. 로컬 저장소 대신 Azure Blob 저장소를 사용하도록 HDInsight Emulator를 구성할 수 있습니다. Azure에서 저장소 컨테이너를 만들어 HDInsight Emulator에 연결하려면 다음 지침을 따릅니다.

>[AZURE.NOTE] HDInsight에서 Azure Blob 저장소를 만드는 방법에 대한 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용][hdinsight-storage]을 참조하세요.

아래 지침에 따라 작업을 시작하기 전에 먼저 저장소 계정을 만들어야 합니다. 자세한 내용은 [저장소 계정을 만드는 방법][azure-create-storage-account]을 참조하세요.

**컨테이너를 만들려면**

1. [관리 포털][azure-management-portal]에 로그인합니다.
2. 왼쪽에서 **저장소**를 클릭합니다. 구독 아래에 저장소 계정 목록이 표시됩니다.
3. 목록에서 컨테이너를 만들 위치의 저장소 계정을 클릭합니다.
4. 페이지 위쪽에서 **컨테이너**를 클릭합니다.
5. 페이지 아래쪽에서 **추가**를 클릭합니다.
6. **이름**을 입력하고 **액세스**를 선택합니다. 세 개의 액세스 수준 중 아무거나 사용할 수 있습니다.  기본값은 **개인**입니다.
7. **확인**을 클릭하여 변경 내용을 저장합니다. 포털에 새 컨테이너가 나열되어 표시됩니다.

구성 파일에 계정 이름과 계정 키를 추가해야 Azure 저장소 계정에 액세스할 수 있습니다.

**Azure 저장소 계정에 대한 연결을 구성하려면**

1. 메모장에서 **C:\hdp\hadoop-2.4.0.2.1.3.0-1981\etc\hadoop\core-site.xml**을 엽니다.
2. 다음 <property\> 태그를 다른 <property\> 태그 옆에 추가합니다.

		<property>
		    <name>fs.azure.account.key.<StorageAccountName>.blob.core.windows.net</name>
		    <value><StorageAccountKey></value>
		</property>

	<StorageAccountName\> 및 <StorageAccountKey\>는 저장소 계정 정보와 일치하는 값으로 대체해야 합니다.

3. 변경 내용을 저장합니다.  Hadoop 서비스를 다시 시작할 필요는 없습니다.

다음 구문을 사용하여 저장소 계정에 액세스합니다.

	wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/

예를 들면 다음과 같습니다.

	hadoop fs -ls wasb://myContainer@myStorage.blob.core.windows.net/


##<a name="powershell"></a> HDInsight PowerShell 실행
HDInsight Emulator에서는 일부 Azure HDInsight PowerShell cmdlet도 지원됩니다. 이러한 cmdlet은 다음과 같습니다.

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

Hadoop 작업 제출에 대한 자세한 내용은 [Hadoop 작업을 프로그래밍 방식으로 제출][hdinsight-submit-jobs]을 참조하세요. HDInsight PowerShell cmdlet에 대한 자세한 내용은 [HDInsight cmdlet 참조][hdinsight-powershell-reference]를 참조하세요.


##<a name="remove"></a> HDInsight Emulator 제거
Emulator를 설치한 컴퓨터에서 제어판을 열고 **프로그램**에서 **프로그램 제거**를 클릭합니다. 설치된 프로그램 목록에서 **Microsoft HDInsight Emulator for Azure**를 마우스 오른쪽 단추로 클릭하고 **제거**를 클릭합니다. 


##<a name="nextsteps"></a> 다음 단계
이 자습서에서는 HDInsight Emulator를 설치했으며 일부 Hadoop 작업을 실행했습니다. 자세한 내용은 다음 문서를 참조하세요.

- [Azure HDInsight를 사용하여 시작][hdinsight-get-started]
- [HDInsight용 Java MapReduce 프로그램 개발][hdinsight-develop-mapreduce]
- [HDInsight용 C# Hadoop 스트리밍 MapReduce 프로그램 개발][hdinsight-develop-deploy-streaming]
- [HDInsight Emulator 릴리스 정보][hdinsight-emulator-release-notes]
- [HDInsight 토론을 위한 MSDN 포럼](http://social.msdn.microsoft.com/Forums/ko-kr/hdinsight)



[azure-sdk]: http://azure.microsoft.com/ko-kr/downloads/
[azure-create-storage-account]: ../storage-create-storage-account/
[azure-management-portal]: https://manage.windowsazure.com/
[netstat-url]: http://technet.microsoft.com/ko-kr/library/ff961504.aspx

[hdinsight-develop-mapreduce]: ../hdinsight-develop-deploy-java-mapreduce/

[hdinsight-emulator-install]: http://www.microsoft.com/web/gallery/install.aspx?appid=HDINSIGHT
[hdinsight-emulator-release-notes]: ../hdinsight-emulator-release-notes/

[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: http://msdn.microsoft.com/ko-kr/library/windowsazure/dn479228.aspx
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-develop-deploy-streaming]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
[hdinsight-versions]: ../hdinsight-component-versioning/

[Powershell-install-configure]: ../install-configure-powershell/

[hadoop-commands-manual]: http://hadoop.apache.org/docs/r1.1.1/commands_manual.html

[image-hdi-emulator-services]: ./media/hdinsight-get-started-emulator/HDI.Emulator.Services.png 

<!--HONumber=42-->
