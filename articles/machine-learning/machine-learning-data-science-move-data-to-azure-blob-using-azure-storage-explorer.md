<properties 
	pageTitle="Azure 저장소 탐색기를 사용하여 Azure Blob 저장소의 데이터 이동 | Microsoft Azure" 
	description="Azure 저장소 탐색기를 사용하여 Azure Blob 저장소의 데이터 이동" 
	services="machine-learning,storage" 
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
	ms.date="02/08/2016" 
	ms.author="bradsev" />

# Azure 저장소 탐색기를 사용하여 Azure Blob 저장소의 데이터 이동

## 소개 

Azure 저장소 탐색기는 Azure 저장소 계정의 데이터를 검사하고 변경할 수 있는 무료 Windows 기반 도구입니다. 이 항목에서는 Azure 저장소 탐색기를 사용하여 Azure Blob 저장소에서 데이터를 업로드 및 다운로드하는 방법을 설명합니다. 이 도구는 [Azure 저장소 탐색기](http://azurestorageexplorer.codeplex.com/)에서 다운로드할 수 있습니다.

Azure Blob 저장소로 및/또는 저장소에서 데이터를 이동하는 데 사용되는 기술 지침은 여기에 연결되어 있습니다.

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]


> [AZURE.NOTE] [Azure의 데이터 과학 가상 컴퓨터](machine-learning-data-science-virtual-machines.md)에서 제공하는 스크립트를 통해 설정된 VM을 사용하는 경우 Azure 저장소 탐색기가 VM에 이미 설치되어 있습니다.

> [AZURE.NOTE] Azure blob 저장소에 대한 전체 지침은 [Azure Blob 기본 사항](../storage-dotnet-how-to-use-blobs.md) 및 [Azure Blob 서비스](https://msdn.microsoft.com/library/azure/dd179376.aspx)를 참조하세요.

## 필수 조건

이 문서에서는 사용자에게 Azure 구독, 저장소 계정 및 계정에 해당하는 저장소 키가 있다고 가정합니다. 데이터를 업로드/다운로드하려면 Azure 저장소 계정 이름 및 계정 키를 알아야 합니다.

- Azure 구독을 설정하려면 [무료 1개월 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
- 저장소 계정을 만들고 계정 및 키 정보를 가져오는 방법에 대한 지침은 [Azure 저장소 계정 정보](../storage-create-storage-account.md)를 참조하세요.


<a id="explorer"></a>
## Azure 저장소 탐색기 사용 

다음 단계에서는 Azure 저장소 탐색기를 사용하여 데이터를 업로드/다운로드하는 방법을 설명합니다.

1.  Azure 저장소 탐색기 실행 
2.  액세스하려는 저장소 탐색기가 Azure 저장소 탐색기에 추가되지 않았으면 "계정 추가" 단추를 클릭하여 계정을 추가합니다. 이미 추가되었으면 "--저장소 계정 선택--" 드롭다운 목록에서 계정을 선택합니다. ![작업 영역 만들기][1] <br>
3. 저장소 계정 이름 및 저장소 계정 키를 입력한 다음 저장소 계정 추가를 클릭합니다. 저장소 계정을 여러 개 추가할 수 있으며 각 계정은 탭에 표시됩니다. 이 저장소 계정의 컨테이너가 왼쪽 패널에 표시됩니다. 오른쪽 패널에서 컨테이너를 선택하면 해당 컨테이너의 blob를 볼 수 있습니다. ![작업 영역 만들기][2] <br> ![작업 영역 만들기][3] <br>
4. "업로드" 단추를 클릭하여 데이터를 업로드합니다. 파일 시스템에서 업로드할 파일을 하나 이상 선택하고 "열기"를 클릭하여 파일 업로드를 시작합니다.
5. 해당 컨테이너의 blob를 선택하고 "다운로드" 단추를 클릭하여 데이터를 다운로드합니다 .

<!-- Images -->

[1]: ./media/machine-learning-data-science-move-azure-blob/data-science-process-uploading-data-to-blob-storage-img1.png
[2]: ./media/machine-learning-data-science-move-azure-blob/data-science-process-uploading-data-to-blob-storage-img2.png
[3]: ./media/machine-learning-data-science-move-azure-blob/data-science-process-uploading-data-to-blob-storage-img3.png

<!---HONumber=AcomDC_0211_2016-->