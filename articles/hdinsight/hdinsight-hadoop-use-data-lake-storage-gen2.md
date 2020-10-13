---
title: Azure HDInsight 클러스터에 Azure Data Lake Storage Gen2 사용
description: Azure HDInsight 클러스터와 함께 Azure Data Lake Storage Gen2를 사용 하는 방법을 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: 4ef53b2249f8ce57255c13126c9310f1c889d64f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91855058"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Azure HDInsight 클러스터에 Azure Data Lake Storage Gen2 사용

[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) 은 [Azure Blob storage](../storage/blobs/storage-blobs-introduction.md)를 기반으로 하는 빅 데이터 분석 전용 클라우드 저장소 서비스입니다. Data Lake Storage Gen2는 Azure Blob Storage 및 Azure Data Lake Storage Gen1의 기능을 결합 합니다. 결과 서비스는 파일 시스템 의미 체계, 디렉터리 수준 및 파일 수준 보안 및 적응성을 포함 하 여 Azure Data Lake Storage Gen1 기능을 제공 합니다. Azure Blob storage의 저렴 한 비용, 계층화 된 저장소, 고가용성 및 재해 복구 기능과 함께 사용 됩니다.

Data Lake Storage Gen2 사용 하 여 클러스터 만들기 옵션을 전체적으로 비교 하려면 [Azure HDInsight 클러스터와 함께 사용 하기 위한 저장소 옵션 비교](hdinsight-hadoop-compare-storage-options.md)를 참조 하세요.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="data-lake-storage-gen2-availability"></a>Data Lake Storage Gen2 가용성

Data Lake Storage Gen2는 거의 모든 Azure HDInsight 클러스터 형식에 대 한 저장소 옵션으로 기본 및 추가 저장소 계정으로 사용할 수 있습니다. 그러나 HBase에는 Data Lake Storage Gen2 있는 계정이 하나만 있을 수 있습니다.

> [!Note]  
> Data Lake Storage Gen2를 **기본 저장소 형식**으로 선택한 후에는 Data Lake Storage Gen1를 추가 저장소로 선택할 수 없습니다.

## <a name="create-hdinsight-clusters-using-data-lake-storage-gen2"></a>Data Lake Storage Gen2를 사용 하 여 HDInsight 클러스터 만들기

Data Lake Storage Gen2에 대 한 액세스 권한이 있는 HDInsight 클러스터를 만드는 방법에 대 한 자세한 지침은 다음 링크를 사용 하세요.

* [포털 사용](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2-portal.md)
* [Azure CLI 사용](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2-azure-cli.md)
* PowerShell은 현재 Azure Data Lake Storage Gen2를 사용 하 여 HDInsight 클러스터를 만드는 데 지원 되지 않습니다.

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>HDInsight의 Data Lake Storage Gen2에 대 한 액세스 제어

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Data Lake Storage Gen2는 어떤 종류의 권한을 지원하나요?

Data Lake Storage Gen2에서는 RBAC (역할 기반 액세스 제어) 및 POSIX와 같은 액세스 제어 목록 (Acl)을 모두 지 원하는 액세스 제어 모델을 사용 합니다. Data Lake Storage Gen1는 데이터에 대 한 액세스를 제어 하기 위한 액세스 제어 목록만 지원 합니다.

RBAC는 역할 할당을 사용 하 여 Azure 리소스에 대 한 사용자, 그룹 및 서비스 사용자에 게 사용 권한 집합을 효과적으로 적용 합니다. 일반적으로 이러한 Azure 리소스는 최상위 리소스 (예: Azure Blob storage 계정)로 제한 됩니다. Azure Blob storage의 경우에도 Data Lake Storage Gen2이 메커니즘이 파일 시스템 리소스로 확장 되었습니다.

RBAC를 사용 하는 파일 사용 권한에 대 한 자세한 내용은 [azure 역할 기반 액세스 제어 (AZURE RBAC)](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac)를 참조 하세요.

Acl을 사용 하는 파일 사용 권한에 대 한 자세한 내용은 [파일 및 디렉터리에 대 한 액세스 제어 목록](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)을 참조 하십시오.

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>Data Lake Storage Gen2 내 데이터에 대 한 액세스를 제어 어떻게 할까요?

HDInsight 클러스터의 Data Lake Storage Gen2 파일에 액세스 하는 기능은 관리 되는 id를 통해 제어 됩니다. 관리 id는 Azure AD (Azure Active Directory)에 등록 된 id로, Azure에서 자격 증명을 관리 합니다. 관리 id를 사용 하면 Azure AD에서 서비스 주체를 등록할 필요가 없습니다. 또는 인증서와 같은 자격 증명을 유지 관리 합니다.

Azure 서비스에는 두 가지 유형의 관리 id 인 시스템 할당 및 사용자 할당이 있습니다. HDInsight는 사용자 할당 관리 id를 사용 하 여 Data Lake Storage Gen2에 액세스 합니다. 는 `user-assigned managed identity` 독립 실행형 Azure 리소스로 생성 됩니다. 만들기 프로세스를 통해 Azure는 사용 중인 구독에서 신뢰하는 Azure AD 테넌트에 ID를 만듭니다. 생성된 ID는 하나 이상의 Azure 서비스 인스턴스에 할당할 수 있습니다.

사용자 할당 ID의 수명 주기는 할당된 Azure 서비스 인스턴스의 수명 주기와 별도로 관리됩니다. 관리 id에 대 한 자세한 내용은 [Azure 리소스에 대 한 관리 되는 Id 란?](../active-directory/managed-identities-azure-resources/overview.md)을 참조 하세요.

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>Hive 또는 다른 서비스를 사용 하 여 Data Lake Storage Gen2에서 데이터를 쿼리할 수 있도록 Azure AD 사용자에 대 한 권한을 설정 어떻게 할까요??

사용자가 데이터를 쿼리할 수 있는 권한을 설정 하려면 Azure AD 보안 그룹을 Acl에서 할당 된 주체로 사용 합니다. 개별 사용자 또는 서비스 사용자에 게 파일 액세스 권한을 직접 할당 하지 않습니다. Azure AD 보안 그룹을 사용 하 여 권한 흐름을 제어 하기 위해 Acl을 전체 디렉터리 구조에 다시 적용 하지 않고 사용자 또는 서비스 사용자를 추가 하 고 제거할 수 있습니다. 적절한 Azure AD 보안 그룹에서 사용자를 추가 또는 제거하기만 하면 됩니다. Acl은 상속 되지 않으므로 Acl을 다시 적용 하려면 모든 파일 및 하위 디렉터리에서 ACL을 업데이트 해야 합니다.

## <a name="access-files-from-the-cluster"></a>클러스터에서 파일 액세스

여러 가지 방법으로 HDInsight 클러스터에서 Data Lake Storage Gen2 파일에 액세스할 수 있습니다.

* **정규화된 이름 사용**. 이 방법의 경우 액세스할 파일에 대한 전체 경로를 제공합니다.

    ```
    abfs://<containername>@<accountname>.dfs.core.windows.net/<file.path>/
    ```

* **줄인 경로 형식 사용**. 이 방식의 경우 클러스터 루트에 대한 경로를 다음으로 대체합니다.

    ```
    abfs:///<file.path>/
    ```

* **상대 경로 사용**. 이 방법의 경우 액세스할 파일에 대한 상대 경로만 제공합니다.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>데이터 액세스 예제

예제는 클러스터의 헤드 노드에 대한 [ssh 연결](./hdinsight-hadoop-linux-use-ssh-unix.md)을 기반으로 합니다. 이 예제에서는 세 가지 URI 스키마를 모두 사용합니다. `CONTAINERNAME` 및 `STORAGEACCOUNT`를 관련 값으로 바꾸기

#### <a name="a-few-hdfs-commands"></a>몇 가지 hdfs 명령

1. 로컬 스토리지에 파일을 만듭니다.

    ```bash
    touch testFile.txt
    ```

1. 클러스터 스토리지에 디렉터리를 만듭니다.

    ```bash
    hdfs dfs -mkdir abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -mkdir abfs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. 로컬 스토리지에서 클러스터 스토리지로 데이터를 복사합니다.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  abfs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. 클러스터 스토리지의 디렉터리 콘텐츠를 나열합니다.

    ```bash
    hdfs dfs -ls abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -ls abfs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>Hive 테이블 만들기

설명을 위해 세 가지 파일 위치가 표시되었습니다. 실제로 실행할 때는 `LOCATION` 항목 중 하나만 사용합니다.

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/example/data/';
LOCATION 'abfs:///example/data/';
LOCATION '/example/data/';
```

## <a name="next-steps"></a>다음 단계

* [Data Lake Storage Gen2 미리 보기와 Azure HDInsight 통합 - ACL 및 보안 업데이트](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Azure Data Lake Storage Gen2 소개](../storage/blobs/data-lake-storage-introduction.md)
* [자습서: Azure HDInsight에서 대화형 쿼리를 사용하여 데이터 추출, 변환 및 로드](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)