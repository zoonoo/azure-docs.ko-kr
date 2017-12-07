---
title: "Azure Data Factory를 사용하여 SQL Server에서 BLob Storage로 데이터 복사 | Microsoft Docs"
description: "Azure Data Factory에서 자체 호스팅 통합 런타임을 사용하여 온-프레미스 데이터 저장소에서 Azure 클라우드로 데이터를 복사하는 방법을 알아봅니다."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/30/2017
ms.author: jingwang
ms.openlocfilehash: ba47f3e3f331929b884f27f49bf6e484ff363765
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2017
---
# <a name="tutorial-copy-data-from-on-premises-sql-server-to-azure-blob-storage"></a>자습서: 온-프레미스 SQL Server에서 Azure Blob Storage로 데이터 복사
이 자습서에서는 Azure PowerShell을 사용하여 온-프레미스 SQL Server 데이터베이스에서 Azure Blob 저장소로 데이터를 복사하는 Data Factory 파이프라인을 만듭니다. 온-프레미스와 클라우드 데이터 저장소 간에 데이터를 이동하는, 자체 호스팅된 통합 런타임을 생성하고 사용합니다. 

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. 일반 공급(GA)되는 Data Factory 버전 1 서비스를 사용하는 경우 [Data Factory 버전 1 설명서](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.
> 
> 이 문서는 Data Factory 서비스의 자세한 소개를 제공하지 않습니다. Azure Data Factory 서비스 소개는 [Azure Data Factory 소개](introduction.md)를 참조하세요. 

이 자습서에서 수행하는 단계는 다음과 같습니다.

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다.
> * 자체 호스팅 통합 런타임을 만듭니다.
> * SQL Server 및 Azure Storage 연결된 서비스를 만듭니다. 
> * SQL Server 및 Azure Blob 데이터 집합을 만듭니다.
> * 데이터를 이동하는 복사 작업으로 파이프라인을 만듭니다.
> * 파이프라인 실행을 시작합니다.
> * 파이프라인 실행을 모니터링합니다.

## <a name="prerequisites"></a>필수 조건
### <a name="azure-subscription"></a>Azure 구독
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

### <a name="azure-roles"></a>Azure 역할
Data Factory 인스턴스를 만들려면 Azure에 로그인하는 데 사용할 사용자 계정은 **참여자** 또는 **소유자** 역할의 구성원이거나, 또는 Azure 구독의 **관리자**이어야 합니다. Azure Portal에서 오른쪽 위 모서리에 있는 **사용자 이름**을 클릭한 다음 **권한**을 선택하면 구독에 따른 사용자 권한을 볼 수입니다. 여러 구독에 액세스할 수 있는 경우 적절한 구독을 선택합니다. 사용자를 역할에 추가하는 방법에 대한 샘플 지침은 [역할 추가](../billing/billing-add-change-azure-subscription-administrator.md) 문서를 참조하세요.

### <a name="sql-server-201420162017"></a>Microsoft SQL Server 2014/2016/2017
이 자습서에서는 온-프레미스 SQL Server 데이터베이스를 **원본** 데이터 저장소로 사용합니다. 이 자습서에서 만든 데이터 팩터리의 파이프라인은 온-프레미스 SQL Server 데이터베이스(원본)에서 Azure Blob 저장소(싱크)로 데이터를 복사합니다. SQL Server 데이터베이스에 **emp**라는 테이블을 만들고 테이블에 동일한 두 개의 샘플 항목을 삽입합니다. 

1. 컴퓨터에서 **Microsoft SQL Server Management Studio**를 시작합니다. 컴퓨터에 Microsoft SQL Server Management Studio가 없는 경우 [다운로드 센터](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms)에서 받아 설치합니다. 
2. 자격 증명을 사용하여 SQL server에 연결합니다. 
3. 샘플 데이터베이스 만들기 트리 뷰에서 **데이터베이스**를 마우스 오른쪽 단추로 클릭하고 **새 데이터베이스**를 클릭합니다. **새 데이터베이스** 대화 상자에서 데이터베이스의 **이름**을 입력하고 **확인**을 클릭합니다. 
4. 데이터베이스에 대해 다음 쿼리 스크립트를 실행하면 **emp** 테이블이 생성되고 일부 샘플 데이터가 이 테이블에 삽입됩니다. 트리 뷰에서 생성된 **데이터베이스**를 마우스 오른쪽 단추로 클릭하고 **새 쿼리**를 클릭합니다. 

    ```sql   
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
        CONSTRAINT PK_emp PRIMARY KEY (ID)
    )

    INSERT INTO emp VALUES ('John', 'Doe')
    INSERT INTO emp VALUES ('Jane', 'Doe')
    GO
    ```

### <a name="azure-storage-account"></a>Azure Storage 계정
이 자습서에서는 범용 Azure Storage 계정(특히 Blob Storage)을 **대상/싱크** 데이터 저장소로 사용합니다. 범용 Azure Storage 계정이 없는 경우 [저장소 계정 만들기](../storage/common/storage-create-storage-account.md#create-a-storage-account)를 참조하여 새로 만듭니다. 이 자습서에서 만든 데이터 팩터리의 파이프라인은 온-프레미스 SQL Server 데이터베이스(원본)에서 이 Azure Blob 저장소(싱크)로 데이터를 복사합니다. 

#### <a name="get-storage-account-name-and-account-key"></a>저장소 계정 이름 및 계정 키 가져오기
이 자습서에서 Azure Storage 계정 이름 및 키를 사용합니다. 다음 프로시저에서는 저장소 계정 이름 및 키를 가져오는 단계를 제공합니다. 

1. 웹 브라우저를 시작하고 [Azure Portal](https://portal.azure.com)로 이동합니다. Azure 사용자 이름과 암호를 사용하여 로그인합니다. 
2. 왼쪽 메뉴에서 **더 많은 서비스>**를 클릭하고 **저장소** 키워드를 사용하여 필터링하고 **저장소 계정**을 선택합니다.

    ![저장소 계정 검색](media/tutorial-hybrid-copy-powershell/search-storage-account.png)
3. 저장소 계정 목록에서 저장소 계정(필요한 경우)을 필터링한 다음 **저장소 계정**을 선택합니다. 
4. **저장소 계정** 페이지의 메뉴에서 **액세스 키**를 선택합니다.

    ![저장소 계정 이름 및 키 가져오기](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)
5. **저장소 계정 이름** 및 **key1** 필드의 값을 클립보드에 복사합니다. 메모장이나 다른 편집기에 붙여넣고 저장합니다. 자습서에서 저장소 계정 이름 및 키를 사용합니다. 

#### <a name="create-the-adftutorial-container"></a>adftutorial 컨테이너 만들기 
이 섹션에서는 Azure Blob Storage에 **adftutorial**이라는 Blob 컨테이너를 만듭니다. 

1. **저장소 계정** 페이지에서 **개요**로 전환한 다음, **Blobs**을 클릭합니다. 

    ![Blob 옵션 선택](media/tutorial-hybrid-copy-powershell/select-blobs.png)
1. **Blob service** 페이지의 도구 모음에서 **+ 컨테이너**를 클릭합니다. 

    ![컨테이너 단추 추가](media/tutorial-hybrid-copy-powershell/add-container-button.png)
3. **새 컨테이너** 대화 상자에서 **adftutorial**을 이름으로 입력하고 **확인**을 클릭합니다. 

    ![컨테이너 이름 입력](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)
4. 컨테이너 목록에서 **adftutorial**을 클릭합니다.  

    ![컨테이너를 선택합니다.](media/tutorial-hybrid-copy-powershell/seelct-adftutorial-container.png)
5. **Adftutorial**에 대한 **컨테이너** 페이지를 열린 상태로 유지합니다. 이 자습서의 끝부분에서 출력을 확인하는 데 사용합니다. 데이터 팩터리는 이 컨테이너에서 출력 폴더를 자동으로 만듭니다. 따라서 새로 만들 필요가 없습니다.

    ![컨테이너 페이지](media/tutorial-hybrid-copy-powershell/container-page.png)

### <a name="windows-powershell"></a>Windows PowerShell

#### <a name="install-powershell"></a>PowerShell 설치 
컴퓨터에 아직 최신 PowerShell이 없는 경우 설치합니다. 

1. 웹 브라우저에서 [Azure SDK 다운로드 및 SDK](https://azure.microsoft.com/downloads/) 페이지로 이동합니다. 
2. **명령줄 도구** -> **PowerShell** 섹션에서 **Windows 설치**를 클릭합니다. 
3. Azure PowerShell을 설치하려면 **MSI** 파일을 실행합니다. 

자세한 지침은 [Azure PoweShell 설치 및 구성 방법](/powershell/azure/install-azurerm-ps)을 참조하세요. 

#### <a name="log-in-to-powershell"></a>PowerShell에 로그인

1. 컴퓨터에서 **PowerShell**을 시작합니다. 이 빠른 시작을 완료할 때까지 PowerShell 창을 열어 둡니다. 닫은 후 다시 여는 경우 이러한 명령을 다시 실행해야 합니다.

    ![PowerShell 시작](media/tutorial-hybrid-copy-powershell/search-powershell.png)
1. 다음 명령을 실행하고 Azure Portal에 로그인하는 데 사용할 Azure 사용자 이름 및 암호를 입력합니다.
       
    ```powershell
    Login-AzureRmAccount
    ```        
2. 여러 Azure 구독이 있는 경우 다음 명령을 실행하여 이 계정의 모든 구독을 확인합니다.

    ```powershell
    Get-AzureRmSubscription
    ```
3. 여러 Azure 구독이 있는 경우 다음 명령을 실행하여 사용하려는 구독을 선택합니다. **SubscriptionId**를 Azure 구독의 ID로 바꿉니다.

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```

## <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다.

1. 나중에 PowerShell 명령에서 사용할 리소스 그룹 이름에 대한 변수를 정의합니다. PowerShell에 다음 명령 텍스트를 복사하고, 큰따옴표에 있는 [Azure 리소스 그룹](../azure-resource-manager/resource-group-overview.md)의 이름을 지정하고, 명령을 실행합니다. 예: `"adfrg"` 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup"
    ```
2. 새 리소스 그룹을 만들려면 다음 명령을 실행합니다. 

    ```powershell
    New-AzureRmResourceGroup $resourceGroupName $location
    ``` 

    리소스 그룹이 이미 있는 경우 덮어쓰지 않는 것이 좋습니다. `$resourceGroupName` 변수에 다른 값을 할당하고 명령을 다시 시도하세요.
3. 나중에 PowerShell 명령에서 사용할 수 있는 데이터 팩터리 이름에 대한 변수를 정의합니다. 이름은 문자 또는 숫자로 시작해야 하며 문자, 숫자 및 대시(-) 문자를 포함할 수 있습니다.

    > [!IMPORTANT]
    >  데이터 팩터리 이름을 전역적으로 고유한 이름으로 업데이트합니다. 예를 들어 ADFTutorialFactorySP1127이라는 이름을 사용합니다. 

    ```powershell
    $dataFactoryName = "ADFTutorialFactory"
    ```
1. 데이터 팩터리의 위치에 대한 변수를 정의합니다. 

    ```powershell
    $location = "East US"
    ```  
5. 데이터 팩터리를 만들려면 다음 **Set-AzureRmDataFactoryV2** cmdlet을 실행합니다. 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName 
    ```

다음 사항에 유의하세요.

* Azure Data Factory 이름은 전역적으로 고유해야 합니다. 다음 오류가 표시되면 이름을 변경하고 다시 시도하세요.

    ```
    The specified Data Factory name 'ADFv2TutorialDataFactory' is already in use. Data Factory names must be globally unique.
    ```

* Data Factory 인스턴스를 만들려면 Azure에 로그인하는 데 사용할 사용자 계정은 **참여자** 또는 **소유자** 역할의 구성원이거나, 또는 Azure 구독의 **관리자**이어야 합니다.
* 현재 미국 동부, 미국 동부 2 및 유럽 서부 지역에서만 Data Factory 버전 2를 사용하여 데이터 팩터리를 만들 수 있습니다. 데이터 팩터리에서 사용되는 데이터 저장소(Azure Storage, Azure SQL Database 등) 및 계산(HDInsight 등)은 다른 지역에 있을 수 있습니다.

## <a name="create-a-self-hosted-ir"></a>자체 호스팅 IR 만들기

이 섹션에서는 자체 호스팅 통합 런타임을 만들고 이를 Microsoft SQL Server 데이터베이스와 함께 온-프레미스 컴퓨터에 연결합니다. 자체 호스팅된 통합 런타임은 사용자 컴퓨터의 Microsoft SQL Server에서 Azure Blob 저장소로 데이터를 복사하는 구성 요소입니다. 

1. Integration Runtime의 이름에 대한 변수를 만듭니다. 고유한 이름을 사용하고 이름을 기록해 둡니다. 이 자습서의 뒷부분에서 사용합니다. 

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```
1. 자체 호스팅 통합 런타임을 만듭니다. 

   ```powershell
   Set-AzureRmDataFactoryV2IntegrationRuntime -Name $integrationRuntimeName -Type SelfHosted -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName
   ```

   샘플 출력은 다음과 같습니다.

   ```json
    Id                : /subscriptions/<subscription ID>/resourceGroups/ADFTutorialResourceGroup/providers/Microsoft.DataFactory/factories/onpremdf0914/integrationruntimes/myonpremirsp0914
    Type              : SelfHosted
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Name              : myonpremirsp0914
    Description       :
    ```
 

2. 다음 명령을 실행하여 만든 통합 런타임의 상태를 검색합니다.

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
   ```

   샘플 출력은 다음과 같습니다.

   ```json
   Nodes                     : {}
   CreateTime                : 9/14/2017 10:01:21 AM
   InternalChannelEncryption :
   Version                   :
   Capabilities              : {}
   ScheduledUpdateDate       :
   UpdateDelayOffset         :
   LocalTimeZoneOffset       :
   AutoUpdate                :
   ServiceUrls               : {eu.frontend.clouddatahub.net, *.servicebus.windows.net}
   ResourceGroupName         : <ResourceGroup name>
   DataFactoryName           : <DataFactory name>
   Name                      : <Integration Runtime name>
   State                     : NeedRegistration
   ```

3. 다음 명령을 실행하여 **인증 키**를 검색하여 자체 호스팅 통합 런타임을 클라우드의 Data Factory 서비스에 등록합니다. 

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
   ```

   샘플 출력은 다음과 같습니다.

   ```json
   {
       "AuthKey1":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=",
       "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy="
   }
   ```    
4. 다음 단계에서 컴퓨터에 설치할 자체 호스팅된 통합 런타임을 등록하기 위한 키(따옴표 제외) 중 하나를 복사합니다.  

## <a name="install-integration-runtime"></a>Integration Runtime 설치
1. 로컬 Windows 컴퓨터에서 자체 호스팅된 Integration Runtime을 [다운로드](https://www.microsoft.com/download/details.aspx?id=39717)하고 설치를 실행합니다. 
2. **Microsoft Integration Runtime 설치 마법사 시작**에서 **다음**을 클릭합니다.  
3. **최종 사용자 사용권 계약** 페이지에서 사용권 계약에 동의하고 **다음**을 클릭합니다. 
4. **대상 폴더** 페이지에서 **다음**을 클릭합니다. 
5. **Microsoft Integration Runtime을 설치할 준비가 됨**에서 **설치**를 클릭합니다. 
6. 컴퓨터를 사용하지 않는 동안 절전 또는 최대 절전 모드로 전환되도록 구성하는 방법에 대한 경고 메시지가 표시되면 **확인**을 클릭합니다. 
7. **전원 옵션** 창이 표시되면 그것을 닫은 후 설치 창으로 전환합니다. 
8. **Microsoft Integration Runtime 설치 마법사 완료** 페이지에서 **마침**을 클릭합니다.
9. **Integration Runtime(자체 호스팅) 등록** 페이지에 이전 섹션에서 저장한 키를 붙여넣고 **등록**을 클릭합니다. 

   ![통합 런타임 등록](media/tutorial-hybrid-copy-powershell/register-integration-runtime.png)
2. 자체 호스팅 통합 런타임이 성공적으로 등록되면 다음 메시지가 표시됩니다.

   ![성공적으로 등록되었습니다.](media/tutorial-hybrid-copy-powershell/registered-successfully.png)

3. **새로운 Integration Runtime(자체 호스팅) 노드** 페이지에서 **다음**을 클릭합니다. 

    ![새 Integration Runtime 노드 페이지](media/tutorial-hybrid-copy-powershell/new-integration-runtime-node-page.png)
4. **인트라넷 통신 채널**에서 **건너뛰기**를 클릭합니다. 다중 노드 Integration Runtime 환경에서 노드 내 통신을 보호하기 위한 TLS/SSL 인증을 선택할 수 있습니다. 

    ![인트라넷 통신 채널 페이지](media/tutorial-hybrid-copy-powershell/intranet-communication-channel-page.png)
5. **Integration Runtime(자체 호스팅) 등록** 페이지에서 **구성 관리자 시작**을 클릭합니다. 
6. 노드가 클라우드 서비스에 연결되면 다음 페이지가 표시됩니다.

   ![노드가 연결됨](media/tutorial-hybrid-copy-powershell/node-is-connected.png)
7. 이제, Microsoft SQL Server 데이터베이스에 대한 연결을 테스트합니다.

    ![진단 탭](media/tutorial-hybrid-copy-powershell/config-manager-diagnostics-tab.png)   

    - **Configuration Manager** 창에서 **진단** 탭으로 전환합니다.
    - **데이터 원본 유형**에 대한 **SqlServer**를 선택합니다.
    - **서버** 이름을 입력합니다.
    - **데이터베이스** 이름을 입력합니다. 
    - **인증** 모드를 선택합니다. 
    - **사용자** 이름을 입력합니다. 
    - 사용자 이름에 대한 **암호**를 입력합니다.
    - 통합 런타임을 Microsoft SQL Server에 연결할 수 있는지 확인하려면 **테스트**를 클릭합니다. 연결이 성공적인 경우 녹색 확인 표시가 나타납니다. 그렇지 않다면 실패와 관련된 오류 메시지가 나타납니다. 모든 문제를 해결하고 통합 런타임을 Microsoft SQL Server에 연결할 수 있는지 확인합니다.
    - 이러한 값(인증 형식, 서버, 데이터베이스, 사용자, 암호)을 적어 둡니다. 이 자습서의 뒷부분에서 사용됩니다. 
    
      
## <a name="create-linked-services"></a>연결된 서비스 만들기
데이터 팩터리에서 연결된 서비스를 만들어 데이터 저장소 및 계산 서비스를 데이터 팩터리에 연결합니다. 이 자습서에서는 Azure Storage Account 및 온-프레미스 SQL Server를 데이터 저장소에 연결합니다. 연결된 서비스에는 Data Factory 서비스가 런타임에 연결하는 데 사용하는 연결 정보가 있습니다. 

### <a name="create-an-azure-storage-linked-service-destinationsink"></a>Azure Storage 연결된 서비스(대상/싱크) 만들기
이 단계에서는 Azure Storage 계정을 데이터 팩터리에 연결합니다.

1. **C:\ADFv2Tutorial** 폴더에 다음 내용이 포함된 **AzureStorageLinkedService.json**이라는 JSON 파일을 만듭니다. ADFv2Tutorial 폴더가 아직 없는 경우 만듭니다.  

    > [!IMPORTANT]
    > 파일을 저장하기 전에 &lt;accountName&gt;과 &lt;accountKey&gt;를 **Azure 저장소 계정**의 이름과 키로 바꿉니다. 앞에서 [필수 구성 요소](#get-storage-account-name-and-account-key)로 적어 두었습니다.

   ```json
    {
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>;EndpointSuffix=core.windows.net"
                }
            }
        },
        "name": "AzureStorageLinkedService"
    }
   ```

    메모장을 사용하는 경우 **다른 이름으로 저장** 대화 상자의 **파일 형식** 필드에서 **모든 파일**을 선택합니다. 선택하지 않으면 파일에 `.txt` 확장이 추가됩니다. 예: `AzureStorageLinkedService.json.txt`. 메모장에서 파일을 열기 전에 파일 탐색기에서 파일을 만들면 **알려진 파일 형식의 확장명 숨기기** 옵션이 기본적으로 설정되어 `.txt` 확장이 보이지 않을 수 있습니다. `.txt` 확장을 제거한 후 다음 단계로 넘어갑니다. 
2. **Azure PowerShell**에서 **C:\ADFv2Tutorial** 폴더로 전환합니다.

   **Set-AzureRmDataFactoryV2LinkedService** cmdlet을 실행하여 **AzureStorageLinkedService** 연결된 서비스를 만듭니다. 

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
   ```

   샘플 출력은 다음과 같습니다.

    ```json
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

    "파일을 찾을 수 없음" 오류가 발생하는 경우. `dir` 명령을 실행하여 파일이 있는지 확인합니다. 파일 이름에 `.txt` 확장명이 있으면(예: AzureStorageLinkedService.json.txt) 확장명을 제거하고 PowerShell 명령을 다시 실행합니다. 

### <a name="create-and-encrypt-a-sql-server-linked-service-source"></a>SQL Server 연결된 서비스(원본) 만들기 및 암호화
이 단계에서 온-프레미스 SQL Server를 데이터 팩터리에 연결합니다.

1. 다음 내용을 사용하여 **C:\ADFv2Tutorial** 폴더에서 **SqlServerLinkedService.json**이라는 JSON 파일을 만듭니다. Microsoft SQL Server에 연결하는 데 사용하는 **인증**에 기초하여 올바른 선택을 합니다.  

    > [!IMPORTANT]
    > Microsoft SQL Server에 연결하는 데 사용하는 **인증**에 기초하여 올바른 선택을 합니다.

    **SQL 인증(sa)를 사용하는 경우 다음 JSON 정의를 복사합니다.**

    ```json
    {
        "properties": {
            "type": "SqlServer",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
                }
            },
            "connectVia": {
                "type": "integrationRuntimeReference",
                "referenceName": "<integration runtime name>"
            }
        },
        "name": "SqlServerLinkedService"
    }
   ```    
    **Windows 인증을 사용하는 경우 다음 JSON 정의를 복사합니다.**

    ```json
    {
        "properties": {
            "type": "SqlServer",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=<server>;Database=<database>;Integrated Security=True"
                },
                "userName": "<user> or <domain>\\<user>",
                "password": {
                    "type": "SecureString",
                    "value": "<password>"
                }
            },
            "connectVia": {
                "type": "integrationRuntimeReference",
                "referenceName": "<integration runtime name>"
            }
        },
        "name": "SqlServerLinkedService"
    }    
    ```
    > [!IMPORTANT]
    > - Microsoft SQL Server에 연결하는 데 사용하는 **인증**에 기초하여 올바른 선택을 합니다.
    > - **&lt;integration** **runtime** **name>**을 통합 런타임 이름으로 바꿉니다.
    > - 파일을 저장하기 전에 **&lt;servername>**, **&lt;databasename>**, **&lt;username>** 및 **&lt;password>**를 Microsoft SQL Server의 값으로 바꿉니다.
    > - 슬래시 문자(`\`)를 사용자 계정 또는 서버 이름에 사용해야 할 경우 이스케이프 문자(`\`)를 사용합니다. 예: `mydomain\\myuser`. 

2. 중요한 데이터(사용자 이름, 암호, 등)를 암호화하려면 **New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential** cmdlet을 실행합니다. 이 암호화를 사용하면 DPAPI(데이터 보호 응용 프로그램 프로그래밍 인터페이스)를 사용하여 자격 증명을 암호화합니다. 암호화된 자격 증명은 자체 호스팅 통합 런타임 노드(로컬 컴퓨터)에 로컬로 저장됩니다. 출력 페이로드는 암호화된 자격 증명을 포함하는 다른 JSON 파일(이 경우 'encryptedLinkedService.json')로 리디렉션될 수 있습니다.
    
   ```powershell
   New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -IntegrationRuntimeName $integrationRuntimeName -File ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
   ```

3. **EncryptedSqlServerLinkedService**를 생성하는 다음 명령을 실행합니다.

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -File ".\encryptedSqlServerLinkedService.json"
   ```


## <a name="create-datasets"></a>데이터 집합 만들기
이 단계에서는 복사 작업(온-프레미스 SQL Server 데이터베이스 = > Azure Blob Storage)의 입력 및 출력 데이터를 나타내는 입력 및 출력 데이터 집합을 만듭니다.

### <a name="create-a-dataset-for-source-sql-database"></a>원본 SQL Database에 대한 데이터 집합 만들기
이 단계에서는 Microsoft SQL Server 데이터베이스에 들어 있는 데이터를 나타내는 데이터 집합을 정의합니다. 데이터 집합은 **SqlServerTable** 형식입니다. 이 데이터 집합은 이전 단계에서 만든 **Microsoft SQL Server 연결된 서비스**를 참조합니다. 연결된 서비스에는 Data Factory 서비스가 런타임에 Microsoft SQL Server에 연결하는 데 사용하는 **연결 정보**가 있습니다. 이 데이터 집합은 데이터가 들어 있는 데이터베이스에 **SQL 테이블**을 지정합니다. 이 자습서에서는 원본 데이터가 포함된 `emp` 테이블입니다. 

1. **C:\ADFv2Tutorial** 폴더에 다음 내용이 포함된 **SqlServerDataset.json**이라는 JSON 파일을 만듭니다.  

    ```json
    {
       "properties": {
            "type": "SqlServerTable",
            "typeProperties": {
                "tableName": "dbo.emp"
            },
            "structure": [
                 {
                    "name": "ID",
                    "type": "String"
                },
                {
                    "name": "FirstName",
                    "type": "String"
                },
                {
                    "name": "LastName",
                    "type": "String"
                }
            ],
            "linkedServiceName": {
                "referenceName": "EncryptedSqlServerLinkedService",
                "type": "LinkedServiceReference"
            }
        },
        "name": "SqlServerDataset"
    }
    ```

2. **Set-AzureRmDataFactoryV2Dataset** cmdlet을 실행하여 **SqlServerDataset** 데이터 집합을 만듭니다.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerDataset" -File ".\SqlServerDataset.json"
    ```

    샘플 출력은 다음과 같습니다.

    ```json
    DatasetName       : SqlServerDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Structure         : {"name": "ID" "type": "String", "name": "FirstName" "type": "String", "name": "LastName" "type": "String"}
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerTableDataset
    ```

### <a name="create-a-dataset-for-sink-azure-blob-storage"></a>싱크 Azure Blob Storage에 대한 데이터 집합 만들기
이 단계에서는 Azure Blob Storage에 복사되는 데이터를 나타내는 데이터 집합을 정의합니다. 데이터 집합은 **AzureBlob** 형식입니다. 이 데이터 집합은 이 자습서의 앞부분에서 만든 **Azure Storage 연결된 서비스**를 참조합니다. 연결된 서비스에는 Data Factory가 런타임에 Azure Storage Account에 연결하는 데 사용하는 **연결 정보**가 있습니다. 이 **데이터 집합**은 Microsoft SQL Server 데이터베이스에서 데이터를 복사할 Azure 저장소의 **폴더**를 지정합니다. 이 자습서에서 폴더는 `adftutorial`가 Blob 컨테이너이며, `fromonprem`이 폴더인 `adftutorial/fromonprem`입니다. 

1. **C:\ADFv2Tutorial** 폴더에 다음 내용이 포함된 **AzureBlobDataset.json**이라는 JSON 파일을 만듭니다.

    ```json
    {
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "adftutorial/fromonprem",
                "format": {
                    "type": "TextFormat"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            }
        },
        "name": "AzureBlobDataset"
    }
    ```

2. **Set-AzureRmDataFactoryV2Dataset** cmdlet을 실행하여 **AzureBlobDataset** 데이터 집합을 만듭니다.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureBlobDataset" -File ".\AzureBlobDataset.json"
    ```

    샘플 출력은 다음과 같습니다.

    ```json
    DatasetName       : AzureBlobDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

## <a name="create-a-pipeline"></a>파이프라인을 만듭니다.
이 자습서에서는 복사 활동을 사용하여 파이프라인을 만듭니다. 복사 활동은 **SqlServerDataset**을 입력 데이터 집합으로 그리고 **AzureBlobDataset** 출력 데이터 집합으로 사용합니다. 원본 형식은 **SqlSource**로 설정되고 싱크 형식은 **BlobSink**로 설정됩니다.

1. **C:\ADFv2Tutorial** 폴더에 다음 내용이 포함된 **SqlServerToBlobPipeline.json**이라는 JSON 파일을 만듭니다.

    ```json
    {
       "name": "SQLServerToBlobPipeline",
        "properties": {
            "activities": [       
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource"
                        },
                        "sink": {
                            "type":"BlobSink"
                        }
                    },
                    "name": "CopySqlServerToAzureBlobActivity",
                    "inputs": [
                        {
                            "referenceName": "SqlServerDataset",
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "AzureBlobDataset",
                            "type": "DatasetReference"
                        }
                    ]
                }
            ]
        }
    }
    ```

2. **Set-AzureRmDataFactoryV2Pipeline** cmdlet을 실행하여 **SQLServerToBlobPipeline** 파이프라인을 만듭니다.

    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SQLServerToBlobPipeline" -File ".\SQLServerToBlobPipeline.json"
    ```

    샘플 출력은 다음과 같습니다.

    ```json
    PipelineName      : SQLServerToBlobPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Activities        : {CopySqlServerToAzureBlobActivity}
    Parameters        :  
    ```


## <a name="create-a-pipeline-run"></a>파이프라인 실행 만들기
"SQLServerToBlobPipeline" 파이프라인에 대한 파이프라인 실행을 시작하고 이후의 모니터링에 대한 파이프라인 실행 ID를 캡처합니다.

```powershell
$runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'SQLServerToBlobPipeline'
```

## <a name="monitor-the-pipeline-run"></a>파이프라인 실행을 모니터링합니다.

1. 다음 스크립트를 실행하여 **SQLServerToBlobPipeline** 파이프라인의 실행 상태를 계속 확인하고 최종 결과를 출력합니다. PowerShell 창에서 다음 스크립트를 복사/붙여넣기하고 ENTER 키를 누릅니다.

    ```powershell
    while ($True) {
        $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

        if (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
            Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
            Start-Sleep -Seconds 30
        }
        else {
            Write-Host "Pipeline 'SQLServerToBlobPipeline' run finished. Result:" -foregroundcolor "Yellow"
            $result
            break
        }
    }
    ```

    샘플 실행의 출력은 다음과 같습니다.

    ```jdon
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : copy
    PipelineRunId     : 4ec8980c-62f6-466f-92fa-e69b10f33640
    PipelineName      : SQLServerToBlobPipeline
    Input             :  
    Output            :  
    LinkedServiceName :
    ActivityRunStart  : 9/13/2017 1:35:22 PM
    ActivityRunEnd    : 9/13/2017 1:35:42 PM
    DurationInMs      : 20824
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    ```

3. 다음과 같이 **SQLServerToBlobPipeline** 파이프라인의 실행 ID를 가져오고, 다음 명령을 실행하여 자세한 작업 실행 결과를 확인할 수 있습니다. 

    ```powershell
    Write-Host "Pipeline 'SQLServerToBlobPipeline' run result:" -foregroundcolor "Yellow"
    ($result | Where-Object {$_.ActivityName -eq "CopySqlServerToAzureBlobActivity"}).Output.ToString()
    ```

    샘플 실행의 출력은 다음과 같습니다.

    ```json
    {
      "dataRead": 36,
      "dataWritten": 24,
      "rowsCopied": 2,
      "copyDuration": 3,
      "throughput": 0.01171875,
      "errors": [],
      "effectiveIntegrationRuntime": "MyIntegrationRuntime",
      "billedDuration": 3
    }
    ```

## <a name="verify-the-output"></a>출력 확인
파이프라인은 자동으로 `adftutorial` Blob 컨테이너에서 `fromonprem`이라는 출력 폴더를 만듭니다. 출력 폴더에서 **dbo.emp.txt** 파일이 표시되는지 확인합니다. 

1. Azure Portal의 **adftutorial** 컨테이너 페이지에서 출력 폴더를 보려면 **새로 고침**을 클릭합니다.

    ![만들어진 출력 폴더](media/tutorial-hybrid-copy-powershell/fromonprem-folder.png)
2. 리소스 목록에서 `fromonprem`을 클릭합니다. 
3. `dbo.emp.txt`이라는 파일이 표시됨을 확인합니다.

    ![출력 파일](media/tutorial-hybrid-copy-powershell/fromonprem-file.png)


## <a name="next-steps"></a>다음 단계
이 샘플의 파이프라인은 Azure Blob 저장소의 한 위치에서 다른 위치로 데이터를 복사합니다. 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다.
> * 자체 호스팅 통합 런타임을 만듭니다.
> * SQL Server 및 Azure Storage 연결된 서비스를 만듭니다. 
> * SQL Server 및 Azure Blob 데이터 집합을 만듭니다.
> * 데이터를 이동하는 복사 작업으로 파이프라인을 만듭니다.
> * 파이프라인 실행을 시작합니다.
> * 파이프라인 실행을 모니터링합니다.

Azure Data Factory에서 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.

원본에서 대상으로 데이터를 대량으로 복사하는 방법을 알아보려면 다음 자습서로 진행하세요.

> [!div class="nextstepaction"]
>[대량 데이터 복사](tutorial-bulk-copy.md)
