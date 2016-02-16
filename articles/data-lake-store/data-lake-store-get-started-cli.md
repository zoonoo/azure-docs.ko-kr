<properties 
   pageTitle="플랫폼 간 명령줄을 사용하여 데이터 레이크 저장소 시작 | Microsoft Azure"
   description="Azure 플랫폼 간 명령줄을 사용하여 데이터 레이크 저장소 계정을 만들고 기본 작업을 수행합니다." 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="01/04/2016"
   ms.author="nitinme"/>

# Azure 명령줄을 사용하여 Azure 데이터 레이크 저장소 시작

> [AZURE.SELECTOR]
- [Using Portal](data-lake-store-get-started-portal.md)
- [Using PowerShell](data-lake-store-get-started-powershell.md)
- [Using .NET SDK](data-lake-store-get-started-net-sdk.md)
- [Using Azure CLI](data-lake-store-get-started-cli.md)
- [Using Node.js](data-lake-store-manage-use-nodejs.md)

Azure 명령줄 인터페이스를 사용하여 Azure 데이터 레이크 저장소 계정을 만들고 폴더 만들기, 데이터 파일 업로드 및 다운로드, 계정 삭제 등의 기본 작업을 수행하는 방법에 대해 알아봅니다. 데이터 레이크 저장소에 대한 자세한 내용은 [데이터 레이크 저장소 개요](data-lake-store-overview.md)를 참조하세요.

Azure CLI는 Node.js로 구현되며 Windows, Mac, Linux를 포함하여 Node.js를 지원하는 플랫폼에서 사용할 수 있습니다. Azure CLI는 오픈 소스입니다. 소스 코드는 <a href= "https://github.com/azure/azure-xplat-cli">https://github.com/azure/azure-xplat-cli</a>의 GitHub에서 관리됩니다. 이 문서에서는 데이터 레이크 저장소에서 Azure CLI를 사용하는 방법만 설명합니다. Azure CLI를 사용하는 방법에 대한 일반 가이드는 [Azure CLI를 사용하는 방법][azure-command-line-tools]을 참조하세요.


##필수 조건

이 문서를 시작하기 전에 다음이 있어야 합니다.

- **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
- 데이터 레이크 저장소 공개 미리 보기를 위해 **Azure 구독을 사용하도록 설정합니다**. [지침](data-lake-store-get-started-portal.md#signup)을 참조하세요. 
- **Azure CLI** - 설치 및 구성 정보는 [Azure CLI 설치 및 구성](../xplat-cli-install.md)을 참조하세요. CLI를 설치한 후 컴퓨터를 다시 부팅해야 합니다.

##Azure 구독에 로그인

[Azure CLI(Azure 명령줄 인터페이스)에서 Azure 구독에 연결](xplat-cli-connect.md)에서 설명된 단계를 따라 __로그인__ 방법을 사용하여 구독에 연결합니다.


## Azure 데이터 레이크 저장소 계정 만들기

명령 프롬프트, 셸 또는 터미널 세션을 열고 다음 명령을 실행합니다.

1. Azure 구독에 로그인합니다.

		azure login

	웹 페이지를 열고 인증 코드를 입력하라는 메시지가 표시됩니다. Azure 구독에 로그인할 페이지의 지침을 따릅니다.

2. 다음 명령을 사용하여 Azure 리소스 관리자 모드로 전환합니다.

		azure config mode arm


3. 계정에 대한 Azure 구독을 나열합니다.

		azure account list


4. 여러 Azure 구독이 있는 경우 다음 명령을 사용하여 Azure CLI 명령이 사용하는 구독을 설정할 수 있습니다.

		azure account set <subscriptionname>

5. 새 리소스 그룹을 만듭니다. 다음 명령에서 사용하려는 매개 변수 값을 제공합니다.

		azure group create <resourceGroup> <location>

	위치 이름이 공백을 포함하는 경우 이중 따옴표로 묶습니다. 예를 들어 "East US 2"입니다.

5. 데이터 레이크 저장소 계정을 만듭니다.

		azure datalake store account create <dataLakeStoreAccountName> <location> <resourceGroup>

## 데이터 레이크 저장소에서 폴더 만들기

Azure 데이터 레이크 저장소 계정에서 폴더를 만들어 데이터를 관리하고 저장할 수 있습니다. 다음 명령을 사용하여 데이터 레이크 저장소의 루트에 있는 "mynewfolder"라는 폴더를 만듭니다.

	azure datalake store filesystem create <dataLakeStoreAccountName> <path> --folder

예:

	azure datalake store filesystem create mynewdatalakestore /mynewfolder --folder

## 데이터 레이크 저장소에 데이터 업로드

루트 수준에서 데이터 레이크 저장소에 직접 데이터를 업로드하거나 계정 내에서 만든 폴더에 업로드할 수 있습니다. 아래 코드 조각은 이전 섹션에서 만든 폴더(**mynewfolder**)에 일부 샘플 데이터를 업로드하는 방법을 보여 줍니다.

업로드할 일부 샘플 데이터를 찾는 경우 [Azure 데이터 레이크 Git 리포지토리](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData)의 **Ambulance Data** 폴더에 있을 수 있습니다. 파일을 다운로드하고 컴퓨터의 로컬 디렉터리(예: C:\\sampledata)에 저장합니다.

	azure datalake store filesystem import <dataLakeStoreAccountName> "<source path>" "<destination path>"

예:

	azure datalake store filesystem import mynewdatalakestore "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" "/mynewfolder/vehicle1_09142014.csv"


## 데이터 레이크 저장소의 파일 나열

다음 명령을 사용하여 데이터 레이크 저장소 계정의 파일을 나열합니다.

	azure datalake store filesystem list <dataLakeStoreAccountName> <path>

예:

	azure datalake store filesystem list mynewdatalakestore /mynewfolder

이 명령의 출력은 다음과 유사합니다.

	info:    Executing command datalake store filesystem list
	data:    accessTime: 1446245025257
	data:    blockSize: 268435456
	data:    group: NotSupportYet
	data:    length: 1589881
	data:    modificationTime: 1446245105763
	data:    owner: NotSupportYet
	data:    pathSuffix: vehicle1_09142014.csv
	data:    permission: 777
	data:    replication: 0
	data:    type: FILE
	data:    ------------------------------------------------------------------------------------
	info:    datalake store filesystem list command OK

## 데이터 레이크 저장소에서 데이터 이름 바꾸기, 다운로드 및 삭제

* **파일의 이름을 바꾸려면** 다음 명령을 사용합니다.

    	azure datalake store filesystem move <dataLakeStoreAccountName> <path/old_file_name> <path/new_file_name>

	예:

		azure datalake store filesystem move mynewdatalakestore /mynewfolder/vehicle1_09142014.csv /mynewfolder/vehicle1_09142014_copy.csv

* **파일을 다운로드하려면** 다음 명령을 사용합니다. 이미 지정한 대상 경로가 있는지 확인합니다.

		azure datalake store filesystem export <dataLakeStoreAccountName> <source_path> <destination_path>

	예:

		azure datalake store filesystem export mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv "C:\mysampledata\vehicle1_09142014_copy.csv"

* **파일을 삭제하려면** 다음 명령을 사용합니다.

		azure datalake store filesystem delete <dataLakeStoreAccountName> <path> 

	예:

		azure datalake store filesystem delete mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv
	
	메시지가 표시되면 **Y**를 입력하여 항목을 삭제합니다.

## 데이터 레이크 저장소의 폴더에 대한 액세스 제어 목록 보기

다음 명령을 사용하여 데이터 레이크 저장소 폴더에 ACL을 봅니다. 현재 릴리스에서 ACL 데이터 레이크 저장소의 루트에 대해서만 설정할 수 있습니다. 따라서 아래 경로 매개 변수는 항상 루트(/)가 됩니다.

	azure datalake store permissions show <dataLakeStoreName> <path>

예:

	azure datalake store permissions show mynewdatalakestore /


## 데이터 레이크 저장소 계정 삭제

다음 명령을 사용하여 데이터 레이크 저장소 계정을 삭제합니다.

	azure datalake store account delete <dataLakeStoreAccountName>

예:

	azure datalake store account delete mynewdatalakestore

메시지가 표시되면 **Y**를 입력하여 계정을 삭제합니다.

## 데이터 레이크 저장소 계정을 만드는 다른 방법

- [포털을 사용하여 데이터 레이크 저장소 시작](data-lake-store-get-started-portal.md)
- [.NET SDK를 사용하여 데이터 레이크 저장소 시작](data-lake-store-get-started-net-sdk.md)
- [PowerShell을 사용하여 데이터 레이크 저장소 시작](data-lake-store-get-started-powershell.md)


## 다음 단계

- [데이터 레이크 저장소의 데이터 보호](data-lake-store-secure-data.md)
- [Azure 데이터 레이크 분석에 데이터 레이크 저장소 사용](data-lake-analytics-get-started-portal.md)
- [Azure HDInsight에 데이터 레이크 저장소 사용](data-lake-store-hdinsight-hadoop-use-portal.md)


[azure-command-line-tools]: ../xplat-cli-install.md

<!---HONumber=AcomDC_0211_2016-->