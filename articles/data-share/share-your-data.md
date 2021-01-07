---
title: '자습서: 조직 외부에서 공유 - Azure Data Share'
description: 자습서 - Azure Data Share를 사용하여 고객 및 파트너와 데이터 공유
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: tutorial
ms.date: 11/12/2020
ms.openlocfilehash: 89c2a725b853b5a2a7578dccc1fd503917e12962
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659627"
---
# <a name="tutorial-share-data-using-azure-data-share"></a>자습서: Azure Data Share를 사용하여 데이터 공유  

이 자습서에서는 새로운 Azure Data Share를 설정하여 Azure 조직 외부의 고객 및 파트너와 데이터를 공유하는 방법을 알아봅니다. 

이 자습서에서 학습할 방법은 다음과 같습니다.

> [!div class="checklist"]
> * Data Share를 만듭니다.
> * Data Share에 데이터 세트를 추가합니다.
> * Data Share에 대해 스냅샷 일정을 사용하도록 설정합니다. 
> * Data Share에 받는 사람을 추가합니다. 

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독: Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* 수신자 Azure 로그인 이메일 주소(이메일 별칭을 사용하면 작동하지 않음).
* 원본 Azure 데이터 저장소가 데이터 공유 리소스를 만드는 데 사용하는 것과 다른 Azure 구독에 있는 경우 Azure 데이터 저장소가 있는 구독에 [Microsoft.DataShare 리소스 공급자](concepts-roles-permissions.md#resource-provider-registration)를 등록합니다. 

### <a name="share-from-a-storage-account"></a>스토리지 계정에서 공유

* Azure Storage 계정: 아직 없는 경우 [Azure Storage 계정](../storage/common/storage-account-create.md)을 만들 수 있습니다.
* 스토리지 계정에 쓸 수 있는 권한으로, *Microsoft.Storage/storageAccounts/write* 에 있습니다. 이 권한은 **기여자** 역할에 있습니다.
* 스토리지 계정에 역할 할당을 추가할 수 있는 권한입니다. 이 권한은 *Microsoft.Authorization/role assignments/write* 에 있습니다. 이 권한은 **소유자** 역할에 있습니다. 


### <a name="share-from-a-sql-based-source"></a>SQL 기반 원본에서 공유
다음은 SQL 원본에서 데이터를 공유하기 위한 필수 구성 요소 목록입니다. 

#### <a name="prerequisites-for-sharing-from-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>Azure SQL Database 또는 Azure Synapse Analytics(이전의 Azure SQL DW)에서 공유하기 위한 필수 구성 요소
[단계별 데모](https://youtu.be/hIE-TjJD8Dc)에 따라 필수 구성 요소를 구성할 수 있습니다.

* 공유하려는 테이블 및 보기를 포함하는 Azure SQL Database 또는 Azure Synapse Analytics(이전의 Azure SQL DW)
* SQL 서버에 데이터베이스를 쓸 수 있는 권한으로, *Microsoft.Sql/servers/databases/write* 에 있습니다. 이 권한은 **기여자** 역할에 있습니다.
* Data Share 리소스의 관리 ID가 데이터베이스에 액세스할 수 있는 권한입니다. 이 작업은 다음 단계를 통해 수행할 수 있습니다. 
    1. Azure Portal에서 SQL 서버로 이동하고 자신을 **Azure Active Directory 관리자** 로 설정합니다.
    1. [쿼리 편집기](../azure-sql/database/connect-query-portal.md#connect-using-azure-active-directory) 또는 Azure Active Directory 인증을 사용하는 SQL Server Management Studio를 사용하여 Azure SQL Database/Data Warehouse에 연결합니다. 
    1. 다음 스크립트를 실행하여 Data Share 리소스 관리 ID를 db_datareader로 추가합니다. SQL Server 인증이 아닌 Active Directory를 사용하여 연결해야 합니다. 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       *<share_acc_name>* 은 Data Share 리소스의 이름입니다. Data Share 리소스를 아직 만들지 않은 경우 나중에 이 필수 조건으로 다시 돌아올 수 있습니다.  

* 공유하려는 테이블 및/또는 보기를 탐색하고 선택할 수 있는 **'db_datareader'** 가 포함된 Azure SQL Database 사용자입니다. 

* SQL Server Firewall 액세스. 이 작업은 다음 단계를 통해 수행할 수 있습니다. 
    1. Azure Portal에서 SQL 서버로 이동합니다. 왼쪽 탐색에서 *방화벽 및 가상 네트워크* 를 선택합니다.
    1. *Azure 서비스 및 리소스가 이 서버에 액세스할 수 있도록 허용* 에 대해 **예** 를 클릭합니다.
    1. **+클라이언트 IP 추가** 를 클릭합니다. 클라이언트 IP 주소는 변경될 수 있습니다. 이 프로세스는 다음에 Azure Portal에서 SQL 데이터를 공유할 때 반복해야 할 수도 있습니다. IP 범위를 추가할 수도 있습니다.
    1. **저장** 을 클릭합니다. 

#### <a name="prerequisites-for-sharing-from-azure-synapse-analytics-workspace-sql-pool"></a>Azure Synapse Analytics(작업 영역) SQL 풀에서 공유하기 위한 필수 구성 요소

* * 공유하려는 테이블이 있는 Azure Synapse Analytics(작업 영역) 전용 SQL 풀입니다. 현재 보기 공유는 지원되지 않습니다. 서버리스 SQL 풀에서 공유는 현재 지원되지 않습니다.
* *Microsoft.Synapse/workspaces/sqlPools/write* 에 있는 Synapse 작업 영역의 SQL 풀에 쓸 수 있는 권한입니다. 이 권한은 **기여자** 역할에 있습니다.
* Data Share 리소스의 관리 ID가 Synapse 작업 영역 SQL 풀에 액세스할 수 있는 권한입니다. 이 작업은 다음 단계를 통해 수행할 수 있습니다. 
    1. Azure Portal에서 Synapse 작업 영역으로 이동합니다. 왼쪽 탐색에서 SQL Active Directory 관리자를 선택하고 자신을 **Azure Active Directory 관리자** 로 설정합니다.
    1. Synapse Studio를 열고 왼쪽 탐색에서 *관리* 를 선택합니다. 보안에서 *액세스 제어* 를 선택합니다. 자신에게 **SQL 관리자** 또는 **작업 영역 관리자** 역할을 할당합니다.
    1. Synapse Studio의 왼쪽 탐색에서 *개발* 을 선택합니다. SQL 풀에서 다음 스크립트를 실행하여 Data Share 리소스 관리 ID를 db_datareader로 추가합니다. 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       *<share_acc_name>* 은 Data Share 리소스의 이름입니다. Data Share 리소스를 아직 만들지 않은 경우 나중에 이 필수 조건으로 다시 돌아올 수 있습니다.  

* Synapse 작업 영역 Firewall 액세스. 이 작업은 다음 단계를 통해 수행할 수 있습니다. 
    1. Azure Portal에서 Synapse 작업 영역으로 이동합니다. 왼쪽 탐색에서 *방화벽* 을 선택합니다.
    1. **Azure 서비스 및 리소스가 이 작업 영역에 액세스할 수 있도록 허용** 하려면 *켜기* 를 클릭합니다.
    1. **+클라이언트 IP 추가** 를 클릭합니다. 클라이언트 IP 주소는 변경될 수 있습니다. 이 프로세스는 다음에 Azure Portal에서 SQL 데이터를 공유할 때 반복해야 할 수도 있습니다. IP 범위를 추가할 수도 있습니다.
    1. **저장** 을 클릭합니다. 


### <a name="share-from-azure-data-explorer"></a>Azure Data Explorer에서 공유
* 공유하려는 데이터베이스가 있는 Azure Data Explorer 클러스터.
* Azure Data Explorer 클러스터에 쓸 수 있는 권한으로, *Microsoft.Kusto/clusters/write* 에 있습니다. 이 권한은 **기여자** 역할에 있습니다.
* Azure Data Explorer 클러스터에 역할 할당을 추가할 수 있는 권한으로, *Microsoft.Authorization/role assignments/write* 에 있습니다. 이 권한은 **소유자** 역할에 있습니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="create-a-data-share-account"></a>Data Share 계정 만들기

### <a name="portal"></a>[포털](#tab/azure-portal)

Azure 리소스 그룹에서 Azure Data Share 리소스를 만듭니다.

1. 포털의 왼쪽 상단 모서리에 있는 메뉴 단추를 선택한 다음, **리소스 만들기**(+)를 선택합니다.

1. *Data Share* 를 검색합니다.

1. Data Share를 선택하고 **만들기** 를 선택합니다.

1. 다음 정보를 사용하여 Azure Data Share 리소스의 기본 세부 정보를 채웁니다. 

     **설정** | **제안 값** | **필드 설명**
    |---|---|---|
    | Subscription | 사용자의 구독 | 데이터 공유 계정에 사용할 Azure 구독을 선택합니다.|
    | 리소스 그룹 | *testresourcegroup* | 기존 리소스 그룹을 사용하거나 새 리소스 그룹을 만듭니다. |
    | 위치 | *미국 동부 2* | 데이터 공유 계정에 대한 지역을 선택합니다.
    | 이름 | *datashareaccount* | 데이터 공유 계정의 이름을 지정합니다. |
    | | |

1. **검토 + 만들기** 를 선택한 다음, **만들기** 를 선택하여 데이터 공유 계정을 프로비저닝합니다. 새 데이터 공유 계정을 프로비저닝하는 데 일반적으로 2분 정도 걸립니다. 

1. 배포가 완료되면 **리소스로 이동** 을 선택합니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure 리소스 그룹에서 Azure Data Share 리소스를 만듭니다.

Azure CLI에 대한 환경을 준비하는 것으로 시작합니다.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

다음 명령을 사용하여 리소스를 만듭니다.

1. [az account set](/cli/azure/account#az_account_set) 명령을 사용하여 구독을 현재 기본 구독으로 설정합니다.

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. [az provider register](/cli/azure/provider#az_provider_register) 명령을 실행하여 리소스 공급자를 등록합니다.

   ```azurecli
   az provider register --name "Microsoft.DataShare"
   ```

1. [az group create](/cli/azure/group#az_group_create) 명령을 실행하여 리소스 그룹을 만들거나 기존 리소스 그룹을 사용합니다.

   ```azurecli
   az group create --name testresourcegroup --location "East US 2"
   ```

1. [az datashare account create](/cli/azure/ext/datashare/datashare/account#ext_datashare_az_datashare_account_create) 명령을 실행하여 Data Share 계정을 만듭니다.

   ```azurecli
   az datashare account create --resource-group testresourcegroup --name datashareaccount --location "East US 2" 
   ```

   [az datashare account list](/cli/azure/ext/datashare/datashare/account#ext_datashare_az_datashare_account_list) 명령을 실행하여 Data Share 계정을 확인합니다.

   ```azurecli
   az datashare account list --resource-group testresourcegroup
   ```

---

## <a name="create-a-share"></a>공유 만들기

### <a name="portal"></a>[포털](#tab/azure-portal)

1. Data Share 개요 페이지로 이동합니다.

    ![데이터 공유](./media/share-receive-data.png "데이터 공유") 

1. **Start sharing your data**(데이터 공유 시작)를 선택합니다.

1. **만들기** 를 선택합니다.   

1. 공유에 대한 세부 정보를 입력합니다. 이름, 공유 유형, 공유 콘텐츠의 설명 및 사용 약관(선택 사항)을 지정합니다. 

    ![EnterShareDetails](./media/enter-share-details.png "공유 세부 정보 입력") 

1. **계속** 을 선택합니다.

1. 공유에 데이터 세트를 추가하려면 **데이터 세트 추가** 를 선택합니다. 

    ![공유에 데이터 세트 추가](./media/datasets.png "데이터 세트")

1. 추가하려는 데이터 세트 형식을 선택합니다. 이전 단계에서 선택한 공유 유형(스냅샷 또는 내부)에 따라 다른 데이터 세트 유형 목록이 표시됩니다. Azure SQL Database 또는 Azure Synapse Analytics(이전의 Azure SQL DW)에서 공유하는 경우 테이블에 나열할 SQL 자격 증명을 입력하라는 메시지가 표시됩니다.

    ![AddDatasets](./media/add-datasets.png "데이터 세트 추가")    

1. 공유하려는 개체로 이동하여 '데이터 세트 추가'를 선택합니다. 

    ![SelectDatasets](./media/select-datasets.png "데이터 세트 선택")    

1. 받는 사람 탭에서 '+ 받는 사람 추가'를 선택하여 데이터 소비자의 이메일 주소를 입력합니다. 

    ![AddRecipients](./media/add-recipient.png "수신자 추가") 

1. **계속** 을 선택합니다.

1. 스냅샷 공유 유형을 선택한 경우 데이터 소비자에게 데이터 업데이트를 제공하도록 스냅샷 일정을 구성할 수 있습니다. 

    ![EnableSnapshots](./media/enable-snapshots.png "스냅샷 사용") 

1. 시작 시간과 되풀이 간격을 선택합니다. 

1. **계속** 을 선택합니다.

1. 검토 + 만들기 탭에서 패키지 콘텐츠, 설정, 받는 사람 및 동기화 설정을 검토합니다. **만들기** 를 선택합니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [az storage account create](/cli/azure/storage/account#az_storage_account_create) 명령을 실행하여 Data Share를 만듭니다.

   ```azurecli
   az storage account create --resource-group testresourcegroup --name ContosoMarketplaceAccount
   ```

1. [az storage container create](/cli/azure/storage/container#az_storage_container_create) 명령을 사용하여 이전 명령에서 공유에 대한 컨테이너를 만듭니다.

   ```azurecli
   az storage container create --name ContosoMarketplaceContainer --account-name ContosoMarketplaceAccount
   ```

1. [az datashare create](/cli/azure/ext/datashare/datashare#ext_datashare_az_datashare_create) 명령을 실행하여 Data Share를 만듭니다.

   ```azurecli
   az datashare create --resource-group testresourcegroup \
     --name ContosoMarketplaceDataShare --account-name ContosoMarketplaceAccount \
     --description "Data Share" --share-kind "CopyBased" --terms "Confidential"
   ```

1. [az datashare invitation create](/cli/azure/ext/datashare/datashare/invitation#ext_datashare_az_datashare_invitation_create) 명령을 사용하여 지정된 주소에 대한 초대를 만듭니다.

   ```azurecli
   az datashare invitation create --resource-group testresourcegroup \
     --name DataShareInvite --share-name ContosoMarketplaceDataShare \
     --account-name ContosoMarketplaceAccount --target-email "jacob@fabrikam"
   ```

---

이제 Azure Data Share가 생성되었고 Data Share의 받는 사람이 초대를 수락할 준비가 되었습니다.

## <a name="clean-up-resources"></a>리소스 정리

리소스가 더 이상 필요하지 않으면 **Data Share 개요** 페이지로 이동하고 **삭제** 를 선택하여 제거합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Data Share를 만들고 받는 사람을 초대하는 방법을 알아보았습니다. 데이터 소비자가 데이터 공유를 수락하고 받는 방법을 알아보려면, 데이터 수락 및 받기 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [자습서: Azure Data Share를 사용하여 데이터 수락 및 받기](subscribe-to-data-share.md)
