<properties
   pageTitle="HDInsight에서 Hadoop Pig 사용 | Azure"
   description="SSH를 통해 HDInsight에서 Hadoop과 Pig을 사용하는 방법에 대해 알아봅니다."
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
 
# Pig 명령을 사용하여 Pig 작업 실행(SSH)

[AZURE.INCLUDE [pig-selector](../includes/hdinsight-selector-use-pig.md)]

이 문서에서는 SSH를 사용한 다음 대화형으로 또는 일괄 작업으로 Pig Latin 문을 실행하기 위한 Pig 명령을 사용하여 Linux 기반 HDInsight 클러스터에 연결하는 프로세스를 안내합니다.

Pig Latin 프로그래밍 언어를 사용하면 원하는 출력을 생성하는 입력 데이터에 적용되는 변환을 설명할 수 있습니다.

> [AZURE.NOTE] 이미 익숙한 Linux 기반 Hadoop 서버를 사용하지만 HDInsight는 처음인 경우, <a href="../hdinsight-hadoop-linux-information/" target="_blank">HDInsight에서 Linux 기반 Hadoop에 대 한 알아야 내용을</a>참조하세요.

## <a id="prereq"></a>필수 조건

이 문서의 단계를 완료하려면 다음이 필요합니다.

* Linux 기반 HDInsight(HDInsight의 Hadoop) 클러스터

* SSH 클라이언트. Linux, Unix 및 Mac OS에는 ssh 클라이언트가 설치되어 있습니다. Windows 사용자는 다음과 같은 클라이언트를 다운로드해야 합니다. <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">Putty</a>

## <a id="ssh"></a>SSH를 사용하여 연결

SSH 명령을 사용하여 HDInsight 클러스터의 정규화된 도메인 이름(FQDN)에 연결합니다. FQDN은 클러스터에 지정한 이름이며 그 다음은 **.azurehdinsight.net**입니다. 예를 들어, 다음은 **myhdinsight** 클러스터에 연결합니다.

	ssh admin@myhdinsight-ssh.azurehdinsight.net

**SSH 인증에 대한 인증서 키를 제공하는 경우** HDInsight 클러스터를 만들 때 클라이언트 시스템에서 개인 키의 위치를 지정해야 할 수도 있습니다.

	ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**SSH 인증을 위해 암호를 제공하는 경우** HDInsight 클러스터를 만들 때 메시지가 표시되면 암호를 제공해야 합니다.

### Putty(Windows 클라이언트)

Windows에는 SSH 클라이언트가 기본 제공되지 않습니다. 다음에서 다운로드할 수 있는 **Putty**를 사용하는 것이 좋습니다. <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html</a>.

Putty 사용에 대한 자세한 내용은 Azure에서 Linux와 함께 SSH를 사용하는 방법 중 **Linux 컴퓨터에 연결하기 위해 Putty 사용** <a href="http://azure.microsoft.com/ documentation/articles/virtual-machines-linux-use-ssh-key/" target="_blank">섹션을</a>참조하세요.

> [AZURE.NOTE] HDInsight 클러스터에 SSH 인증용 자격 증명을 사용한 경우, Azure에서 Linux와 함께 SSH를 사용하는 방법 중 **Putty용 PPK 만들기** <a href="http://azure.microsoft.com/ documentation/articles/virtual-machines-linux-use-ssh-key/" target="_blank">섹션을 참조해야 합니다.</a>

## <a id="pig"></a>Pig 명령 사용

2. 연결되면 다음 명령을 사용하여 Pig CLI(명령줄 인터페이스)를 시작합니다.

        pig

	잠시 후 `grunt>` 프롬프트가 보입니다.

3. 다음 문을 입력합니다.

		LOGS = LOAD 'wasb:///example/data/sample.log';

	이 명령은 sample.log 파일의 내용을 로그에 로드합니다. 다음을 사용하여 파일의 내용을 볼 수 있습니다.

		DUMP LOGS;

4. 이어서 다음을 사용하여 각 레코드에서 로깅 수준만 추출하는 정규식을 적용하여 데이터를 변환합니다.

		LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

	**DUMP**를 사용하여 변환 후 데이터를 볼 수 있습니다. 이 경우에는 `DUMP LEVELS;`입니다.

5. 다음 문을 사용하여 변환 적용을 계속합니다. `DUMP` 를 사용하여 각 단계의 변환 결과를 볼 수 있습니다.

	<table>
	<tr>
	<th>문</th><th>기능</th>
	</tr>
	<tr>
	<td>FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;</td><td>로그 수준에 대한 null 값을 포함하는 행을 제거하고 FILTEREDLEVELS에 결과를 저장합니다.</td>
	</tr>
	<tr>
	<td>GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;</td><td>로그 수준에 따라 행을 그룹화하고 GROUPEDLEVELS에 결과를 저장합니다.</td>
	</tr>
	<tr>
	<td>FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;</td><td>고유한 각 로그 수준 값 및 발생 횟수를 포함하는 데이터의 새 집합을 만듭니다. FREQUENCIES에 저장됩니다.</td>
	</tr>
	<tr>
	<td>RESULT = order FREQUENCIES by COUNT desc;</td><td>로그 수준을 개수(내림차순)를 기준으로 정렬하고 RESULT에 저장합니다.</td>
	</tr>
	</table>

6. `STORE`  문을 사용하여 변환 결과를 저장할 수도 있습니다. 예를 들어, 다음은 클러스터의 기본 저장소 컨테이너에 있는 **/example/data/pigout** 디렉터리에 `RESULT`를 저장합니다.

		STORE RESULT into 'wasb:///example/data/pigout'

	> [AZURE.NOTE] 데이터는 이름이 **part-nnnnn**인 파일에서 지정된 디렉터리에 저장됩니다. 해당 디렉터리가 이미 존재하는 경우 오류가 발생합니다.

7. 성가신 프롬프트를 종료하려면 다음 문을 입력합니다.

		QUIT;

### Pig Latin 배치 파일

Pig 명령을 사용하여 파일에 포함된 Pig Latin을 실행할 수도 있습니다.

3. 성가신 프롬프트를 종료 한 후 다음 명령을 사용하여 STDIN을 **pigbatch.pig**라는 파일에 파이프할 수 있습니다. 이 파일은 SSH 세션에 로그인한 계정에 대한 홈 디렉터리에 만들어집니다.

		cat > ~/pigbatch.pig

4. 다음 줄에 입력하거나 붙여넣은 다음 완료되면 Ctrl+D를 사용합니다.

		LOGS = LOAD 'wasb:///example/data/sample.log';
		LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
		FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
		GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
		FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
		RESULT = order FREQUENCIES by COUNT desc;
		DUMP RESULT;

5. pig 명령을 사용하여 **pigbatch.pig** 파일을 실행하려면 다음을 사용합니다.

		pig ~/pigbatch.pig

	일괄 작업이 완료되면 이전 단계에서 `DUMP RESULT;`를 사용했을 때와 같이 다음과 같은 출력이 보입니다.

		(TRACE,816)
		(DEBUG,434)
		(INFO,96)
		(WARN,11)
		(ERROR,6)
		(FATAL,2)

## <a id="summary"></a>요약

이처럼 Pig 명령을 사용하면 배치 파일에 저장된 문을 실행할 뿐 아니라 Pig Latin을 사용하여 MapReduce 작업을 대화형으로 실행할 수 있습니다.

## <a id="nextsteps"></a>다음 단계

HDInsight에서 Pig에 대한 일반 정보.

* [HDInsight에서 Hadoop과 Pig 사용](../hdinsight-use-pig/)

HDInsight에서 Hadoop으로 작업하는 다른 방법에 관한 내용입니다.

* [HDInsight에서 Hadoop과 Hive 사용](../hdinsight-use-hive/)

* [HDInsight에서 Hadoop과 MapReduce 사용](../hdinsight-use-mapreduce/)
<!--HONumber=45--> 
