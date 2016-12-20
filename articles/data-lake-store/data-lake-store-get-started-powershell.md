---
title: "Data Lake Store 시작 | Microsoft Docs"
description: "Azure PowerShell을 사용하여 데이터 레이크 저장소 계정을 만들고 기본 작업을 수행합니다."
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: bf85f369-f9aa-4ca1-9ae7-e03a78eb7290
ms.service: data-lake-store
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/02/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: a86fd04a7ec0cffabe42d30132b97777c752bbde
ms.openlocfilehash: e37b698436c067faa20b0e589078927d5955934a


---
# <a name="get-started-with-azure-data-lake-store-using-azure-powershell"></a>Azure PowerShell을 사용하여 Azure 데이터 레이크 저장소 시작
> [!div class="op_single_selector"]
> * [포털](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Azure CLI](data-lake-store-get-started-cli.md)
> * [Node.JS](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Azure PowerShell을 사용하여 Azure 데이터 레이크 저장소 계정을 만들고 폴더 만들기, 데이터 파일 업로드 및 다운로드, 계정 삭제 등의 기본 작업을 수행하는 방법에 대해 알아봅니다. Data Lake Store에 대한 자세한 내용은 [Data Lake Store 개요](data-lake-store-overview.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건
이 자습서를 시작하기 전에 다음이 있어야 합니다.

* **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* **Azure PowerShell 1.0 이상**. [Azure PowerShell 설치 및 구성 방법](/powershell/azureps-cmdlets-docs)을 참조하세요.

## <a name="authentication"></a>인증
이 문서는 Data Lake Store에 Azure 계정 자격 증명을 입력하라는 메시지가 표시되는 보다 간단한 인증 방법을 사용합니다. Data Lake Store 계정 및 파일 시스템에 대한 액세스 수준은 로그인한 사용자의 액세스 수준을 따릅니다. 하지만 Data Lake Store에 인증하는 다른 방법인 **최종 사용자 인증** 또는 **서비스간 인증**도 있습니다. 인증 하는 방법에 대한 지침 및 자세한 내용은 [Azure Active Directory를 사용하여 Data Lake Store로 인증](data-lake-store-authenticate-using-active-directory.md)을 참조하세요.

## <a name="create-an-azure-data-lake-store-account"></a>Azure 데이터 레이크 저장소 계정 만들기
1. 데스크톱에서 새 Windows PowerShell 창을 열고 다음 조각을 입력하여 Azure 계정에 로그인하고 구독을 설정한 다음 Data Lake Store 공급자를 등록합니다. 로그인하라는 메시지가 표시되면 구독 관리자/소유자 중 하나로 로그인해야 합니다.

        # Log in to your Azure account
        Login-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Azure Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
2. Azure 데이터 레이크 저장소 계정은 Azure 리소스 그룹과 연결됩니다. Azure 리소스 그룹을 만드는 작업부터 시작합니다.

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    ![Azure 리소스 그룹 만들기](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Azure 리소스 그룹 만들기")
3. Azure 데이터 레이크 저장소 계정을 만듭니다. 지정하는 이름은 소문자와 숫자만 포함해야 합니다.

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    ![Azure Data Lake Store 계정 만들기](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Azure Data Lake Store 계정 만들기")
4. 계정이 성공적으로 만들어졌는지 확인합니다.

        Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

    이에 대한 출력은 **True**여야 합니다.

## <a name="create-directory-structures-in-your-azure-data-lake-store"></a>Azure 데이터 레이크 저장소에서 디렉터리 구조 만들기
Azure 데이터 레이크 저장소 계정에서 디렉터리를 만들어 데이터를 관리하고 저장할 수 있습니다.

1. 루트 디렉터리를 지정합니다.

        $myrootdir = "/"
2. 지정된 루트 아래 **mynewdirectory** 라는 새 디렉터리를 만듭니다.

        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/mynewdirectory
3. 새 디렉터리가 성공적으로 만들어졌는지 확인합니다.

        Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -Path $myrootdir

    다음과 유사한 출력이 표시됩니다.

    ![디렉터리 확인](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "디렉터리 확인")

## <a name="upload-data-to-your-azure-data-lake-store"></a>Azure 데이터 레이크 저장소에 데이터 업로드
루트 수준에서 데이터 레이크 저장소에 직접 데이터를 업로드하거나 계정 내에서 만든 디렉터리에 업로드할 수 있습니다. 아래 코드 조각은 이전 섹션에서 만든 디렉터리(**mynewdirectory**)에 일부 샘플 데이터를 업로드하는 방법을 보여 줍니다.

업로드할 일부 샘플 데이터를 찾는 경우 **Azure 데이터 레이크 Git 리포지토리** 의 [Ambulance Data](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData)폴더에 있을 수 있습니다. 파일을 다운로드하고 컴퓨터의 로컬 디렉터리(예: C:\sampledata\.)에 저장합니다.

    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\sampledata\vehicle1_09142014.csv" -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv


## <a name="rename-download-and-delete-data-from-your-data-lake-store"></a>데이터 레이크 저장소에서 데이터 이름 바꾸기, 다운로드 및 삭제
파일의 이름을 바꾸려면 다음 명령을 사용합니다.

    Move-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

파일을 다운로드하려면 다음 명령을 사용합니다.

    Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"

파일을 삭제하려면 다음 명령을 사용합니다.

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

메시지가 표시되면 **Y** 를 입력하여 항목을 삭제합니다. 삭제할 파일이 둘 이상 있는 경우 쉼표로 구분하여 모든 경로를 입력할 수 있습니다.

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv

## <a name="delete-your-azure-data-lake-store-account"></a>Azure 데이터 레이크 저장소 계정 삭제
다음 명령을 사용하여 데이터 레이크 저장소 계정을 삭제합니다.

    Remove-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

메시지가 표시되면 **Y** 를 입력하여 계정을 삭제합니다.

## <a name="performance-guidance-while-using-powershell"></a>PowerShell을 사용하는 동안 성능 지침

Data Lake Store와 함께 PowerShell을 사용하는 동안 최상의 성능을 얻기 위해 조정할 수 있는 가장 중요한 설정은 다음과 같습니다.

| 속성            | 기본값 | 설명 |
|---------------------|---------|-------------|
| PerFileThreadCount  | 10      | 이 매개 변수로 각 파일의 업로드 또는 다운로드를 위한 병렬 스레드 수를 선택할 수 있습니다. 이 숫자는 파일당 할당할 수 있는 최대 스레드 수를 나타내지만 시나리오에 따라 스레드 수가 줄어들 수 있습니다(예를 들어 1KB 파일을 업로드하는 경우 20개의 스레드를 요청해도 하나의 스레드가 발생합니다).  |
| ConcurrentFileCount | 10      | 이 매개 변수는 특히 폴더 업로드 또는 다운로드를 위한 것입니다. 이 매개 변수는 업로드 또는 다운로드할 수 있는 동시 파일 수를 결정합니다. 이 숫자는 한 번에 업로드 또는 다운로드할 수 있는 최대 동시 파일 수를 나타내지만 시나리오에 따라 동시 수가 줄어들 수 있습니다(예를 들어 두 개 파일을 업로드하는 경우 15개를 요청해도 두 개의 동시 파일 업로드가 발생합니다). |

**예제**

이 명령은 파일당 20개의 스레드와 100개의 동시 파일을 사용하여 Azure Data Lake Store에서 사용자의 로컬 드라이브로 파일을 다운로드합니다.

    Export-AzureRmDataLakeStoreItem -AccountName <Data Lake Store account name> -PerFileThreadCount 20-ConcurrentFileCount 100 -Path /Powershell/100GB/ -Destination C:\Performance\ -Force -Recurse

### <a name="how-do-i-determine-the-value-to-set-for-these-parameters"></a>이러한 매개 변수에 대한 설정 값은 어떻게 결정하나요?

다음은 사용할 수 있는 몇 가지 지침입니다.

* **1단계: 총 스레드 수 확인** - 사용할 총 스레드 수를 계산하는 것으로 시작합니다. 일반적으로 물리적 코어당 6개의 스레드를 사용해야 합니다.

        Total thread count = total physical cores * 6

    **예제**

    16개의 코어가 있는 D14 VM에서 PowerShell 명령을 실행 중이라고 가정합니다.

        Total thread count = 16 cores * 6 = 96 threads


* **2단계: PerFileThreadCount 계산**  - 파일 크기를 기반으로 PerFileThreadCount를 계산합니다. 2.5GB 미만 파일인 경우 기본값인 10이면 충분하므로 이 매개 변수를 변경하지 않아도 됩니다. 2.5GB 초과 파일인 경우 처음 2.5GB를 기준으로 10개 스레드를 사용하고 파일 크기가 256MB 증가당 1개 스레드를 추가해야 합니다. 더 큰 크기의 파일 범위로 폴더를 복사하는 경우 보다 작은 파일 크기로 그룹화하는 것이 좋습니다. 다른 파일 크기를 사용하면 성능이 저하될 수 있습니다. 유사한 파일 크기를 그룹화할 수 없는 경우 가장 큰 파일 크기를 기준으로 PerFileThreadCount를 설정해야 합니다.

        PerFileThreadCount = 10 threads for the first 2.5GB + 1 thread for each additional 256MB increase in file size

    **예제**

    1GB ~ 10GB 범위의 100개 파일이 있다고 가정하면 수식에서 가장 큰 파일 크기로 10GB를 사용하며 다음과 같습니다.

        PerFileThreadCount = 10 + ((10GB - 2.5GB) / 256MB) = 40 threads

* **3단계: ConcurrentFilecount 계산** - 총 스레드 수와 PerFileThreadCount를 사용하여 다음 수식에 따라 ConcurrentFileCount를 계산합니다.

        Total thread count = PerFileThreadCount * ConcurrentFileCount

    **예제**

    사용 중인 예제 값 기준

        96 = 40 * ConcurrentFileCount

    따라서 **ConcurrentFileCount**는 **2.4**이고 **2**로 반올림할 수 있습니다.

### <a name="further-tuning"></a>추가 조정

작동하는 파일 크기 범위가 다양하므로 추가 조정이 필요할 수 있습니다. 위의 계산은 모든 또는 대부분의 파일이 10GB 범위보다 크거나 비슷한 경우 잘 작동합니다. 그렇지 않고 작은 파일이 많고 파일 크기가 매우 다양한 경우 PerFileThreadCount를 줄이면 됩니다. PerFileThreadCount를 줄여서 ConcurrentFileCount를 늘릴 수 있습니다. 따라서 대부분의 파일이 5GB 범위보다 작다면 계산을 다시 수행할 수 있습니다.

    PerFileThreadCount = 10 + ((5GB - 2.5GB) / 256MB) = 20

즉, **ConcurrentFileCount**는 96/20(4.8)이 되고 **4**로 반올림됩니다.

파일 크기 분포에 따라 **PerFileThreadCount**를 늘리거나 줄여 이러한 설정을 계속해서 조정할 수 있습니다.

### <a name="limitation"></a>제한 사항

* **파일 수가 ConcurrentFileCount보다 작음**: 업로드 중인 파일 수가 계산한 **ConcurrentFileCount**보다 작은 경우 **ConcurrentFileCount**가 파일 수와 같도록 줄여야 합니다. 나머지 스레드를 사용하여 **PerFileThreadCount**를 늘릴 수 있습니다.

* **스레드 수 너무 많음**: 클러스터 크기를 늘리지 않고 스레드 수를 너무 늘리면 성능 저하 위험이 있습니다. CPU에서 컨텍스트 전환 시 경합 문제가 발생할 수 있습니다.

* **동시성 부족**: 동시성이 부족한 경우 클러스터가 너무 작을 수 있습니다. 더 많은 동시성을 제공하도록 클러스터에서 노드 수를 늘릴 수 있습니다

* **제한 오류**: 동시성이 너무 높으면 제한 오류가 표시될 수 있습니다. 제한 오류가 표시되면 동시성을 줄이거나 문의해 주세요.

## <a name="next-steps"></a>다음 단계
* [데이터 레이크 저장소의 데이터 보호](data-lake-store-secure-data.md)
* [Azure 데이터 레이크 분석에 데이터 레이크 저장소 사용](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Azure HDInsight에 데이터 레이크 저장소 사용](data-lake-store-hdinsight-hadoop-use-portal.md)




<!--HONumber=Dec16_HO2-->


