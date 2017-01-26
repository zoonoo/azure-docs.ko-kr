---
title: "Apache Storm 자습서: HDInsight에서 Linux 기반 Storm 시작 | Microsoft Docs"
description: "Linux 기반 HDInsight에서 Apache Storm 및 Storm Starter 샘플을 사용한 빅 데이터 분석을 시작합니다. Storm을 사용하여 실시간으로 데이터를 처리하는 방법을 알아봅니다."
keywords: "apache storm, apache storm 자습서, 빅 데이터 분석, storm 시작"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: d710dcac-35d1-4c27-a8d6-acaf8146b485
ms.service: hdinsight
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/12/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 7ade85d98c41bddddb61fe75d212b351ff62eef0
ms.openlocfilehash: 1e24cebace0e6b7de7ae318f06d6430b3caa342a


---
# <a name="apache-storm-tutorial-get-started-with-the-storm-starter-samples-for-big-data-analytics-on-hdinsight"></a>Apache Storm 자습서: Storm Starter 샘플을 사용하여 HDInsight에서 빅 데이터 분석 시작

Apache Storm은 데이터 스트림 처리용 확장 가능한 분산형 실시간 계산 시스템입니다. Azure HDInsight의 Storm을 사용하여 실시간 데이터 분석을 수행하는 클라우드 기반 Storm 클러스터를 만들 수 있습니다.

> [!IMPORTANT]
> Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중단](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

이 Apache Storm 자습서를 성공적으로 완료하려면 다음 항목이 필요합니다.

* **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.

* **SSH 및 SCP 사용 경험**. HDInsight에서 SSH와 SCP를 사용하는 방법에 대한 자세한 내용은 다음을 참조하세요.
  
    * **Linux, Unix 또는 OS X 클라이언트**: [HDInsight의 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.
    
    * **Windows 클라이언트**: [Windows에서 HDInsight의 Linux 기반 Hadoop과 SSH(PuTTY) 사용](hdinsight-hadoop-linux-use-ssh-windows.md)을 참조하세요.

### <a name="access-control-requirements"></a>액세스 제어 요구 사항

[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-a-storm-cluster"></a>Storm 클러스터 만들기

이 섹션에서는 Azure Resource Manager 템플릿을 사용하여 HDInsight 버전 3.5 클러스터(Storm 버전 1.0.1)를 만듭니다. HDInsight 버전 및 해당 SLA에 대한 내용은 [HDInsight 구성 요소 버전 관리](hdinsight-component-versioning.md)를 참조하세요. 클러스터를 만드는 다른 방법은 [HDInsight 클러스터 만들기](hdinsight-hadoop-provision-linux-clusters.md)를 참조하세요.

1. Azure 포털에서 템플릿을 열려면 다음 이미지를 클릭합니다.         
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-storm-cluster-in-hdinsight-35.json" target="_blank"><img src="./media/hdinsight-apache-storm-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    템플릿은 공용 Blob 컨테이너 *https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-storm-cluster-in-hdinsight.json*에 있습니다. 

2. __사용자 지정 배포__ 블레이드에서 다음을 입력합니다.
   
    * __리소스 그룹__: 클러스터를 만들어 놓은 리소스 그룹입니다.

    * **클러스터 이름**: Hadoop 클러스터의 이름입니다.

    * __클러스터 로그인 이름__ 및 __암호__: 기본 로그인 이름은 admin입니다.
    
    * __SSH 사용자 이름__ 및 __암호__: SSH를 사용하여 클러스터에 연결할 사용자 이름 및 암호입니다.

    * __위치__: 클러스터의 지리적 위치입니다.
     
     이러한 값을 기록해 두십시오.  이 정보는 자습서의 뒷부분에서 필요합니다.
     
     > [!NOTE]
     > SSH는 명령줄을 사용하여 HDInsight 클러스터에 원격으로 액세스하는 데 사용됩니다. 여기에 사용되는 사용자 이름 및 암호는 SSH 통해 클러스터에 연결할 때 사용됩니다. 또한 모든 HDInsight 클러스터 노드에 대해 사용자 계정을 생성하므로 SSH 사용자 이름은 고유해야 합니다. 다음은 클러스터에서 서비스에 의해 사용하도록 예약된 계정 이름 중 일부이며 SSH 사용자 이름으로 사용할 수 없습니다.
     > 
     > root, hdiuser, storm, hbase, ubuntu, zookeeper, hdfs, yarn, mapred, hbase, hive, oozie, falcon, sqoop, admin, tez, hcat, hdinsight-zookeeper.
     > 
     > HDInsight에서 SSH를 사용하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.
     > 
     > * [HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)
     > * [Windows의 HDInsight에서 Linux 기반 Hadoop과 SSH(PuTTY) 사용](hdinsight-hadoop-linux-use-ssh-windows.md)

3. __위에 명시된 사용 약관에 동의함__을 선택하고 **확인**을 클릭한 다음 __대시보드에 고정__을 선택합니다.

6. **구매**를 클릭합니다. 템플릿 배포에 배포 제출 중이라는 제목의 새 타일이 표시됩니다. 클러스터를 만들려면 20분 정도가 걸립니다.

## <a name="run-a-storm-starter-sample-on-hdinsight"></a>HDInsight에서 Storm Starter 샘플 실행

[Storm 스타터](https://github.com/apache/storm/tree/master/examples/storm-starter) 예제는 HDInsight 클러스터에 포함됩니다. 다음 단계에서 WordCount 예제를 실행합니다.

1. SSH를 사용하여 HDInsight 클러스터에 연결합니다.
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    SSH 사용자 계정을 보호하도록 암호를 사용한 경우 암호를 묻는 메시지가 나타납니다. 공용 키를 사용하는 경우, `-i` 매개 변수를 사용하고 일치하는 개인 키를 지정합니다. 예: `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
   
    Linux 기반 HDInsight에서 SSH를 사용하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.
   
    * [HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Windows의 HDInsight에서 Linux 기반 Hadoop과 SSH(PuTTY) 사용](hdinsight-hadoop-linux-use-ssh-windows.md)

2. 다음 명령을 사용하여 예제 토폴로지를 시작합니다.
   
        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar storm jar org.apache.storm.starter.WordCountTopology wordcount
   
    > [!NOTE]
    > HDInsight의 이전 버전에서 토폴로지의 클래스 이름은 `org.apache.storm.starter.WordCountTopology`가 아닌 `storm.starter.WordCountTopology`입니다.
   
    'Wordcount'라는 친숙한 이름으로 클러스터에서 예제 WordCount 토폴로지를 시작합니다. 임의로 문장을 생성하고 문장에서 각 단어의 발생 횟수를 계산합니다.
   
    > [!NOTE]
    > 클러스터에 고유한 토폴로지를 제출할 때 `storm` 명령을 사용하기 전에 먼저 jar 파일을 포함하는 클러스터를 복사해야 합니다. 파일이 있는 클라이언트에서 `scp` 명령을 사용하여 수행할 수 있습니다. 예를 들어 `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
    > 
    > WordCount 예제 및 다른 Storm 스타터 예제는 `/usr/hdp/current/storm-client/contrib/storm-starter/`에서 클러스터에 이미 포함되어 있습니다.

Storm 시작 예제의 원본을 보려면 [https://github.com/apache/storm/tree/1.0.x-branch/examples/storm-starter](https://github.com/apache/storm/tree/1.0.x-branch/examples/storm-starter)에서 코드를 찾을 수 있습니다. 이 링크는 Storm 1.0.x에 해당하며 HDInsight 3.5와 함께 제공됩니다. Storm의 다른 버전의 경우 페이지 맨 위에 있는 __분기__ 단추를 사용하여 다른 Storm 버전을 선택합니다.

## <a name="monitor-the-topology"></a>토폴로지 모니터링

Storm UI는 토폴로지를 실행하여 함께 작업하기 위한 웹 인터페이스를 제공하고 HDInsight 클러스터에 포함됩니다.

Storm UI를 사용하여 토폴로지를 모니터링하려면 다음 단계를 사용합니다.

1. https://CLUSTERNAME.azurehdinsight.net/stormui에서 웹 브라우저를 엽니다. 여기서 **CLUSTERNAME**은 클러스터의 이름입니다. Storm UI가 열립니다.
    
    > [!NOTE]
    > 사용자 이름 및 암호를 제공하도록 요청을 받으면 클러스터를 만들 때 사용한 클러스터 관리자(관리자) 및 암호를 입력합니다.

2. **토폴로지 요약**의 **이름** 열에서 **wordcount** 항목을 선택합니다. 그러면 토폴로지에 대한 자세한 정보가 표시됩니다.
    
    ![스톰 스타터 WordCount 토폴로지 정보가 있는 스톰 대시보드.](./media/hdinsight-apache-storm-tutorial-get-started-linux/topology-summary.png)
    
    이 페이지에서는 다음 정보를 제공합니다.
    
    * **토폴로지 통계** - 기간으로 구성된 토폴로지 성능에 대한 기본 정보입니다.
     
        > [!NOTE]
        > 특정 기간을 선택하면 페이지의 다른 섹션에 표시되는 정보에 대한 기간이 변경됩니다.

    * **Spout** - 각 Spout에서 반환된 마지막 오류를 포함하여 Spout에 대한 기본 정보입니다.
    
    * **Bolt** - Bolt에 대한 기본 정보입니다.
    
    * **토폴로지 구성** - 토폴로지 구성에 대한 자세한 정보입니다.
     
    이 페이지에서는 토폴로지에 대해 수행할 수 있는 작업도 제공합니다.
   
    * **활성화** - 비활성화된 토폴로지 처리를 다시 시작합니다.
    
    * **비활성화** - 실행 중인 토폴로지를 일시 중지합니다.
    
    * **균형 다시 맞추기** - 토폴로지의 병렬 처리를 조정합니다. 클러스터에서 노드 수를 변경한 후 실행 중인 토폴로지의 균형을 다시 맞추어야 합니다. 이렇게 하면 토폴로지가 병렬 처리를 조정하여 클러스터에서 증가/감소한 노드 수를 보충할 수 있습니다. 자세한 내용은 [Storm 토폴로지의 병렬 처리 이해](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)를 참조하세요.
    
    * **중단** - 지정된 시간 제한 후 Storm 토폴로지를 종료합니다.

3. 이 페이지의 **Spouts** 또는 **Bolts** 섹션에서 항목을 선택합니다. 그러면 선택한 구성 요소에 대한 정보가 표시됩니다.
   
    ![선택한 구성 요소에 대한 정보가 있는 스톰 대시보드.](./media/hdinsight-apache-storm-tutorial-get-started-linux/component-summary.png)
   
    이 페이지에는 다음 정보가 표시됩니다.
   
    * **Spout/Bolt 통계** - 기간으로 구성된 구성 요소 성능에 대한 기본 정보입니다.
     
        > [!NOTE]
        > 특정 기간을 선택하면 페이지의 다른 섹션에 표시되는 정보에 대한 기간이 변경됩니다.
     
    * **입력 통계** (Bolt에만 해당) - Bolt에서 사용되는 데이터를 생성하는 구성 요소에 대한 정보입니다.
    
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
   
    이 데이터에서 **seven** 이라는 단어가 1493957번 발생했음을 알 수 있습니다. 이는 이 토폴로지가 시작된 이후에 발생한 횟수입니다.

## <a name="stop-the-topology"></a>토폴로지 중지

단어 개수 토폴로지에 대한 **토폴로지 요약** 페이지로 돌아가 **토폴로지 작업** 섹션에서 **Kill** 단추를 선택합니다. 메시지가 표시되면 토폴로지를 중지하기 전에 대기할 시간(초)으로 10을 입력합니다. 제한 시간이 지난 후에는 대시보드의 **Storm UI** 섹션을 방문한 경우 토폴로지가 더 이상 표시되지 않습니다.

## <a name="delete-the-cluster"></a>클러스터 삭제

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="a-idnextanext-steps"></a><a id="next"></a>다음 단계

이 Apache Storm 자습서에서는 Storm Starter를 사용하여 HDInsight의 Storm 클러스터를 만드는 방법 및 Storm 대시보드를 사용하여 Storm 토폴로지를 배포, 모니터링 및 관리하는 방법에 대해 알아보았습니다. 다음으로 [Maven을 사용하여 Java 기반 토폴로지를 개발하는 방법](hdinsight-storm-develop-java-topology.md)에 대해 알아봅니다.

Java 기반 토폴로지를 개발하는 데 익숙하고 기존 토폴로지를 HDInsight에 배포하려는 경우 [HDInsight에서 Apache Storm 토폴로지 배포 및 관리](hdinsight-storm-deploy-monitor-topology-linux.md)를 참조하세요.

.NET 개발자인 경우 Visual Studio를 사용하여 C# 또는 하이브리드 C#/Java 토폴로지를 만들 수 있습니다. 자세한 내용은 [Visual Studio용 Hadoop 도구를 사용하여 HDInsight에서 Apache Storm에 대한 C# 토폴로지 개발](hdinsight-storm-develop-csharp-visual-studio-topology.md)을 참조하세요.

예를 들어 HDInsight에서 Storm과 함께 사용할 수 있는 토폴로지는 다음 예제를 참조하세요.

* [HDInsight의 Storm에 대한 예제 토폴로지](hdinsight-storm-example-topology.md)

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[azureportal]: https://manage.windowsazure.com/
[hdinsight-provision]: hdinsight-provision-clusters.md
[preview-portal]: https://portal.azure.com/



<!--HONumber=Jan17_HO3-->


