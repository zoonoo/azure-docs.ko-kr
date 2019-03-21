---
title: Azure Data Lake Storage Gen2에서 HDFS CLI 사용
description: Data Lake Storage Gen2용 HDFS CLI 소개
services: storage
author: artemuwka
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: artek
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: c77981574ff2e507af7012f26f742dda62d952fc
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57991960"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>Data Lake Storage Gen2에서 HDFS CLI 사용

Azure Data Lake Storage Gen2를 사용하면 [HDFS(Hadoop 분산 파일 시스템)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html)에서와 마찬가지로 데이터를 관리하고 액세스할 수 있습니다. HDInsight 클러스터가 연결되어 있든, Azure Databricks를 사용하는 Apache Spark 작업을 실행하여 Azure Storage 계정에 저장된 데이터를 분석하든, CLI(명령줄 인터페이스)를 사용하여 로드된 데이터를 검색 및 조작할 수 있습니다.

## <a name="hdfs-cli-with-hdinsight"></a>HDInsight를 사용한 HDFS CLI

HDInsight는 컴퓨터 노드에 로컬로 연결된 분산 파일 시스템에 대한 액세스를 제공합니다. 이 파일 시스템은 Hadoop이 지원하는 HDFS 및 기타 파일 시스템과 직접 상호 작용하는 셸을 사용하여 액세스할 수 있습니다. 다음은 일반적으로 사용되는 명령 및 유용한 리소스 링크입니다.

>[!IMPORTANT]
>클러스터가 만들어지면 HDInsight 클러스터 요금 청구가 시작되고 클러스터가 삭제되면 요금 청구가 중지됩니다. 분 단위로 청구되므로 더 이상 사용하지 않으면 항상 클러스터를 삭제해야 합니다. 클러스터를 삭제하는 방법은 [토픽에 대한 문서](../../hdinsight/hdinsight-delete-cluster.md)를 참조하세요. 그러나 Data Lake Storage Gen2가 사용되는 스토리지 계정에 저장된 데이터는 HDInsight 클러스터가 삭제된 후에도 유지됩니다.

### <a name="create-a-file-system"></a>파일 시스템 만들기

    hdfs dfs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/

* `<file-system-name>` 자리 표시자를 파일 시스템에 지정할 이름으로 바꿉니다.

* `<storage-account-name>` 자리 표시자를 스토리지 계정 이름으로 바꿉니다.

### <a name="get-a-list-of-files-or-directories"></a>파일 또는 디렉터리 목록 가져오기

    hdfs dfs -ls <path>

`<path>` 자리 표시자를 파일 시스템 또는 파일 시스템 폴더의 URI로 바꿉니다.

예: `hdfs dfs -ls abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name`

### <a name="create-a-directory"></a>디렉터리 만들기

    hdfs dfs -mkdir [-p] <path>

`<path>` 자리 표시자를 루트 파일 시스템 이름 또는 파일 시스템 내의 폴더로 바꿉니다.

예: `hdfs dfs -mkdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/`

### <a name="delete-a-file-or-directory"></a>파일 또는 디렉터리 삭제

    hdfs dfs -rm <path>

`<path>` 자리 표시자를 삭제하려는 파일 또는 폴더의 URI로 바꿉니다.

예: `hdfs dfs -rmdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name/my-file-name`

### <a name="use-the-hdfs-cli-with-an-hdinsight-hadoop-cluster-on-linux"></a>Linux에서 HDInsight Hadoop 클러스터로 HDFS CLI 사용

먼저 [서비스에 원격 액세스](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-information#remote-access-to-services)를 확인합니다. [SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)를 선택하면 샘플 PowerShell 코드가 다음과 같이 표시합니다.

```PowerShell
#Connect to the cluster via SSH.
ssh sshuser@clustername-ssh.azurehdinsight.net
#Execute basic HDFS commands. Display the hierarchy.
hdfs dfs -ls /
#Create a sample directory.
hdfs dfs -mkdir /samplefolder
```
Azure Portal에 있는 HDInsight 클러스터 블레이드의 “SSH + 클러스터 로그인” 섹션에서 연결 문자열을 찾을 수 있습니다. SSH 자격 증명은 클러스터 생성 시 지정되었습니다.

HDFS CLI에 대한 자세한 내용은 [공식 문서](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) 및 [HDFS Permissions Guide](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)(HDFS 권한 가이드)를 참조하세요. Databricks에서 ACL에 대한 자세한 내용은 [Secrets CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#secrets-cli)를 참조하세요.

## <a name="hdfs-cli-with-azure-databricks"></a>Azure Databricks를 사용한 HDFS CLI

Databricks는 Databricks REST API 위에 빌드된 사용하기 쉬운 CLI를 제공합니다. 오픈 소스 프로젝트는 [GitHub](https://github.com/databricks/databricks-cli)에서 호스트됩니다. 다음은 일반적으로 사용되는 명령입니다.

### <a name="get-a-list-of-files-or-directories"></a>파일 또는 디렉터리 목록 가져오기

    dbfs ls [-l]

### <a name="create-a-directory"></a>디렉터리 만들기

    dbfs mkdirs

### <a name="delete-a-file"></a>파일 삭제

    dbfs rm [-r]

Databricks와 상호 작용하는 또 다른 방법은 Notebook입니다. Notebook에 기본 언어가 있는 동안 셀의 시작 부분에 언어 magic 명령 %language를 지정하여 언어를 혼합할 수 있습니다. 특히 %sh를 사용하면 이 문서의 앞쪽에 있는 HDInsight 예제처럼 Notebook에서 셸 코드를 실행할 수 있습니다.

### <a name="get-a-list-of-files-or-directories"></a>파일 또는 디렉터리 목록 가져오기

    %sh ls <args>

### <a name="create-a-directory"></a>디렉터리 만들기

    %sh mkdir [-p] <paths>

### <a name="delete-a-file-or-a-directory"></a>파일 또는 디렉터리 삭제

    %sh rm [-skipTrash] URI [URI ...]

Azure Databricks에서 Spark 클러스터를 시작한 후 새 Notebook을 만듭니다. 샘플 Notebook 스크립트는 다음과 같이 표시됩니다.

    #Execute basic HDFS commands invoking the shell. Display the hierarchy.
    %sh ls /
    #Create a sample directory.
    %sh mkdir /samplefolder
    #Get the ACL of the newly created directory.
    hdfs dfs -getfacl /samplefolder

Databricks CLI에 대한 자세한 내용은 [공식 문서](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html)를 참조하세요. Notebook에 대한 자세한 내용은 설명서의 [Notebook](https://docs.azuredatabricks.net/user-guide/notebooks/index.html) 섹션을 참조하세요.

## <a name="set-file-and-directory-level-permissions"></a>파일 및 디렉터리 수준 사용 권한 설정

파일 및 디렉터리 수준에서 액세스 권한을 설정하고 가져옵니다. 시작하기 위한 몇 가지 명령이 있습니다. 

Azure Data Lake Gen2 파일 시스템의 파일 및 디렉터리 수준 사용 권한에 대한 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어](storage-data-lake-storage-access-control.md)를 참조하세요.

### <a name="display-the-access-control-lists-acls-of-files-and-directories"></a>파일 및 디렉터리의 Access Control Lists(ACL) 표시

    hdfs dfs -getfacl [-R] <path>

예제:

`hdfs dfs -getfacl -R /dir`

[getfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#getfacl) 참조

### <a name="set-acls-of-files-and-directories"></a>파일 및 디렉터리의 ACL 설정

    hdfs dfs -setfacl [-R] [-b|-k -m|-x <acl_spec> <path>]|[--set <acl_spec> <path>]

예제:

`hdfs dfs -setfacl -m user:hadoop:rw- /file`

[setfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#setfacl) 참조

### <a name="change-the-owner-of-files"></a>파일의 소유자 변경

    hdfs dfs -chown [-R] <new_owner>:<users_group> <URI>

[chown](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chown) 참조

### <a name="change-group-association-of-files"></a>파일의 그룹 연결 변경

    hdfs dfs -chgrp [-R] <group> <URI>

[chgrp](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chgrp) 참조

### <a name="change-the-permissions-of-files"></a>파일의 사용 권한 변경

    hdfs dfs -chmod [-R] <mode> <URI>

[chmod](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chmod)를 참조합니다.

[Apache Hadoop 2.4.1 파일 시스템 셸 가이드](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) 웹 사이트에서 명령의 전체 목록을 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure Databricks에서 Azure Data Lake Storage Gen2 지원 계정 사용](./data-lake-storage-quickstart-create-databricks-account.md) 
