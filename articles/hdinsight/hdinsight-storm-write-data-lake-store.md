---
title: "Azure HDInsight에서 Apache Storm으로 Azure 데이터 레이크 저장소 사용"
description: "HDInsight의 Apache Storm 토폴로지에서 Azure 데이터 레이크 저장소로 데이터를 작성하는 방법을 알아봅니다. 이 문서 및 관련된 예제는 HdfsBolt 구성 요소가 데이터 레이크 저장소에 작성하는 데 사용될 수 있는 방법을 보여줍니다."
services: hdinsight
documentationcenter: na
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 1df98653-a6c8-4662-a8c6-5d288fc4f3a6
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/03/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: 376415d34592d18de00513ee9142512eb716e426
ms.lasthandoff: 03/04/2017


---
# <a name="use-azure-data-lake-store-with-apache-storm-with-hdinsight-java"></a>HDInsight에서 Apache Storm으로 Azure Data Lake Store 사용(Java)

Azure 데이터 레이크 저장소는 데이터에 대한 높은 처리량, 가용성, 내구성 및 안정성을 제공하는 HDFS와 호환 가능한 클라우드 저장소 서비스입니다. 이 문서에서는 Java 기반 Storm 토폴로지를 사용하여 Azure Data Lake Store에 데이터를 쓰는 방법을 알아봅니다. 이 문서에 나와 있는 단계는 Apache Storm의 일부로 제공되는 [HdfsBolt](http://storm.apache.org/releases/1.0.2/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) 구성 요소를 사용합니다.

> [!IMPORTANT]
> 이 문서에 사용된 예제 토폴로지는 HDInsight 클러스터의 Storm에 포함되는 구성 요소에 의존하며 다른 Apache Storm 클러스터와 함께 사용될 때 Azure 데이터 레이크 저장소와 함께 작동하도록 수정이 필요할 수 있습니다.

## <a name="how-to-work-with-azure-data-lake-store"></a>Azure Data Lake Store를 사용하는 방법

Data Lake Store는 HDFS 호환 파일 시스템으로 HDInsight에 나타나므로 Storm-HDFS Bolt를 사용하여 작성할 수 있습니다. HDInsight에서 Azure Data Lake로 작업할 경우 파일 스키마 `adl://`를 사용할 수 있습니다.

* Data Lake Storage가 클러스터의 기본 저장소인 경우 `adl:///`를 사용합니다. 이것은 Azure Data Lake에서 클러스터 저장소의 루트입니다. 이것은 Data Lake Storage 계정에서 /clusters/CLUSTERNAME의 경로로 변환될 수 있습니다.
* Data Lake Storage가 클러스터의 보조 저장소인 경우 `adl://DATALAKEACCOUNT.azuredatalakestore.net/`을 사용합니다. 이 URI는 데이터가 기록되는 Data Lake Storage 계정을 지정합니다. 데이터는 Data Lake Store의 루트부터 기록되기 시작합니다.

    > [!NOTE]
    > 또한 클러스터용 기본 저장소를 포함하는 Data Lake Store 계정에 데이터를 저장하려는 경우에도 이 URI 형식을 사용할 수 있습니다. 이 경우 HDInsight를 포함하는 디렉터리 경로 외부에 데이터를 저장할 수 있습니다.

다음 Java 코드는 `MYDATALAKE`라는 Data Lake Store 계정의 `/stormdata`라는 디렉터리에 Storm-HDFS Bolt를 사용하여 데이터를 작성하는 방법을 보여 줍니다.

```java
// 1. Create sync and rotation policies to control when data is synched
//    (written) to the file system and when to roll over into a new file.
SyncPolicy syncPolicy = new CountSyncPolicy(1000);
FileRotationPolicy rotationPolicy = new FileSizeRotationPolicy(0.5f, Units.KB);
// 2. Set the format. In this case, comma delimited
RecordFormat recordFormat = new DelimitedRecordFormat().withFieldDelimiter(",");
// 3. Set the directory name. In this case, '/stormdata/'
FileNameFormat fileNameFormat = new DefaultFileNameFormat().withPath("/stormdata/");
// 4. Create the bolt using the previously created settings,
//    and also tell it the base URL to your Data Lake Store.
// NOTE! Replace 'MYDATALAKE' below with the name of your data lake store.
HdfsBolt adlsBolt = new HdfsBolt()
    .withFsUrl("adl://MYDATALAKE.azuredatalakestore.net/")
        .withRecordFormat(recordFormat)
        .withFileNameFormat(fileNameFormat)
        .withRotationPolicy(rotationPolicy)
        .withSyncPolicy(syncPolicy);
// 4. Give it a name and wire it up to the bolt it accepts data
//    from. NOTE: The name used here is also used as part of the
//    file name for the files written to Data Lake Store.
builder.setBolt("ADLStoreBolt", adlsBolt, 1)
    .globalGrouping("finalcount");
```

다음 YAML은 Flux 프레임워크의 Storm-HDFS Bolt 사용 방법을 보여 줍니다.

```yaml
components:
  - id: "syncPolicy"
    className: "org.apache.storm.hdfs.bolt.sync.CountSyncPolicy"
    constructorArgs:
      - 1000
  - id: "rotationPolicy"
    className: "org.apache.storm.hdfs.bolt.rotation.FileSizeRotationPolicy"
    constructorArgs:
      - 5
      - KB

  - id: "fileNameFormat"
    className: "org.apache.storm.hdfs.bolt.format.DefaultFileNameFormat"
    configMethods:
      - name: "withPath"
        args: ["${hdfs.write.dir}"]
      - name: "withExtension"
        args: [".txt"]

  - id: "recordFormat"
    className: "org.apache.storm.hdfs.bolt.format.DelimitedRecordFormat"
    configMethods:
      - name: "withFieldDelimiter"
        args: ["|"]

  - id: "rotationAction"
    className: "org.apache.storm.hdfs.common.rotation.MoveFileAction"
    configMethods:
      - name: "toDestination"
        args: ["${hdfs.dest.dir}"]

# bolt definitions
bolts:
  - id: "hdfs-bolt"
    className: "org.apache.storm.hdfs.bolt.HdfsBolt"
    configMethods:
      - name: "withConfigKey"
        args: ["hdfs.config"]
      - name: "withFsUrl"
        args: ["${hdfs.url}"]
      - name: "withFileNameFormat"
        args: [ref: "fileNameFormat"]
      - name: "withRecordFormat"
        args: [ref: "recordFormat"]
      - name: "withRotationPolicy"
        args: [ref: "rotationPolicy"]
      - name: "withSyncPolicy"
        args: [ref: "syncPolicy"]
    parallelism: 1
```

> [!NOTE]
> 이 문서의 예제에서는 Flux 프레임워크를 사용합니다.

## <a name="prerequisites"></a>필수 조건

* [Java JDK 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) 이상 HDInsight 3.5를 사용하려면 Java 8이 필요합니다.

* [Maven 3.x](https://maven.apache.org/download.cgi)

* HDInsight 클러스터 버전 3.5의 Storm. HDInsight 클러스터에서 새 Storm을 만들려면 [Azure를 사용하는 Data Lake 저장소로 HDInsight 사용](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md) 문서의 단계를 따릅니다.

### <a name="configure-environment-variables"></a>환경 변수 구성

Java 및 JDK를 설치할 때 사용자의 개발 워크스테이션에 다음 환경 변수를 설정할 수 있습니다. 하지만 변수가 존재하며 시스템에 대한 올바른 값을 포함하는지 확인해야 합니다.

* **JAVA_HOME** - JRE(Java runtime environment)가 설치된 디렉터리를 가리켜야 합니다. 예를 들어 Unix 또는 Linux 배포에서는 `/usr/lib/jvm/java-8-oracle`과 유사한 값이어야 합니다. Windows에서는 `c:\Program Files (x86)\Java\jre1.8`과 유사한 값이어야 합니다.
* **PATH** - 다음 경로를 포함해야 합니다.
  
  * **JAVA\_HOME** 또는 그와 동등한 경로
  * **JAVA\_HOME\bin** 또는 그와 동등한 경로
  * Maven이 설치된 디렉터리

## <a name="topology-implementation"></a>토폴로지 구현

이 문서에 사용된 예제는 Java로 작성되었으며 다음과 같은 구성 요소를 사용합니다.

* **TickSpout**: 토폴로지의 다른 구성 요소에서 사용하는 데이터를 생성합니다.
* **PartialCount**: TickSpout에 의해 생성된 이벤트를 계산합니다.
* **FinalCount**: PartialCount에서 개수 데이터를 집계합니다.
* **ADLStoreBolt**: [HdfsBolt](http://storm.apache.org/releases/1.0.2/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) 구성 요소를 사용하여 Azure Data Lake 저장소에 데이터를 작성합니다.

이 토폴로지를 포함하는 프로젝트는 [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store)에서 다운로드로 제공됩니다.

## <a name="build-and-package-the-topology"></a>토폴로지 빌드 및 패키지

1. [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store ](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) 에서 개발 환경에 예제 프로젝트를 다운로드합니다.

2. 편집기에서 `StormToDataLake\src\main\java\com\microsoft\example\StormToDataLakeStore.java` 파일을 열고 `.withFsUrl("adl://MYDATALAKE.azuredatalakestore.net/")`을 포함하는 줄을 찾습니다. **MYDATALAKE** 를 HDInsight 서버를 만들 때 사용한 Azure Data Lake 저장소의 이름으로 변경합니다.

3. 명령 프롬프트, 터미널 또는 셸 세션에서 다운로드한 프로젝트의 루트에 디렉터리를 변경하고 다음 명령을 실행하여 토폴로지를 빌드하고 패키지합니다.
   
        mvn compile
        mvn package
   
    빌드 및 패키징이 완료되면 `target`이라는 새 디렉터리가 있고 이는 `StormToDataLakeStore-1.0-SNAPSHOT.jar`라고 명명된 파일을 포함합니다. 컴파일된 토폴로지를 포함합니다.

## <a name="deploy-and-run-the-topology"></a>토폴로지 배포 및 실행

1. 다음 명령을 사용하여 HDInsight 클러스터에 토폴로지를 복사합니다. **USER** 를 클러스터를 만들 때 사용한 SSH 사용자 이름으로 대체합니다. **CLUSTERNAME**은 클러스터 이름으로 바꿉니다.
   
        scp target\StormToDataLakeStore-1.0-SNAPSHOT.jar USER@CLUSTERNAME-ssh.azurehdinsight.net:StormToDataLakeStore-1.0-SNAPSHOT.jar
   
    메시지가 표시되면 클러스터에 대한 SSH 사용자를 만들 때 사용한 암호를 입력합니다. 암호 대신 공용 키를 사용하는 경우 `-i` 매개 변수를 사용하여 개인 키와 일치하는 경로를 지정합니다.
   
   > [!NOTE]
   > 개발을 위해 Windows 클라이언트를 사용하는 경우 `scp` 명령이 없을 수 있습니다. 그렇다면 [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)에서 다운로드할 수 있는 `pscp`를 사용할 수 있습니다.

2. 업로드가 완료되면 SSH를 사용하여 HDInsight 클러스터에 연결하도록 다음을 사용합니다. **USER** 를 클러스터를 만들 때 사용한 SSH 사용자 이름으로 대체합니다. **CLUSTERNAME**은 클러스터 이름으로 바꿉니다.
   
        ssh USER@CLUSTERNAME-ssh.azurehdinsight.net
   
    메시지가 표시되면 클러스터에 대한 SSH 사용자를 만들 때 사용한 암호를 입력합니다. 암호 대신 공용 키를 사용하는 경우 `-i` 매개 변수를 사용하여 개인 키와 일치하는 경로를 지정합니다.
   
   > [!NOTE]
   > 개발을 위해 Windows 클라이언트를 사용하는 경우 PuTTY 클라이언트를 사용하여 클러스터에 연결하는 정보는 [Windows에서 SSH로 Linux 기반 HDInsight에 연결](hdinsight-hadoop-linux-use-ssh-windows.md) 에 있는 내용을 수행합니다.

3. 연결되면 다음 명령을 사용하여 `dev.properties`라는 파일을 만듭니다.

        nano dev.properties

4. `dev.properties` 파일의 내용으로 다음 텍스트를 사용합니다.

        hdfs.write.dir: /stormdata
        hdfs.url: adl:///
    
    파일을 저장하려면 __Ctrl + X__를 사용한 다음 __Y__를 입력하고 마지막으로 __Enter__ 키를 누릅니다. 이 파일의 값은 데이터가 기록되는 Data Lake store URL 및 디렉터리 이름을 설정합니다.

3. 다음 명령을 사용하여 토폴로지를 시작합니다.
   
        storm jar StormToDataLakeStore-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /datalakewriter.yaml --filter dev.properties

    이 명령은 Flux 프레임워크를 사용하여 토폴로지를 시작합니다. 토폴로지를 jar에 포함된 `datalakewriter.yaml` 파일에 의해 정의됩니다. `dev.properties` 파일은 필터로 전달되고 파일에 포함된 값은 토폴로지에서 읽힙니다.

## <a name="view-output-data"></a>출력 데이터 보기

데이터를 보려면 다음 명령을 사용합니다.

    hdfs dfs -ls /stormdata/

그러면 토폴로지에 의해 생성된 파일 목록이 표시됩니다.

Data Lake Store가 클러스터의 기본 저장소가 아닌 경우 음 명령을 사용하여 데이터를 확인합니다.

    hdfs dfs -ls adl://MYDATALAKE.azuredatalakestore.net/stormdata/

이전 명령에서 __MYDATALAKE__를 Data Lake Store 계정으로 바꿉니다.

다음 목록은 이전 명령에서 반환된 데이터 예제입니다.

    Found 30 items
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-0-1488568403092.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-1-1488568404567.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-10-1488568408678.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-11-1488568411636.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-12-1488568411884.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-13-1488568412603.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-14-1488568415055.txt

## <a name="stop-the-topology"></a>토폴로지 중지

Storm 토폴로지가 중지될 때까지 실행하거나 클러스터가 삭제됩니다. 토폴로지를 중지하려면 다음 정보를 사용합니다.

**Linux 기반 HDInsight의 경우**:

SSH 세션에서 클러스터로 다음 명령을 사용합니다.

    storm kill datalakewriter

## <a name="delete-your-cluster"></a>클러스터 삭제

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>다음 단계

이제 Storm을 사용하여 Azure Data Lake 저장소에 작성하는 방법을 알아보았으므로 다른 [HDInsight에 대한 Storm 예제](hdinsight-storm-example-topology.md)를 검색합니다.


