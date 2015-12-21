<properties 
   pageTitle="데이터 레이크 저장소 시작 | Azure" 
   description="Azure PowerShell을 사용하여 데이터 레이크 저장소 계정을 만들고 기본 작업을 수행합니다." 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="12/04/2015"
   ms.author="nitinme"/>

# Azure PowerShell을 사용하여 Azure 데이터 레이크 저장소 시작

> [AZURE.SELECTOR]
- [Using Portal](data-lake-store-get-started-portal.md)
- [Using PowerShell](data-lake-store-get-started-powershell.md)
- [Using .NET SDK](data-lake-store-get-started-net-sdk.md)
- [Using Azure CLI](data-lake-store-get-started-cli.md)

Azure PowerShell을 사용하여 Azure 데이터 레이크 저장소 계정을 만들고 폴더 만들기, 데이터 파일 업로드 및 다운로드, 계정 삭제 등의 기본 작업을 수행하는 방법에 대해 알아봅니다. 데이터 레이크 저장소에 대한 자세한 내용은 [데이터 레이크 저장소 개요](data-lake-store-overview.md)를 참조하세요.

## 필수 조건

이 자습서를 시작하기 전에 다음이 있어야 합니다.

- **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
- 데이터 레이크 저장소 공개 미리 보기를 위해 **Azure 구독을 사용하도록 설정합니다**. [지침](data-lake-store-get-started-portal.md#signup)을 참조하세요.


##Azure PowerShell 1.0 이상 설치

시작하려면 0.9x 버전의 Azure PowerShell을 제거해야 합니다. 설치된 PowerShell의 버전을 확인하려면 PowerShell 창에서 다음 명령을 실행합니다.

	Get-Module *azure*
	
이전 버전을 제거하려면 제어판에서 **프로그램 및 기능**을 실행하고 PowerShell 1.0보다 이전 버전인 경우 설치된 버전을 제거합니다.

Azure PowerShell을 설치하기 위한 두 가지 주요 옵션이 있습니다.

- [PowerShell 갤러리](https://www.powershellgallery.com/). 관리자 권한 PowerShell ISE 또는 관리자 권한 Windows PowerShell 콘솔에서 다음 명령을 실행합니다.

		# Install the Azure Resource Manager modules from PowerShell Gallery
		Install-Module AzureRM
		Install-AzureRM
		
		# Install the Azure Service Management module from PowerShell Gallery
		Install-Module Azure
		
		# Import AzureRM modules for the given version manifest in the AzureRM module
		Import-AzureRM
		
		# Import Azure Service Management module
		Import-Module Azure

	자세한 내용은 [PowerShell 갤러리](https://www.powershellgallery.com/)를 참조하세요.

- [Microsoft WebPI(웹 플랫폼 설치 관리자)](http://aka.ms/webpi-azps). Azure PowerShell 0.9.x가 설치되어 있는 경우 0.9.x를 제거하라는 메시지가 표시됩니다. PowerShell 갤러리에서 Azure PowerShell 모듈을 설치한 경우 일관성 있는 Azure PowerShell 환경이 보장되도록 설치하기 전에 설치 관리자에서 모듈을 제거해야 합니다. 자세한 내용은 [WebPI를 통해 Azure PowerShell 1.0 설치](https://azure.microsoft.com/blog/azps-1-0/)를 참조하세요.

WebPI는 월별 업데이트를 받습니다. PowerShell 갤러리는 지속적으로 업데이트를 받습니다. PowerShell 갤러리에서 설치가 익숙하다면 이는 Azure PowerShell에서 가장 유용한 최신의 첫 번째 채널이 될 것입니다.

## Azure 데이터 레이크 저장소 계정 만들기

1. 데스크톱에서 새 Azure PowerShell 창을 열고 다음 조각을 입력하여 Azure 계정에 로그인하고 구독을 설정한 다음 데이터 레이크 저장소 공급자를 등록합니다. 로그인하라는 메시지가 표시되면 구독 관리자/소유자 중 하나로 로그인해야 합니다.

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

2. Azure 데이터 레이크 저장소 계정을 만듭니다. 지정하는 이름은 소문자와 숫자만 포함해야 합니다.

		$dataLakeStoreName = "<your new Data Lake Store name>"
    	New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

	![Azure 데이터 레이크 저장소 계정 만들기](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Azure 데이터 레이크 저장소 계정 만들기")

3. 계정이 성공적으로 만들어졌는지 확인합니다.

		Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

	이에 대한 출력은 **True**여야 합니다.

## Azure 데이터 레이크 저장소에서 디렉터리 구조 만들기

Azure 데이터 레이크 저장소 계정에서 디렉터리를 만들어 데이터를 관리하고 저장할 수 있습니다.

1. 루트 디렉터리를 지정합니다.

		$myrootdir = "/"

2. 지정된 루트 아래 **mynewdirectory**라는 새 디렉터리를 만듭니다.

		New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/mynewdirectory

3. 새 디렉터리가 성공적으로 만들어졌는지 확인합니다.

		Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -Path $myrootdir

	다음과 유사한 출력이 표시됩니다.

	![디렉터리 확인](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "디렉터리 확인")


## Azure 데이터 레이크 저장소에 데이터 업로드

루트 수준에서 데이터 레이크 저장소에 직접 데이터를 업로드하거나 계정 내에서 만든 디렉터리에 업로드할 수 있습니다. 아래 코드 조각은 이전 섹션에서 만든 디렉터리(**mynewdirectory**)에 일부 샘플 데이터를 업로드하는 방법을 보여 줍니다.

업로드할 일부 샘플 데이터를 찾는 경우 [Azure 데이터 레이크 Git 리포지토리](https://github.com/MicrosoftBigData/AzureDataLake/tree/master/SQLIPSamples/SampleData/AmbulanceData)의 **Ambulance Data** 폴더에 있을 수 있습니다. 파일을 다운로드하고 컴퓨터의 로컬 디렉터리(예: C:\\sampledata)에 저장합니다.

	Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\sampledata\vehicle1_09142014.csv" -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv


## 데이터 레이크 저장소에서 데이터 이름 바꾸기, 다운로드 및 삭제

파일의 이름을 바꾸려면 다음 명령을 사용합니다.

    Move-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

파일을 다운로드하려면 다음 명령을 사용합니다.

	Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"

파일을 삭제하려면 다음 명령을 사용합니다.

	Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv 
	
메시지가 표시되면 **Y**를 입력하여 항목을 삭제합니다. 삭제할 파일이 둘 이상 있는 경우 쉼표로 구분하여 모든 경로를 입력할 수 있습니다.

	Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv

## Azure 데이터 레이크 저장소 계정 삭제

다음 명령을 사용하여 데이터 레이크 저장소 계정을 삭제합니다.

	Remove-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

메시지가 표시되면 **Y**를 입력하여 계정을 삭제합니다.


## 데이터 레이크 저장소 계정을 만드는 다른 방법

- [포털을 사용하여 데이터 레이크 저장소 시작](data-lake-store-get-started-portal.md)
- [.NET SDK를 사용하여 데이터 레이크 저장소 시작](data-lake-store-get-started-net-sdk.md)
- [Azure CLI를 사용하여 데이터 레이크 저장소 시작](data-lake-store-get-started-cli.md)


## 다음 단계

- [데이터 레이크 저장소의 데이터 보호](data-lake-store-secure-data.md)
- [Azure 데이터 레이크 분석에 데이터 레이크 저장소 사용](data-lake-analytics-get-started-portal.md)
- [Azure HDInsight에 데이터 레이크 저장소 사용](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_1210_2015-->