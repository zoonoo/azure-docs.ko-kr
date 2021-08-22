---
title: Azure HDInsight 클러스터에 Azure Data Lake Storage Gen2 사용
description: Azure HDInsight 클러스터와 함께 Azure Data Lake Storage Gen2를 사용하는 방법을 알아봅니다.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: 4bf2024a54535e5091857ccedf01a812b2f6a49c
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112280180"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Azure HDInsight 클러스터에 Azure Data Lake Storage Gen2 사용

[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)는 [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)를 기반으로 하는 빅 데이터 분석 전용 클라우드 스토리지 서비스입니다. Data Lake Storage Gen2는 Azure Blob Storage와 Azure Data Lake Storage Gen1의 기능을 결합합니다. 결과 서비스는 파일 시스템 의미 체계, 디렉터리 수준 및 파일 수준 보안, 적응성을 포함하는 Azure Data Lake Storage Gen1의 기능을 제공합니다. Azure Blob Storage의 저렴한 비용, 계층화된 스토리지, 고가용성 및 재해 복구 기능과 함께 사용됩니다.

Data Lake Storage Gen2를 사용하여 클러스터 만들기 옵션을 전체적으로 비교하려면 [Azure HDInsight 클러스터와 함께 사용하기 위한 스토리지 옵션 비교](hdinsight-hadoop-compare-storage-options.md)를 참조하세요.

[!INCLUDE [delete-cluster-warning](includes/hdinsight-delete-cluster-warning.md)]

## <a name="data-lake-storage-gen2-availability"></a>Data Lake Storage Gen2 가용성

Data Lake Storage Gen2는 거의 모든 Azure HDInsight 클러스터 유형에 대한 스토리지 옵션으로 제공되며 기본 및 추가 스토리지 계정으로 사용할 수 있습니다. 그러나 HBase는 Data Lake Storage Gen2 계정 하나만 사용할 수 있습니다.

> [!Note]  
> **기본 스토리지 유형** 으로 Data Lake Storage Gen2를 선택하면 Data Lake Storage Gen1 계정을 추가 스토리지로 선택할 수 없습니다.

## <a name="create-hdinsight-clusters-using-data-lake-storage-gen2"></a>Data Lake Storage Gen2를 사용하여 HDInsight 클러스터 만들기

Data Lake Storage Gen2에 대한 액세스로 HDInsight 클러스터를 만드는 방법에 대한 자세한 지침은 다음 링크를 사용하세요.

* [포털 사용](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2-portal.md)
* [Azure CLI 사용](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2-azure-cli.md)
* PowerShell은 현재 Azure Data Lake Storage Gen2를 사용하여 HDInsight 클러스터를 만드는 데 지원되지 않습니다.

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>HDInsight의 Data Lake Storage Gen2에 대한 액세스 제어

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Data Lake Storage Gen2는 어떤 종류의 권한을 지원하나요?

Data Lake Storage Gen2는 Azure RBAC(Azure 역할 기반 액세스 제어)와 POSIX 같은 ACL(액세스 제어 목록)을 모두 지원하는 액세스 제어 모델을 사용합니다. Data Lake Storage Gen1은 데이터에 대한 액세스를 제어하기 위한 액세스 제어 목록만 지원합니다.

Azure RBAC는 역할 할당을 사용하여 Azure 리소스에 대한 사용자, 그룹 및 서비스 주체에 권한 세트를 효과적으로 적용합니다. 일반적으로 이러한 Azure 리소스는 최상위 리소스(예: Azure Blob Storage 계정)로 제한됩니다. Azure Blob Storagee 및 Data Lake Storage Gen2의 경우 이 메커니즘이 파일 시스템 리소스로 확장되었습니다.

Azure RBAC의 파일 권한에 대한 자세한 내용은 [Azure RBAC(역할 기반 액세스 제어)](../storage/blobs/data-lake-storage-access-control-model.md#role-based-access-control)를 참조하세요.

ACL을 사용하는 파일 권한에 대한 자세한 내용은 [파일 및 디렉터리에 대한 액세스 제어 목록](../storage/blobs/data-lake-storage-access-control.md)을 참조하세요.

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>Data Lake Storage Gen2에서 내 데이터에 대한 액세스를 제어하려면 어떻게 해야 하나요?

HDInsight 클러스터의 Data Lake Storage Gen2 파일에 액세스하는 기능은 관리 ID를 통해 제어됩니다. 관리 ID는 Azure AD(Azure Active Directory)에 등록된 ID로, Azure에서 자격 증명이 관리됩니다. 관리 ID를 사용하면 Azure AD에서 서비스 주체를 등록할 필요가 없습니다. 또는 인증서와 같은 자격 증명을 유지 관리합니다.

Azure 서비스에는 두 가지 관리 ID 형식(시스템 할당 및 사용자 할당)이 있습니다. HDInsight는 사용자 할당 관리 ID를 사용하여 Data Lake Storage Gen2에 액세스합니다. `user-assigned managed identity`는 독립 실행형 Azure 리소스로 생성됩니다. 만들기 프로세스를 통해 Azure는 사용 중인 구독에서 신뢰하는 Azure AD 테넌트에 ID를 만듭니다. 생성된 ID는 하나 이상의 Azure 서비스 인스턴스에 할당할 수 있습니다.

사용자 할당 ID의 수명 주기는 할당된 Azure 서비스 인스턴스의 수명 주기와 별도로 관리됩니다. 관리 ID에 대한 자세한 내용은 [Azure 리소스에 대한 관리 ID란?](../active-directory/managed-identities-azure-resources/overview.md)을 참조하세요.

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>Azure AD 사용자가 Hive 또는 기타 서비스를 사용하여 Data Lake Storage Gen2의 데이터를 쿼리할 수 있도록 권한을 설정하려면 어떻게 해야 하나요?

사용자가 데이터를 쿼리할 수 있도록 권한을 설정하려면 Azure AD 보안 그룹을 ACL에서 할당된 주체로 사용합니다. 개별 사용자 또는 서비스 주체에 대한 파일 액세스 권한을 직접 할당하지 마세요. 권한 흐름을 제어하기 위해 Azure AD 보안 그룹을 사용하여 전체 디렉터리 구조에 ACL을 다시 적용하지 않고 사용자 또는 서비스 주체를 추가하고 제거할 수 있습니다. 적절한 Azure AD 보안 그룹에서 사용자를 추가 또는 제거하기만 하면 됩니다. ACL은 상속되지 않으므로 ACL을 다시 적용하려면 모든 파일 및 하위 디렉터리에서 ACL을 업데이트해야 합니다.

## <a name="access-files-from-the-cluster"></a>클러스터에서 파일 액세스

HDInsight 클러스터에서 Data Lake Storage Gen2의 파일에 액세스할 수 있는 방법은 여러 가지입니다.

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
