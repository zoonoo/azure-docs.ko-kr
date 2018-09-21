---
title: Azure Data Lake Storage Gen2 미리 보기에서 HDFS CLI 사용
description: Data Lake Storage Gen2 미리 보기용 HDFS CLI 소개
services: storage
author: artemuwka
ms.service: storage
ms.topic: article
ms.date: 06/27/2018
ms.author: artek
ms.component: data-lake-storage-gen2
ms.openlocfilehash: c59331c772e140fccfefb89eef086a35837171e1
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/14/2018
ms.locfileid: "45576978"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>Data Lake Storage Gen2에서 HDFS CLI 사용

Azure Data Lake Storage Gen2 미리 보기를 사용하면 [HDFS(Hadoop 분산 파일 시스템)](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html)를 사용하는 것처럼 데이터를 관리하고 액세스할 수 있습니다. HDInsight 클러스터가 연결되어 있거나 Azure Databricks를 사용하는 Apache Spark 작업을 실행하여 Azure Data Lake Storage Gen2에 저장된 데이터에 대한 분석을 수행하는지에 관계없이 CLI(명령줄 인터페이스)를 사용하여 로드된 데이터를 검색 및 조작할 수 있습니다. 나머지 문서에서는 [Azure Storage 팀이 Azure Storage 탐색기 및 Azure Portal에 대한 지원을 추가하는 작업을 수행](https://azure.microsoft.com/roadmap/)하는 동안 제공되는 옵션에 대해 설명합니다.

## <a name="hdfs-cli-with-hdinsight"></a>HDInsight를 사용한 HDFS CLI

HDInsight는 컴퓨터 노드에 로컬로 연결된 분산 파일 시스템에 대한 액세스를 제공합니다. 이 파일 시스템은 Hadoop이 지원하는 HDFS 및 기타 파일 시스템과 직접 상호 작용하는 셸을 사용하여 액세스할 수 있습니다. 다음은 일반적으로 사용되는 명령 및 유용한 리소스 링크입니다.

>[!IMPORTANT]
>클러스터가 만들어지면 HDInsight 클러스터 청구가 시작되고 클러스터가 삭제되면 중지됩니다. 분 단위로 청구되므로 더 이상 사용하지 않으면 항상 클러스터를 삭제해야 합니다([클러스터를 삭제](../../hdinsight/hdinsight-delete-cluster.md)하는 방법 알아보기). 그러나 Azure Data Lake Storage Gen2에 저장된 데이터는 HDInsight 클러스터가 삭제된 후에도 유지됩니다.

파일 또는 디렉터리 목록을 가져오려면:

    hdfs dfs -ls <args>
디렉터리를 만들려면:

    hdfs dfs -mkdir [-p] <paths>
파일 또는 디렉터리를 삭제하려면:

    hdfs dfs -rm [-skipTrash] URI [URI ...]


이제 Linux에서 HDInsight Hadoop 클러스터를 예제로 사용해 보겠습니다. HDFS CLI를 사용하려면 먼저 [서비스에 대한 원격 액세스](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-information#remote-access-to-services)를 설정해야 합니다. [SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)를 선택하면 샘플 PowerShell 코드가 다음과 같이 표시합니다.
```PowerShell
#Connect to the cluster via SSH.
ssh sshuser@clustername-ssh.azurehdinsight.net
#Execute basic HDFS commands. Display the hierarchy.
hdfs dfs -ls /
#Create a sample directory.
hdfs dfs -mkdir /samplefolder
```

Azure Portal에 있는 HDInsight 클러스터 블레이드의 “SSH + 클러스터 로그인” 섹션에서 연결 문자열을 찾을 수 있습니다. SSH 자격 증명은 클러스터 생성 시 지정되었습니다.

HDFS CLI에 대한 자세한 내용은 [공식 문서](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) 및 [HDFS Permissions Guide](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)(HDFS 권한 가이드)를 참조하세요.

## <a name="hdfs-cli-with-azure-databricks"></a>Azure Databricks를 사용한 HDFS CLI

Databricks는 Databricks REST API 위에 빌드된 사용하기 쉬운 CLI를 제공합니다. 오픈 소스 프로젝트는 [GitHub](https://github.com/databricks/databricks-cli)에서 호스트됩니다. 다음은 일반적으로 사용되는 명령입니다.

파일 또는 디렉터리 목록을 가져오려면:

    dbfs ls [-l]
디렉터리를 만들려면:

    dbfs mkdirs
파일을 삭제하려면:

    dbfs rm [-r]

Databricks와 상호 작용하는 또 다른 방법은 Notebook입니다. Notebook에 기본 언어가 있는 동안 셀의 시작 부분에 언어 magic 명령 %language를 지정하여 언어를 혼합할 수 있습니다. 특히 %sh를 사용하면 이 문서의 앞쪽에 있는 HDInsight 예제처럼 Notebook에서 셸 코드를 실행할 수 있습니다.

파일 또는 디렉터리 목록을 가져오려면:

    %sh ls <args>
디렉터리를 만들려면:

    %sh mkdir [-p] <paths>
파일 또는 디렉터리를 삭제하려면:

    %sh rm [-skipTrash] URI [URI ...]

Azure Databricks에서 Spark 클러스터를 시작한 후 새 Notebook을 만듭니다. 샘플 Notebook 스크립트는 다음과 같이 표시됩니다.

    #Execute basic HDFS commands invoking the shell. Display the hierarchy.
    %sh ls /
    #Create a sample directory.
    %sh mkdir /samplefolder

Databricks CLI에 대한 자세한 내용은 [공식 문서](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html)를 참조하세요. Notebook에 대한 자세한 내용은 설명서의 [Notebook](https://docs.azuredatabricks.net/user-guide/notebooks/index.html) 섹션을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Data Lake Storage Gen2로 HDInsight 클러스터 만들기](./quickstart-create-connect-hdi-cluster.md)
- [Azure Databricks에서 Azure Data Lake Storage Gen2 지원 계정 사용](./quickstart-create-databricks-account.md) 