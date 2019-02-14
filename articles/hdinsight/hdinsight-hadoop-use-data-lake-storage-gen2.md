---
title: Azure HDInsight 클러스터에 Azure Data Lake Storage Gen2 사용
description: Azure HDInsight 클러스터에 Azure Data Lake Storage Gen2를 사용하는 방법을 알아봅니다.
services: hdinsight
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: howto
ms.date: 01/10/2019
ms.author: hrasheed
ms.openlocfilehash: fae92f8e09cc2ad6b63cb15599e0b1ab72588ed8
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2019
ms.locfileid: "55728846"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Azure HDInsight 클러스터에 Azure Data Lake Storage Gen2 사용

Azure Data Lake Storage(Data Lake Storage) Gen2는 Azure Blob 스토리지를 기반으로 하는 빅 데이터 분석 전용의 기능 세트입니다. Data Lake Storage Gen2는 Azure Blob 스토리지와 Azure Data Lake Storage Gen1의 기능을 결합한 결과입니다. 파일 시스템 의미 체계, 디렉터리, 파일 수준 보안, 크기 조정 등의 Azure Data Lake Storage Gen1 기능과 함께 Azure Blob 스토리지의 저비용, 계층화된 스토리지, 고가용성/재해 복구 기능을 제공합니다.

## <a name="data-lake-storage-gen2-availability"></a>Data Lake Storage Gen2 가용성

Azure Data Lake Storage Gen2는 거의 모든 Azure HDInsight 클러스터 형식의 스토리지 옵션으로(기본 및 추가 스토리지 계정 둘 다) 사용할 수 있습니다. 그러나 HBase는 Data Lake Storage Gen2 계정 하나만 사용할 수 있습니다.

> [!Note] 
> **기본 스토리지 유형**으로 Data Lake Storage Gen2를 선택하면 Data Lake Storage Gen1 계정을 추가 스토리지로 선택할 수 없습니다.

## <a name="creating-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>Data Lake Storage Gen2를 사용하여 HDInsight 클러스터 만들기

## <a name="using-the-azure-portal"></a>Azure Portal 사용

스토리지에 Data Lake Storage Gen2를 사용하는 HDInsight 클러스터를 만들려면 다음 단계에 따라 올바르게 구성된 Data Lake Storage Gen2 계정을 만듭니다.

1. 사용자 할당 관리 ID가 아직 없는 경우 지금 만듭니다. [Azure Portal을 사용하여 사용자 할당 관리 ID를 생성, 나열, 삭제 또는 할당](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)을 참조하세요.

    ![사용자 할당 관리 ID 만들기](./media/hdinsight-hadoop-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

1. Azure Data Lake Storage Gen2 스토리지 계정을 만듭니다. **계층적 파일 시스템** 옵션을 사용하도록 설정합니다. [빠른 시작: Azure Data Lake Storage Gen2 스토리지 계정 만들기](../storage/blobs/data-lake-storage-quickstart-create-account.md)를 참조하세요.

    ![Azure Portal에서 스토리지 계정을 만드는 과정을 보여 주는 스크린샷](./media/hdinsight-hadoop-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)
 
1. 스토리지 계정의 **Storage Blob 데이터 소유자(미리 보기)** 역할에 관리 ID를 할당합니다. [RBAC를 사용하여 Azure Blob 및 큐 데이터에 대한 액세스 권한 관리(미리 보기)](../storage/common/storage-auth-aad-rbac.md#assign-a-role-scoped-to-the-storage-account-in-the-azure-portal)를 참조하세요.

    1. [Azure Portal](https://portal.azure.com)에서 저장소 계정으로 이동합니다.
    1. 스토리지 계정을 선택한 다음, **액세스 제어(IAM)** 를 선택하여 계정에 대한 액세스 제어 설정을 표시합니다. **역할 할당** 탭을 선택하여 역할 할당 목록을 봅니다.
    
        ![스토리지 액세스 제어 설정을 보여 주는 스크린샷](./media/hdinsight-hadoop-data-lake-storage-gen2/portal-access-control.png)
    
    1. **역할 할당 추가** 단추를 클릭하여 새 역할을 추가합니다.
    1. **역할 할당 추가** 창에서 **Storage Blob 데이터 소유자(미리 보기)** 역할을 선택합니다. 그런 다음, 관리 ID 및 스토리지 계정이 있는 구독을 선택합니다. 다음으로, 앞에서 만든 사용자 할당 관리 ID를 검색하여 찾습니다. 마지막으로, 관리 ID를 선택하면 **선택한 멤버** 아래에 나열될 것입니다.
    
        ![RBAC 역할을 할당하는 방법을 보여주는 스크린샷](./media/hdinsight-hadoop-data-lake-storage-gen2/add-rbac-role3.png)
    
    1. **저장**을 클릭합니다. 선택한 사용자 할당 ID가 이제 **기여자** 역할 아래에 나열됩니다.

    1. 이 초기 설정이 완료되면 포털을 통해 클러스터를 만들 수 있습니다. 클러스터가 스토리지 계정과 동일한 Azure 영역에 있어야 합니다. 클러스터 만들기 메뉴의 **스토리지** 섹션에서 다음 옵션을 선택합니다.
        
        * **기본 스토리지 형식**으로 **Azure Data Lake Storage Gen2**를 클릭합니다.
        * **스토리지 계정 선택**에서 새로 만든 Data Lake Storage Gen2 스토리지 계정을 검색하여 선택합니다.
        
            ![Azure HDInsight에서 Data Lake Storage Gen2를 사용하기 위한 스토리지 설정](./media/hdinsight-hadoop-data-lake-storage-gen2/primary-storage-type-adls-gen2.png)
        
        * **ID** 아래에서 올바른 구독과 새로 만든 사용자 할당 관리 ID를 선택합니다.
        
            ![Azure HDInsight에서 Data Lake Storage Gen2를 사용하기 위한 ID 설정](./media/hdinsight-hadoop-data-lake-storage-gen2/managed-identity-cluster-creation.png)

### <a name="using-a-resource-manager-template-deployed-with-azure-cli"></a>Azure CLI를 사용하여 배포된 Resource Manager 템플릿 사용

샘플 템플릿 파일은 [여기](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json)서, 샘플 매개 변수 파일은 [여기](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json)서 다운로드할 수 있습니다. 템플릿을 사용하기 전에 `<SUBSCRIPTION_ID>` 문자열을 실제 Azure 구독 ID로 대체합니다. 또한 `<PASSWORD>` 문자열을 선택한 암호로 대체하여 클러스터에 로그인하는 데 사용할 로그인 암호와 SSH 암호를 모두 설정합니다.

아래 코드 조각은 다음 초기 단계를 수행합니다.

1. Azure 계정에 로그인합니다.
1. 만들기 작업을 수행할 활성 구독을 설정합니다.
1. 새 배포 활동 `hdinsight-deployment-rg`용으로 새 리소스 그룹을 만듭니다.
1. 사용자 MSI(관리 서비스 ID) `test-hdinsight-msi`를 만듭니다.
1. Data Lake Storage Gen2용 기능을 사용할 수 있도록 Azure CLI에 확장을 추가합니다.
1. `--hierarchical-namespace true` 플래그를 사용하여 새 Data Lake Storage Gen2 계정 `hdinsightadlsgen2`를 만듭니다.

```azurecli
az login
az account set --subscription <subscription_id>

#create resource group
az group create --name hdinsight-deployment-rg --location eastus

# Create managed identity
az identity create -g hdinsight-deployment-rg -n test-hdinsight-msi

az extension add --name storage-preview

az storage account create --name hdinsightadlsgen2 \
    --resource-group hdinsight-deployment-rg \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

다음으로는 포털에 로그인하여 위의 [Azure Portal 사용](hdinsight-hadoop-use-data-lake-storage-gen2.md#using-the-azure-portal)에서 3단계의 설명에 따라 스토리지 계정의 **Storage Blob 데이터 기여자(미리 보기)** 역할에 새 MSI를 추가합니다.

포털에서 MSI 역할 할당을 완료한 후 아래의 코드 조각을 사용하여 템플릿 배포를 진행합니다.

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group hdinsight-deployment-rg \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>HDInsight의 Data Lake Storage Gen2에 대한 액세스 제어

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Data Lake Storage Gen2는 어떤 종류의 권한을 지원하나요?

Azure Data Lake Storage Gen2는 Azure RBAC(역할 기반 액세스 제어)와 POSIX 같은 ACL(액세스 제어 목록)을 모두 지원하는 액세스 제어 모델을 구현합니다. Data Lake Storage Gen1은 데이터 액세스를 제어하는 액세스 제어 목록만 지원합니다.

Azure RBAC(역할 기반 액세스 제어)는 역할 할당을 사용하여 Azure 리소스에 대한 사용자, 그룹 및 서비스 주체에 권한 세트를 효과적으로 적용합니다. 일반적으로 이러한 Azure 리소스는 최상위 리소스(예: Azure Storage 계정)로 제한됩니다. Azure Storage 그리고 Azure Data Lake Storage Gen2의 경우 이 메커니즘이 파일 시스템 리소스로 확장되었습니다.

 RBAC를 사용하는 파일 권한에 대한 자세한 내용은 [Azure RBAC(역할 기반 액세스 제어)](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac)를 참조하세요.

ACL을 사용하는 파일 권한에 대한 자세한 내용은 [파일 및 디렉터리에 대한 액세스 제어 목록](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)을 참조하세요.


### <a name="how-do-i-control-access-to-my-data-in-gen2"></a>Gen2에서 내 데이터에 대한 액세스를 제어하려면 어떻게 할까요?

HDInsight 클러스터가 Data Lake Storage Gen2의 파일에 액세스하는 기능은 관리 ID를 통해 제어됩니다. 관리 ID는 Azure AD에 등록된 ID로, Azure에서 자격 증명이 관리됩니다. Azure AD에서 서비스 주체를 등록하고 인증서 같은 자격 증명을 유지 관리할 필요가 없습니다.

Azure 서비스의 관리 ID 형식으로는 시스템 할당 및 사용자 할당의 두 가지가 있습니다. Azure HDInsight는 사용자 할당 관리 ID를 사용하여 Azure Data Lake Storage Gen2에 액세스합니다. 사용자 할당 관리 ID는 독립 실행형 Azure 리소스로 생성됩니다. 만들기 프로세스를 통해 Azure는 사용 중인 구독에서 신뢰하는 Azure AD 테넌트에 ID를 만듭니다. 생성된 ID는 하나 이상의 Azure 서비스 인스턴스에 할당할 수 있습니다. 사용자 할당 ID의 수명 주기는 할당된 Azure 서비스 인스턴스의 수명 주기와 별도로 관리됩니다. 관리 ID에 대한 자세한 내용은 [Azure 리소스에 대한 관리 ID의 작동 원리](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-worka-namehow-does-it-worka)를 참조하세요.

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-using-hive-or-other-services"></a>Azure AD 사용자가 Hive 또는 기타 서비스를 사용하여 Data Lake Storage Gen2의 데이터를 쿼리하는 권한을 설정하려면 어떻게 할까요?

Azure AD 보안 그룹을 ACL에서 할당된 주체로 사용합니다. 개별 사용자 또는 서비스 주체에 파일 액세스 권한을 직접 할당하지 마세요. AD 보안 그룹을 사용하여 권한 흐름을 제어하면 전체 디렉터리 구조에 ACL을 다시 적용하지 않고 사용자 또는 서비스 주체를 추가하고 제거할 수 있습니다. 적절한 Azure AD 보안 그룹에서 사용자를 추가 또는 제거하기만 하면 됩니다. ACL은 상속되지 않으므로 ACL을 다시 적용하려면 모든 파일 및 하위 디렉터리에서 ACL을 업데이트해야 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure HDInsight 클러스터에 Azure Data Lake Storage Gen2 미리 보기 사용](../storage/blobs/data-lake-storage-use-hdi-cluster.md)
* [Data Lake Storage Gen2 미리 보기와 Azure HDInsight 통합 - ACL 및 보안 업데이트](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Azure Data Lake Storage Gen2 미리 보기 소개](../storage/blobs/data-lake-storage-introduction.md)
