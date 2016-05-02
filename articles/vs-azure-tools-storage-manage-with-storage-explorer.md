<properties
	pageTitle="저장소 탐색기(미리 보기)를 사용하여 Azure 저장소 리소스 관리 | Microsoft Azure"
	description="Microsoft Azure 저장소 탐색기(미리 보기)를 사용하여 Azure 저장소 리소스를 만들고 관리하는 방법을 설명합니다."
	services="visual-studio-online"
	documentationCenter="na"
	authors="TomArcher"
	manager="douge"
	editor="" />

 <tags
	ms.service="visual-studio-online"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="04/19/2016"
	ms.author="tarcher" />

# 저장소 탐색기(미리 보기)를 사용하여 Azure 저장소 리소스 관리

Microsoft Azure 저장소 탐색기(미리 보기)는 Azure 저장소 계정을 손쉽게 관리하도록 도와주는 독립 실행형 도구입니다. Visual Studio에서 앱을 개발하는 경우와 같이, Azure 포털 외부에서 저장소를 신속하게 관리하려는 상황에 유용합니다. 이번 미리 보기 릴리스를 사용하면 Blob 저장소에서 손쉽게 작업할 수 있습니다. 컨테이너를 만들고 삭제하고, Blob을 업로드하고 다운로드하고 삭제하고, 모든 컨테이너와 Blob을 검색할 수 있습니다. 개발자와 ops는 고급 기능을 사용하여 SAS 키 및 정책 작업을 수행할 수 있습니다. Windows 개발자는 Azure 저장소 에뮬레이터를 통해 로컬 개발 저장소 계정을 사용하여 코드를 테스트할 수 있습니다.

저장소 탐색기에서 저장소 리소스를 보거나 관리하려면 구독 또는 외부 저장소 계정을 통해 Azure 저장소 계정에 액세스할 수 있어야 합니다. 저장소 계정이 없는 경우에는 몇 분 만에 계정을 만들 수 있습니다. MSDN 구독이 있는 경우에는 [Visual Studio 구독자를 위한 월간 Azure 크레딧](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 참조하세요. 그렇지 않으면 [1개월 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

## Azure 계정 및 구독 관리

저장소 탐색기에서 Azure 저장소 리소스를 보려면 하나 이상의 구독이 활성화된 Azure 계정에 로그인해야 합니다. Azure 계정이 둘 이상 있으면 저장소 탐색기에서 추가한 다음 저장소 탐색기 리소스 보기에 포함하려는 구독을 선택합니다. 이전에 Azure를 사용하지 않았거나 Visual Studio에 필요한 계정을 추가하지 않은 경우, Azure 계정에 로그인하라는 메시지가 표시됩니다.

### 저장소 탐색기에 Azure 계정 추가

1.	저장소 탐색기 도구 모음에서 **설정**(기어) 아이콘을 선택합니다.
1.	**계정 추가** 링크를 선택합니다. 검색하려는 저장소 리소스의 Azure 계정에 로그인합니다. 방금 추가한 계정을 계정 선택 드롭다운 목록에서 선택해야 합니다. 해당 계정에 대한 모든 구독은 계정 항목 아래에 나타납니다.

	![][0]

1.	탐색할 계정 구독에 대한 확인란을 선택한 다음 **적용** 단추를 선택합니다.

	![][1]

	선택한 구독에 대한 Azure 저장소 리소스는 저장소 탐색기에 나타납니다.

### 외부 저장소 연결

1. 연결할 저장소 계정에 대한 계정 이름 및 키를 준비합니다.
	1.	Azure Preview 포털에서 연결할 저장소 계정을 선택합니다.
	1.	Azure Preview 포털 **설정** 창의 **관리** 섹션에서 **키** 단추를 선택합니다.
	1.	**저장소 계정 이름** 및 **기본 액세스 키** 값을 복사합니다.

		![][2]

1.	저장소 탐색기에서 **저장소 계정** 노드의 바로 가기 메뉴에서 **외부 저장소 연결** 명령을 선택합니다.

	![][3]

1. **계정 이름** 상자에 저장소 계정 이름을 입력하고 **계정 키** 상자에 기본 액세스 키를 입력합니다. **확인** 단추를 선택하여 계속합니다.

	![][4]

	외부 저장소가 저장소 탐색기에 표시됩니다.

	![][5]

1. 외부 저장소를 제거하려면 외부 저장소의 바로 가기 메뉴에서 분리 명령을 선택합니다.

	![][6]

## 저장소 리소스 보기 및 탐색

저장소 탐색기에서 Azure 저장소 리소스로 이동하여 정보를 보려면 저장소 유형을 확장한 후 리소스를 선택합니다. 선택한 리소스에 대한 정보가 저장소 탐색기 아래쪽의 **작업** 및 **속성** 탭에 표시됩니다.

![][7]

-	**작업** 탭은 선택한 저장소 리소스에 대해 저장소 탐색기에서 수행할 수 있는 작업(예: 열기, 복사, 삭제 등)을 표시합니다. 작업은 리소스의 바로 가기 메뉴에도 표시됩니다.

-	**속성** 탭은 저장소 리소스의 속성(예: 유형, 로캘, 연결된 리소스 그룹, URL 등)을 표시합니다.

모든 저장소 계정에는 **포털에서 열기** 작업이 있습니다. 이 작업을 선택하면 저장소 탐색기는 Azure Preview 포털에서 선택한 저장소 계정을 표시합니다.

추가 작업 및 속성 값은 선택된 리소스에 따라 다르게 나타납니다. 예를 들어, Blob 컨테이너, 큐, 테이블 노드에는 모두 **만들기** 작업이 있습니다. Blob 컨테이너와 같은 개별 항목에는 **열기**, **삭제**, **Get Shared Access Signature**(공유 액세스 서명 가져오기)와 같은 작업이 있습니다. Blob 편집기를 여는 작업은 저장소 계정 Blob을 선택하면 표시됩니다.

## 저장소 계정 및 Blob 컨테이너 검색

Azure 계정 구독에서 특정 이름으로 저장소 계정 및 Blob 컨테이너를 찾으려면 저장소 탐색기의 **검색** 상자에 해당 이름을 입력합니다.

![][8]

**검색** 상자에 문자를 입력하면 리소스 트리에 해당 문자와 이름이 일치하는 저장소 계정 또는 Blob 컨테이너만 표시됩니다. 검색 조건을 지우려면 **검색** 상자의 **x** 단추를 선택합니다.

## 저장소 계정 편집

저장소 계정의 콘텐츠를 추가하거나 변경하려면 해당 저장소 유형의 **Open Editor**(편집기 열기) 명령을 선택합니다. 선택한 항목의 바로 가기 메뉴 또는 저장소 탐색기 아래쪽의 **작업** 탭에서 작업을 선택할 수 있습니다.

![][9]

Blob 컨테이너, 큐 및 테이블을 만들거나 삭제할 수 있습니다. **Open Blob Container Editor**(Blob 컨테이너 편집기 열기) 작업을 선택하여 저장소 탐색기에서 Blob을 편집할 수 있습니다.

### Blob 컨테이너 편집

1.	**Open Blob Container Editor**(Blob 컨테이너 편집기 열기) 작업을 선택합니다. Blob 컨테이너 편집기가 오른쪽 창에 나타납니다.

	![][10]

1.	**업로드** 단추를 선택한 후에 **파일 업로드** 명령을 선택합니다.

	![][11]

	업로드할 파일이 단일 폴더 내에 있으면 대신 Upload Folder(폴더 업로드) 명령을 선택할 수 있습니다.

1. **파일 업로드** 대화 상자에서 파일 상자 오른쪽의 줄임표(**…**) 단추를 선택하여 업로드할 파일을 선택합니다. 그 후 Blob에 대한 업로드 유형(블록, 페이지 또는 추가)을 선택합니다. 원한다면 Blob 컨테이너의 폴더에 파일을 업로드 하도록 선택할 수 있습니다. **Upload to folder (optional)**(업로드할 폴더(선택 사항)) 상자에 폴더의 이름을 입력합니다. 폴더가 없으면 폴더가 만들어집니다.

	![][12]

	다음 스크린샷에, 이미지 파일 세 개가 **Images** Blob 컨테이너의 **My New Files**라는 새 폴더에 업로드되었습니다.

	![][13]

	Blob 편집기 도구 모음의 단추를 사용하여 파일에 대해 선택, 다운로드, 열기, 복사, 삭제 등의 작업을 수행할 수 있습니다. 대화 상자 아래쪽의 **활동** 창에 작업이 성공적으로 수행되었는지가 표시되며, 보기에서 성공적인 활동만 제거하거나 전체 창을 지우는 것이 가능합니다. 파일 업로드 옆의 **+** 아이콘을 선택하여 업로드한 파일 목록을 자세히 볼 수 있습니다.

## 공유 액세스 서명(SAS) 만들기

일부 작업의 경우, 저장소 리소스에 액세스하려면 SAS가 필요합니다. 저장소 탐색기를 사용하여 SAS를 만들 수 있습니다.

1.	SAS를 만들 항목을 선택한 후 **작업** 창이나 항목의 바로 가기 메뉴에서 **Get Shared Access Signature**(공유 액세스 서명 가져오기) 명령을 선택합니다.

	![][14]

1.	**공유 액세스 서명** 대화 상자에서 정책, 시작 및 만료 날짜와 표준 시간대를 선택합니다. 또한 리소스에 적용할 액세스 수준(예: 읽기 전용, 읽기/쓰기 등)에 해당하는 확인란을 선택합니다. 완료되면 **만들기** 단추를 선택하여 SAS를 만듭니다.

	![][15]

1.	**공유 액세스 서명** 대화 상자에는 컨테이너와 함께 저장소 리소스에 액세스하는 데 사용할 수 있는 URL 및 쿼리 문자열이 나열됩니다. **복사** 단추를 클릭하여 문자열을 복사합니다.

	![][16]

## SAS 및 사용 권한 관리

Blob 컨테이너에 대한 액세스를 제어하려면 **Manage Access Control List**(액세스 제어 목록 관리) 및 **Set Public Access Level**(공용 액세스 수준 설정) 명령을 선택합니다.

-	Manage Access Control List(액세스 제어 목록 관리)를 사용하면 선택한 Blob 컨테이너에서 액세스 정책(사용자 읽기 쓰기 가능 여부)을 추가, 편집, 제거할 수 있습니다.
-	Set Public Access Level(공용 액세스 수준 설정)을 사용하면 리소스에 대한 공용 사용자의 액세스 수준을 지정할 수 있습니다.  

-

1.	Blob 컨테이너를 선택한 후 바로 가기 메뉴 또는 **작업** 창에서 **Manage Access Control List**(액세스 제어 목록 관리)를 선택합니다.

	![][17]

1.	**액세스 제어 목록** 대화 상자에서 **추가** 단추를 선택하여 액세스 정책을 추가합니다. 액세스 정책을 선택한 후 그에 대한 사용 권한을 선택합니다. 완료되면 **저장** 단추를 선택합니다.

	![][18]

1.	Blob 컨테이너에 대한 액세스 수준을 설정하려면 저장소 탐색기에서 Blob 컨테이너를 선택한 후 바로 가기 메뉴 또는 **작업** 창에서 **Set Public Access Level**(공용 액세스 수준 설정) 명령을 선택합니다.

	![][19]

1.	**Set Container Public Access Level**(컨테이너 공용 액세스 수준 설정) 대화 상자에서 공용 사용자에게 부여할 액세스 수준의 옵션 단추를 선택한 후 **적용** 단추를 선택합니다.

	![][20]

## 다음 단계
[Microsoft Azure 저장소 소개](./storage/storage-introduction.md)의 문서를 참조하여 Azure 저장소 서비스의 기능에 대해 알아봅니다.

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AddAccount1c.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AddAccount2c.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/External1c.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/External2c.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/External3c.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/External4c.png
[6]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/External5c.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Navigatec.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Searchc.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Edit1c.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Edit2c.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Edit3c.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Edit4c.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Edit5c.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SAS1c.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SAS2c.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SAS3c.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageSAS1c.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageSAS2c.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageSAS3c.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageSAS4c.png

<!---HONumber=AcomDC_0420_2016-->