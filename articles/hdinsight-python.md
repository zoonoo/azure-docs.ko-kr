<properties 
	pageTitle="Azure HDInsight에서 Hive 및 Pig와 함께 Python 사용" 
	description="Azure HDInsight의 Hive 및 Pig에서 Python UDF(사용자 정의 함수)를 사용하는 방법에 대해 알아봅니다." 
	services="hdinsight" 
	documentationCenter="" 
	authors="blackmist" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="larryfr"/>

#HDInsight에서 Hive 및 Pig와 함께 Python 사용

Hive 및 Pig는 HDInsight의 데이터 작업에 적합하지만 보다 일반적인 언어가 필요한 경우도 있습니다. Hive 및 Pig를 통해 다양한 프로그래밍 언어를 사용하여 UDF(사용자 정의 함수)를 만들 수 있습니다. 이 문서에서는 Hive 및 Pig에서 Python UDF를 사용하는 방법을 알아봅니다.

> [AZURE.NOTE] 이 문서의 단계는 HDInsight 클러스터 버전 2.1, 3.0 및 3.1에 적용됩니다.

##목차

* [HDInsight의 Python](#python)
*   [Hive 및 Python](#hivepython)
*   [Pig 및 Python](#pigpython)
* [예제 실행](#running)
* [문제 해결](#troubleshooting)
* [다음 단계](#next)

##<a name="python"></a>HDInsight의 Python

Python2.7은 기본적으로 HDInsight 3.0 클러스터에 설치됩니다. 설치는 D:\Python 폴더에 있습니다. Hive는 이 버전의 Python과 함께 사용하여 스트림을 처리할 수 있습니다(데이터는 STDOUT/STDIN을 사용하여 Hive와 Python 간에 전달됨).

HDInsight에는 Java로 작성된 Python 구현인 Jython도 포함되어 있습니다. Pig는 스트리밍을 사용하지 않고도 Jython과 통신하는 방법을 인식하므로, Pig를 사용할 때 Jython이 선호됩니다.

###<a name="hivepython"></a>Hive 및 Python

Python은 HiveQL **TRANSFORM** 문을 통해 Hive의 UDF로 사용할 수 있습니다. 예를 들어 다음 HiveQL은 **streaming.py** 파일에 저장된 Python 스크립트를 호출합니다.

	add file wasb:///streaming.py;
	
	SELECT TRANSFORM (clientid, devicemake, devicemodel)
	  USING 'D:\Python27\python.exe streaming.py' AS
	  (clientid string, phoneLable string, phoneHash string)
	FROM hivesampletable
	ORDER BY clientid LIMIT 50;

다음은 이 예제에서 수행하는 작업입니다.

1. 파일의 시작 부분에 있는 **add file** 문이 분산 캐시에 **streaming.py** 파일을 추가하므로, 클러스터의 모든 노드에서 액세스할 수 있습니다.

2.   **SELECT TRANSFORM ... USING 'D:\Python27\python.exe streaming.py'** 문은 **hivesampletable**의 데이터를 선택하고 clientid, devicemake 및 devicemodel을 **streaming.py** 스크립트로 전달합니다.

	> [AZURE.NOTE] **USING** 절은 경로에 없는 python.exe에 대한 전체 경로를 지정합니다.

3. **AS** 절은 **streaming.py**에서 반환된 필드를 설명합니다.


<a name="streamingpy"></a>
다음은 HiveQL 예제에 사용된 **streaming.py** 파일입니다.

	import sys
	import string
	import hashlib
	
	while True:
	  line = sys.stdin.readline()
	  if not line:
	    break
	
	  line = string.strip(line, "\n ")
	  clientid, devicemake, devicemodel = string.split(line, "\t")
	  phone_label = devicemake + ' ' + devicemodel
	  print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])

스트리밍을 사용하고 있으므로, 이 스크립트는 다음을 수행해야 합니다.

1. STDIN에서 데이터를 읽습니다. 이 예제에서는 `sys.stdin.readline()`을 사용하여 데이터를 읽습니다.

2. 텍스트 데이터만 필요하고 줄의 끝 표시자는 필요하지 않으므로 후행 줄 바꿈 문자는 `string.strip(line, "\n ")`를 사용하여 제거합니다.

2. 스트림 처리를 할 때 모든 값과 각 값 사이의 탭 문자가 한 줄에 포함됩니다. 따라서 `string.split(line, "\t")`를 사용하여 각 탭의 입력을 분할하여 필드만 반환할 수 있습니다.

3. 처리가 완료되면 출력을 단일 행(각 필드 사이에 탭 포함)으로 STDOUT에 작성해야 합니다.  `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`을 사용하여 그렇게 할 수 있습니다.

4. 이 작업은 모두 `while`루프에서 발생하며, 이 루프는 `line`이 읽히지 않아서 `break` 지점이 루프를 종료하고 스크립트도 종료될 때까지 반복됩니다.

그 이후에는 스크립트가 `devicemake` 및 `devicemodel`의 입력 값을 연결하고, 연결된 값의 해시를 계산합니다. 단순하게 말하면 Hive에서 호출된 Python 스크립트가 기능을 수행하는 방식의 기본 내용을 설명합니다. Python 스크립트의 기능은 루프, 입력이 더 이상 없을 때까지 입력 읽기, 탭에서 각 입력 줄 구분, 처리, 탭으로 구분된 출력을 한 줄에 쓰기입니다.

HDInsight 클러스터에서 이 예제를 실행하는 방법에 대해서는 [예제 실행]을(#running) 참조하세요.

###<a name="pigpython"></a>Pig 및 Python

**GENERATE** 문을 통해 Python 스크립트를 Pig의 UDF로 사용할 수 있습니다. 예를 들어 다음 예제에서는 **jython.py** 파일에 저장된 Python 스크립트를 사용합니다.

	Register 'wasb:///jython.py' using jython as myfuncs;
    LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
    LOG = FILTER LOGS by LINE is not null;
    DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
    DUMP DETAILS;

다음은 이 예제의 작업 방식입니다.

1. 이 예제는 **Jython**을 사용하여 Python 스크립트(**jython.py**)를 포함하는 파일을 등록하며 Pig에 **myfuncs**로 표시합니다. Jython은 Java를 사용한 Python 구현이며 Pig와 동일한 Java 가상 컴퓨터에서 실행됩니다. 따라서 Python 스크립트를 기존 함수 호출 및 Hive에 사용되는 스트리밍 접근 방식처럼 처리할 수 있습니다.

2. 다음 줄은 샘플 데이터 파일인 **sample.log**를 **LOGS**에 로드합니다. 이 로그 파일에 일관된 스키마가 없으므로 각 레코드(이 경우, **LINE**)를 **chararray**로 정의합니다. Chararray는 결과적으로 문자열입니다.

3. 세 번째 줄은 모든 Null 값을 제외하고 작업의 결과를 **LOG**에 저장합니다.

4. 그런 다음 **LOG**의 레코드에 대해 반복하고 **GENERATE**을 사용하여 **create_structure** 메서드를 호출합니다. 이 메서드는 **myfuncs**로 로드된 **jython.py** 스크립트에 포함되어 있습니다.  **LINE**은 현재 레코드를 함수에 전달하는 데 사용됩니다.

5. 마지막으로 출력은 **DUMP** 명령을 사용하여 STDOUT로 덤프됩니다. 따라서 작업이 완료되면 바로 결과가 표시됩니다. 실제 스크립트에서는 보통 데이터를 새 파일에 **저장**합니다.

<a name="jythonpy"></a>
다음은 Pig 예제에 사용된 **jython.py** 파일입니다.

	@outputSchema("log: {(date:chararray, time:chararray, classname:chararray, level:chararray, detail:chararray)}")
	def create_structure(input):
	  if (input.startswith('java.lang.Exception')):
	    input = input[21:len(input)] + ' - java.lang.Exception'
	  date, time, classname, level, detail = input.split(' ', 4)
	  return date, time, classname, level, detail

입력에 대한 일관된 스키마가 없으므로, 앞에서 **LINE** 입력을 chararray로 정의했습니다. **jython.py**가 수행하는 작업은 데이터를 출력의 일관된 스키마로 변환하는 것입니다. 다음과 같이 작업합니다.

1. **@outputSchema** 문은 Pig에 반환되는 데이터의 형식을 정의합니다. 이 경우 Pig 데이터 형식은 **데이터 모음**입니다. 모음에는 모두 chararray(문자열)인 다음과 같은 필드가 포함됩니다.

	* date - 로그 항목이 생성된 날짜
	* time - 로그 항목이 생성된 시간
	* classname - 항목이 생성된 클래스 이름
	* level - 로그 수준
	* detail - 로그 항목에 대한 세부 정보

2. 그런 다음 **def create_structure(input)**가 Pig에서 줄 항목을 전달할 함수를 정의합니다.

3. 예제 데이터인 **sample.log**는 대개 date, time, classname, level 및 detail(반환을 원하는 필드) 스키마를 준수합니다. 그러나 '*java.lang.Exception*' 문자열로 시작하며 스키마와 일치하도록 수정해야 하는 몇 개의 줄도 포함되어 있습니다. **if** 문이 이러한 줄을 확인한 후 '*java.lang.Exception*' 문자열을 끝으로 이동하고, 원하는 출력 스키마에 따라 인라인 데이터를 가져오는 입력 데이터를 전달합니다.

4. 그런 다음 **split** 명령을 사용하여 첫 4개 공백 문자에서 데이터를 분할합니다. 그 결과, 5개 값이 생성되어 d**date**, **time**, **classname**, **level** 및 **detail**에 할당됩니다.

5. 마지막으로 값은 Pig로 반환됩니다.

데이터가 Pig로 반환되면 **@outputSchema** 문에 정의된 일관된 스키마를 포함합니다.

HDInsight 클러스터에서 이 예제를 실행하는 방법에 대해서는 [예제 실행]을(#running) 참조하세요.

##<a name="running"></a>예제 실행

이 단계에서는 Microsoft Azure PowerShell을 사용합니다. 이 도구를 아직 개발 컴퓨터에 설치하여 구성하지 않은 경우 다음 단계를 사용하기 전에 [Azure PowerShell을 설치 및 구성하는 방법](http://azure.microsoft.com/documentation/articles/install-configure-powershell/)을 참조하세요.


1. Python 예제인 [streaming.py](#streamingpy)과 [jython.py](#jythonpy)를 사용하여개발 컴퓨터에서 파일의 로컬 복사본을 만듭니다.

2. 다음  PowerShell 스크립트를 사용하여 **streaming.py** 및 **jython.py** 파일을 서버에 업로드합니다. 스크립트의 첫 3개 줄에서 Azure HDInsight 클러스터의 이름과 **streaming.py** 및 **jython.py** 파일의 경로를 바꿉니다.

		$clusterName = YourHDIClusterName
		$pathToStreamingFile = "C:\path\to\streaming.py"
		$pathToJythonFile = "C:\path\to\jython.py"

		$hdiStore = get-azurehdinsightcluster -name $clusterName
		$storageAccountName = $hdiStore.DefaultStorageAccount.StorageAccountName.Split(".",2)[0]
		$storageAccountKey = $hdiStore.defaultstorageaccount.storageaccountkey
		$defaultContainer = $hdiStore.DefaultStorageAccount.StorageContainerName
		
		$destContext = new-azurestoragecontext -storageaccountname $storageAccountName -storageaccountkey $storageAccountKey
		set-azurestorageblobcontent -file $pathToStreamingFile -Container $defaultContainer -Blob "streaming.py" -context $destContext
		set-azurestorageblobcontent -file $pathToJythonFile -Container $defaultContainer -Blob "jython.py" -context $destContext

	이 스크립트는 HDInsight 클러스터의 정보를 검색한 후 계정 및 기본 저장소 계정의 키를 추출하고 컨테이너의 루트에 파일을 업로드합니다.

	> [AZURE.NOTE] 그 외에 스크립트를 업로드하는 방법은 [HDInsight에서 Hadoop 작업용 데이터 업로드](hdinsight-upload-data.md) 문서에서 찾아볼 수 있습니다.

###Hive 대시보드 사용(Hive 예제만)

1. 파일을 업로드한 후 브라우저를 열고 https://YourClusterName.azurehdinsight.net/으로 이동합니다. 자격 증명을 묻는 메시지가 표시되면 클러스터 관리자의 사용자 이름 및 암호를 입력합니다.

	> [AZURE.NOTE] 또한 Azure 관리 포털의 HDInsight **대시보드** 하단에 있는 **클러스터 관리** 링크를 사용하여 Hive 대시보드를 시작할 수 있습니다.

2. **Hive 편집기**를 사용하여 `select * from hivesampletable` 을 다음 HiveQL로 바꿉니다.

		add file wasb:///streaming.py;
		SELECT TRANSFORM (clientid, devicemake, devicemodel)
		  USING 'D:\Python27\python.exe streaming.py' AS
		  (clientid string, phoneLable string, phoneHash string)
		FROM hivesampletable
		ORDER BY clientid LIMIT 50;

3. **제출** 단추를 클릭하여 작업을 제출합니다. HDInsight 클러스터 버전에 따라 작업 세부 정보 페이지로 리디렉션될 수 있습니다. 그렇지 않은 경우 페이지 하단에 있는 **작업 세션** 영역에서 **세부 정보 보기**를 선택합니다.

4. 작업이 완료될 때까지 **작업 세부 정보** 페이지 새로 고침이 수행됩니다. 완료되면 작업에 대한 정보뿐 아니라 출력도 표시됩니다.

###PowerShell 사용(Hive 및 Pig 예제)

파일을 업로드한 후 다음 PowerShell 스크립트를 사용하여 작업을 시작합니다. 작업이 완료되면 출력이 PowerShell 콘솔에 작성됩니다.

**Hive 작업을 실행하려면**
    
    # Replace 'YourHDIClusterName' with the name of your cluster
	$clusterName = YourHDIClusterName

	$HiveQuery = "add file wasb:///streaming.py;" +
	             "SELECT TRANSFORM (clientid, devicemake, devicemodel) " +
	               "USING 'D:\Python27\python.exe streaming.py' AS " +
	               "(clientid string, phoneLable string, phoneHash string) " +
	             "FROM hivesampletable " +
	             "ORDER BY clientid LIMIT 50;"
	
	$jobDefinition = New-AzureHDInsightHiveJobDefinition -Query $HiveQuery -StatusFolder '/hivepython'
	
	$job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
	Write-Host "Wait for the Hive job to complete ..." -ForegroundColor Green
	Wait-AzureHDInsightJob -Job $job
    # Uncomment the following to see stderr output
    # Get-AzureHDInsightJobOutput -StandardError -JobId $job.JobId -Cluster $clusterName
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput

**Hive** 작업의 출력은 다음과 유사하게 표시됩니다.

	100041	RIM 9650	d476f3687700442549a83fac4560c51c
	100041	RIM 9650	d476f3687700442549a83fac4560c51c
	100042	Apple iPhone 4.2.x	375ad9a0ddc4351536804f1d5d0ea9b9
	100042	Apple iPhone 4.2.x	375ad9a0ddc4351536804f1d5d0ea9b9
	100042	Apple iPhone 4.2.x	375ad9a0ddc4351536804f1d5d0ea9b9

**Pig 작업을 실행하려면**

	# Replace 'YourHDIClusterName' with the name of your cluster
	$clusterName = YourHDIClusterName

	$PigQuery = "Register wasb:///jython.py using jython as myfuncs;" +
	            "LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);" +
	            "LOG = FILTER LOGS by LINE is not null;" +
	            "DETAILS = foreach LOG generate myfuncs.create_structure(LINE);" +
	            "DUMP DETAILS;"
	
	$jobDefinition = New-AzureHDInsightPigJobDefinition -Query $PigQuery -StatusFolder '/pigpython'
	
	$job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
	Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
	Wait-AzureHDInsightJob -Job $job
    # Uncomment the following to see stderr output
    # Get-AzureHDInsightJobOutput -StandardError -JobId $job.JobId -Cluster $clusterName
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput

**Pig** 작업의 출력은 다음과 유사하게 표시됩니다.

	((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
	((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
	((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
	((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
	((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

##<a name="troubleshooting"></a>문제 해결

이 예제를 실행하는 데 사용된 두 가지 예제 PowerShell 스크립트는 작업의 오류 출력을 표시하는 주석 처리된 줄을 포함합니다. 작업의 필요한 출력이 표시되지 않으면 다음 줄의 주석 처리를 제거하고 오류 정보가 문제를 나타내는지 확인합니다.

	# Get-AzureHDInsightJobOutput -StandardError -JobId $job.JobId -Cluster $clusterName

오류 정보(STDERR) 및 작업의 결과(STDOUT)도 다음 위치의 클러스터용 기본 Blob 컨테이너에 로깅됩니다.

<table>
<tr>
<td>이 작업의 경우</td><td>Blob 컨테이너에서 이러한 파일을 찾습니다.</td>
</tr>
<td>Hive</td><td>/HivePython/stderr</br>/HivePython/stdout</td>
</tr>
<td>Pig</td><td>/PigPython/stderr</br>/PigPython/stdout</td>
</tr>
</table>

##<a name="next"></a>다음 단계

기본적으로 제공되지 않는 Python 모듈을 로드해야 하는 경우 수행 방법의 예제에 대해서는 [Azure HDInsight에 모듈을 배포하는 방법](http://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx)(영문)을 참조하세요.

PowerShell을 사용하지 않고 HDInsight에서 작업을 원격으로 실행하려는 경우 Python을 사용하여 WebHCat REST API를 통해 작업을 실행하는 예제에 대해서는 [Linux에서 Azure HDInsight를 사용하는 방법](http://blogs.msdn.com/b/benjguin/archive/2014/02/18/how-to-use-hdinsight-from-linux.aspx)을 참조하세요.
<!--HONumber=42-->
