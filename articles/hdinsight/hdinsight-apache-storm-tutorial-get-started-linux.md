<properties
	pageTitle="Apache Storm 자습서: HDInsight에서 Linux 기반 Storm 시작 | Microsoft Azure"
	description="Linux 기반 HDInsight에서 Apache Storm 및 Storm Starter 샘플을 사용한 빅 데이터 분석을 시작합니다. Storm을 사용하여 실시간으로 데이터를 처리하는 방법을 알아봅니다."
	keywords="apache storm, apache storm 자습서, 빅 데이터 분석, storm 시작"
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="03/07/2016"
   ms.author="larryfr"/>


# Apache Storm 자습서: Storm Starter 샘플을 사용하여 HDInsight에서 빅 데이터 분석 시작

Apache Storm은 데이터 스트림 처리용 확장 가능한 분산형 실시간 계산 시스템입니다. Azure HDInsight의 Storm을 사용하여 실시간 데이터 분석을 수행하는 클라우드 기반 Storm 클러스터를 만들 수 있습니다.

> [AZURE.NOTE] 이 문서의 단계는 Linux 기반 HDInsight 클러스터를 만듭니다. HDInsight 클러스터에서 Linux 기반 Storm을 만드는 단계에 대해서는 [Apache Storm 자습서: HDInsight에서 빅 데이터 분석을 사용하여 Storm Starter 샘플 시작](hdinsight-apache-storm-tutorial-get-started.md)을 참조하세요.

## 시작하기 전에

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

이 Apache Storm 자습서를 성공적으로 완료하려면 다음 항목이 필요합니다.

- **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.

- **SSH 및 SCP 사용 경험**. HDInsight에서 SSH와 SCP를 사용하는 방법에 대한 자세한 내용은 다음을 참조하세요.

    - **Linux, Unix 또는 OS X 클라이언트**: [Linux, OS X 또는 Unix에서 HDInsight의 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

	- **Windows 클라이언트**: [Windows에서 HDInsight의 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows.md)을 참조하세요.

## Storm 클러스터 만들기

HDInsight의 Storm에서는 Azure Blob 저장소를 사용하여 클러스터에 제출된 토폴로지 및 로그 파일을 저장합니다. 다음 단계를 사용하여 클러스터에서 사용할 Azure 저장소 계정을 만들 수 있습니다.

1. [Azure 포털][preview-portal]에 로그인합니다.

2. **새로 만들기**를 선택하고 __데이터 분석__, __HDInsight__를 차례로 선택합니다.

	![Azure 포털에서 새 클러스터 만들기](./media/hdinsight-apache-storm-tutorial-get-started-linux/new-cluster.png)

3. __클러스터 이름__을 입력한 후 __클러스터 형식__에 대해 __Storm__을 선택합니다. 클러스터 이름을 사용할 수 있는 경우 __클러스터 이름__ 옆에 녹색 확인 표시가 나타납니다.

	![클러스터 이름, 클러스터 유형 및 OS 유형](./media/hdinsight-apache-storm-tutorial-get-started-linux/clustername.png)

	__Ubuntu__를 선택하여 Linux 기반 HDInsight 클러스터를 만듭니다.
    
    > [AZURE.NOTE] 이 문서의 단계에 대한 기본 값에 __버전__ 필드를 남겨둡니다.
	
4. 둘 이상의 구독이 있는 경우 __구독__ 항목을 선택하여 클러스터에 사용할 Azure 구독을 선택합니다.

5. __리소스 그룹__의 경우 기존 리소스 그룹 목록을 표시할 항목을 선택한 다음 클러스터를 만들 리소스 그룹을 선택할 수 있습니다. 또는 __새로 만들기__를 선택한 다음 새 리소스 그룹의 이름을 입력할 수 있습니다. 새 그룹 이름을 사용할 수 있는지 여부를 나타내는 녹색 확인 표시가 나타납니다.

	> [AZURE.NOTE] 사용할 수 있는 경우 이 항목은 기존 리소스 그룹 중 하나로 기본 설정됩니다.

6. __자격 증명__을 선택한 다음 __클러스터 로그인 사용자 이름__에 대한 __클러스터 로그인 암호__를 입력합니다. __SSH 사용자 이름__과 __암호__ 또는 __공개 키__(SSH 사용자를 인증하는 데 사용됨)도 입력해야 합니다. 마지막으로 __선택__ 단추를 사용하여 자격 증명을 설정합니다.

	![클러스터 자격 증명 블레이드](./media/hdinsight-administer-use-portal-linux/clustercredentials.png)

	HDInsight에서 SSH를 사용하는 방법에 대한 자세한 내용은 다음 문서 중 하나를 참조하세요.

	* [Linux, Unix 또는 OS X의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Windows의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows.md)

6. __데이터 원본__의 경우 항목을 선택하여 기존 데이터 원본을 선택하거나 새로 만듭니다.

	![데이터 원본 블레이드](./media/hdinsight-apache-storm-tutorial-get-started-linux/datasource.png)
	
	현재 HDInsight 클러스터의 데이터 원본으로 Azure 저장소 계정을 선택할 수 있습니다. 다음을 사용하여 __데이터 원본__ 블레이드의 항목을 이해합니다.
	
	- __선택 방법__: 구독에서 저장소 계정을 찾을 수 있도록 하려면 이 항목을 __From all subscriptions(모든 구독에서)__로 설정합니다. 기존 저장소 계정의 __저장소 이름__ 및 __선택키__를 입력하려면 __선택키__로 설정합니다.
    
    - __저장소 계정 선택__: 구독에 저장소 계정이 이미 있는 경우 이를 사용하여 클러스터에 사용할 계정을 선택합니다.
	
	- __새로 만들기__: 새 저장소 계정을 만들려면 사용합니다. 저장소 계정의 이름을 입력할 때 나타나는 필드를 사용합니다. 이름을 사용할 수 있는 경우 녹색 확인 표시가 나타납니다.
	
	- __기본 컨테이너 선택__: 클러스터에 사용할 기본 컨테이너의 이름을 입력하려면 이 항목을 사용합니다. 여기에 아무 이름이나 입력할 수 있지만, 컨테이너가 이 특정 클러스터에 사용됨을 쉽게 인식할 수 있도록 클러스터와 같은 이름을 사용하는 것이 좋습니다.
	
	- __위치__: 저장소 계정이 있거나 저장소 계정을 만들 지리적 지역입니다.
	
		> [AZURE.IMPORTANT] 기본 데이터 원본의 위치를 선택하면 HDInsight 클러스터의 위치도 설정됩니다. 클러스터와 기본 데이터 원본은 같은 지역에 있어야 합니다.
    
    - __클러스터 AAD ID__: 이를 사용하여 Azure Data Lake 저장소에 액세스하는 클러스터에서 사용할 수 있는 Azure Active Directory ID를 선택합니다.
    
        > [AZURE.NOTE] 이 문서에서 사용되지 않으며 기본 설정에 남아 있을 수 있습니다. 이 항목 및 Azure Data Lake 저장소를 HDInsight와 함께 사용하는 데 대한 자세한 내용은 [Azure Data Lake 저장소를 사용하는 HDInsight 클러스터 만들기](data-lake-store-hdinsight-hadoop-use-portal.md)를 참조하세요.
		
	- __선택__: 데이터 원본 구성을 저장하려면 이 항목을 사용합니다.
	
7. __노드 가격 책정 계층__을 선택하여 이 클러스터에 대해 만들어질 노드에 대한 정보를 표시합니다. 기본적으로 작업자 노드 수는 __4__로 설정됩니다. 클러스터의 예상 비용이 이 블레이드의 맨 아래에 표시됩니다.

	![노드 가격 책정 계층 블레이드](./media/hdinsight-apache-storm-tutorial-get-started-linux/nodepricingtiers.png)
	
    클러스터에서 이러한 노드에 사용되는 VM 형식을 변경하려면 각 노드 형식을 선택할 수 있습니다. 이 문서의 단계에 대한 기본 설정에 둡니다.
    
	__선택__ 단추를 사용하여 __노드 가격 책정 계층__ 정보를 저장합니다.

8. __선택적 구성__을 선택합니다. 이 블레이드를 사용하면 클러스터가 __가상 네트워크__에 조인하고 __스크립트 작업__을 사용하여 클러스터를 사용자 지정하거나 __사용자 지정 Metastore__를 사용하여 Hive 및 Oozie에 대한 데이터를 확보할 수 있습니다.

	![선택적 구성 블레이드](./media/hdinsight-apache-storm-tutorial-get-started-linux/optionalconfiguration.png)
    
    이 문서의 단계에서 이러한 설정을 __구성되지 않음__으로 둡니다.

9. __시작 보드에 고정__이 선택되어 있는지 확인한 다음 __만들기__를 선택합니다. 그러면 클러스터가 만들어지고 Azure 포털의 시작 보드에 클러스터 타일이 추가됩니다. 아이콘이 클러스터를 프로비전 중임을 나타내고 프로비전이 완료되면 변경되어 HDInsight 아이콘을 표시합니다.

	| 프로비전 중 | 프로비전 완료 |
	| ------------------ | --------------------- |
	| ![시작 보드에 표시기 프로비전](./media/hdinsight-apache-storm-tutorial-get-started-linux/provisioning.png) | ![프로비전된 클러스터 타일](./media/hdinsight-apache-storm-tutorial-get-started-linux/provisioned.png) |

	> [AZURE.NOTE] 클러스터를 만드는데 약간의 시간이 걸리며, 일반적으로 약 15분이 소요됩니다. 시작 보드에 있는 타일 또는 페이지 왼쪽에 있는 __알림__ 항목을 사용하여 프로비전 프로세스를 확인합니다.

##HDInsight에서 Storm Starter 샘플 실행

[Storm 스타터](https://github.com/apache/storm/tree/master/examples/storm-starter) 예제는 HDInsight 클러스터에 포함됩니다. 다음 단계에서 WordCount 예제를 실행합니다.

1. SSH를 사용하여 HDInsight 클러스터에 연결합니다.

		ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
		
	SSH 사용자 계정을 보호하도록 암호를 사용한 경우 암호를 묻는 메시지가 나타납니다. 공용 키를 사용하는 경우, `-i` 매개 변수를 사용하고 일치하는 개인 키를 지정합니다. 예: `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
		
	Linux 기반 HDInsight에서 SSH를 사용하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.
	
	* [Linux, Unix 또는 OS X의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Windows의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows)

2. 다음 명령을 사용하여 예제 토폴로지를 시작합니다.

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-0.9.3.2.2.4.9-1.jar storm.starter.WordCountTopology wordcount
		
	> [AZURE.NOTE] 파일 이름의 `0.9.3.2.2.4.9-1` 부분은 HDinsight가 새 버전의 Storm으로 업데이트되면 변경될 수 있습니다.

    'Wordcount'라는 친숙한 이름으로 클러스터에서 예제 WordCount 토폴로지를 시작합니다. 임의로 문장을 생성하고 문장에서 각 단어의 발생 횟수를 계산합니다.

    > [AZURE.NOTE] 클러스터에 토폴로지를 제출할 때 `storm` 명령을 사용하기 전에 먼저 Jar 파일을 포함하는 클러스터를 복사해야 합니다. 파일이 있는 클라이언트에서 `scp` 명령을 사용하여 수행할 수 있습니다. 예를 들어 `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`과 같습니다.
    >
    > WordCount 예제 및 다른 Storm 스타터 예제는 `/usr/hdp/current/storm-client/contrib/storm-starter/`에서 클러스터에 이미 포함되어 있습니다.

##토폴로지 모니터링

Storm UI는 토폴로지를 실행하여 함께 작업하기 위한 웹 인터페이스를 제공하고 HDInsight 클러스터에 포함됩니다.

Storm UI를 사용하여 토폴로지를 모니터링하려면 다음 단계를 사용합니다.

1. https://CLUSTERNAME.azurehdinsight.net/stormui에 웹 브라우저를 엽니다. 여기서 __CLUSTERNAME__은 클러스터의 이름입니다. Storm UI가 열립니다.

	> [AZURE.NOTE] 사용자 이름 및 암호를 제공하도록 요청을 받으면 클러스터를 만들 때 사용한 클러스터 관리자(관리자) 및암호를 입력합니다.

2. **토폴로지 요약** 아래의 **이름** 열에서 **wordcount** 항목을 선택합니다. 그러면 토폴로지에 대한 자세한 정보가 표시됩니다.

	![스톰 스타터 WordCount 토폴로지 정보가 있는 스톰 대시보드.](./media/hdinsight-apache-storm-tutorial-get-started-linux/topology-summary.png)

	이 페이지에서는 다음 정보를 제공합니다.

	* **토폴로지 통계** - 기간으로 구성된 토폴로지 성능에 대한 기본 정보입니다.

		> [AZURE.NOTE] 특정 기간을 선택하면 페이지의 다른 섹션에 표시되는 정보에 대한 기간이 변경됩니다.

	* **Spout** - 각 Spout에서 반환된 마지막 오류를 포함하여 Spout에 대한 기본 정보입니다.

	* **Bolt** - Bolt에 대한 기본 정보입니다.

	* **토폴로지 구성** - 토폴로지 구성에 대한 자세한 정보입니다.

	이 페이지에서는 토폴로지에 대해 수행할 수 있는 작업도 제공합니다.

	* **활성화** - 비활성화된 토폴로지 처리를 다시 시작합니다.

	* **비활성화** - 실행 중인 토폴로지를 일시 중지합니다.

	* **균형 다시 맞추기** - 토폴로지의 병렬 처리를 조정합니다. 클러스터에서 노드 수를 변경한 후 실행 중인 토폴로지의 균형을 다시 맞추어야 합니다. 이렇게 하면 토폴로지가 병렬 처리를 조정하여 클러스터에서 증가/감소한 노드 수를 보충할 수 있습니다. 자세한 내용은 [Storm 토폴로지의 병렬 처리 이해](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)를 참조하세요.

	* **중단** - 지정된 시간 제한 후 Storm 토폴로지를 종료합니다.

3. 이 페이지의 **Spout** 또는 **Bolt** 섹션에서 항목을 선택합니다. 그러면 선택한 구성 요소에 대한 정보가 표시됩니다.

	![선택한 구성 요소에 대한 정보가 있는 스톰 대시보드.](./media/hdinsight-apache-storm-tutorial-get-started-linux/component-summary.png)

	이 페이지에는 다음 정보가 표시됩니다.

	* **Spout/Bolt 통계** - 기간으로 구성된 구성 요소 성능에 대한 기본 정보입니다.

		> [AZURE.NOTE] 특정 기간을 선택하면 페이지의 다른 섹션에 표시되는 정보에 대한 기간이 변경됩니다.

	* **입력 통계**(Bolt에만 해당) - Bolt에서 사용되는 데이터를 생성하는 구성 요소에 대한 정보입니다.

	* **출력 통계** - 이 Bolt에서 내보낸 데이터에 대한 정보입니다.

	* **실행자** - 이 구성 요소의 인스턴스에 대한 정보입니다.

	* **오류** - 이 구성 요소에 의해 생성된 오류입니다.

4. Spout 또는 Bolt에 대한 세부 정보를 볼 때 구성 요소의 특정 인스턴스에 대한 세부 정보를 확인하려면 **실행자** 섹션의 **포트** 열에서 항목을 선택합니다.

		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["with"]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["nature"]
		2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [snow]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [snow, 747293]
		2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [white]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [white, 747293]
		2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [seven]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [seven, 1493957]

	이 데이터에서 **seven**이라는 단어가 1493957번 발생했음을 알 수 있습니다. 이는 이 토폴로지가 시작된 이후에 발생한 횟수입니다.

##토폴로지 중지

단어 개수 토폴로지에 대한 **토폴로지 요약** 페이지로 돌아가 **토폴로지 동작** 섹션에서 **중단** 단추를 선택합니다. 메시지가 표시되면 토폴로지를 중지하기 전에 대기할 시간(초)으로 10을 입력합니다. 제한 시간이 지난 후에는 대시보드의 **Storm UI** 섹션을 방문한 경우 토폴로지가 더 이상 표시되지 않습니다.

##클러스터 삭제

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

##요약

이 Apache Storm 자습서에서는 Storm Starter를 사용하여 HDInsight의 Storm 클러스터를 만드는 방법 및 Storm 대시보드를 사용하여 Storm 토폴로지를 배포, 모니터링 및 관리하는 방법에 대해 알아보았습니다.

##<a id="next"></a>다음 단계

* 다음 문서에는 HDInsight의 Storm에서 사용할 수 있는 다른 예제의 목록이 들어 있습니다.

	* [HDInsight의 Storm에 대한 예제 토폴로지](hdinsight-storm-example-topology.md)

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[azureportal]: https://manage.windowsazure.com/
[hdinsight-provision]: hdinsight-provision-clusters.md
[preview-portal]: https://portal.azure.com/

<!---HONumber=AcomDC_0309_2016-->