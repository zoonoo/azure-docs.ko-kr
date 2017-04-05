---
title: "Azure HDInsight의 Apache Spark 클러스터로 Event Hubs에서 데이터 스트리밍 | Microsoft Docs"
description: "Azure 이벤트 허브에 데이터 스트림을 보내는 방법에 대한 단계별 지침 및 scala 응용 프로그램을 사용하여 HDInsight Spark에서 해당 이벤트 수신"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 68894e75-3ffa-47bd-8982-96cdad38b7d0
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 91c60e944dd3b72f5bf1137d93ba2ae70537b2f7
ms.lasthandoff: 03/29/2017


---
# <a name="spark-streaming-process-events-from-azure-event-hubs-with-apache-spark-cluster-on-hdinsight"></a>Spark 스트리밍: HDInsight에서 Apache Spark 클러스터로 Azure Event Hubs의 이벤트 처리

이 문서에서는 Apache Spark를 사용하여 스트리밍에 관련된 몇 가지 개념을 이해한 다음, 다음 단계를 포함하는 스트리밍 솔루션을 만듭니다.

1. 독립 실행형 응용 프로그램을 사용하여 Azure 이벤트 허브로 메시지를 수집합니다.

2. Azure HDInsight의 Spark 클러스터에서 실행 중인 응용 프로그램을 사용하여 실시간으로 이벤트 허브에서 메시지를 검색합니다.

3. Azure Storage Blob, Hive 테이블 또는 SQL 테이블과 같은 다른 출력에 데이터를 라우팅합니다. 

## <a name="prerequisites"></a>필수 조건

* Azure 구독. [Azure 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.

* HDInsight의 Apache Spark 클러스터입니다. 자세한 내용은 [Azure HDInsight에서 Apache Spark 클러스터 만들기](hdinsight-apache-spark-jupyter-spark-sql.md)를 참조하세요.

## <a name="spark-streaming-concepts"></a>Spark 스트리밍 개념

Apache Spark에서의 스트리밍 처리 방법에 대한 자세한 설명은 [Apache Spark 스트리밍 개요](http://spark.apache.org/docs/latest/streaming-programming-guide.html#overview)를 참조하세요. HDInsight는 Azure에서 Spark 클러스터에 동일한 스트리밍 기능을 제공합니다.  

## <a name="what-does-this-solution-do"></a>이 솔루션의 기능은 무엇입니까?

이 문서에서 스트리밍 솔루션을 만들려면 다음 단계를 수행합니다.

1. 이벤트 스트림을 받을 Azure 이벤트 허브를 만듭니다.

2. 이벤트를 생성하고 Azure 이벤트 허브를 푸시하는 로컬 독립 실행형 응용 프로그램을 실행합니다. 이 작업을 수행하는 샘플 응용 프로그램은 [https://github.com/hdinsight/spark-streaming-data-persistence-examples](https://github.com/hdinsight/spark-streaming-data-persistence-examples)에 게시되어 있습니다.

3. Azure 이벤트 허브에서 스트리밍 이벤트를 읽고 다른 위치(Azure Blob, Hive 테이블 및 SQL 데이터베이스 테이블)로 푸시하는 Spark 클러스터에서 원격으로 스트리밍 응용 프로그램을 실행합니다. 

## <a name="create-azure-event-hub"></a>Azure 이벤트 허브 만들기

1. [Azure Portal](https://manage.windowsazure.com)에 로그온하고 화면 왼쪽 위에 있는 **새로 만들기**를 클릭합니다.

2. **사물 인터넷**을 클릭한 다음 **Event Hubs**를 클릭합니다.
   
    ![이벤트 허브 만들기](./media/hdinsight-apache-spark-eventhub-streaming/create-event-hub9.png)

3. **네임스페이스 만들기** 블레이드에서 네임스페이스 이름을 입력합니다. 가격 책정 계층(기본 또는 표준)을 선택합니다. 또한 리소스를 만들 Azure 구독, 리소스 그룹 및 위치를 선택합니다. **만들기** 를 클릭하여 네임스페이스를 만듭니다.
   
    ![이벤트 허브 만들기](./media/hdinsight-apache-spark-eventhub-streaming/create-event-hub1.png)

    > [!NOTE]
       > 대기 시간 및 비용을 줄이려면 HDInsight의 Apache Spark 클러스터와 동일한 **위치** 를 선택해야 합니다.
       > 
       > 

4. Event Hubs 네임스페이스 목록에서 새로 만든 네임스페이스를 클릭합니다.      
   
    
5. 네임스페이스 블레이드에서 **이벤트 허브**를 클릭하고 **+ 이벤트 허브**를 클릭하여 새 이벤트 허브를 만듭니다.
   
    ![이벤트 허브 만들기](./media/hdinsight-apache-spark-eventhub-streaming/create-event-hub3.png)

6. 이벤트 허브에 사용할 이름을 입력하고, 파티션 수를 10으로, 메시지 보존을 1로 설정합니다. 이 솔루션에서 메시지를 보관하지 않으므로 나머지는 기본 설정으로 둔 다음, **만들기**를 클릭합니다.
   
    ![이벤트 허브 만들기](./media/hdinsight-apache-spark-eventhub-streaming/create-event-hub5.png)

7. 새로 만든 이벤트 허브가 이벤트 허브 블레이드에 나열됩니다.
    
     ![](./media/hdinsight-apache-spark-eventhub-streaming/create-event-hub6.png)

8. 네임스페이스 블레이드(특정 Event Hub 블레이드가 아님)로 돌아와서 **공유 액세스 정책**을 클릭한 다음 **RootManageSharedAccessKey**를 클릭합니다.
    
     ![](./media/hdinsight-apache-spark-eventhub-streaming/create-event-hub7.png)

9. 복사 단추를 클릭하여 **RootManageSharedAccessKey** 기본 키 및 연결 문자열을 클립보드에 복사합니다. 이러한 항목은 자습서에서 나중에 사용할 수 있도록 저장합니다.
    
     ![](./media/hdinsight-apache-spark-eventhub-streaming/create-event-hub8.png)

## <a name="send-messages-to-an-azure-event-hub-using-a-scala-application"></a>Scala 응용 프로그램을 사용하여 Azure 이벤트 허브에 메시지 전송

이 섹션에서는 이벤트 스트림을 만들고 이전 단계에서 만든 Azure 이벤트 허브에 전송하는 독립 실행형 로컬 Scala 응용 프로그램을 사용합니다. 이 응용 프로그램은 [https://github.com/hdinsight/eventhubs-sample-event-producer](https://github.com/hdinsight/eventhubs-sample-event-producer)의 GitHub에서 사용할 수 있습니다. 여기의 단계는 이 GitHub 리포지토리를 이미 분기했다고 가정합니다.

1. 이 응용 프로그램을 실행하는 컴퓨터에 다음 항목이 설치되어 있는지 확인해야 합니다.

    * Oracle Java Development 키트. [여기](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)에서 설치할 수 있습니다.
    * Java IDE. 이 문서에서는 IntelliJ IDEA 15.0.1을 사용합니다. [여기](https://www.jetbrains.com/idea/download/)에서 설치할 수 있습니다.


2. IntelliJ IDEA에서 응용 프로그램, **EventhubsSampleEventProducer**를 엽니다.

3. 프로젝트를 빌드합니다. **빌드** 메뉴에서 **프로젝트 만들기**를 클릭합니다. IntelliJ IDEA 구성에 따라 출력 jar가 **\classes\artifacts** 아래에 만들어집니다.

    > [!TIP]
    > IntelliJ IDEA에서 사용할 수 있는 옵션을 사용하여 GitHub 리포지토리에서 프로젝트를 직접 만들 수도 있습니다. 해당 방법을 사용하는 방법을 이해하려면 다음 섹션의 지침을 사용합니다. 다음 섹션에 설명된 많은 단계는 이 단계에서 만든 Scala 응용 프로그램에 적용되지 않습니다. 예:
    > 
    > * Spark 버전을 포함하도록 POM을 업데이트할 필요가 없습니다. 이 응용 프로그램을 만들기 위한 Spark에 대한 종속성이 없기 때문입니다.
    > * 프로젝트 라이브러리에 몇 가지 종속성 jar을 추가할 필요가 없습니다. 그러한 jar은 이 프로젝트에 필요하지 않습니다.
    > 
    > 

## <a name="receive-messages-from-the-event-hub-using-a-streaming-application-running-on-spark-cluster"></a>Spark 클러스터에서 실행되는 스트리밍 응용 프로그램을 사용하여 이벤트 허브에서 메시지 받기

이벤트를 받고 이를 다른 대상으로 라우팅하는 샘플 Scala 응용 프로그램은 [https://github.com/hdinsight/spark-streaming-data-persistence-examples](https://github.com/hdinsight/spark-streaming-data-persistence-examples)에서 사용 가능합니다. 다음 단계를 수행하여 응용 프로그램을 업데이트하고 출력 jar을 만듭니다.

1. IntelliJ IDEA를 시작하고 시작 화면에서 **버전 제어에서 확인**을 선택한 다음 **Git**를 클릭합니다.
   
    ![Git에서 원본 가져오기](./media/hdinsight-apache-spark-eventhub-streaming/get-source-from-git.png)
2. **리포지토리 복제** 대화 상자에서 복제할 Git 리포지토리의 URL을 제공하고 복제할 디렉터리를 지정한 다음 **복제**를 클릭합니다.
   
    ![Git에서 복제](./media/hdinsight-apache-spark-eventhub-streaming/clone-from-git.png)
3. 프로젝트가 완전히 복제될 때까지 지시를 따릅니다. **Alt + 1** 키를 눌러 **프로젝트 보기**를 엽니다. 다음과 유사합니다.
   
    ![프로젝트 보기](./media/hdinsight-apache-spark-eventhub-streaming/project-view.png)
4. Java8로 컴파일된 응용 프로그램 코드가 있는지 확인합니다. 이를 확인하려면 **파일**, **프로젝트 구조**를 차례로 클릭한 다음 **프로젝트** 탭에서 프로젝트 언어 수준이 **8 람다, 형식 주석 등**으로 설정되어 있는지 확인합니다.
   
    ![프로젝트 구조](./media/hdinsight-apache-spark-eventhub-streaming/java-8-compiler.png)
5. **pom.xml** 을 열고 Spark 버전이 정확한지 확인합니다. `<properties>` 노드 아래에서 다음 코드 조각을 찾고 Spark 버전을 확인합니다.
   
        <scala.version>2.11.8</scala.version>
        <scala.compat.version>2.11.8</scala.compat.version>
        <scala.binary.version>2.11</scala.binary.version>
        <spark.version>2.0.0</spark.version>

6. 응용 프로그램에는 **JDBC 드라이버 jar**이라고도 하는 종속성 jar이 필요합니다. 이벤트 허브에서 받은 메시지를 Azure SQL 데이터베이스에 작성해야 합니다. 이 jar 파일의 v4.1 이상을 [여기](https://msdn.microsoft.com/sqlserver/aa937724.aspx)에서 다운로드할 수 있습니다. 프로젝트 라이브러리에 이러한 jar에 대한 참조를 추가합니다. 다음 단계를 수행합니다.
     
     1. 응용 프로그램이 열려 있는 IntelliJ IDEA 창에서 **파일**, **프로젝트 구조**, **라이브러리**를 차례로 클릭합니다. 
     2. 추가 아이콘(![추가 아이콘](./media/hdinsight-apache-spark-eventhub-streaming/add-icon.png))을 클릭하고 **Java**를 클릭한 다음 JDBC 드라이버 jar를 다운로드한 위치로 이동합니다. 지시에 따라 프로젝트 라이브러리에 jar 파일을 추가합니다.
        
         ![누락된 종속성 추가](./media/hdinsight-apache-spark-eventhub-streaming/add-missing-dependency-jars.png "누락된 종속성 jar 추가")
     3. **Apply**를 클릭합니다.
7. 출력 jar 파일을 만듭니다. 다음 단계를 수행합니다.
   
   1. **프로젝트 구조** 대화 상자에서 **아티팩트**를 클릭한 다음 +(더하기) 기호를 클릭합니다. 팝업 대화 상자에서 **JAR**, **종속성이 있는 모듈에서**를 차례로 클릭합니다.
      
       ![JAR 만들기](./media/hdinsight-apache-spark-eventhub-streaming/create-jar-1.png)
   2. **모듈에서 JAR 만들기** 대화 상자에서 **주 클래스**에 대한 ![ellipsis](./media/hdinsight-apache-spark-eventhub-streaming/ellipsis.png)(줄임표)를 클릭합니다.
   3. **주 클래스 선택** 대화 상자에서 사용 가능한 클래스를 선택한 다음 **확인**을 클릭합니다.
      
       ![JAR 만들기](./media/hdinsight-apache-spark-eventhub-streaming/create-jar-2.png)
   4. **모듈에서 JAR 만들** 대화 상자에서 **대상 JAR에 추출** 옵션이 선택되었는지 확인한 다음 **확인**을 클릭합니다. 이렇게 하면 모든 종속성이 있는 단일 JAR이 만들어집니다.
      
       ![JAR 만들기](./media/hdinsight-apache-spark-eventhub-streaming/create-jar-3.png)
   5. **출력 레이아웃** 탭에는 Maven 프로젝트의 일부분으로 포함된 jar이 모두 나열됩니다. 직접 종속성이 없는 Scala 응용 프로그램을 선택하고 삭제할 수 있습니다. 여기에서 만든 응용 프로그램의 경우 마지막을 제외한 모두를 제거할 수 있습니다(**microsoft-spark-streaming-examples compile output**). 삭제할 jar을 선택한 다음 ![delete icon](./media/hdinsight-apache-spark-eventhub-streaming/delete-icon.png)(**삭제**) 아이콘을 클릭합니다.
      
       ![JAR 만들기](./media/hdinsight-apache-spark-eventhub-streaming/delete-output-jars.png)
      
       프로젝트를 작성하거나 업데이트할 때마다 jar이 생성되는지 확인하는 **Build on make** 확인란이 선택되었는지 확인합니다. **Apply**를 클릭합니다.
   6. 이전에 프로젝트 라이브러리에 추가한 SQL JDBC jar가 **출력 레이아웃** 탭의 **사용 가능한 요소** 상자의 오른쪽 아래에 있습니다. **출력 레이아웃** 탭에 이를 추가해야 합니다. jar 파일을 마우스 오른쪽 단추로 클릭한 다음 **출력 루트로 추출**을 클릭합니다.
      
       ![종속성 jar 추출](./media/hdinsight-apache-spark-eventhub-streaming/extract-dependency-jar.png)  
      
       **출력 레이아웃** 탭은 이제 다음과 같아집니다.
      
       ![최종 출력 탭](./media/hdinsight-apache-spark-eventhub-streaming/final-output-tab.png)        
      
       **프로젝트 구조** 대화 상자에서 **적용**을 클릭한 다음 **확인**을 클릭합니다.    
   7. 메뉴 모음에서 **빌드**를 클릭한 다음 **프로젝트 만들기**를 클릭합니다. **빌드 아티팩트** 를 클릭하여 jar을 만들 수도 있습니다. **\classes\artifacts** 아래에 출력 jar이 만들어집니다.
      
       ![JAR 만들기](./media/hdinsight-apache-spark-eventhub-streaming/output.png)

## <a name="run-the-applications-remotely-on-a-spark-cluster-using-livy"></a>Livy를 사용하여 Spark 클러스터에서 원격으로 응용 프로그램 실행

Livy를 사용하여 Spark 클러스터에서 스트리밍 응용 프로그램을 원격으로 실행합니다. HDInsight Spark 클러스터와 함께 Livy를 사용하는 방법에 대한 자세한 내용은 [Azure HDInsight에서 Apache Spark 클러스터에 원격으로 작업 제출](hdinsight-apache-spark-livy-rest-interface.md)을 참조하세요. Spark를 사용하여 스트림 이벤트에 원격 작업 실행을 시작하기 전에 몇 가지 작업을 수행해야 합니다.

1. 로컬 독립 실행형 응용 프로그램을 시작하여 이벤트를 생성하고 이벤트 허브로 전송합니다. 이렇게 하려면 다음 명령을 사용합니다.
   
        java -cp com-microsoft-azure-eventhubs-client-example.jar com.microsoft.eventhubs.client.example.EventhubsClientDriver --eventhubs-namespace "mysbnamespace" --eventhubs-name "myeventhub" --policy-name "mysendpolicy" --policy-key "<policy key>" --message-length 32 --thread-count 32 --message-count -1

2. 스트리밍 jar(**spark-streaming-data-persistence-examples.jar**)을 클러스터와 연결된 Azure Blob Storage에 복사합니다. 이렇게 하면 jar이 Livy에 액세스할 수 있습니다. [**AzCopy**](../storage/storage-use-azcopy.md) 명령줄 유틸리티를 사용하면 이렇게 할 수 있습니다. 데이터를 업로드하는 데 사용할 수 있는 다른 클라이언트도 많이 있습니다. [HDInsight에서 Hadoop 작업용 데이터 업로드](hdinsight-upload-data.md)에서 자세한 정보를 찾을 수 있습니다.
3. 이러한 응용 프로그램을 실행 중인 컴퓨터에 CURL을 설치합니다. Livy 끝점을 호출하는 CURL을 사용하여 작업을 원격으로 실행합니다.

### <a name="run-the-applications-to-receive-the-events-into-an-azure-storage-blob-as-text"></a>텍스트로 Azure 저장소 Blob에 이벤트를 수신하는 응용 프로그램 실행

명령 프롬프트를 열고 CURL을 설치한 디렉터리로 이동하고 다음 명령을 실행합니다(사용자 이름/암호 및 클러스터 이름 바꾸기).

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputBlob.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

파일 **inputBlob.txt**에서 매개 변수는 다음과 같이 정의됩니다.

    { "file":"wasbs:///example/jars/spark-streaming-data-persistence-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsEventCount", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

입력 파일에서 매개 변수가 무엇인지 이해하도록 합니다.

* **파일**은 클러스터와 연결된 Azure 저장소 계정의 응용 프로그램 jar 파일에 대한 경로입니다.
* **className**은 jar의 클래스 이름입니다.
* **args**는 클래스에 필요한 인수 목록입니다.
* **numExecutors**는 스트리밍 응용 프로그램을 실행하기 위해 Spark에서 사용되는 코어 수입니다. 이는 항상 이벤트 허브 파티션 수의 두 배 이상이어야 합니다.
* **executorMemory**, **executorCores**, **driverMemory**는 스트리밍 응용 프로그램에 필요한 리소스를 할당하는 데 사용되는 매개 변수입니다.

> [!NOTE]
> 매개 변수로 사용되는 출력 폴더(EventCheckpoint, EventCount/EventCount10)를 만들 필요가 없습니다. 스트리밍 응용 프로그램은 이를 만듭니다.
> 
> 

명령을 실행하면 다음과 유사한 출력이 표시됩니다.

    < HTTP/1.1 201 Created
    < Content-Type: application/json; charset=UTF-8
    < Location: /18
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Tue, 01 Dec 2015 05:39:10 GMT
    < Content-Length: 37
    <
    {"id":1,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

출력의 마지막 줄에서 배치 ID를 기록해 둡니다(이 예에서 '1'). 응용 프로그램이 성공적으로 실행되는지 확인하려면 클러스터와 연결된 Azure 저장소 계정을 확인하고 거기서 만든 **/EventCount/EventCount10** 폴더가 표시되어야 합니다. 이 폴더는 매개 변수 **batch-interval-in-seconds**에 지정된 시간 간격 내에 처리되는 이벤트 수를 캡처하는 Blob을 포함해야 합니다.

응용 프로그램은 중지할 때까지 실행을 계속합니다. 이렇게 하려면 다음 명령을 사용합니다.

    curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/1"

### <a name="run-the-applications-to-receive-the-events-into-an-azure-storage-blob-as-json"></a>JSON으로 Azure 저장소 Blob에 이벤트를 수신하는 응용 프로그램 실행
명령 프롬프트를 열고 CURL을 설치한 디렉터리로 이동하고 다음 명령을 실행합니다(사용자 이름/암호 및 클러스터 이름 바꾸기).

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputJSON.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

파일 **inputJSON.txt** 에서 매개 변수는 다음과 같이 정의됩니다.

    { "file":"wasbs:///example/jars/spark-streaming-data-persistence-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToAzureBlobAsJSON", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--event-store-folder", "/EventStore10"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

매개 변수는 이전 단계에서 텍스트 출력에 지정한 것과 유사합니다. 마찬가지로 매개 변수로 사용되는 출력 폴더(EventCheckpoint, EventCount/EventCount10)를 만들 필요가 없습니다. 스트리밍 응용 프로그램은 이를 만듭니다.

 명령을 실행한 후 클러스터와 연결된 Azure 저장소 계정을 확인하고 거기서 만든 **/EventStore10** 폴더가 표시됩니다. **part-** 접두사가 있는 파일을 열고 JSON 형식으로 처리된 이벤트가 표시됩니다.

### <a name="run-the-applications-to-receive-the-events-into-a-hive-table"></a>Hive 테이블에 이벤트를 수신하는 응용 프로그램 실행
Hive 테이블에 이벤트를 스트리밍하는 응용 프로그램을 실행하려면 몇 가지 추가 구성 요소가 필요합니다. 다음과 같습니다.

* datanucleus-api-jdo-3.2.6.jar
* datanucleus-rdbms-3.2.9.jar
* datanucleus-core-3.2.10.jar
* hive-site.xml

**.jar** 파일은 `/usr/hdp/current/spark-client/lib`의 HDInsight Spark 클러스터에서 사용할 수 있습니다. **hive-site.xml**은 `/usr/hdp/current/spark-client/conf`에서 사용할 수 있습니다.

[WinScp](http://winscp.net/eng/download.php) 를 사용하여 해당 파일을 클러스터에서 로컬 컴퓨터에 복사할 수 있습니다. 그런 다음 도구를 사용하여 클러스터와 연결된 저장소 계정에 이러한 파일을 복사할 수 있습니다. 저장소 계정에 파일을 업로드하는 방법에 대한 자세한 내용은 [HDInsight에서 Hadoop 작업용 데이터 업로드](hdinsight-upload-data.md)를 참조하세요.

Azure 저장소 계정에 파일을 복사한 후 명령 프롬프트를 열고 CURL을 설치한 디렉터리로 이동하고 다음 명령을 실행합니다(사용자 이름/암호 및 클러스터 이름 바꾸기).

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputHive.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

파일 **inputHive.txt** 에서 매개 변수는 다음과 같이 정의됩니다.

    { "file":"wasbs:///example/jars/spark-streaming-data-persistence-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToHiveTable", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--event-hive-table", "EventHiveTable10" ], "jars":["wasbs:///example/jars/datanucleus-api-jdo-3.2.6.jar", "wasbs:///example/jars/datanucleus-rdbms-3.2.9.jar", "wasbs:///example/jars/datanucleus-core-3.2.10.jar"], "files":["wasbs:///example/jars/hive-site.xml"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

매개 변수는 이전 단계에서 텍스트 출력에 지정한 것과 유사합니다. 마찬가지로 매개 변수로 사용되는 출력 폴더(EventCheckpoint, EventCount/EventCount10) 또는 출력 Hive 테이블(EventHiveTable10)을 만들 필요가 없습니다. 스트리밍 응용 프로그램은 이를 만듭니다. **jars** 및 **files** 옵션은 저장소 계정에 복사한 .jar 파일 및 hive-site.xml에 대한 경로를 포함합니다.

hive 테이블이 성공적으로 만들어졌는지 확인하려면 클러스터로 SSH하고 Hive 쿼리를 실행할 수 있습니다. 자세한 내용은 [SSH를 사용하는 HDInsight에서 Hadoop과 Hive 사용](hdinsight-hadoop-use-hive-ssh.md)을 참조하세요. SSH를 사용하여 연결된 후 다음 명령을 실행하여 Hive 테이블, **EventHiveTable10**이 만들어졌는지 확인할 수 있습니다.

    show tables;

다음과 유사한 결과가 표시됩니다.

    OK
    eventhivetable10
    hivesampletable

또한 SELECT 쿼리를 실행하여 테이블의 내용을 볼 수 있습니다.

    SELECT * FROM eventhivetable10 LIMIT 10;

다음과 유사한 출력이 표시됩니다.

    ZN90apUSQODDTx7n6Toh6jDbuPngqT4c
    sor2M7xsFwmaRW8W8NDwMneFNMrOVkW1
    o2HcsU735ejSi2bGEcbUSB4btCFmI1lW
    TLuibq4rbj0T9st9eEzIWJwNGtMWYoYS
    HKCpPlWFWAJILwR69MAq863nCWYzDEw6
    Mvx0GQOPYvPR7ezBEpIHYKTKiEhYammQ
    85dRppSBSbZgThLr1s0GMgKqynDUqudr
    5LAWkNqorLj3ZN9a2mfWr9rZqeXKN4pF
    ulf9wSFNjD7BZXCyunozecov9QpEIYmJ
    vWzM3nvOja8DhYcwn0n5eTfOItZ966pa
    Time taken: 4.434 seconds, Fetched: 10 row(s)


### <a name="run-the-applications-to-receive-the-events-into-an-azure-sql-database-table"></a>Azure SQL 데이터베이스 테이블에 이벤트를 수신하는 응용 프로그램 실행
이 단계를 실행하기 전에 Azure SQL 데이터베이스를 만들었는지 확인합니다. 자세한 내용은 [몇 분 만에 SQL 데이터베이스 만들기](../sql-database/sql-database-get-started.md)를 참조하세요. 이 섹션을 완료하려면 매개 변수로 데이터베이스 이름, 데이터베이스 서버 이름 및 데이터베이스 관리자 자격 증명에 대한 값이 필요합니다. 그러나 데이터베이스 테이블을 만들 필요가 없습니다. 스트리밍 응용 프로그램은 이를 만듭니다.

명령 프롬프트를 열고 CURL을 설치한 디렉터리로 이동하고 다음 명령을 실행합니다.

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputSQL.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

파일 **inputSQL.txt** 에서 매개 변수는 다음과 같이 정의됩니다.

    { "file":"wasbs:///example/jars/spark-streaming-data-persistence-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToAzureSQLTable", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--sql-server-fqdn", "<database-server-name>.database.windows.net", "--sql-database-name", "mysparkdatabase", "--database-username", "sparkdbadmin", "--database-password", "<put-password-here>", "--event-sql-table", "EventContent" ], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

응용 프로그램이 성공적으로 실행되는지 확인하려면 SQL Server Management Studio를 사용하여 Azure SQL 데이터베이스에 연결할 수 있습니다. 작업을 수행하는 방법에 대한 자세한 내용은 [SQL Server Management Studio를 사용하여 SQL 데이터베이스에 연결](../sql-database/sql-database-connect-query-ssms.md)을 참조하세요. 데이터베이스에 연결한 후 스트리밍 응용 프로그램에서 만든 **EventContent** 테이블로 이동할 수 있습니다. 빠른 쿼리를 실행하여 테이블에서 데이터를 가져올 수 있습니다. 다음 쿼리를 실행합니다.

    SELECT * FROM EventCount

다음과 비슷한 결과가 나타나야 합니다.

    00046b0f-2552-4980-9c3f-8bba5647c8ee
    000b7530-12f9-4081-8e19-90acd26f9c0c
    000bc521-9c1b-4a42-ab08-dc1893b83f3b
    00123a2a-e00d-496a-9104-108920955718
    0017c68f-7a4e-452d-97ad-5cb1fe5ba81b
    001KsmqL2gfu5ZcuQuTqTxQvVyGCqPp9
    001vIZgOStka4DXtud0e3tX7XbfMnZrN
    00220586-3e1a-4d2d-a89b-05c5892e541a
    0029e309-9e54-4e1b-84be-cd04e6fce5ec
    003333cf-874f-4045-9da3-9f98c2b4ea49
    0043c07e-8d73-420a-9af7-1fcb94575356
    004a11a9-0c2c-4bc0-a7d5-2e0ebd947ab9


## <a name="seealso"></a>참고 항목
* [개요: Azure HDInsight에서 Apache Spark](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>시나리오
* [BI와 Spark: BI 도구와 함께 HDInsight에서 Spark를 사용하여 대화형 데이터 분석 수행](hdinsight-apache-spark-use-bi-tools.md)
* [기계 학습과 Spark: HVAC 데이터를 사용하여 건물 온도를 분석하는 데 HDInsight의 Spark 사용](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [기계 학습과 Spark: 음식 검사 결과를 예측하는 데 HDInsight의 Spark 사용](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [HDInsight의 Spark를 사용하여 웹 사이트 로그 분석](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>응용 프로그램 만들기 및 실행
* [Scala를 사용하여 독립 실행형 응용 프로그램 만들기](hdinsight-apache-spark-create-standalone-application.md)
* [Livy를 사용하여 Spark 클러스터에서 원격으로 작업 실행](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>도구 및 확장
* [IntelliJ IDEA용 HDInsight 도구 플러그 인을 사용하여 Spark Scala 응용 프로그램 만들기 및 제출](hdinsight-apache-spark-intellij-tool-plugin.md)
* [IntelliJ IDEA용 HDInsight 도구 플러그 인을 사용하여 Spark 응용 프로그램을 원격으로 디버그](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight에서 Spark 클러스터와 함께 Zeppelin Notebook 사용](hdinsight-apache-spark-use-zeppelin-notebook.md)
* [HDInsight의 Spark 클러스터에서 Jupyter Notebook에 사용할 수 있는 커널](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Jupyter 노트북에서 외부 패키지 사용](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [컴퓨터에 Jupyter를 설치하고 HDInsight Spark 클러스터에 연결](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>리소스 관리
* [Azure HDInsight에서 Apache Spark 클러스터에 대한 리소스 관리](hdinsight-apache-spark-resource-manager.md)
* [HDInsight의 Apache Spark 클러스터에서 실행되는 작업 추적 및 디버그](hdinsight-apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/ 

