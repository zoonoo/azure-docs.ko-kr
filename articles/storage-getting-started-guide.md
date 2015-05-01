<properties 
	pageTitle="5분 만에 Azure Blob, 테이블 및 큐 시작" 
	description="Azure 빠른 시작 및 Visual Studio를 사용하여 Microsoft Azure Blob, 테이블 및 큐의 규모를 신속하게 확장하는 방법에 대해 알아봅니다." 
	services="storage" 
	documentationCenter=".net" 
	authors="Selcin" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="02/18/2015" 
	ms.author="selcint"/>

# 5분 만에 Azure Blob, 테이블 및 큐 시작 

이 자습서에서는 Visual Studio에서 간단한 Azure 응용 프로그램을 개발하여 **Azure 저장소 Blob**, **테이블** 및 **큐**에 대해 신속하게 프로그래밍하는 방법을 보여 줍니다. 

이 자습서에는 Azure 저장소의 신속한 규모 확장에 대한 다음과 같은 두 가지 기본 시나리오가 포함되어 있습니다.

- Azure 저장소 에뮬레이터에서 첫 번째 Azure 저장소 응용 프로그램 실행
- Azure 저장소 서비스에서 첫 번째 Azure 저장소 응용 프로그램 실행

코드를 살펴보기 전에 Azure 저장소에 대해 알아보려면 [다음 단계](#next-steps)를 참조하세요.

## Azure 저장소 에뮬레이터에서 첫 번째 Azure 저장소 응용 프로그램 실행

이 섹션에서는 [Azure 저장소 에뮬레이터](https://msdn.microsoft.com/library/azure/hh403989.aspx)에 액세스하는 샘플 응용 프로그램을 개발하여 **Azure 저장소 Blob**, **테이블** 및 **큐**에 대해 프로그래밍하는 방법을 보여 줍니다. Microsoft Azure 저장소 에뮬레이터는 개발 목적으로 Azure Blob, 큐 및 테이블 서비스를 에뮬레이트하는 로컬 환경을 제공합니다. 이 저장소 에뮬레이터를 사용하면 비용을 발생시키지 않고도 로컬에서 저장소 서비스에 대해 응용 프로그램을 테스트할 수 있습니다.

이 섹션을 완료하려면 먼저 다음과 같은 필수 조건 작업을 수행해야 합니다.

1. 응용 프로그램을 컴파일 및 빌드하려면 컴퓨터에 [Visual Studio](http://www.visualstudio.com/visual-studio-homepage-vs.aspx)가 설치되어 있어야 합니다. Visual Studio가 설치되어 있지 않은 경우 [Azure SDK 2.5 for Visual Studio 2013](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409) 이상을 설치할 때 웹용 Visual Studio Express를 설치할 수 있습니다. 
2. 컴퓨터에 [Azure SDK 2.5 for Visual Studio 2013](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409) 이상이 설치되어 있는지 확인합니다. 여기에 Azure 빠른 시작 샘플 프로젝트 및 [.NET용 Azure 저장소 클라이언트 라이브러리](https://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)가 포함되어 있기 때문입니다.  
3. Azure 빠른 시작 샘플 프로젝트에 필요하므로 컴퓨터에 [.NET Framework 4.5](http://www.microsoft.com/download/details.aspx?id=30653)가 설치되어 있는지 확인합니다. 컴퓨터에 어떤 .NET Framework 버전이 설치되어 있는지 모를 경우 [방법: 설치된 .NET Framework 버전 확인](https://msdn.microsoft.com/vstudio/hh925568.aspx)을 참조하세요. 또는 **시작** 단추나 Windows 키를 누르고 **제어판**을 입력합니다. 그런 다음 **프로그램** > **프로그램 및 기능**을 클릭합니다. 설치된 모든 프로그램 중에 .NET Framework 4.5가 나열되는지 확인합니다.

이제 Visual Studio에서 Azure 빠른 시작 샘플 프로젝트 중 하나를 사용하여 간단한 Azure 저장소 응용 프로그램을 만듭니다. 이 자습서는 **Azure Blob 저장소**, **Azure 테이블 저장소** 및 **Azure 저장소 큐** 샘플 프로젝트를 중점적으로 다룹니다. 각 샘플 프로젝트에 대해 다음 지침이 적용됩니다. 단, 3.a단계에서 선택하는 템플릿은 각각 다릅니다.

1. **시작** 단추나 Windows 키를 누르고 Visual Studio 2013 또는 VS Express 2013 for Web을 입력합니다. 프로그램을 클릭하여 시작합니다.
2. **파일** 메뉴에서 **새 프로젝트**를 클릭합니다.
3. **새 프로젝트** 대화 상자에서 **설치됨** > **템플릿** > **Visual C#** > **클라우드** > **빠른 시작** > **데이터 서비스**를 클릭합니다.
	- 3.a.  Azure Blob 저장소, Azure 테이블 저장소 또는 Azure 저장소 큐 템플릿 중 하나를 선택합니다. 
	- 3.b. **.NET Framework 4.5**가 대상 프레임워크로 선택되었는지 확인합니다.	
	- 3.c. 선택한 템플릿에 따라 응용 프로그램 이름을 **DataBlobStorage**, **DataTableStorage** 또는 **DataStorageQueue**로 지정합니다. **확인**을 클릭합니다. 그러면 새 Visual Studio 솔루션이 만들어집니다. 다음 스크린샷을 예제로 참조하세요.
	
	![Azure QuickStarts][Image1]

응용 프로그램을 실행하기 전에 소스 코드를 검토하여 Azure 저장소에 대해 프로그래밍하는 방법을 알아보는 것이 좋습니다. 코드를 검토하려면 Visual Studio의 **보기** 메뉴에서 **솔루션 탐색기**를 선택합니다. 그런 다음 Program.cs 파일을 두 번 클릭합니다. 

이제 다음과 같이 Azure SDK의 일부로 설치되는 [Azure 저장소 에뮬레이터](https://msdn.microsoft.com/library/azure/hh403989.aspx)를 사용하여 샘플 응용 프로그램을 실행합니다.

1.	Azure 저장소 에뮬레이터 시작: **시작** 단추나 Windows 키를 누르고 Azure 저장소 에뮬레이터를 입력하여 검색합니다. 응용 프로그램 목록에서 해당 프로그램을 선택하여 시작합니다.
2.	Visual Studio의 **빌드** 메뉴에서 **솔루션 빌드**를 클릭합니다. 
3.	**디버그** 메뉴에서 **F11** 키를 눌러 단계별로 솔루션을 실행하거나 **F5** 키를 눌러 솔루션을 실행합니다.

## 클라우드의 Azure 저장소 서비스에서 첫 번째 Azure 저장소 응용 프로그램 실행
이 섹션에서는 [Azure 저장소 서비스](http://azure.microsoft.com/documentation/services/storage/)에 액세스하는 샘플 응용 프로그램을 개발하여 **Azure 저장소 Blob**, **테이블** 및 **큐**에 대해 프로그래밍하는 방법을 보여 줍니다.

이 섹션을 완료하려면 먼저 다음과 같은 필수 조건 작업을 수행해야 합니다.

1. 응용 프로그램을 컴파일 및 빌드하려면 컴퓨터에 [Visual Studio](http://www.visualstudio.com/visual-studio-homepage-vs.aspx)가 설치되어 있어야 합니다. Visual Studio가 설치되어 있지 않은 경우 [Azure SDK 2.5 for Visual Studio 2013](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409) 이상을 설치할 때 웹용 Visual Studio Express를 설치할 수 있습니다. 
2. 컴퓨터에 [Azure SDK 2.5 for Visual Studio 2013](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409) 이상이 설치되어 있는지 확인합니다. 여기에 Azure 빠른 시작 샘플 프로젝트 및 [.NET용 Azure 저장소 클라이언트 라이브러리](https://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)가 포함되어 있기 때문입니다.  
3. Azure 빠른 시작 샘플 프로젝트에 필요하므로 컴퓨터에 [.NET Framework 4.5](http://www.microsoft.com/download/details.aspx?id=30653)가 설치되어 있는지 확인합니다. 컴퓨터에 어떤 .NET Framework 버전이 설치되어 있는지 모를 경우 [방법: 설치된 .NET Framework 버전 확인](https://msdn.microsoft.com/vstudio/hh925568.aspx)을 참조하세요. 또는 **시작** 단추나 Windows 키를 누르고 **제어판**을 입력합니다. 그런 다음 **프로그램** > **프로그램 및 기능**을 클릭합니다. 설치된 모든 프로그램 중에 .NET Framework 4.5가 나열되는지 확인합니다.
4.	Azure 구독이 아직 없는 경우 하나 만들고 **표준 저장소** 계정도 만듭니다.
	- Azure 구독을 만들려면 [무료 평가판](http://azure.microsoft.com/pricing/free-trial/), [구입 옵션](http://azure.microsoft.com/pricing/purchase-options/) 및 [회원 제안](http://azure.microsoft.com/pricing/member-offers/)(MSDN, Microsoft 파트너 네트워크, BizSpark 및 기타 Microsoft 프로그램의 회원인 경우)을 참조하세요.
	- Azure에서 **표준 저장소** 계정을 만들려면 [저장소 계정을 만들거나, 관리하거나, 삭제하는 방법](storage-create-storage-account.md)을 참조하세요. **참고:** Azure의 저장소 계정 유형 두 가지는 표준 저장소 계정과 프리미엄 저장소 계정입니다. 표준 저장소 계정은 Azure Blob, 테이블 및 큐 저장소에 대한 액세스를 제공합니다. 현재 프리미엄 저장소 계정은 Azure 가상 컴퓨터에서 사용되는 디스크에 데이터를 저장하는 데만 사용할 수 있습니다. 자세한 내용은 [프리미엄 저장소: Azure 가상 컴퓨터 작업을 위한 고성능 저장소](storage-premium-storage-preview-portal.md)를 참조하세요.

이제 Visual Studio에서 Azure 빠른 시작 샘플 프로젝트 중 하나를 사용하여 간단한 Azure 저장소 응용 프로그램을 만듭니다. 이 자습서는 **Azure Blob 저장소**, **Azure 테이블 저장소** 및 **Azure 저장소 큐** 샘플 프로젝트를 중점적으로 다룹니다. 각 샘플 프로젝트에 대해 다음 지침이 적용됩니다. 단, 3.a단계에서 선택하는 템플릿은 각각 다릅니다.

1. **시작** 단추나 Windows 키를 누르고 Visual Studio 2013 또는 VS Express 2013 for Web을 입력합니다. 프로그램을 클릭하여 시작합니다.
2. **파일** 메뉴에서 **새 프로젝트**를 클릭합니다.
3. **새 프로젝트** 대화 상자에서 **설치됨** > **템플릿** > **Visual C#** > **클라우드** > **빠른 시작** > **데이터 서비스**를 클릭합니다.
	- 3.a. Azure Blob 저장소, Azure 테이블 저장소 또는 Azure 저장소 큐 템플릿 중 하나를 선택합니다. 
	- 3.b. **.NET Framework 4.5**가 대상 프레임워크로 선택되었는지 확인합니다.
	- 3.c. 선택한 템플릿에 따라 응용 프로그램 이름을 **DataBlobStorage**, **DataTableStorage** 또는 **DataStorageQueue**로 지정합니다. **확인**을 클릭합니다. 그러면 새 Visual Studio 솔루션이 만들어집니다. 

응용 프로그램을 실행하기 전에 소스 코드를 검토하여 Azure 저장소에 대해 프로그래밍하는 방법을 알아보는 것이 좋습니다. 코드를 검토하려면 Visual Studio의 **보기** 메뉴에서 **솔루션 탐색기**를 선택합니다. 그런 다음 Program.cs 파일을 두 번 클릭합니다. 

이제 다음과 같이 샘플 응용 프로그램을 실행합니다.

1.	Visual Studio의 **보기** 메뉴에서 **솔루션 탐색기**를 선택합니다. App.config 파일을 두 번 클릭하고 다음과 같이 Azure SDK 저장소 에뮬레이터에 대한 연결 문자열을 주석으로 처리합니다. 

	`<!--<add key="StorageConnectionString" value = "UseDevelopmentStorage=true;"/>-->`

2.	다음과 같이 Azure 저장소 서비스에 대한 연결 문자열의 주석 처리를 제거하고 App.config 파일에서 저장소 계정 이름과 액세스 키를 제공합니다.
	`<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]"` 

	저장소 계정 이름과 액세스 키를 찾으려면 [저장소 계정이란?](storage-whatis-account.md)을 참조하세요. 

3.	App.config 파일에서 저장소 계정 이름과 액세스 키를 제공한 후 **파일** 메뉴에서 **모두 저장**을 클릭하여 모든 프로젝트 파일을 저장합니다. 
4.	**빌드** 메뉴에서 **솔루션 빌드**를 클릭합니다. 
5.	**디버그** 메뉴에서 **F11** 키를 눌러 단계별로 솔루션을 실행하거나 **F5** 키를 눌러 솔루션을 실행합니다.


## 다음 단계
이 자습서에서는 Azure Blob 저장소, Azure 테이블 저장소 및 Azure 저장소 큐에 대해 프로그래밍하는 방법을 배웠습니다. 

이에 대한 자세한 내용을 알아보려면 다음 링크를 따라가세요.

* [Microsoft Azure 저장소 소개](storage-introduction.md)
* [.NET에서 Blob 저장소를 사용하는 방법(영문)](storage-dotnet-how-to-use-blobs.md)
* [.NET에서 테이블 저장소를 사용하는 방법(영문)](storage-dotnet-how-to-use-tables.md)
* [.NET에서 큐 저장소를 사용하는 방법(영문)](storage-dotnet-how-to-use-queues.md)
* [Azure 저장소 설명서](http://azure.microsoft.com/documentation/services/storage/)
* [Azure 저장소 MSDN 참조](http://msdn.microsoft.com/library/azure/gg433040.aspx)
* [Azure 저장소 클라이언트 라이브러리](https://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)
* [Azure 저장소 REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx)

[Image1]: ./media/storage-getting-started-guide/QuickStart.png



<!--HONumber=52-->