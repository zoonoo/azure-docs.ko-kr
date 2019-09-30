---
title: 자습서 - Apache Storm에서 Storage/Data Lake Storage에 쓰기 - Azure HDInsight
description: 자습서 - Apache Storm을 사용하여 Azure HDInsight용 HDFS 호환 스토리지에 쓰는 방법을 알아봅니다.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/24/2019
ms.openlocfilehash: b6114a764d0834b7bcfe4b95d34fae6a03a8a40e
ms.sourcegitcommit: a19bee057c57cd2c2cd23126ac862bd8f89f50f5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71181028"
---
# <a name="tutorial-write-to-apache-hadoop-hdfs-from-apache-storm-on-azure-hdinsight"></a>자습서: Azure HDInsight의 Apache Storm에서 Apache Hadoop HDFS에 쓰기

이 자습서에서는 Apache Storm을 사용하여 HDInsight의 Apache Storm에서 사용하는 HDFS 호환 스토리지에 데이터를 쓰는 방법에 대해 알아봅니다. HDInsight는 Azure Storage 및 Azure Data Lake Storage를 모두 HDFS 호환 스토리지로 사용할 수 있습니다. Storm은 HDFS에 데이터를 쓰는 [HdfsBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) 구성 요소를 제공합니다. 이 문서는 HdfsBolt에서 두 가지 유형의 스토리지에 쓰는 방법에 대한 정보를 제공합니다.

이 문서에서 사용되는 예제 토폴로지는 HDInsight의 Storm에 포함된 구성 요소를 사용합니다. 다른 Apache Storm 클러스터와 함께 Azure Data Lake Storage를 사용하려면 수정해야 할 수도 있습니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 스크립트 작업으로 클러스터 구성
> * 토폴로지 빌드 및 패키지
> * 토폴로지 배포 및 실행
> * 출력 데이터 보기
> * 토폴로지 중지

## <a name="prerequisites"></a>필수 조건

* [JDK(Java Developer Kit) 버전 8](https://aka.ms/azure-jdks)

* Apache에 따라 올바르게 [설치된](https://maven.apache.org/install.html) [Apache Maven](https://maven.apache.org/download.cgi)  Maven은 Java 프로젝트용 프로젝트 빌드 시스템입니다.

* SSH 클라이언트. 자세한 내용은 [SSH를 사용하여 HDInsight(Apache Hadoop)에 연결](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

* 클러스터 기본 스토리지에 대한 [URI 체계](../hdinsight-hadoop-linux-information.md#URI-and-scheme)입니다. Azure Storage는 `wasb://`, Azure Data Lake Storage Gen2는 `abfs://`, Azure Data Lake Storage Gen1은 `adl://`입니다. Azure Storage에 대해 보안 전송이 활성화된 경우 URI는 `wasbs://`입니다.  [보안 전송](../../storage/common/storage-require-secure-transfer.md)도 참조하세요.

### <a name="example-configuration"></a>예제 구성

다음 YAML은 예제에 포함된 `resources/writetohdfs.yaml` 파일에서 인용한 부분입니다. 이 파일에서는 Apache Storm의 [Flux](https://storm.apache.org/releases/current/flux.html) 프레임워크를 사용하여 Storm 토폴로지를 정의합니다.

```yaml
components:
  - id: "syncPolicy"
    className: "org.apache.storm.hdfs.bolt.sync.CountSyncPolicy"
    constructorArgs:
      - 1000

  # Rotate files when they hit 5 MB
  - id: "rotationPolicy"
    className: "org.apache.storm.hdfs.bolt.rotation.FileSizeRotationPolicy"
    constructorArgs:
      - 5
      - "MB"

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

# spout definitions
spouts:
  - id: "tick-spout"
    className: "com.microsoft.example.TickSpout"
    parallelism: 1


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
```

이 YAML에서 정의하는 항목은 다음과 같습니다.

* `syncPolicy`: 파일 시스템에 파일이 동기화/플러시되는 시기를 정의합니다. 이 예제에서는 1,000개 튜플마다 동기화/플러시되도록 정의했습니다.
* `fileNameFormat`: 파일을 쓸 때 사용할 경로 및 파일 이름 패턴을 정의합니다. 이 예제에서 경로는 필터를 사용하여 런타임에 제공되며 파일 확장명은 `.txt`입니다.
* `recordFormat`: 기록된 파일의 내부 형식을 정의합니다. 이 예제에서 필드는 `|` 문자로 구분되었습니다.
* `rotationPolicy`: 파일을 회전하는 시기를 정의합니다. 이 예제에서는 회전이 수행되지 않습니다.
* `hdfs-bolt`: 이전 구성 요소를 `HdfsBolt` 클래스의 구성 매개 변수로 사용합니다.

Flux 프레임워크에 대한 자세한 내용은 [https://storm.apache.org/releases/current/flux.html](https://storm.apache.org/releases/current/flux.html)을 참조하세요.

## <a name="configure-the-cluster"></a>클러스터 구성

기본적으로 HDInsight의 Storm에는 `HdfsBolt`에서 Storm의 클래스 경로에 있는 Azure Storage 또는 Data Lake Storage와 통신하는 데 사용하는 구성 요소가 포함되지 않습니다. 다음 스크립트 동작을 사용하여 클러스터의 Storm에 대한 `extlib` 디렉터리에 이러한 구성 요소를 추가합니다.

| 자산 | 값 |
|---|---|
|스크립트 유형 |- 사용자 지정|
|Bash 스크립트 URI |`https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh`|
|노드 유형 |Nimbus, Supervisor|
|매개 변수 |없음|

HDInsight에서 이 스크립트를 사용하는 방법에 대한 자세한 내용은 [스크립트 작업을 사용하여 Linux 기반 HDInsight 클러스터 사용자 지정](./../hdinsight-hadoop-customize-cluster-linux.md) 문서를 참조하세요.

## <a name="build-and-package-the-topology"></a>토폴로지 빌드 및 패키지

1. [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store)에서 예제 프로젝트를 개발 환경에 다운로드합니다.

2. 명령 프롬프트, 터미널 또는 셸 세션에서 다운로드한 디렉터리를 변경합니다. 토폴로지를 빌드하고 패키지하려면 다음 명령을 사용합니다.

    ```cmd
    mvn compile package
    ```

    빌드 및 패키징이 완료되면 `StormToHdfs-1.0-SNAPSHOT.jar`이라는 파일이 포함된 `target`이라는 새 디렉터리가 있습니다. 이 파일에는 컴파일된 토폴로지가 포함되어 있습니다.

## <a name="deploy-and-run-the-topology"></a>토폴로지 배포 및 실행

1. 다음 명령을 사용하여 HDInsight 클러스터에 토폴로지를 복사합니다. `CLUSTERNAME`을 클러스터의 이름으로 바꿉니다.

    ```cmd
    scp target\StormToHdfs-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:StormToHdfs-1.0-SNAPSHOT.jar
    ```

1. 업로드가 완료되면 SSH를 사용하여 HDInsight 클러스터에 연결하도록 다음을 사용합니다. `CLUSTERNAME`을 클러스터의 이름으로 바꿉니다.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 연결되면 다음 명령을 사용하여 `dev.properties`라는 파일을 만듭니다.

    ```bash
    nano dev.properties
    ```

1. 다음 텍스트를 `dev.properties` 파일의 내용으로 사용합니다. [URI 체계](../hdinsight-hadoop-linux-information.md#URI-and-scheme)를 기반으로 필요에 따라 수정합니다.

    ```
    hdfs.write.dir: /stormdata/
    hdfs.url: wasbs:///
    ```

    파일을 저장하려면 __Ctrl + X__ 를 사용한 다음 __Y__ 를 입력하고 마지막으로 __Enter__ 키를 누릅니다. 이 파일의 값은 데이터가 기록되는 스토리지 URL 및 디렉터리 이름을 설정합니다.

1. 다음 명령을 사용하여 토폴로지를 시작합니다.

    ```bash
    storm jar StormToHdfs-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writetohdfs.yaml --filter dev.properties
    ```

    이 명령은 클러스터의 Nimbus 노드에 제출하여 Flux 프레임워크를 사용하는 토폴로지를 시작합니다. 토폴로지를 jar에 포함된 `writetohdfs.yaml` 파일에 의해 정의됩니다. `dev.properties` 파일은 필터로 전달되고 파일에 포함된 값은 토폴로지에서 읽힙니다.

## <a name="view-output-data"></a>출력 데이터 보기

데이터를 보려면 다음 명령을 사용합니다.

  ```bash
  hdfs dfs -ls /stormdata/
  ```

이 토폴로지로 만든 파일 목록이 표시됩니다. 다음 목록은 이전 명령에서 반환된 데이터 예제입니다.

```output
Found 23 items
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-0-1561407909895.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-1-1561407915577.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-10-1561407943327.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-11-1561407946312.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-12-1561407949320.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-13-1561407952662.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-14-1561407955502.txt
```

## <a name="stop-the-topology"></a>토폴로지 중지

Storm 토폴로지가 중지될 때까지 실행되거나 클러스터가 삭제됩니다. 토폴로지를 중지하려면 다음 명령을 사용합니다.

```bash
storm kill hdfswriter
```

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에서 만든 리소스를 정리하려면 리소스 그룹을 삭제합니다. 리소스 그룹을 삭제하면 연결된 HDInsight 클러스터 및 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

Azure Portal을 사용하여 리소스 그룹을 제거하려면:

1. Azure Portal에서 왼쪽의 메뉴를 확장하여 서비스 메뉴를 연 다음 __리소스 그룹__ 을 선택하여 리소스 그룹 목록을 표시합니다.
2. 삭제할 리소스 그룹을 찾은 다음 목록 오른쪽에 있는 __자세히__ 단추(...)를 마우스 오른쪽 단추로 클릭합니다.
3. __리소스 그룹 삭제__ 를 선택한 다음 확인합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에는 Apache Storm을 사용하여 HDInsight의 Apache Storm에서 사용하는 HDFS 호환 스토리지에 데이터를 쓰는 방법에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [HDInsight에 대한 다른 Apache Storm 예](apache-storm-example-topology.md) 검색