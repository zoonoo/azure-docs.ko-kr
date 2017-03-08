---
title: "Apache Storm 자습서: Storm 시작 | Microsoft 문서"
description: "HDInsight에서 Storm Starter 샘플을 실행하여 Apache Storm을 사용한 빅 데이터 분석을 시작합니다. Storm을 사용하여 실시간으로 데이터를 처리하는 방법을 알아봅니다."
keywords: "apache storm, apache storm 자습서, 빅 데이터 분석, storm 시작"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 6f93f7ff-0736-4708-80ef-4289dae532a9
ms.service: hdinsight
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/28/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cfaade8249a643b77f3d7fdf466eb5ba38143f18
ms.openlocfilehash: e505d02895abd011661b3e4f66c7f4f7ea042358
ms.lasthandoff: 03/01/2017

---
# <a name="get-started-with-the-storm-starter-samples-for-big-data-analytics-on-hdinsight"></a>HDInsight에서 Storm Starter 샘플을 사용하여 빅 데이터 분석 시작

Apache Storm은 데이터 스트림 처리용 확장 가능한 분산형 실시간 계산 시스템입니다. Microsoft Azure HDInsight의 Storm을 사용하여 실시간 빅 데이터 분석을 수행하는 클라우드 기반 Storm 클러스터를 만들 수 있습니다. 

> [!IMPORTANT]
> 이 문서의 단계는 Windows 기반 HDInsight 클러스터를 만듭니다. HDInsight는 HDInsight 3.4 이하 버전의 경우 Windows에서만 사용 가능합니다. Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중단](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)을 참조하세요.
>
> HDInsight 클러스터에서 Linux 기반 Storm을 만드는 단계에 대해서는 [Apache Storm 자습서: HDInsight에서 데이터 분석을 사용하여 Storm Starter 샘플 시작](hdinsight-apache-storm-tutorial-get-started-linux.md)

## <a name="prerequisites"></a>필수 조건
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

이 Apache Storm 자습서를 성공적으로 완료하려면 다음 항목이 필요합니다.

* **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.

### <a name="access-control-requirements"></a>액세스 제어 요구 사항
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-a-storm-cluster"></a>Storm 클러스터 만들기

다음 단계를 사용하여 HDInsight 클러스터에 Storm을 만듭니다.

1. [Azure Portal](https://portal.azure.com)에서 **+ 새로 만들기**, **인텔리전스 + 분석**, **HDInsight**를 차례로 선택합니다.
   
    ![HDInsight 클러스터 만들기](./media/hdinsight-apache-storm-tutorial-get-started/create-hdinsight.png)

2. **기본** 블레이드에서 다음 정보를 입력합니다.

    * **클러스터 이름**: HDInsight 클러스터의 이름입니다.
    * **구독**: 사용할 구독을 선택합니다.
    * **클러스터 로그인 사용자 이름** 및 **클러스터 로그인 암호**: HTTPS를 통해 클러스터에 액세스할 때 로그인입니다. 이러한 자격 증명을 사용하여 Ambari Web UI 또는 REST API와 같은 서비스에 액세스합니다.
    * **SSH(secure Shell) 사용자 이름**: 이 필드를 기본값으로 유지합니다. Windows 기반 HDInsight 클러스터에는 사용되지 않습니다.
    * **리소스 그룹**: 클러스터를 만들어 놓은 리소스 그룹입니다.
    * **위치**: 클러스터를 만들어 놓은 Azure 지역입니다.
   
    ![구독 선택](./media/hdinsight-apache-storm-tutorial-get-started/hdinsight-basic-configuration.png)

3. **클러스터 유형**을 선택한 다음 **클러스터 구성** 블레이드에서 다음 값을 설정합니다.
   
    * **클러스터 유형**: Storm

    * **운영 체제**: Windows

    * **버전**: Storm 0.10.0(HDI 3.3)

        > [!NOTE]
        > HDInsight 버전 3.4 이상은 Linux 운영 체제에서만 사용 가능합니다.

    * **클러스터 계층**: 표준
     
    마지막으로 **선택** 단추를 사용하여 이러한 설정을 저장합니다.
     
    ![클러스터 유형 선택](./media/hdinsight-apache-storm-tutorial-get-started/set-hdinsight-cluster-type.png)

4. 클러스터 유형을 선택한 후 __선택__ 단추를 사용하여 클러스터 유형을 설정합니다. 그 다음, __다음__ 단추를 사용하여 기본 구성을 완료합니다.

5. **저장소** 블레이드에서 저장소 계정을 선택하거나 만듭니다. 이 문서의 단계에서는 이 블레이드의 다른 필드를 기본값으로 둡니다. __다음__ 단추를 사용하여 저장소 구성을 저장합니다.

    ![HDInsight에 대한 저장소 계정 설정 지정](./media/hdinsight-apache-storm-tutorial-get-started/set-hdinsight-storage-account.png)

6. **요약** 블레이드에서 클러스터에 대한 구성을 검토합니다. __편집__ 링크를 사용하여 올바르지 않은 설정을 변경합니다. 마지막으로 __만들기__ 단추를 사용하여 클러스터를 만듭니다.
   
    ![클러스터 구성 요약](./media/hdinsight-apache-storm-tutorial-get-started/hdinsight-configuration-summary.png)
   
    > [!NOTE]
    > 클러스터를 만드는 데 최대 20분이 걸릴 수 있습니다.

## <a name="run-a-storm-starter-sample-on-hdinsight"></a>HDInsight에서 Storm Starter 샘플 실행
이 Apache Storm 자습서에서는 GitHub의 Storm Starter 샘플을 사용한 빅 데이터 분석을 소개합니다.

각 HDInsight Storm 클러스터에는 클러스터에서 Storm 토폴로지를 업로드 및 실행하는 데 사용할 수 있는 Storm 대시보드가 제공됩니다. 또한 각 클러스터에는 Storm 대시보드에서 직접 실행할 수 있는 샘플 토폴로지가 제공됩니다.

### <a id="connect"></a>대시보드에 연결
대시보드는 **https://&lt;clustername>.azurehdinsight.net//**에 있으며, 여기서 **clustername**은 클러스터의 이름입니다. 또한 시작 보드에서 클러스터를 선택하고 블레이드 위쪽의 **대시보드** 링크를 선택하면 대시보드에 대한 링크를 찾을 수 있습니다.

![Storm 대시보드 링크가 있는 Azure 포털](./media/hdinsight-apache-storm-tutorial-get-started/dashboard.png)

> [!NOTE]
> 대시보드를 연결할 때 사용자 이름 및 암호를 입력하라는 메시지가 표시됩니다. 이는 관리자 이름(**admin**) 및 클러스터를 만들 때 사용한 암호입니다.
> 
> 

Storm 대시보드가 로드되면 **토폴로지 제출** 양식이 표시됩니다.

![스톰 대시보드와 함께 스톰 스타터 토폴로지를 제출합니다.](./media/hdinsight-apache-storm-tutorial-get-started/submit.png)

**토폴로지 제출** 양식은 Storm 토폴로지가 포함된 .jar 파일을 업로드 및 실행하는 데 사용됩니다. 여기에는 클러스터와 함께 제공된 몇 가지 기본 샘플도 포함되어 있습니다.

### <a id="run"></a>GitHub의 Storm Starter 프로젝트에서 단어 개수 샘플 실행
클러스터와 함께 제공된 샘플에는 여러 변형의 단어 계산 토폴로지가 포함되어 있습니다. 이러한 샘플에는 문장을 임의로 내보내는 **spout** 및 각 문장을 개별 단어로 나눈 다음 각 단어가 발생한 횟수를 계산하는 **bolts**가 있습니다. 이러한 샘플은 Apache Storm의 일부인 [Storm Starter 샘플](https://github.com/apache/storm/tree/master/examples/storm-starter)에서 제공됩니다.

Storm Starter 샘플을 실행하려면 다음 단계를 수행합니다.

1. **Jar 파일** 드롭다운 목록에서 **StormStarter-WordCount**를 선택합니다. 그러면 **클래스 이름** 및 **추가 매개 변수** 필드가 이 샘플의 매개 변수로 채워집니다.
   
    ![스톰 대시보드에서 스톰 스타터 WordCount가 선택됩니다.](./media/hdinsight-apache-storm-tutorial-get-started/submit.png)
   
   * **클래스 이름** - 토폴로지를 제출하는 .jar 파일의 클래스입니다.
   * **추가 매개 변수** - 토폴로지에 필요한 모든 매개 변수입니다. 이 예제에서는 이 필드를 사용하여 제출된 토폴로지의 이름을 제공합니다.
2. **제출**을 클릭합니다. 잠시 후 **결과** 필드에 작업을 제출하는 데 사용된 명령 및 해당 명령의 결과가 표시됩니다. **오류** 필드에는 토폴로지 제출 시 발생한 모든 오류가 표시됩니다.
   
    ![톰 스타터 WordCount의 결과 및 제출 단추.](./media/hdinsight-apache-storm-tutorial-get-started/submit-results.png)
   
   > [!NOTE]
   > 결과에서는 토폴로지가 완료되었음을 나타내지 않습니다. **Storm 토폴로지는 일단 시작된 후에는 중지할 때까지 실행됩니다.** 단어 개수 토폴로지는 임의의 문장을 생성하며 중지할 때까지 각 단어가 나오는 횟수를 계산합니다.
   > 
   > 

### <a id="monitor"></a>토폴로지 모니터링
Storm UI를 사용하여 토폴로지를 모니터링할 수 있습니다.

1. Storm 대시보드의 위쪽에서 **Storm UI** 를 선택합니다. 실행 중인 모든 토폴로지 및 클러스터에 대한 요약 정보가 표시됩니다.
   
    ![스톰 스타터 WordCount 토폴로지 요약을 표시하는 스톰 대시보드.](./media/hdinsight-apache-storm-tutorial-get-started/stormui.png)
   
    위 페이지에서는 토폴로지가 활성화된 시간과 작업자, 실행자 및 사용 중인 작업 수를 볼 수 있습니다.
   
   > [!NOTE]
   > **이름** 열에는 **추가 매개 변수** 필드를 통해 이전에 제공된 이름이 포함되어 있습니다.
   > 
   > 
2. **토폴로지 요약**의 **이름** 열에서 **wordcount** 항목을 선택합니다. 그러면 토폴로지에 대한 자세한 정보가 표시됩니다.
   
    ![스톰 스타터 WordCount 토폴로지 정보가 있는 스톰 대시보드.](./media/hdinsight-apache-storm-tutorial-get-started/topology-summary.png)
   
    이 페이지에서는 다음 정보를 제공합니다.
   
   * **토폴로지 통계** - 기간으로 구성된 토폴로지 성능에 대한 기본 정보입니다.
     
     > [!NOTE]
     > 특정 기간을 선택하면 페이지의 다른 섹션에 표시되는 정보에 대한 기간이 변경됩니다.
     > 
     > 
   * **Spout** - 각 Spout에서 반환된 마지막 오류를 포함하여 Spout에 대한 기본 정보입니다.
   * **Bolt** - Bolt에 대한 기본 정보입니다.
   * **토폴로지 구성** - 토폴로지 구성에 대한 자세한 정보입니다.
     
     이 페이지에서는 토폴로지에 대해 수행할 수 있는 작업도 제공합니다.
   * **활성화** - 비활성화된 토폴로지 처리를 다시 시작합니다.
   * **비활성화** - 실행 중인 토폴로지를 일시 중지합니다.
   * **균형 다시 맞추기** - 토폴로지의 병렬 처리를 조정합니다. 클러스터에서 노드 수를 변경한 후 실행 중인 토폴로지의 균형을 다시 맞추어야 합니다. 이렇게 하면 토폴로지가 병렬 처리를 조정하여 클러스터에서 증가/감소한 노드 수를 보충할 수 있습니다. 자세한 내용은 [Storm 토폴로지의 병렬 처리 이해](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)를 참조하세요.
   * **중단** - 지정된 시간 제한 후 Storm 토폴로지를 종료합니다.
3. 이 페이지의 **Spouts** 또는 **Bolts** 섹션에서 항목을 선택합니다. 그러면 선택한 구성 요소에 대한 정보가 표시됩니다.
   
    ![선택한 구성 요소에 대한 정보가 있는 스톰 대시보드.](./media/hdinsight-apache-storm-tutorial-get-started/component-summary.png)
   
    이 페이지에는 다음 정보가 표시됩니다.
   
   * **Spout/Bolt 통계** - 기간으로 구성된 구성 요소 성능에 대한 기본 정보입니다.
     
     > [!NOTE]
     > 특정 기간을 선택하면 페이지의 다른 섹션에 표시되는 정보에 대한 기간이 변경됩니다.
     > 
     > 
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

### <a name="stop-the-topology"></a>토폴로지 중지
단어 개수 토폴로지에 대한 **토폴로지 요약** 페이지로 돌아가 **토폴로지 동작** 섹션에서 **중단**을 선택합니다. 메시지가 표시되면 토폴로지를 중지하기 전에 대기할 시간(초)으로 10을 입력합니다. 시간 제한이 지난 후에는 대시보드의 **Storm UI** 섹션을 방문한 경우 토폴로지가 더 이상 표시되지 않습니다.

## <a name="delete-the-cluster"></a>클러스터 삭제
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="summary"></a>요약
이 Apache Storm 자습서에서는 Storm Starter를 사용하여 HDInsight의 Storm 클러스터를 만드는 방법 및 Storm 대시보드를 사용하여 Storm 토폴로지를 배포, 모니터링 및 관리하는 방법에 대해 알아보았습니다.

## <a id="next"></a>다음 단계
* **Visual Studio용 HDInsight 도구** - HDInsight 도구를 사용하면 Visual Studio에서 위에 설명된 Storm 대시보드와 유사한 Storm 토폴로지를 제출, 모니터링 및 관리할 수 있습니다. 또한 HDInsight 도구는 C# Storm 토폴로지를 만드는 기능을 제공하며, 클러스터에서 배포 및 실행할 수 있는 샘플 토폴로지를 포함합니다.
  
    자세한 내용은 [Visual Studio용 HDInsight 도구 사용 시작](hdinsight-hadoop-visual-studio-tools-get-started.md)을 참조하세요.
* **샘플 파일** - HDInsight Storm 클러스터의 **%STORM_HOME%\contrib** 디렉터리에 제공되는 몇 가지 예제가 있습니다. 각 예제는 다음 항목을 포함합니다.
  
  * 소스 코드 - storm-starter-0.9.1.2.1.5.0-2057-sources.jar 등
  * Java 문서 - storm-starter-0.9.1.2.1.5.0-2057-javadoc.jar 등
  * 예제 - storm-starter-0.9.1.2.1.5.0-2057-jar-with-dependencies.jar 등
    
    'jar' 명령을 사용하여 소스 코드 또는 Java 문서를 추출합니다. 예를 들어 'jar -xvf storm-starter-0.9.1.2.1.5.0.2057-javadoc.jar' 등을 사용할 수 있습니다.
    
    > [!NOTE]
    > Java 문서는 웹 페이지로 구성됩니다. 추출 후에는 브라우저를 사용하여 **index.html** 파일을 확인합니다.
    > 
    > 
    
    이러한 샘플에 액세스하려면 HDInsight의 Storm 클러스터에 대한 원격 데스크톱을 사용하도록 설정한 다음 **%STORM_HOME%\contrib**에서 파일을 복사해야 합니다.
* 다음 문서에는 HDInsight의 Storm에서 사용할 수 있는 다른 예제의 목록이 들어 있습니다.
  
  * [HDInsight의 Storm에 대한 예제 토폴로지](hdinsight-storm-example-topology.md)

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[azureportal]: https://manage.windowsazure.com/
[hdinsight-provision]: hdinsight-provision-clusters.md
[preview-portal]: https://portal.azure.com/

