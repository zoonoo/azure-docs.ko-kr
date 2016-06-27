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
	ms.date="05/10/2016" 
	ms.author="bradsev" />


# Microsoft 데이터 과학 가상 컴퓨터 프로비전

Microsoft 데이터 과학 가상 컴퓨터는 데이터 분석 및 기계 학습에 흔히 사용되는 몇 가지 인기 있는 도구로 사전 설치 및 구성된 Azure VM(가상 컴퓨터) 이미지입니다. 포함된 도구는 다음과 같습니다.

- Microsoft R Server Developer Edition
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

- **Azure 구독**: 다운로드하려면 [Azure 무료 평가판 받기](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)를 참조하세요.

*   **Azure 저장소 계정**: 계정을 만들려면 [Azure 저장소 계정 만들기](storage-create-storage-account.md#create-a-storage-account)를 참조하세요. 또는 기존 계정을 사용하지 않을 경우 VM을 만드는 프로세스의 일부로서 저장소 계정을 만들 수 있습니다.


## Microsoft 데이터 과학 가상 컴퓨터 만들기

Microsoft 데이터 과학 가상 컴퓨터의 인스턴스를 만드는 단계는 다음과 같습니다.

1.	[Azure 포털](https://portal.azure.com/#create/microsoft-ads.standard-data-science-vmstandard-data-science-vm)에서 가상 컴퓨터 목록으로 이동합니다.
2.	 마법사로 이동하려면 아래에 있는 **만들기** 단추를 클릭합니다.![configure-data-science-vm](./media/machine-learning-data-science-provision-vm/configure-data-science-virtual-machine.png)
3.	 다음 섹션에서는 Microsoft 데이터 과학 가상 컴퓨터를 만드는 데 사용되는 마법사의 각 **5단계**(위의 그림 오른쪽에 열거됨)에 **입력**을 제공합니다. 다음은 이러한 각 단계를 구성하는 데 필요한 입력입니다.

  **a. 기본 사항**:

   - **이름**: 만들려는 데이터 과학 서버 이름
   - **사용자 이름**: 관리자 계정 로그인 ID
   - **암호**: 관리자 계정 암호
   - **구독**: 둘 이상의 구독을 보유한 경우, 컴퓨터를 만들고 요금을 청구할 구독 하나를 선택합니다.
   - **리소스 그룹**: 새 그룹을 만들거나 기존 그룹을 사용할 수 있습니다.
   - **위치**: 가장 적합한 데이터 센터를 선택합니다. 이는 대개 대부분의 데이터가 저장되어 있거나 네트워크에 가장 빠르게 액세스하기 위해 물리적 위치와 가장 가까이 있는 데이터 센터입니다.

  **b. 크기**:

   - 기능 요구 사항과 비용 제약 조건에 부합하는 서버 유형 중 하나를 선택합니다. "모두 보기"를 선택하면 보다 다양한 VM 크기를 선택할 수 있습니다.

  **c. 설정**

   - **디스크 유형**: SSD(반도체 드라이브)를 선호하는 경우 프리미엄을 선택하고 그렇지 않은 경우 "표준"을 선택합니다.
   - **저장소 계정**: 구독에서 새 Azure 저장소 계정을 만들거나 마법사의 기본 단계에서 선택했던 것과 동일한 *위치*에 있는 기존 계정을 사용할 수 있습니다.
   - **기타 매개 변수**: 대부분의 경우, 기본값을 사용합니다. 기본값이 아닌 것을 사용하려는 경우 특정 필드의 정보 링크를 가리키면 도움말을 볼 수 있습니다.

  **d. 요약**:

   - 입력한 모든 정보가 올바른지 확인합니다.

  **e. 구입**:

   - **구입**을 클릭하여 프로비전을 시작합니다. 트랜잭션의 조건에는 링크가 제공됩니다. VM은 **크기** 단계에서 선택한 서버 크기에 대한 계산 이외에 추가 요금이 발생하지 않습니다. 


프로비전은 약 10-20분 정도 소요됩니다. 프로비전의 상태는 Azure 포털에 표시됩니다.

## Microsoft 데이터 과학 가상 컴퓨터에 액세스하는 방법

VM이 만들어지면 기본 섹션의 4단계에서 만든 관리자 계정 자격 증명으로 원격 데스크톱을 사용하여 로그인할 수 있습니다.

VM이 만들어지고 프로비전되면 여기에 설치 및 구성되는 도구를 사용하여 시작할 준비가 되었습니다. 여러 도구에 대한 시작 메뉴 타일 및 데스크톱 아이콘이 있습니다.

## Jupyter Notebook 서버에 강력한 암호를 만드는 방법 

컴퓨터에 설치된 Jupyter Notebook에 대한 사용자 고유의 강력한 암호를 만들려면 데이터 과학 가상 컴퓨터의 명령 프롬프트에서 다음 명령을 실행합니다.

	c:\anaconda\python.exe -c "import IPython;print IPython.lib.passwd()"

메시지가 나타나면 강력한 암호를 선택합니다.

출력에 "sha1:xxxxxx" 형식의 암호 해시가 표시됩니다. 이 암호 해시를 복사하여 매개 변수 이름 ***c.NotebookApp.password***가 포함된 **C:\\ProgramData\\jupyter\\jupyter\_notebook\_config.py**의 Notebook 구성 파일에서 기존 해시를 바꿉니다.

따옴표 안에 있는 기존 해시 값만 바꿔야 합니다. 따옴표와 매개 변수 값에 대한 ***sha1:*** 접두사는 유지해야 합니다.

마지막으로 "Start\_IPython\_Notebook"이라는 Windows 예약 작업으로 VM에서 실행 중인 Ipython 서버를 중지하고 다시 시작해야 합니다. 이 작업을 다시 시작한 후 새 암호가 허용되지 않으면 가상 컴퓨터를 다시 시작합니다.

## Microsoft 데이터 과학 가상 컴퓨터에 설치된 도구

### Microsoft R Server Developer Edition
분석에 R을 사용하려는 경우 VM에는 Microsoft R Server Developer Edition이 설치됩니다. Microsoft R Server는 지원되고 확장 가능하며 안전한 R에 따라 광범위하게 배포 가능한 엔터프라이즈급 분석 플랫폼입니다. R Server는 다양한 빅 데이터 통계, 예측 모델링 및 기계 학습 기능을 지원하며 분석-탐색, 분석, 시각화 및 모델링의 전체 범위를 지원합니다. Microsoft R Server는 오픈 소스 R을 사용하고 확장하여 엔터프라이즈 규모에서 데이터를 분석하기 위해 R 스크립트, 함수 및 CRAN 패키지와 완전히 호환됩니다. 또한 Microsoft R 서버에서 데이터의 병렬 및 청크된 처리를 추가하여 오픈 소스 R의 메모리 내 한계를 해결하며 이는 사용자가 주 메모리에 적합한 것 보다 훨씬 큰 데이터에 분석을 실행할 수 있게 됩니다. 또한 R에 대한 IDE는 시작 메뉴 또는 바탕 화면의 "Revolution R Enterprise 8.0" 아이콘을 클릭하여 액세스할 수 있는 VM에서 패키징됩니다. 무료로 다운로드하여 [RStudio](http://www.rstudio.com)와 같은 다른 IDE도 사용할 수 있습니다.

### Python
Python을 사용하여 개발하는 경우를 위해, Anaconda Python 배포 2.7 및 3.5가 설치되었습니다. 이 배포 버전에는 약 300개의 가장 인기 있는 수학, 엔지니어링 및 데이터 분석 패키지와 함께 기본 Python이 포함되어 있습니다. Visual Studio 2015 Community 버전 또는 IDLE이나 Spyder 등의 Anaconda와 함께 제공되는 IDE 중 하나에 설치된 PTVS(Python Tools for Visual Studio)를 사용할 수 있습니다. 검색 표시줄에서 검색(**Win** + **S** 키)하여 다음 중 하나를 시작할 수 있습니다. **참고**: Anaconda Python 2.7 및 3.5에서 Visual Studio용 Python 도구를 가리키기 위해 Visual Studio 2015 Community Edition 및 환경 경로 설정에서 도구 -> Python 도구-> Python 환경으로 이동한 다음 "+ 사용자 지정"을 클릭하여 각 버전에 대한 사용자 지정 환경 만들기로 이동해야 합니다. Anaconda Python 2.7은 C:\\Anaconda 아래에 설치되어 있으며 Anaconda Python 3.5는 c:\\Anaconda\\envs\\py35 아래에 설치되어 있습니다. 자세한 단계는 [PTVS 설명서](https://github.com/Microsoft/PTVS/wiki/Selecting-and-Installing-Python-Interpreters#hey-i-already-have-an-interpreter-on-my-machine-but-ptvs-doesnt-seem-to-know-about-it)를 참조하세요.

### Jupyter 노트북
Anaconda 배포는 코드 및 분석을 공유 하는 환경인 Jupyter Notebook도 제공됩니다. Jupyter Notebook 서버는 Python 2, Python 3 및 R 커널로 미리 구성되었습니다. Notebook 서버에 액세스하려면 브라우저를 시작하는 "Jupyter Notebook이라는 바탕 화면 아이콘이 있습니다. 원격 데스크톱을 통해 VM을 사용 중인 경우 [https://localhost:9999/](https://localhost:9999/)에 방문하여 Jupyter Notebook 서버에 액세스할 수도 있습니다.(참고: 인증서 경고가 발생하더라도 계속 진행하세요.) 샘플 Notebook을 Python 및 R에 각각 패키징했습니다. 이전 단계에서 만든 암호를 사용하여 Jupyter Notebook에 인증한 후에 Notebook 홈 페이지에서 샘플에 대한 링크를 볼 수 있습니다.

### Visual Studio 2015 Community edition
VM에 설치된 Visual Studio Community edition 평가 목적이나 매우 작은 팀에 사용할 수 있는 Microsoft의 인기 있는 IDE의 무료 버전입니다. 사용 조건은 [여기](https://www.visualstudio.com/support/legal/mt171547)에서 확인할 수 있습니다. 바탕 화면 아이콘이나 **시작** 메뉴를 두 번 클릭하여 Visual Studio를 엽니다. 또한 **Win** + **S**를 누른 후 "Visual Studio"를 입력하여 프로그램을 검색할 수도 있습니다. 여기서 C#, Python과 같은 언어로 된 프로젝트를 만들 수 있습니다. 또한 Azure 데이터 카탈로그, Azure HDInsight(Hadoop, Spark) 및 Azure 데이터 레이크와 같은 Azure 서비스로 작동하기 편하게 하는 플러그 인이 설치되어 있습니다.

참고: 평가 기간이 만료되었다는 메시지가 나타날 수 있습니다. Microsoft 계정 자격 증명을 입력하거나 새로 만들어 입력하여 Visual Studio Community Edition에 액세스할 수 있습니다.

### SQL Server Express
SQL Server의 제한된 버전에는 Visual Studio Community edition도 포함되어 있습니다. **SQL Server Management Studio**를 시작하여 SQL Server에 액세스할 수 있습니다. 사용 중인 VM 이름이 서버 이름으로 사용됩니다. Windows의 관리자 권한으로 로그인할 때 Windows 인증을 사용합니다. SQL Server Management Studio에서 다른 사용자를 만들고, 데이터베이스를 만들며, 데이터를 가져오고, SQL 쿼리를 실행할 수 있습니다.

### Azure 
몇 개의 Azure 도구가 VM에 설치됩니다.
- 바탕 화면에 Azure SDK 설명서에 액세스하는 바로 가기가 있습니다. 
- **AzCopy** - Microsoft Azure 저장소 계정 내부/외부로 데이터를 이동시키는 데 사용합니다. 
- **Azure 저장소 탐색기** - Azure 저장소 계정 내에 저장한 개체를 검색하는 데 사용합니다. 
- **Microsoft Azure Powershell** - VM에도 설치된 Powershell 스크립트 언어의 Azure 리소스를 관리하는 데 사용되는 도구입니다. 

###Power BI

대시보드와 멋진 시각화를 구축할 수 있도록 **Power BI Desktop**이 설치되어 있습니다. 이 도구를 사용하여 여러 원본에서 데이터를 추출하고, 대시보드 및 보고서를 작성하며, 클라우드에 게시합니다. 자세한 내용은 [Power BI](http://powerbi.microsoft.com) 사이트를 참조하세요.

참고: Power BI에 액세스하기 위해서는 Office 365 계정이 필요합니다.

## 추가 Microsoft 개발 도구
[**Microsoft 웹 플랫폼 설치 관리자**](https://www.microsoft.com/web/downloads/platform.aspx)는 다른 Microsoft 개발 도구를 검색하고 다운로드하는 데 사용할 수 있습니다. 또한 Microsoft 데이터 과학 가상 컴퓨터 데스크톱에서 제공되는 도구에 대한 바로 가기도 있습니다.

## 다음 단계
학습 및 탐색을 계속하려면 다음 단계는 다음과 같습니다.

* 시작 메뉴를 클릭하여 메뉴에 나열된 도구를 확인하여 데이터 과학 VM에 다양한 데이터 과학 도구를 탐색합니다.
* 엔터프라이즈 규모에서 데이터 분석을 지원하는 R의 RevoScaleR 라이브러리를 사용하는 샘플은 **C:\\Program Files\\Microsoft\\MRO-for-RRE\\8.0\\R-3.2.2\\library\\RevoScaleR\\demoScripts**로 이동합니다.  
* 문서 참조: [데이터 과학 가상 컴퓨터로 할 수 있는 10가지 일](http://aka.ms/dsvmtenthings)
* 체계적으로 [데이터 과학 프로세스](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/)를 사용하여 종단 간 분석 솔루션을 구축하는 방법을 알아봅니다.
* Cortana Intelligence 모음을 사용하는 기계 학습 및 데이터 분석 샘플은 [Cortana Intelligence 갤러리](http://gallery.cortanaintelligence.com)를 방문하세요. 또한 손쉽게 액세스할 수 있도록 가상 컴퓨터의 시작 메뉴 및 데스크톱에 아이콘을 제공합니다. 

<!---HONumber=AcomDC_0615_2016-->