<properties 
	pageTitle="Microsoft 데이터 과학 가상 컴퓨터 프로비전 | Microsoft Azure" 
	description="분석 및 기계 학습을 수행하기 위해 Azure에서 데이터 과학 가상 컴퓨터 구성 및 만들기" 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/23/2015" 
	ms.author="bradsev" />


# Microsoft 데이터 과학 가상 컴퓨터 프로비전

## 소개

Microsoft 데이터 과학 가상 컴퓨터는 데이터 분석 및 기계 학습에 흔히 사용되는 몇 가지 인기 있는 도구로 사전 설치 및 구성된 Azure VM(가상 컴퓨터) 이미지입니다. 포함된 도구는 다음과 같습니다.

- Revolution R Open
- Enthought Python 배포
- Visual Studio Community Edition
- Power BI 데스크톱
- SQL Server Express Edition
- Azure SDK


데이터 과학은 찾기, 로딩 및 전처리 데이터 등 작업 순서의 반복, 모델 구축 및 테스트, 지능형 응용 프로그램에서 사용을 위해 모델 배포와 관련이 있습니다. 이러한 작업을 완료하기 위해 다양한 도구를 사용하는 데이터 과학자에게는 흔한 일입니다. 소프트웨어의 적합한 버전을 찾고 다운로드하여 설치하는 데 소비되는 시간이 상당히 걸릴 수 있습니다. Microsoft 데이터 과학 가상 컴퓨터에서는 이러한 부담을 줄일 수 있습니다.

Microsoft 데이터 과학 가상 컴퓨터는 분석 프로젝트를 빠르게 시작합니다. 따라서 R, Python, SQL 및 C# 등의 다양한 언어로 태스크를 수행할 수 있습니다. Visual Studio는 사용하기 쉬운 코드를 개발하고 테스트하기 위해 IDE를 제공합니다. VM에 포함된 Azure SDK를 통해 Microsoft의 클라우드 플랫폼에서 다양한 서비스를 사용하여 응용 프로그램을 빌드할 수 있습니다.

이 데이터 과학 VM 이미지에 대한 소프트웨어 요금은 부과되지 않습니다. 단지 이 VM 이미지로 프로비전되는 가상 컴퓨터의 크기에 따른 Azure 사용 비용만 지불하면 됩니다. 계산 요금에 대한 자세한 내용은 [여기](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/)에 나와 있습니다.


## 필수 조건

Microsoft 데이터 과학 가상 컴퓨터를 만들려면 먼저 다음이 있어야 합니다.

- **Azure 구독**: 다운로드하려면 [Azure 무료 평가판 받기](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)를 참조하세요.

*   **Azure 저장소 계정**: 계정을 만들려면 [Azure 저장소 계정 만들기](storage-create-storage-account.md#create-a-storage-account)를 참조하세요. 또는 기존 계정을 사용하지 않을 경우 VM을 만드는 프로세스의 일부로서 저장소 계정을 만들 수 있습니다.


## Microsoft 데이터 과학 가상 컴퓨터 만들기

Microsoft 데이터 과학 가상 컴퓨터의 인스턴스를 만드는 단계는 다음과 같습니다.

1.	[Azure 포털](https://portal.azure.com/#gallery/microsoft-ads.standard-data-science-vmstandard-data-science-vm)에서 가상 컴퓨터 목록으로 이동합니다.
2.	 마법사로 이동하려면 아래에 있는 **만들기** 단추를 클릭합니다.![configure-data-science-vm](./media/machine-learning-data-science-provision-vm/configure-data-science-virtual-machine.png)
3.	 다음 섹션에서는 Microsoft 데이터 과학 가상 컴퓨터를 만드는 데 사용되는 마법사의 각 **5단계**(위의 그림 오른쪽에 열거됨)에 **입력**을 제공합니다. 다음은 이러한 각 단계를 구성하는 데 필요한 입력입니다.

  **a. 기본 사항**:

    - **Name**: Name of your data science server you are creating.
    - **User Name**: Admin account login id
    - **Password**: Admin account password
    - **Subscription**: If you have more than one subscription, select the one on which the machine will be created and billed
    - **Resource Group**: You can create a new one or use an existing group
    - **Location**: Select the data center that is most appropriate. Usually it is the data center that has most of your data or is closest to your physical location for fastest network access

  **b. 크기**:

    - Select one of the server types that meets your functional requirement and cost constraints. You can get more choices of VM sizes by selecting “View All”

  **c. 설정**

    - **Disk Type**: Choose Premium if you prefer a solid state drive (SSD), else choose “Standard”.
    - **Storage Account**: You can create a new Azure storage account in your subscription or use an existing one in the same *Location* that was chosen on the Basics step of the wizard.
    - **Other parameters**: In most cases you will just use the default values. You can hover over the informational link for help on the specific fields in case you want to consider the use of non-default values.

  **d. 요약**:

    - Verify that all information you entered is correct.

  **e. 구입**:

    - Click on **Buy** to start the provisioning. A link is provided to the terms of the transaction. The VM does not have any additional charges beyond the compute for the server size you chose in the **Size** step. 


프로비전은 약 10-20분 정도 소요됩니다. 프로비전의 상태는 Azure 포털에 표시됩니다.

## Microsoft 데이터 과학 가상 컴퓨터에 액세스하는 방법

VM이 만들어지면 기본 섹션의 4단계에서 만든 관리자 계정 자격 증명으로 원격 데스크톱을 사용하여 로그인할 수 있습니다.

VM이 만들어지고 프로비전되면 여기에 설치 및 구성되는 도구를 사용하여 시작할 준비가 되었습니다. 바탕 화면 아이콘과 여러 도구의 시작 메뉴 타일이 있습니다.

## Microsoft 데이터 과학 가상 컴퓨터에 설치된 도구

### R
분석에 R을 사용하려는 경우 VM에 Revolution R Open (RRO)이 설치되어야 합니다. 이는 R의 공개 소스 배포이며 CRAN-R와 완벽하게 호환됩니다. Intel Math Kernel Library와 함께 최신 공개 소스 R 엔진이 포함되어 있습니다. "RRO RGui"라고 하는 IDE도 VM에 포함되어 있습니다. 무료로 다운로드하여 [RStudio](http://www.rstudio.com)와 같은 다른 IDE도 사용할 수 있습니다.

### Python
Python을 사용하여 개발하는 경우를 위해, Anaconda Python 배포 2.7이 설치되었습니다. 이 배포 버전에는 약 300개의 가장 인기 있는 수학, 엔지니어링 및 데이터 분석 패키지와 함께 기본 Python이 포함되어 있습니다. Visual Studio 2015 Community 버전 또는 IDLE이나 Spyder 등의 Anaconda와 함께 제공되는 IDE 중 하나에 설치된 PTVS(Python Tools for Visual Studio)를 사용할 수 있습니다. 검색 표시줄에서 검색하여 다음 중 하나를 시작할 수 있습니다(**Win** + **S** 키).

### IPython Notebook
Anaconda 배포는 코드 및 분석을 공유 하는 환경인 IPython notebook도 제공됩니다. IPython Notebook 서버는 미리 구성되어 있습니다. Notebook 서버에 액세스하려면 브라우저를 시작하는 바탕 화면 아이콘이 있습니다. 원격 데스크톱을 통해 VM을 사용 중인 경우 [https://localhost:9999/](https://localhost:9999/)에 방문하여 IPython Notebook 서버에 액세스할 수도 있습니다(참고: 인증서 경고가 발생하더라도 계속 진행하세요.).

### Visual Studio 2015 Community edition
VM에 설치된 Visual Studio Community edition 평가 목적이나 매우 작은 팀에 사용할 수 있는 Microsoft의 인기 있는 IDE의 무료 버전입니다. 사용 조건은 [여기](https://www.visualstudio.com/support/legal/mt171547)에서 확인할 수 있습니다. 바탕 화면 아이콘이나 **시작** 메뉴를 두 번 클릭하여 Visual Studio를 엽니다. 또한 **Win** + **S** 키를 누른 후 "Visual Studio"를 입력하여 프로그램을 검색할 수도 있습니다.

참고: 평가 기간이 만료되었다는 메시지가 나타날 수 있습니다. Microsoft 계정 자격 증명을 입력하거나 새로 만들어 입력하여 Visual Studio Community Edition에 액세스할 수 있습니다. 여기서 C#, Python과 같은 언어로 된 프로젝트를 만들 수 있습니다.

### SQL Server Express
SQL Server의 제한된 버전에는 Visual Studio Community edition도 포함되어 있습니다. **SQL Server Management Studio**를 시작하여 SQL Server에 액세스할 수 있습니다. 사용 중인 VM 이름이 서버 이름으로 사용됩니다. Windows의 관리자 권한으로 로그인할 때 Windows 인증을 사용합니다. SQL Server Management Studio에서 다른 사용자를 만들고, 데이터베이스를 만들며, 데이터를 가져오고, SQL 쿼리를 실행할 수 있습니다.

### Azure 
몇 개의 Azure 도구가 VM에 설치됩니다. - Azure SDK 설명서에 액세스하는 바탕 화면 바로 가기가 있습니다. - Microsoft Azure 저장소 계정 내부/외부로 데이터를 이동시키는 데 사용되는 **AzCopy**.- Azure 저장소 계정 내에 저장된 개체를 통해 검색하는 데 사용되는 **Azure 저장소 탐색기**.- **Microsoft Azure Powershell** - VM에도 설치된 스크립트 언어로 된 Azure 리소스를 관리하는 데 사용되는 스크립트 언어

###Power BI

대시보드와 멋진 시각화를 구축할 수 있도록 **Power BI Desktop**이 설치되어 있습니다. 이 도구를 사용하여 여러 원본에서데이터를 추출하고, 대시보드 및 보고서를 작성하며, 클라우드에 게시합니다. 자세한 내용은 [Power BI](http://powerbi.microsoft.com) 사이트를 참조하세요.

참고: Power BI에 액세스하기 위해서는 Office 365 계정이 필요합니다.

## 추가 Microsoft 개발 도구
[**Microsoft 웹 플랫폼 설치 관리자**](https://www.microsoft.com/web/downloads/platform.aspx)는 다른 Microsoft 개발 도구를 검색하고 다운로드하는 데 사용할 수 있습니다. 또한 Microsoft 데이터 과학 가상 컴퓨터 데스크톱에서 제공되는 도구에 대한 바로 가기도 있습니다.

<!---HONumber=AcomDC_1125_2015-->