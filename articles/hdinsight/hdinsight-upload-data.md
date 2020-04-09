---
title: HDInsight에서 Apache Hadoop 작업용 데이터 업로드
description: HDInsight에서 아파치 하두롭 작업에 대한 데이터를 업로드하고 액세스하는 방법을 알아봅니다. Azure 클래식 CLI, Azure 저장소 탐색기, Azure PowerShell, Hadoop 명령줄 또는 Sqoop을 사용합니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdiseo17may2017
ms.date: 04/07/2020
ms.openlocfilehash: c862633245e75613f9e4f9956486f872b96239f8
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875013"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>HDInsight에서 Apache Hadoop 작업용 데이터 업로드

HDInsight는 Azure 저장소 및 Azure 데이터 레이크 스토리지를 통해 하두롭 분산 파일 시스템(HDFS)을 제공합니다. 이 스토리지에는 Gen1 및 Gen2가 포함됩니다. Azure 저장소 및 데이터 레이크 저장소 Gen1 및 Gen2는 HDFS 확장으로 설계되었습니다. 이를 통해 Hadoop 환경의 전체 구성 요소 집합이 관리하는 데이터에서 직접 작동할 수 있습니다. Azure 저장소, 데이터 레이크 저장소 Gen1 및 Gen2는 별개의 파일 시스템입니다. 시스템은 해당 데이터에 대한 데이터 및 계산 저장에 최적화되어 있습니다. Azure 저장소 사용의 이점에 대한 자세한 내용은 [HDInsight를 사용하여 Azure 저장소 사용을](hdinsight-hadoop-use-blob-storage.md)참조하십시오. 또한 [HDInsight와 함께 데이터 레이크 스토리지 Gen1을 사용하고 HDInsight와](hdinsight-hadoop-use-data-lake-store.md) [함께 데이터 레이크 스토리지 Gen2를 사용합니다.](hdinsight-hadoop-use-data-lake-storage-gen2.md)

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 다음 요구 사항을 확인하세요.

* Azure HDInsight 클러스터를 만듭니다. 자세한 내용은 [Azure HDInsight 시작](hadoop/apache-hadoop-linux-tutorial-get-started.md) 또는 [HDInsight 클러스터 만들기](hdinsight-hadoop-provision-linux-clusters.md)를 참조하세요.
* 다음 문서의 정보:
    * [HDInsight와 함께 Azure 스토리지 사용](hdinsight-hadoop-use-blob-storage.md)
    * [HDInsight에서 Data Lake Store Gen1 사용](hdinsight-hadoop-use-data-lake-store.md)
    * [HDInsight에서 Data Lake Store Gen2 사용](hdinsight-hadoop-use-data-lake-storage-gen2.md)  

## <a name="upload-data-to-azure-storage"></a>Azure Storage에 데이터 업로드

## <a name="utilities"></a>공공 시설

Microsoft는 Azure Storage에서 작업할 다음 유틸리티를 제공합니다.

| 도구 | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure portal](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [아즈카피](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |
| [Hadoop 명령](#hadoop-command-line) |✔ |✔ |✔ |

> [!NOTE]  
> Hadoop 명령은 HDInsight 클러스터에서만 사용할 수 있습니다. 이 명령을 통해 로컬 파일 시스템의 데이터를 Azure Storage로 로드할 수만 있습니다.  

## <a name="hadoop-command-line"></a>하두프 커맨드 라인

데이터가 클러스터 헤드 노드에 존재하는 경우 Hadoop 명령줄은 Azure Storage Blob에 데이터를 저장하는데만 유용합니다.

Hadoop 명령을 사용하려면 먼저 [SSH 또는 PuTTY를](hdinsight-hadoop-linux-use-ssh-unix.md)사용하여 헤드 노드에 연결해야 합니다.

연결된 후에 스토리지에 파일을 업로드하려면 다음 구문을 사용할 수 있습니다.

```bash
hadoop fs -copyFromLocal <localFilePath> <storageFilePath>
```

예를 들어 `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

HDInsight의 기본 파일 시스템은 Azure 저장소에 있기 때문에 /예제/데이터/data.txt는 실제로 Azure 저장소에 있습니다. 파일이 다음과 같을 수도 있습니다.

    wasbs:///example/data/data.txt

또는

    wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

파일에서 작동하는 다른 Hadoop 명령 목록은[https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]  
> Apache HBase 클러스터에서 데이터 쓰기 시 사용되는 기본 블록 크기는 256KB입니다. HBase API 또는 REST API를 사용할 때는 잘 작동하는 반면 `hadoop` 또는 `hdfs dfs` 명령을 사용하여 12GB를 초과하는 데이터를 기록하면 오류가 발생합니다. 자세한 내용은 이 문서의 [Blob에서 쓰기를 위한 스토리지 예외](#storage-exception-for-write-on-blob) 섹션을 참조하세요.

## <a name="graphical-clients"></a>그래픽 클라이언트

Azure Storage를 사용하기 위한 그래픽 인터페이스를 제공하는 몇몇 애플리케이션이 있습니다. 다음 테이블은 이러한 애플리케이션 중 일부의 목록입니다.

| 클라이언트 | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [HDInsight 용 Microsoft Visual Studio Tools](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |✔ |✔ |✔ |
| [Azure Storage Explorer](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) |✔ |✔ |✔ |
| [`Cerulea`](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |✔ |
| [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [Microsoft Azure용 CloudBerry Explorer](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |

## <a name="mount-azure-storage-as-local-drive"></a>Azure Storage를 로컬 드라이브로 탑재

[Azure Storage를 로컬 드라이브로 탑재](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx)를 참조하세요.

## <a name="upload-using-services"></a>서비스를 사용하여 업로드

### <a name="azure-data-factory"></a>Azure 데이터 팩터리

Azure Data Factory 서비스는 데이터를 구성하기 위한 완전히 관리되는 서비스입니다: 스토리지, 처리 및 이동 서비스를 간소화되고 적응가능하며 신뢰할 수 있는 데이터 프로덕션 파이프라인으로 이동합니다.

|스토리지 유형|문서화|
|----|----|
|Azure Blob 스토리지|[Azure 데이터 팩터리를 사용하여 Azure Blob 저장소에 데이터를 복사합니다.](../data-factory/connector-azure-blob-storage.md)|
|Azure Data Lake Storage Gen1|[Azure Data Factory를 사용하여 Azure Data Lake Storage Gen1 간에 데이터 복사](../data-factory/connector-azure-data-lake-store.md)|
|Azure Data Lake Storage Gen2 |[Azure Data Factory를 사용하여 Azure Data Lake Storage Gen2에 데이터 로드](../data-factory/load-azure-data-lake-storage-gen2.md)|

### <a name="apache-sqoop"></a>Apache Sqoop

Sqoop은 Hadoop과 관계형 데이터베이스 간 데이터 전송을 위해 설계된 도구입니다. SQL Server, MySQL 또는 Oracle과 같은 관계형 데이터베이스 관리 시스템(RDBMS)에서 데이터를 가져오는 데 사용합니다. 그런 다음 하눕 분산 파일 시스템 (HDFS)에. MapReduce 또는 하이브로 Hadoop의 데이터를 변환한 다음 데이터를 RDBMS로 다시 내보냅니다.

자세한 내용은 [HDInsight에서 Sqoop 사용](hadoop/hdinsight-use-sqoop.md)을 참조하세요.

### <a name="development-sdks"></a>개발 SDK

Azure Storage는 다음 프로그래밍 언어에서 Azure SDK를 사용하여 액세스할 수 있습니다.

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Azure SDK 설치에 대한 자세한 내용은 [Azure 다운로드](https://azure.microsoft.com/downloads/)

## <a name="troubleshooting"></a>문제 해결

### <a name="storage-exception-for-write-on-blob"></a>Blob에서 쓰기를 위한 스토리지 예외

**증상**: `hadoop` HBase `hdfs dfs` 클러스터에서 ~ 12GB 이상인 파일을 작성하거나 명령을 사용하는 경우 다음과 같은 오류가 발생할 수 있습니다.

    ERROR azure.NativeAzureFileSystem: Encountered Storage Exception for write on Blob : example/test_large_file.bin._COPYING_ Exception details: null Error Code : RequestBodyTooLarge
    copyFromLocal: java.io.IOException
            at com.microsoft.azure.storage.core.Utility.initIOException(Utility.java:661)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:366)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:350)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
            at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
            at java.lang.Thread.run(Thread.java:745)
    Caused by: com.microsoft.azure.storage.StorageException: The request body is too large and exceeds the maximum permissible limit.
            at com.microsoft.azure.storage.StorageException.translateException(StorageException.java:89)
            at com.microsoft.azure.storage.core.StorageRequest.materializeException(StorageRequest.java:307)
            at com.microsoft.azure.storage.core.ExecutionEngine.executeWithRetry(ExecutionEngine.java:182)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlockInternal(CloudBlockBlob.java:816)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlock(CloudBlockBlob.java:788)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:354)
            ... 7 more

**원인**: HDInsight 클러스터의 HBase는 Azure 저장소에 쓸 때 256KB의 블록 크기로 기본설정됩니다. HBase API 또는 REST API를 사용할 때는 잘 작동하는 반면 `hadoop` 또는 `hdfs dfs` 명령줄 유틸리티를 사용할 때 오류가 발생합니다.

**해결 방법**: `fs.azure.write.request.size`를 사용하여 블록 크기를 더 크기 지정합니다. 매개 변수를 사용하여 사용별로 이 수정을 `-D` 수행할 수 있습니다. `hadoop` 명령에서 이 매개 변수를 사용하는 예는 다음 명령과 같습니다.

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

Apache Ambari를 사용하여 `fs.azure.write.request.size` 값을 전역적으로 늘릴 수도 있습니다. 다음 단계에 따라 Ambari 웹 UI 값을 변경합니다.

1. 브라우저에서 클러스터에 대한 Ambari 웹 UI로 이동합니다. URL은 `https://CLUSTERNAME.azurehdinsight.net`클러스터의 `CLUSTERNAME` 이름입니다. 메시지가 표시되면 클러스터의 관리자 이름 및 암호를 입력합니다.
2. 화면 왼쪽에서 **HDFS**를 선택한 다음 **구성** 탭을 선택합니다.
3. **필터...** 필드에 `fs.azure.write.request.size`를 입력합니다.
4. 값을 262144(256KB)에서 새 값으로 변경합니다. 예를 들어 4194304(4MB)로 변경합니다.

    ![Ambari 웹 UI를 통해 값을 변경하는 이미지](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

Ambari 사용에 대한 자세한 내용은 [Apache Ambari 웹 UI를 사용하여 HDInsight 클러스터 관리](hdinsight-hadoop-manage-ambari.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

이제 HDInsight로 데이터를 얻는 방법을 이해하게 되었으므로 다음 문서를 읽고 분석을 알아보십시오.

* [Azure HDInsight 시작](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [프로그래밍 방식으로 Apache Hadoop 작업 제출](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [HDInsight에서 Apache Hive 사용](hadoop/hdinsight-use-hive.md)
* [HDInsight에서 Apache Pig 사용](./use-pig.md)
