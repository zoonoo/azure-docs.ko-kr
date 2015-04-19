<properties
   pageTitle="HDInsight에서 Hadoop과 MapReduce 사용 | Azure"
   description="SSH를 사용하여 HDInsight에서 Hadoop으로 MapReduce 작업을 실행하는 방법에 대해 알아봅니다."
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

# SSH 사용하는 HDInsight에서 Hadoop과 Hive 사용

[AZURE.INCLUDE [mapreduce-selector](../includes/hdinsight-selector-use-mapreduce.md)]

이 문서에서는 HDInsight 클러스터에서 SSH를 사용하여 Hadoop에 연결하고 Hadoop 명령을 사용하여 MapReduce 작업을 제출하는 방법을 배웁니다.

> [AZURE.NOTE] Linux 기반 Hadoop 서버를 익숙하게 사용하지만 HDInsight는 처음인 경우 <a href="../hdinsight-hadoop-linux-information/" target="_blank">HDInsight의 Linux 기반 Hadoop에 대해 알아야 할 정보</a>를 참조하세요.

##<a id="prereq"></a>필수 조건

이 문서의 단계를 완료하려면 다음이 필요합니다.

* Linux 기반 HDInsight(HDInsight의 Hadoop) 클러스터

* SSH 클라이언트. Linux, Unix 및 Mac OS에는 SSH 클라이언트가 함께 제공됩니다. Windows 사용자는 <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">Putty</a>와 같은 클라이언트를 다운로드해야 합니다.

##<a id="ssh"></a>SSH를 사용하여 연결

SSH 명령을 사용하여 HDInsight 클러스터의 정규화된 도메인 이름(FQDN)에 연결합니다. FQDN은 클러스터에 지정한 이름이며 그 다음은 **.azurehdinsight.net**입니다. 예를 들어, 다음은 **myhdinsight** 클러스터에 연결합니다.

	ssh admin@myhdinsight-ssh.azurehdinsight.net

**SSH 인증에 대한 인증서 키를 제공하는 경우**, HDInsight 클러스터를 만들 때 클라이언트 시스템에서 개인 키의 위치를 지정해야 할 수도 있습니다.

	ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**SSH 인증을 위해 암호를 제공하는 경우**, HDInsight 클러스터를 만들 때 메시지가 표시되면 암호를 제공해야 합니다.

###Putty(Windows 클라이언트)

Windows에는 SSH 클라이언트가 기본 제공되지 않습니다. **Putty**는 <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html</a>에서 다운로드하여 사용하는 것이 좋습니다.

Putty 사용에 대한 자세한 내용은 <a href="http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-ssh-key/" target="_blank">Azure에서 Linux와 함께 SSH를 사용하는 방법</a>의 **Putty를 사용하여 Linux 컴퓨터에 연결** 섹션을 참조하세요.

> [AZURE.NOTE] HDInsight 클러스터에 대한 SSH 인증을 위해 인증서를 사용한 경우 <a href="http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-ssh-key/" target="_blank">Azure에서 Linux와 함께 SSH를 사용하는 방법</a>의 **Putty용 PPK 만들기** 섹션도 참조해야 할 수 있습니다.

##<a id="hadoop"></a>Hadoop 명령 사용

1. HDInsight 클러스터에 연결되면 다음을 사용하여 MapReduce 작업을 시작하는 **Hadoop** 명령을 사용합니다.

		hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount wasb:///example/data/gutenberg/davinci.txt wasb:///example/data/WordCountOutput

	이 명령은 **hadoop-mapreduce-examples.jar** 파일에 있는 **wordcount** 클래스를 시작합니다. 입력으로 **wasb://example/data/gutenberg/davinci.txt** 문서를 사용하고 출력은 **wasb:///example/data/WordCountOutp**에 저장됩니다.

	> [AZURE.NOTE] 이 MapReduce 작업 및 예제 데이터에 대한 자세한 내용은 <a href="../hdinsight-use-mapreduce/" target="_blank">HDInsight Hadoop에서 MapReduce 사용</a>을 참조하세요.

2. 작업이 처리되는 동안 세부 정보를 내보내며 마지막으로 작업이 완료될 때 반환 정보는 다음과 유사합니다.

		File Input Format Counters
        Bytes Read=1395666
		File Output Format Counters
        Bytes Written=337623

3. 완료되면 **wasb://example/data/WordCountOutput**에 저장된 출력 파일을 나열하려면 다음 명령을 사용합니다.

		hadoop fs -ls wasb:///example/data/WordCountOutput

	**_SUCCESS** 및 **part-r-00000**이라는 두 파일이 표시되어야 합니다. **part-r-00000** 파일은 이 작업에 대한 출력을 포함합니다.

	> [AZURE.NOTE] 일부 MapReduce 작업은 여러 **part-r-#####** 파일로 나눠질 수 있습니다. 그럴 경우 ##### 접미사가 파일의 순서를 나타냅니다.

4. 출력을 보려면 다음 명령을 사용합니다.

		hadoop fs -cat wasb:///example/data/WordCountOutput/part-r-00000

	그러면 각 단어가 나타나는 횟수뿐만 아니라 **wasb://example/data/gutenberg/davinci.txt** 파일에 포함된 단어의 목록이 표시됩니다.  다음은 파일에 포함된 데이터의 예입니다.

		wreathed        3
		wreathing       1
		wreaths 		1
		wrecked 		3
		wrenching       1
		wretched        6
		wriggling       1

##<a id="summary"></a>요약

여기에서 볼 수 있듯이 Hadoop 명령은 HDInsight 클러스터에서 MapReduce 작업을 실행하고 작업 출력을 볼 수 있는 쉬운 방법을 제공합니다.

##<a id="nextsteps"></a>다음 단계

HDInsight에서 MapReduce 작업의 일반적인 내용.

* [HDInsight Hadoop에서 MapReduce 사용](hdinsight-use-mapreduce.md)

HDInsight에서 Hadoop으로 작업하는 다른 방법에 관한 내용입니다.

* [HDInsight에서 Hadoop과 Hive 사용](hdinsight-use-hive.md)

* [HDInsight에서 Hadoop과 Pig 사용](hdinsight-use-pig.md)
<!--HONumber=47-->
