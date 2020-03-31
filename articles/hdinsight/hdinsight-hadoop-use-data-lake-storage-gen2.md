---
title: Azure HDInsight 클러스터에 Azure Data Lake Storage Gen2 사용
description: Azure HDInsight 클러스터를 사용하여 Azure 데이터 레이크 스토리지 Gen2를 사용하는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/20/2020
ms.openlocfilehash: d711cc7e58fb055eda62cfc364a5552a7d10f7bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272293"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Azure HDInsight 클러스터에 Azure Data Lake Storage Gen2 사용

Azure Data Lake Storage Gen2는 Azure Blob 저장소를 기반으로 구축된 빅 데이터 분석 전용 클라우드 스토리지 서비스입니다. 데이터 레이크 저장소 Gen2는 Azure Blob 저장소 및 Azure 데이터 레이크 저장소 Gen1의 기능을 결합합니다. 결과 서비스는 파일 시스템 의미 체계, 디렉터리 수준 및 파일 수준 보안 및 확장성과 같은 Azure Data Lake Storage Gen1의 기능과 저비용, 계층형 스토리지, 고가용성 및 재해 복구 기능을 제공합니다. Azure Blob 저장소에서 볼 수 있습니다.

## <a name="data-lake-storage-gen2-availability"></a>Data Lake Storage Gen2 가용성

데이터 레이크 저장소 Gen2는 거의 모든 Azure HDInsight 클러스터 유형에 대한 저장소 옵션으로 기본 계정과 추가 저장소 계정으로 사용할 수 있습니다. 그러나 HBase는 하나의 데이터 레이크 스토리지 Gen2 계정만 가질 수 있습니다.

Data Lake Storage Gen2를 사용하는 클러스터 생성 옵션의 전체 비교는 [Azure HDInsight 클러스터와 함께 사용할 저장소 비교 옵션을](hdinsight-hadoop-compare-storage-options.md)참조하십시오.

> [!Note]  
> 데이터 레이크 스토리지 Gen2를 **기본 저장소 유형으로**선택한 후에는 데이터 레이크 저장소 Gen1 계정을 추가 저장소로 선택할 수 없습니다.

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-portal"></a>Azure 포털을 통해 데이터 레이크 저장소 Gen2를 사용하여 클러스터 만들기

스토리지에 데이터 레이크 스토리지 Gen2를 사용하는 HDInsight 클러스터를 만들려면 다음 단계를 따라 데이터 레이크 스토리지 Gen2 계정을 구성합니다.

### <a name="create-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID 만들기

사용자 할당 관리 ID가 아직 없는 경우 지금 만듭니다.

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
1. 왼쪽 상단에서 **리소스 만들기를**클릭합니다.
1. 검색 상자에서 **할당된 사용자를** 입력하고 **사용자 할당된 관리되는 ID**를 클릭합니다.
1. **만들기**를 클릭합니다.
1. 관리되는 ID의 이름을 입력하고 올바른 구독, 리소스 그룹 및 위치를 선택합니다.
1. **만들기**를 클릭합니다.

Azure HDInsight에서 관리되는 ID가 작동하는 방식에 대한 자세한 내용은 [Azure HDInsight의 관리ID](hdinsight-managed-identities.md)를 참조하십시오.

![사용자 할당 관리 ID 만들기](./media/hdinsight-hadoop-use-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

### <a name="create-a-data-lake-storage-gen2-account"></a>Data Lake Storage Gen2 계정 만들기

Azure Data Lake Storage Gen2 스토리지 계정을 만듭니다.

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
1. 왼쪽 상단에서 **리소스 만들기를**클릭합니다.
1. 검색 상자에서 **저장소를** 입력하고 **저장소 계정을**클릭합니다.
1. **만들기**를 클릭합니다.
1. 저장소 계정 만들기 화면에서 다음을 **수행합니다.**
    1. 올바른 구독 및 리소스 그룹을 선택합니다.
    1. 데이터 레이크 스토리지 Gen2 계정의 이름을 입력합니다. 저장소 계정 이름 지정 규칙에 대한 자세한 내용은 [Azure 리소스에 대한 명명 규칙을](/azure/azure-resource-manager/management/resource-name-rules#microsoftstorage)참조하십시오.
    1. **고급** 탭을 클릭합니다.
    1. **데이터 레이크 저장소 Gen2**에서 **계층 적 네임 스페이스** 옆의 **활성화를** 클릭합니다.
    1. **검토 + 만들기를**클릭합니다.
    1. **만들기를 클릭합니다.**

저장소 계정 생성 중 다른 옵션에 대한 자세한 내용은 [빠른 시작: Azure Data Lake Storage Gen2 저장소 계정 만들기를](../storage/blobs/data-lake-storage-quickstart-create-account.md)참조하십시오.

![Azure Portal에서 스토리지 계정을 만드는 과정을 보여 주는 스크린샷](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)

### <a name="set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account"></a>데이터 레이크 저장소 Gen2 계정에서 관리되는 ID에 대한 사용 권한 설정

저장소 계정의 저장소 **Blob 데이터 소유자** 역할에 관리되는 ID를 할당합니다.

1. [Azure Portal](https://portal.azure.com)에서 스토리지 계정으로 이동합니다.
1. 저장소 계정을 선택한 다음 **IAM(액세스 제어)을** 선택하여 계정의 액세스 제어 설정을 표시합니다. **역할 할당** 탭을 선택하여 역할 할당 목록을 봅니다.

    ![스토리지 액세스 제어 설정을 보여 주는 스크린샷](./media/hdinsight-hadoop-use-data-lake-storage-gen2/portal-access-control.png)

1. + **역할 할당 추가** 단추를 선택하여 새 역할을 추가합니다.
1. 역할 **할당 추가** 창에서 **저장소 Blob 데이터 소유자 역할을 선택합니다.** 그런 다음, 관리 ID 및 스토리지 계정이 있는 구독을 선택합니다. 다음으로, 앞에서 만든 사용자 할당 관리 ID를 검색하여 찾습니다. 마지막으로 관리되는 ID를 선택하면 **선택된 구성원**아래에 나열됩니다.

    ![RBAC 역할을 할당하는 방법을 보여주는 스크린샷](./media/hdinsight-hadoop-use-data-lake-storage-gen2/add-rbac-role3-window.png)

1. **저장**을 선택합니다. 선택한 사용자 할당 ID가 이제 선택한 역할 아래에 나열됩니다.
1. 이 초기 설정이 완료되면 포털을 통해 클러스터를 만들 수 있습니다. 클러스터가 스토리지 계정과 동일한 Azure 영역에 있어야 합니다. 클러스터 만들기 메뉴의 **저장소** 탭에서 다음 옵션을 선택합니다.

    * **기본 저장소 유형의**경우 **Azure 데이터 레이크 저장소 Gen2를**선택합니다.
    * **기본 저장소 계정에서**새로 만든 Data Lake Storage Gen2 저장소 계정을 검색하고 선택합니다.

    * **ID에서**새로 만든 사용자 할당관리 ID를 선택합니다.

        ![Azure HDInsight에서 Data Lake Storage Gen2를 사용하기 위한 스토리지 설정](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-portal-cluster-storage-gentwo.png)

    > [!NOTE]
    > * 저장소 계정 수준에서 보조 Data Lake Storage Gen2 계정을 추가하려면 추가하려는 새 Data Lake Storage Gen2 저장소 계정에 이전에 만든 관리 ID를 할당하기만 하면 됩니다. HDInsight의 "추가 저장소 계정" 블레이드를 통해 보조 데이터 레이크 스토리지 Gen2 계정을 추가하는 것은 지원되지 않습니다.
    > * HDInsight가 사용하는 Azure 저장소 계정에서 RA-GRS 또는 RA-ZRS를 활성화할 수 있습니다. 그러나 RA-GRS 또는 RA-ZRS 보조 끝점에 대해 클러스터를 만드는 것은 지원되지 않습니다.


## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-cli"></a>Azure CLI를 통해 데이터 레이크 스토리지 Gen2를 사용하여 클러스터 만들기

샘플 [템플릿 파일을 다운로드하고](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json) [샘플 매개 변수 파일을 다운로드할](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json)수 있습니다. 아래 템플릿 및 Azure CLI 코드 조각을 사용하기 전에 다음 자리 표시자를 올바른 값으로 바꿉니다.

| 자리 표시자 | 설명 |
|---|---|
| `<SUBSCRIPTION_ID>` | Azure 구독의 ID |
| `<RESOURCEGROUPNAME>` | 새 클러스터 및 저장소 계정을 만들려는 리소스 그룹입니다. |
| `<MANAGEDIDENTITYNAME>` | Azure Data Lake Storage Gen2 계정에 대한 사용 권한을 부여할 관리되는 ID의 이름입니다. |
| `<STORAGEACCOUNTNAME>` | 생성될 새 Azure 데이터 레이크 저장소 Gen2 계정입니다. |
| `<CLUSTERNAME>` | HDInsight 클러스터의 이름입니다. |
| `<PASSWORD>` | SSH와 Ambari 대시보드를 사용하여 클러스터에 로그인하기 위해 선택한 암호입니다. |

아래 코드 조각은 다음과 같은 초기 단계를 수행합니다.

1. Azure 계정에 로그인합니다.
1. 만들기 작업이 수행될 활성 구독을 설정합니다.
1. 새 배포 활동에 대한 새 리소스 그룹을 만듭니다.
1. 사용자 할당된 관리 되는 ID를 만듭니다.
1. Azure CLI에 확장을 추가하여 데이터 레이크 저장소 Gen2에 대한 기능을 사용합니다.
1. `--hierarchical-namespace true` 플래그를 사용하여 새 데이터 레이크 저장소 Gen2 계정을 만듭니다.

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus

# Create managed identity
az identity create -g <RESOURCEGROUPNAME> -n <MANAGEDIDENTITYNAME>

az extension add --name storage-preview

az storage account create --name <STORAGEACCOUNTNAME> \
    --resource-group <RESOURCEGROUPNAME> \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

다음으로 포털에 로그인합니다. Azure 포털 사용 아래의 3단계에서 설명한 대로 저장소 계정의 **저장소 Blob 데이터 기여자** 역할에 새 사용자 할당된 관리되는 [ID를](hdinsight-hadoop-use-data-lake-storage-gen2.md)추가합니다.

사용자가 할당한 관리 되는 ID에 대 한 역할을 할당 한 후 다음 코드 조각을 사용 하 여 템플릿을 배포 합니다.

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-azure-powershell"></a>Azure PowerShell을 통해 데이터 레이크 스토리지 Gen2를 사용하여 클러스터 만들기

PowerShell을 사용하여 Azure 데이터 레이크 저장소 Gen2를 사용하여 HDInsight 클러스터를 만드는 것은 현재 지원되지 않습니다.

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>HDInsight의 데이터 레이크 스토리지 Gen2에 대한 액세스 제어

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Data Lake Storage Gen2는 어떤 종류의 권한을 지원하나요?

Data Lake Storage Gen2는 역할 기반 액세스 제어(RBAC) 및 POSIX와 같은 액세스 제어 목록(ACL)을 모두 지원하는 액세스 제어 모델을 사용합니다. Data Lake Storage Gen1은 데이터에 대한 액세스를 제어하기 위한 액세스 제어 목록을 지원합니다.

RBAC는 역할 할당을 사용하여 Azure 리소스에 대한 사용자, 그룹 및 서비스 주체에 대한 사용 권한 집합을 효과적으로 적용합니다. 일반적으로 이러한 Azure 리소스는 최상위 리소스(예: Azure Storage 계정)로 제한됩니다. Azure 저장소 및 데이터 레이크 저장소 Gen2의 경우 이 메커니즘이 파일 시스템 리소스로 확장되었습니다.

 RBAC를 사용 하 여 파일 권한에 대 한 자세한 내용은 [Azure 역할 기반 액세스 제어 (RBAC)를](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac)참조 하십시오.

ACL을 사용 하 여 파일 사용 권한에 대 한 자세한 내용은 [파일 및 디렉터리에서 액세스 제어 목록을](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)참조 하십시오.

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>데이터 레이크 스토리지 Gen2에서 내 데이터에 대한 액세스를 제어하려면 어떻게 해야 합니까?

데이터 레이크 스토리지 Gen2의 파일에 액세스하는 HDInsight 클러스터의 기능은 관리되는 ID를 통해 제어됩니다. 관리되는 ID는 Azure Active Directory(Azure AD)에 등록된 ID로, 자격 증명은 Azure에서 관리합니다. 관리되는 ID를 사용하면 Azure AD에 서비스 주체를 등록하거나 인증서와 같은 자격 증명을 유지할 필요가 없습니다.

Azure 서비스에는 시스템 할당 및 사용자 할당의 두 가지 유형의 관리ID가 있습니다. HDInsight는 데이터 레이크 스토리지 Gen2에 액세스하기 위해 사용자가 할당한 관리 ID를 사용합니다. 사용자 할당 관리 ID는 독립 실행형 Azure 리소스로 생성됩니다. 만들기 프로세스를 통해 Azure는 사용 중인 구독에서 신뢰하는 Azure AD 테넌트에 ID를 만듭니다. 생성된 ID는 하나 이상의 Azure 서비스 인스턴스에 할당할 수 있습니다.

사용자 할당 ID의 수명 주기는 할당된 Azure 서비스 인스턴스의 수명 주기와 별도로 관리됩니다. 관리되는 ID에 대한 자세한 내용은 [Azure 리소스에 대한 관리ID의 작동 방식을 참조하세요.](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work)

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>Azure AD 사용자가 Hive 또는 다른 서비스를 사용하여 Data Lake Storage Gen2의 데이터를 쿼리할 수 있는 권한을 설정하려면 어떻게 해야 합니까?

사용자가 데이터를 쿼리할 수 있는 권한을 설정하려면 Azure AD 보안 그룹을 ACL에서 할당된 보안 서버로 사용합니다. 개별 사용자 또는 서비스 주체에게 파일 액세스 권한을 직접 할당하지 마십시오. Azure AD 보안 그룹을 사용하여 사용 권한 흐름을 제어하는 경우 전체 디렉터리 구조에 ACL을 다시 적용하지 않고 사용자 또는 서비스 주체를 추가 및 제거할 수 있습니다. 적절한 Azure AD 보안 그룹에서 사용자를 추가 또는 제거하기만 하면 됩니다. ACL은 상속되지 않으므로 ACL을 다시 적용하려면 모든 파일 및 하위 디렉터리에서 ACL을 업데이트해야 합니다.

## <a name="access-files-from-the-cluster"></a>클러스터에서 파일 액세스

HDInsight 클러스터에서 데이터 레이크 스토리지 Gen2의 파일에 액세스할 수 있는 방법에는 여러 가지가 있습니다.

* **정규화된 이름 사용**. 이 방법의 경우 액세스할 파일에 대한 전체 경로를 제공합니다.

    ```
    abfs://<containername>@<accountname>.dfs.core.windows.net/<file.path>/
    ```

* **줄인 경로 형식 사용**. 이 방법을 사용하면 클러스터 루트까지의 경로를 다음과 같은 것으로 바꿉을 바꿀 수 있습니다.

    ```
    abfs:///<file.path>/
    ```

* **상대 경로 사용**. 이 방법의 경우 액세스할 파일에 대한 상대 경로만 제공합니다.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>데이터 액세스 예제

예를 들어 클러스터의 헤드 노드에 대한 [ssh 연결을](./hdinsight-hadoop-linux-use-ssh-unix.md) 기반으로 합니다. 예제는 세 가지 URI 체계를 모두 사용합니다. 관련 `CONTAINERNAME` `STORAGEACCOUNT` 값으로 바꾸기

#### <a name="a-few-hdfs-commands"></a>몇 가지 HDF명령

1. 로컬 저장소에 파일을 만듭니다.

    ```bash
    touch testFile.txt
    ```

1. 클러스터 저장소에 디렉터리를 만듭니다.

    ```bash
    hdfs dfs -mkdir abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -mkdir abfs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. 로컬 저장소에서 클러스터 저장소로 데이터를 복사합니다.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  abfs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. 클러스터 저장소에 디렉터리 내용을 나열합니다.

    ```bash
    hdfs dfs -ls abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -ls abfs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>하이브 테이블 만들기

설명을 위해 세 개의 파일 위치가 표시됩니다. 실제 실행의 경우 `LOCATION` 항목 중 하나만 사용합니다.

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
* [자습서: Azure HDInsight에서 대화형 쿼리를 사용하여 데이터를 추출, 변환 및 로드](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
